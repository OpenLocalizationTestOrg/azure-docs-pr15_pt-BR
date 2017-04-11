<properties 
    pageTitle="Mover dados entre bancos de dados externa dimensionada nuvem | Microsoft Azure" 
    description="Explica como manipular fragmentos e mover dados através de um serviço de hospedagem interna usando o banco de dados Elástico APIs." 
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
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="moving-data-between-scaled-out-cloud-databases"></a>Mover dados entre bancos de dados de nuvem dimensionada-out

Se você for um Software como um desenvolvedor de serviço e inesperadamente seu aplicativo passa por demanda enorme, você precisa acomodar o crescimento. Assim que você adiciona mais bancos de dados (fragmentos). Como você redistribuir os dados para os novos bancos de dados sem interromper a integridade dos dados? Use a **ferramenta de mesclagem de divisão** para mover dados de bancos de dados restritos para os novos bancos de dados.  

A ferramenta de divisão direta é executada como um serviço web Azure. Um administrador ou desenvolvedor usa a ferramenta para mover shardlets (dados de um fragmentar) entre os bancos de dados diferentes (fragmentos). A ferramenta usa o gerenciamento de mapa fragmentar para manter o banco de dados de metadados do serviço e garantir a consistência mapeamentos.

![Visão geral][1]

## <a name="download"></a>Baixar
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)


## <a name="documentation"></a>Documentação
1. [Tutorial de ferramenta de banco de dados elástica direta de divisão](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
* [Configuração de segurança de divisão direta](sql-database-elastic-scale-split-merge-security-configuration.md)
* [Considerações de segurança de divisão direta](sql-database-elastic-scale-split-merge-security-configuration.md)
* [Gerenciamento de mapa fragmentar](sql-database-elastic-scale-shard-map-management.md)
* [Migrar bancos de dados existentes para fora de escala](sql-database-elastic-convert-to-use-elastic-tools.md)
* [Ferramentas de banco de dados elástica](sql-database-elastic-scale-introduction.md)
* [Elástico Glossário de ferramentas de banco de dados](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Por que usar a ferramenta de mesclagem de divisão?

**Flexibilidade**

Aplicativos precisam Alongar flexibilidade além dos limites de um banco de dados único DB do SQL Azure. Use a ferramenta para mover os dados conforme necessário para novos bancos de dados, mantendo a integridade.

**Dividir a crescer** 

Você precisa aumentar a capacidade de geral para lidar com crescimento explosivo. Para fazer isso, crie capacidade adicional por fragmentação os dados e distribuindo-o entre bancos de dados de forma incremental mais até que as necessidades de capacidade são preenchidas. Este é um exemplo perfeito do recurso 'Dividir'. 

**Mesclar para reduzir**

Capacidade precisa reduzir devido a natureza sazonal de um negócio. A ferramenta permite que você dimensionar menos unidades de escala de quando business diminui. O recurso 'direta' no serviço de divisão direta de dimensionamento Elástico aborda esse requisito. 

**Gerenciar áreas interativas movendo shardlets**

Com vários locatários por banco de dados, a alocação de shardlets para fragmentos pode levar a gargalos de capacidade em alguns fragmentos. Isso requer realocação shardlets ou movendo shardlets ocupado para fragmentos de novos ou menos utilizados. 

## <a name="concepts--key-features"></a>Conceitos e recursos principais

**Serviços hospedados do cliente**

A mesclagem de divisão é fornecida como um serviço hospedado no cliente. Você deve implantar e hospedar o serviço em sua assinatura do Microsoft Azure. O pacote que você baixar do NuGet contém um modelo de configuração para concluir com as informações para sua implantação específica. Consulte o [tutorial de divisão direta](sql-database-elastic-scale-configure-deploy-split-and-merge.md) para obter detalhes. Como o serviço é executado em sua assinatura do Azure, você pode controlar e configurar a maioria dos aspectos de segurança do serviço. O modelo padrão inclui opções para configurar SSL, a autenticação baseada em certificado de cliente, a criptografia de credenciais armazenadas, DoS preservando e restrições de IP. Você pode encontrar mais informações sobre os aspectos de segurança na seguinte [configuração de segurança de divisão direta](sql-database-elastic-scale-split-merge-security-configuration.md)de documento.

O padrão implantado o serviço é executado com um trabalhador e uma função de web. Cada usa o tamanho de máquina virtual A1 nos serviços de nuvem do Azure. Enquanto você não pode modificar essas configurações ao implantar o pacote, eles podem ser alterados após uma implantação bem-sucedida no serviço de nuvem em execução, (por meio do portal Azure). Observe que a função de trabalho não deve ser configurada para mais de uma única instância por razões técnicas. 

**Integração com o mapa fragmentar**

O serviço de divisão direta interage com o mapa de fragmentar do aplicativo. Ao usar o serviço de divisão direta para dividir ou mesclar intervalos ou mover shardlets entre fragmentos, o serviço mantém automaticamente o mapa de fragmentar atualizados. Para fazer isso, o serviço se conecta ao banco fragmentar mapa Gerenciador do aplicativo e mantém intervalos e mapeamentos como progresso de solicitações de mesclar/dividir/mover. Isso garante que o mapa de fragmentar sempre apresenta uma exibição atualizada quando operações de divisão direta estão acontecendo. Dividir, operações de movimentação de mesclagem e shardlet são implementadas movendo um lote de shardlets do fragmentar a fonte para o fragmentar de destino. Durante a shardlet operação de movimento a shardlets sujeito lote atual são marcadas como offline no mapa fragmentar e não estão disponível para conexões de roteamento dependentes de dados usando o **OpenConnectionForKey** API. 

**Conexões de shardlet consistente**

Quando inicia a movimentação de dados para um novo lote de shardlets, qualquer mapa fragmentar fornecido dependentes de dados roteamento conexões estabelecidas com o fragmentar armazenando o shardlet são eliminadas e subsequentes do mapa fragmentar APIs nesses shardlets são bloqueados enquanto a movimentação dos dados está em andamento para evitar inconsistências. Conexões com outras shardlets na mesma fragmentar também será obter eliminadas, mas terá êxito novamente imediatamente em Repetir. Depois que o lote é movido, os shardlets são marcados online novamente para fragmentar o destino e os dados de origem são removidos do fragmentar a fonte. O serviço passa por essas etapas para cada lote até que todos os shardlets foram movidos. Isso levará para diversas operações de kill de conexão durante o curso de concluir a operação Mesclar/Dividir/mover.  

**Gerenciar a disponibilidade de shardlet**

Limitar a conexão eliminar para o lote atual de shardlets conforme discutido acima restringe o escopo de indisponibilidade um lote de shardlets por vez. Isso é preferível uma abordagem de onde a fragmentar completa permanecerá offline para todos os seus shardlets durante o curso de uma operação de divisão ou mesclagem. O tamanho de um lote, definido como o número de shardlets distintas para mover-se ao mesmo tempo, é um parâmetro de configuração. Ele pode ser definido para cada operação de divisão e mesclagem dependendo das necessidades de disponibilidade e o desempenho do aplicativo. Observe que o intervalo que está sendo bloqueado no mapa fragmentar pode ser maior que o tamanho de lote especificado. Isso ocorre porque o serviço seleciona o tamanho do intervalo, para que o número real de valores-chave fragmentação nos dados de aproximadamente corresponde ao tamanho de lote. Isso é importante lembrar em particular para chaves de modo disperso preenchida fragmentação. 

**Armazenamento de metadados**

O serviço de divisão direta usa um banco de dados para manter seu status e manter logs durante o processamento de solicitação. O usuário cria este banco de dados em sua assinatura e fornece a cadeia de conexão para ele no arquivo de configuração para a implantação do serviço. Administradores da organização do usuário também podem se conectar ao banco de dados para analisar o andamento de solicitação e para investigar informações detalhadas sobre possíveis falhas.

**Reconhecimento de fragmentação**

O serviço de divisão direta faz distinção entre tabelas (1) sharded, tabelas de referência (2) e tabelas (3) normais. A semântica de uma operação de mesclar/dividir/mover depende do tipo da tabela usada e é definida da seguinte maneira: 

* **Tabelas de sharded**: mover operações, mesclar e dividir mover shardlets de fonte para fragmentar de destino. Após a conclusão bem-sucedida da solicitação geral, esses shardlets já não estão presentes na origem. Observe que as tabelas de destino precisam existir em fragmentar o destino e não devem conter dados no intervalo de destino antes de processamento da operação. 

* **Tabelas de referência**: para tabelas de referência, a divisão, mesclar e mover operações copiem os dados da origem para o fragmentar de destino. Observe, contudo, que nenhuma alteração ocorre em fragmentar o destino para uma determinada tabela se qualquer linha já estiver presente nesta tabela de destino. A tabela deve ser vazio para qualquer operação de cópia de tabela de referência ser processado.

* **Outras tabelas**: outras tabelas podem estar presentes na origem ou de destino de uma operação de divisão e mesclagem. O serviço de divisão direta ignora essas tabelas qualquer movimentação de dados ou operações de cópia. Observe, contudo, que eles podem interferir com essas operações em caso de restrições.

As informações na referência versus tabelas sharded são fornecidas por **SchemaInfo** APIs no mapa fragmentar. O exemplo a seguir ilustra o uso dessas APIs em um smm de objeto do Gerenciador de mapa fragmentar determinado: 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

As tabelas 'região' e 'país' são definidas como tabelas de referência e serão copiadas com operações de mesclar/dividir/mover. 'cliente' e 'orders' alternadamente são definidos como tabelas sharded. C_CUSTKEY e O_CUSTKEY servem como a chave de fragmentação. 

**Integridade referencial**

O serviço de divisão direta analisa dependências entre tabelas e usa relações de chave estrangeira de chave primária para as operações para mover as tabelas de referência e shardlets do estágio. Em geral, tabelas de referência são copiadas primeiro ordem de dependência, então shardlets são copiados na ordem de suas dependências dentro de cada lote. Isso é necessário para que as restrições de FK CP em fragmentar o destino são cumpridas chegam os novos dados. 

**Fragmentar mapa consistência e Eventual conclusão**

Na presença de falhas, o serviço de divisão direta currículos operações após qualquer interrupção e pretende concluir qualquer em solicitações de andamento. No entanto, pode haver irrecuperáveis situações, por exemplo, quando o fragmentar de destino for perdido ou comprometido reparado. Nessas circunstâncias, pode continuar algumas shardlets que deveriam ser movido residem em fragmentar a fonte. O serviço garante que os mapeamentos de shardlet são atualizados somente depois que os dados necessários foi copiados com êxito para o destino. Shardlets só são excluídos na origem depois que todos os seus dados foi copiado para o destino e os mapeamentos correspondentes tenham sido atualizados com êxito. A operação de exclusão ocorre em segundo plano enquanto o intervalo já está online em fragmentar o destino. O serviço de divisão direta sempre garante correção dos mapeamentos armazenada no mapa fragmentar.


## <a name="the-split-merge-user-interface"></a>A interface do usuário de divisão direta

O pacote de serviço de divisão direta inclui uma função de trabalho e uma função web. A função web é usada para enviar solicitações de divisão direta de forma interativa. Os principais componentes da interface do usuário são da seguinte maneira:

-    Tipo de operação: O tipo de operação é um botão de rádio que controla o tipo de operação realizada pelo serviço para esta solicitação. Você pode escolher entre a divisão, mesclar e mover cenários. Você também pode cancelar uma operação de enviada anteriormente. Você pode usar dividido, mesclar e mover solicitações de intervalo fragmentar mapas. Fragmentar lista mapeia somente operações de movimentação de suporte.

-    Mapa de fragmentar: A próxima seção de parâmetros de solicitação cobrir informações sobre o mapa de fragmentar e o banco de dados que hospeda seu mapa fragmentar. Em particular, você precisa fornecer o nome do servidor de banco de dados do Azure SQL e do banco de dados que hospeda a shardmap, credenciais para se conectar ao banco de dados de mapa fragmentar e finalmente o nome do mapa fragmentar. Atualmente, a operação aceita somente um único conjunto de credenciais. Essas credenciais precisam ter permissões suficientes para realizar as alterações para o mapa de fragmentar assim como para os dados do usuário nos fragmentos.

-    Intervalo de origem (dividir e mesclar): uma operação de divisão e mesclagem processa um intervalo usando sua chave de alto e baixo. Para especificar uma operação com um valor de chave alto ilimitado, marque a caixa de seleção "chave alta é máx" e deixe o campo de chave alto vazio. Os valores de chave do intervalo que você especificar não precisa coincidir com precisão um mapeamento e seus limites no seu mapa fragmentar. Se você não especificar qualquer limites de intervalo todo o serviço irá inferir o intervalo mais próximo para você automaticamente. Você pode usar o script do PowerShell GetMappings.ps1 para recuperar os mapeamentos atuais em um mapa de fragmentar determinado.

-    O comportamento de origem de divisão (divisão): para operações de divisão, defina o ponto para dividir o intervalo de origem. Você pode fazer isso, fornecendo a chave de fragmentação onde deseja que a divisão deverá ocorrer. Use o botão de rádio especificar se deseja que a parte inferior do intervalo (excluindo a chave de divisão) para mover, ou se você deseja que a parte superior para mover (incluindo a chave de divisão).

-    Shardlet (mover) de origem: mover operações são diferentes das operações de dividir ou mesclar conforme eles não exigem um intervalo para descrever a fonte. Uma fonte para mover simplesmente é identificada pelo valor de chave de fragmentação que pretende mover.

-    Fragmentar (divisão) de destino: depois que você tiver fornecido as informações na origem da sua operação de divisão, você precisa definir onde você deseja que os dados sejam copiados para fornecendo o nome de servidor e banco de dados de Db do SQL Azure para o destino.

-    Intervalo de destino (mesclar): operações de mesclagem mover shardlets para um fragmentar existente. Você identificar o fragmentar existente, fornecendo os limites de intervalo do intervalo existente que você deseja mesclar com.

-    Tamanho do lote: O tamanho do lote controla o número de shardlets que irão offline por vez durante a movimentação dos dados. Este é um valor inteiro, onde você pode usar valores menores quando você é sensível ao longo de períodos de tempo de inatividade para shardlets. Valores maiores aumentará o tempo que um determinado shardlet está offline mas podem melhorar o desempenho.

-    Id da operação (Cancelar): Se você tiver uma operação em andamento que não for mais necessário, você pode cancelar a operação, fornecendo sua ID de operação neste campo. Você pode recuperar a ID de operação da tabela de status da solicitação (consulte a seção 8.1) ou da saída no navegador da web onde você enviou a solicitação.


## <a name="requirements-and-limitations"></a>Requisitos e limitações 

A implementação atual do serviço de mesclagem dividida está sujeito às seguintes requisitos limitações: 

* Os fragmentos precisam existir e ser registrado no mapa fragmentar antes de uma operação de mesclagem de divisão nesses fragmentos pode ser executada. 

* O serviço não criar tabelas ou quaisquer outros objetos de banco de dados automaticamente como parte de suas operações. Isso significa que o esquema para sharded todas as tabelas e tabelas de referência precisa existir em fragmentar o destino antes de qualquer operação de mesclar/dividir/mover. Tabelas sharded em particular devem estar vazias no intervalo onde shardlets novos serão adicionados por uma operação de mesclar/dividir/mover. Caso contrário, a operação falhará verificação de consistência inicial em fragmentar o destino. Observe também que fazem referência a dados são copiados somente se a referência de tabela está vazia e que não há nenhuma garantia de consistência em relação à outra simultânea operações de gravação nas tabelas de referência. Recomendamos que isso: ao executar operações de mesclar/dividir, não há outras operações de gravação fazer alterações a tabelas de referência.

* O serviço depende de identidade de linha estabelecida por um índice exclusivo ou chave que inclui a chave de fragmentação para melhorar o desempenho e confiabilidade para shardlets grande. Isso permite que o serviço mover dados em um detalhamento ainda menores que apenas o valor de chave de fragmentação. Isso ajuda a reduzir a quantidade máxima de espaço de log e bloqueios que são necessários durante a operação. Considere criar um índice exclusivo ou uma chave primária, incluindo a chave de fragmentação em uma determinada tabela se desejar usar essa tabela com solicitações de mesclar/dividir/mover. Por razões de desempenho, a chave de fragmentação deve ser a coluna à esquerda na chave ou o índice.

* Durante o curso de processamento de solicitação, alguns dados de shardlet podem estar presentes tanto na origem e o fragmentar de destino. Isso é necessário para proteger contra falhas durante a movimentação de shardlet. A integração de divisão direta com o mapa de fragmentar garante que conexões através dos dados dependentes roteamento APIs usando o método **OpenConnectionForKey** no mapa fragmentar não vê qualquer estados intermediários inconsistentes. No entanto, quando a conexão com a fonte ou os fragmentos de destino sem usar o método **OpenConnectionForKey** , estados intermediários inconsistentes podem estar visíveis quando Mesclar/Dividir/mover solicitações estão acontecendo. Essas conexões podem mostrar resultados parciais ou duplicados dependendo do tempo ou o fragmentar subjacente a conexão. Essa limitação atualmente inclui as conexões feitas por dimensionamento Elástico Multi-Shard consultas.

* O banco de dados de metadados para o serviço de divisão direta não deve ser compartilhado entre diferentes funções. Por exemplo, uma função do serviço de divisão direta em execução no teste precisa apontar para um banco de dados de metadados diferentes que a função de produção.
 

## <a name="billing"></a>Cobrança 

O serviço de divisão direta é executado como um serviço de nuvem em sua assinatura do Microsoft Azure. Portanto encargos para serviços de nuvem se aplicam à sua instância do serviço. A menos que você frequentemente realizar operações de mesclar/dividir/mover, recomendamos que você exclua seu serviço de nuvem de divisão direta. Que economiza custos para execução ou implantados instâncias de serviço de nuvem. Você pode implantar novamente e iniciar sua configuração prontamente executável sempre que você precisa realizar operações dividir ou mesclar. 
 
## <a name="monitoring"></a>Monitoramento 
### <a name="status-tables"></a>Tabelas de status 

O serviço de divisão direta fornece a tabela de **status da solicitação** nos metadados armazenar o banco de dados para monitoramento de solicitações em andamento e concluídas. A tabela lista uma linha para cada solicitação de divisão direta que foi enviada para esta instância do serviço de mesclagem de divisão. Ele fornece as seguintes informações para cada solicitação:

* **Carimbo de hora**: quando a solicitação foi iniciada de data e hora.

* **OperationId**: um GUID que identifica exclusivamente a solicitação. Essa solicitação também pode ser usada para cancelar a operação enquanto ela estiver ainda em andamento.

* **Status**: O estado atual da solicitação. Para solicitações em andamento, ele também lista a fase atual no qual a solicitação é.

* **CancelRequest**: um sinalizador que indica se a solicitação foi cancelada.

* **Andamento**: uma estimativa de porcentagem de conclusão para a operação. Um valor de 50 indica que a operação é aproximadamente 50% concluída.

* **Detalhes**: valor de um XML que fornece um relatório de andamento mais detalhado. O relatório de progresso é atualizado periodicamente conforme conjuntos de linhas são copiados da origem ao destino. No caso de falhas ou exceções, esta coluna também inclui informações mais detalhadas sobre a falha.


### <a name="azure-diagnostics"></a>Diagnóstico do Azure

O serviço de divisão direta usa diagnóstico do Azure com base no Azure SDK 2,5 para monitoramento e diagnóstico. Você controla a configuração de diagnóstico, conforme explicado aqui: [Habilitando diagnóstico nos serviços de nuvem do Azure e máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md). O pacote de download inclui duas configurações de diagnóstico – uma para a função da web e outra para a função de trabalho. Essas configurações de diagnóstico para o serviço sigam as orientações de [Conceitos básicos de serviço de nuvem do Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Ele inclui as definições para registrar contadores de desempenho, IIS logs, Logs de eventos do Windows e logs de eventos do aplicativo de divisão direta. 

## <a name="deploy-diagnostics"></a>Implantar o diagnóstico 

Para habilitar o monitoramento e diagnóstico usando a configuração de diagnóstico para as funções da web e trabalhador fornecidas pelo pacote NuGet, execute os seguintes comandos usando o PowerShell do Azure: 

    $storage_name = "<YourAzureStorageAccount>" 
    
    $key = "<YourAzureStorageAccountKey" 
    
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  
    
    
    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 
    
    
    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

Você pode encontrar mais informações sobre como configurar e implantar as configurações do diagnóstico aqui: [Habilitando diagnóstico nos serviços de nuvem do Azure e máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md). 

## <a name="retrieve-diagnostics"></a>Recuperar diagnóstico 

Você pode acessar facilmente o diagnóstico do Explorer do servidor Visual Studio na parte Azure da árvore de Server Explorer. Abrir uma instância do Visual Studio e na barra de menus, clique em modo de exibição e Server Explorer. Clique no ícone Azure para se conectar à sua assinatura do Azure. Navegue até Azure -> armazenamento -> <your storage account> -> tabelas -> WADLogsTable. Para obter mais informações, consulte [Recursos de armazenamento de navegação com Server Explorer](http://msdn.microsoft.com/library/azure/ff683677.aspx). 

![WADLogsTable][2]

O WADLogsTable realçado na figura acima contém os eventos detalhados do log de aplicativo do serviço direta dividido. Observe que a configuração padrão do pacote baixado é voltada para uma implantação de produção. Portanto, o intervalo em que os logs e contadores são extraídos das instâncias de serviço é grandes (5 minutos). Para teste e desenvolvimento, reduza o intervalo ajustando as configurações de diagnóstico da web ou a função de trabalho às suas necessidades. Clique com botão direito na função no Visual Studio Server Explorer (veja acima) e ajuste o período de transferência na caixa de diálogo para as configurações de diagnóstico: 

![Configuração][3]


## <a name="performance"></a>Desempenho

Em geral, é melhor desempenho esperado de maior, mais alto desempenho serviço camadas em Azure SQL Database. Maiores alocações de IO, CPU e memória para os níveis de serviço superiores se beneficiar a cópia em massa e excluir operações que usa o serviço de divisão direta. Por que motivo, aumente o nível de serviço apenas para os bancos de dados para um período limitado, definido.

O serviço também executa consultas de validação como parte de suas operações normais. Essas consultas de validação Verificar presença inesperada de dados no intervalo de destino e certifique-se de que qualquer operação de mesclar/dividir/mover inicia a partir de um estado consistente. Essas consultas tudo funcionam em intervalos de chave fragmentação definidos pelo escopo da operação e o tamanho do lote fornecidos como parte da definição da solicitação. Essas consultas executam melhor quando um índice está presente que tem uma chave de fragmentação como a coluna à esquerda. 

Além disso, uma propriedade de exclusividade com a chave de fragmentação como a coluna à esquerda permitirá que o serviço use uma abordagem otimizada que limita consumo de recursos em termos de memória e espaço de log. Essa propriedade exclusividade é necessária para mover os tamanhos de dados grande (normalmente acima de 1GB). 

## <a name="how-to-upgrade"></a>Como atualizar

1. Siga as etapas em [implantar um serviço de divisão direta](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Altere seu arquivo de configuração do serviço de nuvem para a sua implantação de divisão direta refletir os novos parâmetros de configuração. Um novo parâmetro obrigatório é as informações sobre o certificado usado para criptografia. Uma maneira fácil de fazer isso é comparar o novo arquivo de modelo de configuração do download em relação a sua configuração existente. Verifique se que você adicionar as configurações de "DataEncryptionPrimaryCertificateThumbprint" e "DataEncryptionPrimary" para a web e a função de trabalho.
3. Antes de implantar a atualização do Azure, certifique-se de que todas as operações de divisão atualmente em execução direta tiveram terminado. Você pode fazer isso facilmente consultando os status da solicitação e PendingWorkflows tabelas do banco de dados de metadados de mesclagem de divisão para solicitações em andamento.
4. Atualize sua implantação de serviço de nuvem existente para dividir direta em sua assinatura do Azure com o novo pacote e seu arquivo de configuração do serviço atualizado.

Você não precisa provisionar um novo banco de dados de metadados para dividir direta de atualização. A nova versão atualizará automaticamente o seu banco de dados de metadados existente para a nova versão. 

## <a name="best-practices--troubleshooting"></a>Práticas recomendadas e solução de problemas
 
-    Defina um locatário de teste e exercício as operações de mesclar/dividir/mover mais importantes com o locatário de teste entre vários fragmentos. Certifique-se de que todos os metadados é definida corretamente no seu mapa fragmentar e que as operações não violar restrições ou chaves estrangeiras.
-    Manter o locatário de teste problemas relacionados ao tamanho dos dados acima o tamanho máximo de dados do seu locatário do maior para garantir que você não está encontrando o tamanho dos dados. Isso ajuda você a avaliar um limite superior no período de tempo que leva para mover um locatário único. 
-    Certifique-se de que seu esquema permite exclusões. O serviço de divisão direta requer a capacidade de remover dados de fragmentar a origem depois que os dados foram copiados com êxito para o destino. Por exemplo, **Excluir disparadores** pode impedir que o serviço excluir os dados na origem e pode causar operações falha.
-    A chave de fragmentação deve ser a coluna à esquerda em sua chave primária ou a definição de índice exclusivo. Que garante o melhor desempenho para as consultas de validação dividir ou mesclar e para as operações de movimentação e exclusão de dados reais que sempre operam em intervalos de chave fragmentação.
-    Colocar seu serviço de divisão direta no Centro de dados e região onde residem os bancos de dados. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]



<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
 
