<properties
    pageTitle="Planejar a atualização para o V12 de banco de dados do SQL | Microsoft Azure"
    description="Descreve os preparativos e limitações envolvidas na atualização para versão V12 do Azure SQL Database."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="genemi"/>


# <a name="plan-and-prepare-to-upgrade-to-sql-database-v12"></a>Planejar e preparar a atualização para V12 de banco de dados SQL


Este tópico descreve o planejamento e preparativos você deve executar para atualizar seus bancos de dados do SQL Azure da versão V11 para V12.


Um novo [Portal Azure](https://portal.azure.com/) está disponível para dar suporte a atualização para o V12.


A tabela a seguir lista outros tópicos da Ajuda para V12.


| Título e link | Descrição do conteúdo |
| :--- | :--- |
| [O que há de novo no V12 de banco de dados SQL](sql-database-v12-whats-new.md) | Descreve os detalhes de como V12 traz Azure SQL Database mais próximo à paridade completa com o Microsoft SQL Server. |
| [Criar um banco de dados no V12 de banco de dados SQL](sql-database-get-started.md) | Descreve como você pode criar um novo banco de dados do SQL Azure na versão V12. Ele descreve várias opções além de apenas um banco de dados vazio. |


## <a name="plan-ahead"></a>Planejar


As subseções a seguir descrevem a aprendizagem e a tomada de decisões que você deve abordar antes de fazer um ações em direção a atualizar seu banco de dados do SQL Azure para V12.

### <a name="version-clarification"></a>Explicação de versão


Este documento se refere a atualização do Microsoft Azure SQL Database da versão V11 para V12. Mais anteriormente os números de versão são próximo dois valores a seguir, conforme relatado pela instrução Transact-SQL **Selecione @@version; ** :


- 12.0.2000.8 *(ou um pouco maior, V12)*
- 11.0.9228.18 *(V11)*
 - Às vezes, V11 foi também denominados SAWA v2.

### <a name="service-tier-planning"></a>Planejamento de nível de serviço


Começando com V12, Azure SQL Database oferecerá suporte apenas os níveis de serviço chamados Basic, Standard e Premium. Não há suporte para o nível de serviço Web e Business V12. Portanto, se você planeja atualizar seu banco de dados do SQL Azure que está na camada de serviço Web ou empresas, você deve decidir qual seu novo nível de serviço deve ser.


Para obter informações detalhadas sobre os níveis de serviço Basic, Standard e Premium, consulte:

- [Níveis de serviço de banco de dados SQL](sql-database-service-tiers.md)
- [Atualizar bancos de dados do SQL banco de dados da Web/Business para novos níveis de serviço](sql-database-upgrade-server-portal.md)



### <a name="review-the-geo-replication-configuration"></a>Revisar a configuração de replicação geográfica


Se seu banco de dados do SQL Azure está configurado para replicação de localização geográfica, você deve documentar sua configuração atual e parar replicação de localização geográfica, antes de iniciar as ações de preparação para atualização. Após a conclusão da atualização reconfigure seu banco de dados para replicação de localização geográfica.


A estratégia é deixar a fonte intacto e testar em uma cópia do banco de dados.


## <a name="preparation-actions"></a>Ações de preparação


Depois de terminar seu planejamento, você pode executar as etapas de ação descritas nas subseções a seguir para se preparar para a fase de atualização final.


Descrições detalhadas da fase de atualização final estão disponíveis nos tópicos da Ajuda que estão vinculados na parte superior deste tópico de Ajuda.


### <a name="service-tier-actions"></a>Ações de nível de serviço


Não há suporte para o serviço da Web e Business preços camada em V12.


Se seu banco de dados do SQL Azure de V11 é um banco de dados da Web ou de negócios, o processo de atualização oferece mudar seu banco de dados para um nível compatível. A atualização recomenda uma camada que se adapte o histórico de carga de trabalho de seu banco de dados. No entanto, você pode escolher qualquer nível com suporte que você gostar.


Você pode reduzir as etapas necessárias durante a atualização, alternando o banco de dados de V11 longe a camada de negócios e da Web antes de iniciar a atualização. Você pode fazer isso usando o novo [Portal Azure](https://portal.azure.com/).


Se você não tiver certeza de qual nível de serviço para alternar para, o nível de S2 da camada padrão talvez seja uma opção sensata de inicial. Qualquer nível menor terá menos recursos que a camada da Web e Business tinha.


### <a name="suspend-geo-replication-during-upgrade"></a>Suspender replicação geográfica durante a atualização


A atualização para o V12 não é possível executar se replicação geográfica estiver ativa no seu banco de dados. Primeiro você deve reconfigurar o banco de dados para parar de usar replicação geográfica.


Após a conclusão da atualização, você pode configurar seu banco de dados para usar novamente a replicação de localização geográfica.


### <a name="open-ports-on-an-azure-vm-for-client-connectivity"></a>Portas abertas em uma máquina virtual do Azure conectividade de cliente


Se o programa cliente se conecta ao V12 de banco de dados do SQL enquanto seu cliente é executado em uma máquina virtual Azure (máquina virtual), você deve abrir os seguintes intervalos de porta na VM:

- 11999 11000
- 14000-14999


Clique [aqui](sql-database-develop-direct-route-ports-adonet-v12.md) para obter detalhes sobre as portas para V12 de banco de dados do SQL. As portas são necessárias por melhorias de desempenho V12 de banco de dados do SQL.


##<a id="limitations"></a>Limitações durante e depois da atualização V12


### <a name="portals-for-v12"></a>Portais para V12


Há três portais do Azure e cada tem capacidades diferentes sobre V12 de banco de dados do SQL.


- [http://portal.Azure.com/](https://portal.azure.com/)<br/>Este Portal Azure é novo e ainda está no status de visualização. Este portal ainda não está na disponibilidade geral completo (GA). Este portal:
 - Pode gerenciar seu servidor de V12 e o banco de dados.
 - Pode atualizar seu banco de dados de V11 para V12.


- [http://Manage.WindowsAzure.com/](http://manage.windowsazure.com/)<br/>Este Portal clássico do Azure eventualmente podem ser utilizado. Este portal:
 - Pode gerenciar seu servidor de V12 e o banco de dados.
 - Pode *não* atualizar sua V11 banco de dados a V12.


- (http://*nomedoservidor*. database.windows.net)<br/>Portal de clássico do banco de dados do SQL Azure:
 - Pode*não* gerenciar V12 servidores.


Recomendamos que você se conecte a seus bancos de dados do SQL Azure com o Visual Studio 2015 (VS2015). VS2015 pode ser usada para tarefas como o seguinte:


- Para executar uma instrução Transact-SQL.
- Para criar um esquema.
- Desenvolver um banco de dados online ou offline.


Ou, em vez disso, gratuitamente baixe [2015 de comunidade do Visual Studio](https://www.visualstudio.com/vs/community/), que é uma versão completa do VS2015.


No mais antigos Portal do Azure clássico, na página do banco de dados, você pode clicar em **aberto no Visual Studio** para iniciar VS2015 em seu computador para conexão ao seu banco de dados do SQL Azure.


Para outra alternativa, você pode usar o SQL Server Management Studio (SSMS) 2014 com [CU6](http://support.microsoft.com/kb/3031047/) para se conectar ao banco de dados do SQL Azure. Mais detalhes estão essa postagem de blog:<br/>[Ferramentas de cliente atualizações do Azure SQL Database](https://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


> [AZURE.IMPORTANT] É recomendável que você sempre usar a versão mais recente do Management Studio para permanecer sincronizados com atualizações do Microsoft Azure e banco de dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


### <a name="limitation-during-upgrade-to-v12"></a>Limitação *durante a* atualização para V12


O banco de dados V11 permanecerão disponível para acesso a dados durante a atualização para V12. Ainda existem algumas limitações a serem consideradas.


| Limitação | Descrição |
| :--- | :--- |
| Duração de uma atualização | A duração de uma atualização depende o tamanho, a edição e o número de bancos de dados no servidor. O processo de atualização pode executar para horas e dias para servidores especialmente para servidores com bancos de dados:<br/><br/>*Maior do que 50 GB, ou<br/> * Em um nível de serviço não-premium<br/><br/>Criação de novos bancos de dados no servidor durante a atualização também pode aumentar a duração da atualização. |
| Sem replicação geográfica | Não há suporte para replicação geográfica em um servidor de V12 que no momento está envolvido em uma atualização da V11. |
| Banco de dados está temporariamente indisponível na fase final de uma atualização para V12 | Os bancos de dados pertencentes ao seu servidor de V11 permanecem disponíveis durante o processo de atualização. No entanto, a conexão com o servidor e bancos de dados está temporariamente indisponível na fase final, quando a opção sobre começando com V11 para o V12 pronto.<br/><br/>A opção período pode variar de 40 segundos para 5 minutos. Para a maioria dos servidores, alternar sobre espera concluir em 90 segundos. Aumenta a mudança ao longo do tempo para servidores que têm um grande número de bancos de dados ou quando os bancos de dados têm cargas de trabalho pesado gravação. |


### <a name="limitation-after-upgrade-to-v12"></a>Limitação *depois de* atualizar para o V12


| Limitação | Descrição |
| :--- | :--- |
| Não é possível reverter para V11 | Após uma atualização in-loco, o resultado não pode ser revertido ou desfeito. |
| Nível da Web ou de negócios | Após a atualização começa, o servidor do banco de dados novo V12 já não pode reconhecer ou aceite o nível de serviço Web ou Business. |



### <a name="export-and-import-after-upgrade-to-v12"></a>Exportar e importar *após a* atualização para V12


Você pode exportar ou importar um banco de dados V12 usando o [Portal do Azure](https://portal.azure.com/). Ou você pode exportar ou importar usando qualquer uma das seguintes ferramentas:


- SQL Server Management Studio (SSMS)
- Visual Studio de 2015
- Estrutura de aplicativo de camada de dados (DacFx)


No entanto, para usar as ferramentas, você deve primeiro ou instale suas atualizações mais recentes para garantir que eles suportam os novos recursos de V12:


- [Atualização cumulativa 6 para SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Atualização de fevereiro de 2015 para o banco de dados do servidor SQL ferramentas no Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Estrutura do aplicativo fevereiro de 2015 camada de dados (DacFx) para V12 de banco de dados do SQL Azure](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] Os links de ferramenta anteriores foram atualizados em ou após 2 de março de 2015. Recomendamos que você use essas atualizações mais recentes dessas ferramentas.


#### <a name="automated-export"></a>Exportar automatizado


Exportar automatizada continua disponível como visualização.  Nenhuma atualização da ferramenta de cliente é necessárias ao usar automatizada exportar.  Se você optar por fazer o arquivo resultante e importar para um servidor local, as atualizações de ferramentas listadas acima são necessários para importar com êxito.


### <a name="restore-to-v12-of-a-deleted-v11-database"></a>Restaurar para V12 um banco de dados excluídos do V11

O cenário a seguir explica o que um banco de dados do SQL Azure de V11 excluído pode ser restaurado em um servidor de V12 Azure SQL Database.

1. Suponha que você tenha um servidor V11 Azure SQL Database. <br/> No servidor, você tem um banco de dados na camada de serviço Web-e-Business obsoleta.
2. Excluir o banco de dados.
3. Atualizar o servidor para V12.
4. Em seguida você restaurar o banco de dados no servidor. <br/> O banco de dados, portanto, é atualizado para V12, nível S0 da camada de serviço padrão.
5. Você pode alternar o banco de dados para qualquer nível de serviço com suporte, se S0 não for sua preferência.


### <a name="powershell-cmdlets"></a>Cmdlets do PowerShell


Cmdlets do PowerShell estão disponíveis para iniciar, parar ou monitorar uma atualização para V12 de banco de dados do SQL Azure de V11 ou qualquer outra versão do pré-V12.

- [Atualização para V12 de banco de dados do SQL usando o PowerShell](sql-database-upgrade-server-powershell.md)

Para documentação de referência sobre esses cmdlets do PowerShell, consulte:


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Início-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Parar AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


O cmdlet parada significa Cancelar, pausar não. Não há nenhuma maneira de retomar uma atualização, além de iniciar novamente desde o começo. O cmdlet parada limpará e libera todos os recursos apropriados.


## <a name="failure-resolution"></a>Resolução de falha


Se a atualização falhar por qualquer motivo ímpar, seu banco de dados V11 permanece ativo e disponível como normal.


## <a name="related-links"></a>Links relacionados


- [Recursos de visualização](https://azure.microsoft.com/services/preview/) do Microsoft Azure


<!--Anchors-->
[Subheading 1]: #subheading-1
