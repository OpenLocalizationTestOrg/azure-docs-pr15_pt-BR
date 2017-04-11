<properties 
    pageTitle="Usando o Gerenciador de recuperação para corrigir problemas de mapa de fragmentar | Microsoft Azure" 
    description="Usar a classe RecoveryManager para solucionar problemas com mapas de fragmentar" 
    services="sql-database" 
    documentationCenter=""  
    manager="jhubbard"
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/05/2016" 
    ms.author="ddove"/>

# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Usando a classe RecoveryManager para corrigir problemas de mapa de fragmentar

A classe de [RecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.aspx) fornece aplicativos ADO.Net a capacidade de facilmente detectar e corrigir qualquer inconsistência entre o mapa de fragmentar global (GSM) e o mapa de fragmentar local (LSM) em um ambiente de banco de dados sharded. 

O GSM e LSM acompanhar o mapeamento de cada banco de dados em um ambiente sharded. Ocasionalmente, uma quebra ocorre entre o GSM e o LSM. Nesse caso, use a classe de RecoveryManager para detectar e reparar a quebra.

A classe RecoveryManager é parte da [biblioteca de cliente do banco de dados elástica](sql-database-elastic-database-client-library.md). 


![Mapa de fragmentar][1]


Para definições de termos, consulte [Glossário de ferramentas de banco de dados elástica](sql-database-elastic-scale-glossary.md). Para compreender como os **ShardMapManager** é usada para gerenciar dados em uma solução sharded, consulte [Fragmentar mapear gerenciamento](sql-database-elastic-scale-shard-map-management.md).


## <a name="why-use-the-recovery-manager"></a>Por que usar o Gerenciador de recuperação?

Em um ambiente de banco de dados sharded, há um locatário por banco de dados e muitos bancos de dados por servidor. Também pode haver vários servidores no ambiente. Cada banco de dados é mapeado no mapa fragmentar, para que as chamadas podem ser encaminhadas para o servidor correto e o banco de dados. Bancos de dados são controlados de acordo com uma **chave de fragmentação**, e cada fragmentar é atribuído a um **intervalo de valores-chave**. Por exemplo, uma chave de fragmentação pode representar os nomes de cliente do "D" para "F" O mapeamento de todos os fragmentos (também conhecidos como bancos de dados) e seus intervalos de mapeamento estão contidos no **mapa fragmentar global (GSM)**. Cada banco de dados também contém um mapa dos intervalos contidos na fragmentar — isso é conhecido como o **local fragmentar mapear (LSM)**. Quando um aplicativo se conecta a uma fragmentar, o mapeamento será armazenado com o aplicativo para recuperação rápida. O LSM é usado para validar dados armazenados em cache. 

O GSM e LSM podem ficar fora de sincronia pelos seguintes motivos:

1. A exclusão de um fragmentar cujo intervalo é considerado não está mais em uso ou renomeação de um fragmentar. Excluindo um resultados fragmentar em um **mapeamento de fragmentar órfão**. Similary, um banco de dados renomeado pode causar um mapeamento de fragmentar órfão. Dependendo da finalidade da alteração, o fragmentar talvez precise ser removido ou o local de fragmentar precisa ser atualizado. Para recuperar um banco de dados excluído, consulte [restaurar um banco de dados a um ponto anterior no tempo, restaurar um banco de dados excluído ou recuperar uma interrupção do Centro de dados](sql-database-troubleshoot-backup-and-restore.md).
2. Ocorre um evento de failover localização geográfica. Para continuar, um deve atualizar o nome do servidor e o nome de banco de dados do Gerenciador de mapa fragmentar no aplicativo e, em seguida, atualizar os detalhes de mapeamento de fragmentar para todos e quaisquer fragmentos em um mapa de fragmentar. No caso de um failover de localização geográfica, uma lógica de recuperação deve ser automatizada do fluxo de trabalho de failover. Automatizar ações de recuperação permite uma capacidade de gerenciamento irrestrito para bancos de dados habilitados por localização geográfica e evita ações humanas manuais.
3. Um fragmentar ou banco de dados ShardMapManager é restaurado para um anteriormente ponto no tempo.

Para obter mais informações sobre as ferramentas de banco de dados de SQL Azure Database elástica, localização geográfica replicação e restauração, consulte o seguinte: 

* [Visão geral: Nuvem business banco de dados e continuidade de recuperação de dados com o banco de dados SQL](sql-database-business-continuity.md) 
* [Introdução às ferramentas de banco de dados elástica](sql-database-elastic-scale-get-started.md)  
* [Gerenciamento de ShardMap](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Recuperando RecoveryManager de um ShardMapManager 

A primeira etapa é criar uma instância de RecoveryManager. O [método GetRecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager.aspx) retorna o Gerenciador de recuperação para a instância atual do [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) . Para resolver qualquer inconsistência no mapa fragmentar, primeiro você deve recuperar o RecoveryManager para o mapa de fragmentar específico. 

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager(); 

Neste exemplo, o RecoveryManager é inicializado da ShardMapManager. O ShardMapManager que contém um ShardMap também já foi inicializado. 

Como este código de aplicativo manipula o mapa de fragmentar em si, as credenciais usadas no método fábrica (no exemplo acima, smmConnectionString) devem ser credenciais que tem permissões de leitura e gravação no banco de dados GSM referenciado pela cadeia de conexão. Essas credenciais são normalmente diferentes das credenciais usadas para abrir conexões para o roteamento de dependentes de dados. Para obter mais informações, consulte [usando credenciais no cliente do banco de dados elástica](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Removendo uma fragmentar do ShardMap depois que um fragmentar é excluído

O [método de DetachShard](https://msdn.microsoft.com/library/azure/dn842083.aspx) desconecta o fragmentar determinado do mapa fragmentar e exclui mapeamentos associados a fragmentar.  

* O parâmetro de local é o local de fragmentar, especificamente o nome do servidor e o nome do banco de dados, da fragmentar sendo desconectado. 
* O parâmetro shardMapName é o nome do mapa fragmentar. Isso só é necessária quando vários mapas de fragmentar são gerenciados pelo Gerenciador de mapa fragmentar mesmo. Opcional. 

**Importante**: usar essa técnica somente se você tiver certeza de que o intervalo para o mapeamento atualizado está vazio. Os métodos acima não marque dados para o intervalo sendo movido, portanto, é melhor incluir verificações no seu código.

Este exemplo remove fragmentos do mapa fragmentar. 

    rm.DetachShard(s.Location, customerMap); 

O mapa no local de fragmentar no GSM antes da exclusão da fragmentar. Porque o fragmentar foi excluído, ele será considerado foi intencional e o intervalo de chave fragmentação não está mais em uso. Se esse não for o caso, você pode executar restauração de ponto no tempo. para recuperar a fragmentar um ponto no tempo anterior. (Nesse caso, revise a seção abaixo para detectar inconsistências fragmentar.) Para recuperar, consulte [restaurar um banco de dados a um ponto anterior no tempo, restaurar um banco de dados excluído ou recuperar uma interrupção do Centro de dados](sql-database-troubleshoot-backup-and-restore.md).

Considerando que a exclusão de banco de dados foi intencional, a ação de limpeza administrativo final é excluir a entrada para o fragmentar no Gerenciador de mapa de fragmentar. Isso impede que o aplicativo inadvertidamente gravar informações em um intervalo que não é esperado.

## <a name="to-detect-mapping-differences"></a>Detectar diferenças de mapeamento 

O [método de DetectMappingDifferences](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences.aspx) seleciona e retorna um dos mapas fragmentar (locais ou globais) como a origem de verdade e reconcilia mapeamentos em ambos os mapas de fragmentar (GSM e LSM).

    rm.DetectMappingDifferences(location, shardMapName);

* O *local* Especifica o nome do servidor e o nome do banco de dados. 
* O parâmetro *shardMapName* é o nome do mapa fragmentar. Isso só é necessário se vários mapas de fragmentar são gerenciados pelo Gerenciador de mapa fragmentar mesmo. Opcional. 

## <a name="to-resolve-mapping-differences"></a>Para resolver diferenças de mapeamento

O [método de ResolveMappingDifferences](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences.aspx) seleciona um dos mapas fragmentar (locais ou globais) como fonte de verdade e reconcilia mapeamentos em ambos os mapas de fragmentar (GSM e LSM).

    ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   
* O parâmetro *RecoveryToken* enumera as diferenças na mapeamentos entre o GSM e o LSM para o fragmentar específico. 

* A [enumeração MappingDifferenceResolution](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution.aspx) é usada para indicar o método para resolver a diferença entre os mapeamentos de fragmentar. 
* **MappingDifferenceResolution.KeepShardMapping** é recomendável que o LSM contém o mapeamento preciso e, portanto, o mapeamento na fragmentar deve ser usado. Esse é geralmente o caso em caso de um failover: a fragmentar agora reside em um novo servidor. Desde que a fragmentar primeiro deve ser removido do GSM (usando o método RecoveryManager.DetachShard), um mapeamento não existe mais sobre o GSM. Portanto, ao LSM deve ser usado para estabelecer o mapeamento de fragmentar novamente.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Anexar um fragmentar o ShardMap depois que um fragmentar for restaurado 

O [método de AttachShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard.aspx) anexa o fragmentar determinado para o mapa de fragmentar. Ele detecta qualquer inconsistência de mapa fragmentar e atualiza os mapeamentos para corresponder a fragmentar no ponto a restauração de fragmentar. Presume-se que o banco de dados também é renomeado para refletir o nome do banco de dados original (antes de quando o fragmentar foi restaurado), desde o ponto no tempo padrões de restauração para um novo banco de dados anexado com o carimbo de hora. 

    rm.AttachShard(location, shardMapName) 

* O parâmetro de *local* é o nome do servidor e o nome do banco de dados, do fragmentar sendo anexado. 

* O parâmetro *shardMapName* é o nome do mapa fragmentar. Isso só é necessária quando vários mapas de fragmentar são gerenciados pelo Gerenciador de mapa fragmentar mesmo. Opcional. 

Este exemplo adiciona um fragmentar para o mapa de fragmentar que foi restaurado recentemente de um anteriormente ponto no tempo. Como o fragmentar (ou seja, o mapeamento para o fragmentar no LSM) foi restaurado é potencialmente inconsistente com a entrada de fragmentar no GSM. Fora do código neste exemplo, o fragmentar foi restaurado e renomeado para o nome original do banco de dados. Desde que ele foi restaurado, será considerado que o mapeamento no LSM é o mapeamento confiável. 

    rm.AttachShard(s.Location, customerMap); 
    var gs = rm.DetectMappingDifferences(s.Location); 
      foreach (RecoveryToken g in gs) 
       { 
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
       } 

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Atualizando locais fragmentar após um geográfica-failover (restauração) dos fragmentos

Em caso de um failover de localização geográfica, o banco de dados secundário é disponibilizado escrever e mudará para o novo banco de dados principal. O nome do servidor e possivelmente o banco de dados (dependendo da configuração), pode ser diferente do principal original. Portanto, as entradas de mapeamento para o fragmentar na GSM e LSM devem ser corrigidas. Da mesma forma, se o banco de dados for restaurado para um nome ou local diferente, ou para um ponto anterior no tempo, isso pode causar inconsistências nos mapas fragmentar. O Gerenciador de mapa fragmentar controla a distribuição de conexões abertas no banco de dados corretos. Distribuição baseia-se nos dados de mapa fragmentar e o valor da chave de fragmentação que é o destino de solicitação do aplicativo. Após um failover de localização geográfica, essa informação deve ser atualizada com o nome do servidor precisos, o nome de banco de dados e o mapeamento de fragmentar do banco de dados recuperado. 

## <a name="best-practices"></a>Práticas recomendadas

Localização geográfica failover e recuperação são operações normalmente gerenciadas por um administrador de nuvem do aplicativo intencionalmente utilizando um dos recursos de continuidade de negócios de bancos de dados do Azure SQL. Planejamento de continuidade de negócios requer processos, procedimentos e medidas para garantir que as operações de negócios podem continuar sem interrupção. Os métodos disponíveis como parte da classe RecoveryManager deve ser usado dentro este fluxo de trabalho para garantir que o GSM e LSM são atualizadas com base na ação de recuperação realizada. Há 5 etapas básicas para corretamente garantindo que a GSM e LSM refletem as informações precisas após um evento de failover. O código de aplicativo para executar essas etapas pode ser integrado ao fluxo de trabalho e ferramentas existentes. 

1. Recupere o RecoveryManager da ShardMapManager. 
2. Desanexe o fragmentar antigo do mapa fragmentar.
3. Anexe a fragmentar novo para o mapa de fragmentar, incluindo o novo local de fragmentar.
4. Detecte inconsistências no mapeamento entre o GSM e LSM. 
5. Resolva diferenças entre o GSM e LSM, confiante o LSM. 

Este exemplo executa as seguintes etapas:
1. Remove fragmentos de mapa de fragmentar que refletir fragmentar locais antes do evento de failover.
2. Anexa fragmentos para o mapa de fragmentar refletindo os novos locais de fragmentar (o parâmetro "Configuration.SecondaryServer" é o novo nome de servidor, mas o mesmo nome de banco de dados).
3. Recupera os tokens de recuperação por detectar diferenças de mapeamento entre o GSM e o LSM para cada fragmentar. 
4. Resolve as inconsistências confiar no mapeamento do LSM de cada fragmentar. 

    var fragmentos = smm. GetShards();  foreach (fragmentar s em fragmentos) {se (s.Location.Server = = Configuration.PrimaryServer) {ShardLocation slNew = ShardLocation novo (Configuration.SecondaryServer, s.Location.Database); 
        
          rm.DetachShard(s.Location); 
        
          rm.AttachShard(slNew); 
        
          var gs = rm.DetectMappingDifferences(slNew); 
    
          foreach (RecoveryToken g in gs) 
            { 
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
            } 
        } 
    } 



[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png
 
