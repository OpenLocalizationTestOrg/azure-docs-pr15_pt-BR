< propriedades
    
    pageTitle="Upgrade to the latest elastic database client library | Microsoft Azure" 
    description="Upgrade apps and libraries using Nuget" 
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

# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Atualizar um aplicativo para usar a biblioteca de cliente mais recente do banco de dados elástica

Novas versões da [biblioteca de cliente do banco de dados elástica](sql-database-elastic-database-client-library.md) estão disponíveis por meio de NuGetand a interface do Gerenciador de NuGetPackage no Visual Studio. Atualizações contêm correções de bugs e suportam para novos recursos da biblioteca cliente.

**Para a versão mais recente:** Vá para [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Recriar seu aplicativo com a nova biblioteca, bem como alterar sua metadados Gerenciador do mapa fragmentar existentes armazenados em seus bancos de dados do SQL Azure para suportar novos recursos.

Executar essas etapas na ordem garante que versões antigas da biblioteca cliente não está mais presentes no seu ambiente quando forem atualizados objetos de metadados, que significa que os objetos de metadados de versão antigo não serão criados após a atualização.   

## <a name="upgrade-steps"></a>Etapas de atualização

**1. Atualize seus aplicativos.** No Visual Studio, baixe e fazer referência a versão mais recente de biblioteca de cliente em todos os seus projetos de desenvolvimento que usam a biblioteca; em seguida, recriar e implantar. 

 * Em sua solução do Visual Studio, selecione **Ferramentas** --> **NuGet Package Manager** -->  **Gerenciar pacotes NuGet para solução**. 
 * (Visual Studio 2013) No painel esquerdo, selecione **as atualizações**e, em seguida, selecione o botão de **atualização** no pacote da **Biblioteca de cliente do elástica escala do Azure SQL banco de dados** que aparece na janela.
 * (Visual Studio 2015) Defina a caixa de filtro de **atualização disponível**. Selecione o pacote para atualizar e clique no botão **Atualizar** .
    
 
 * Construa e implante. 

**2. Atualize seus scripts.** Se você estiver usando scripts **do PowerShell** para gerenciar fragmentos, [Baixar a nova versão de biblioteca](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) e copiá-la para o diretório do qual você executar scripts. 

**3. Atualize o seu serviço de divisão direta.** Se você usar a ferramenta de mesclagem de divisão de elástica banco de dados para reorganizar dados sharded, [baixar e instalar a versão mais recente da ferramenta](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Detalhadas etapas de atualização para o serviço pode ser encontrado [aqui](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. Atualize seus bancos de dados do Gerenciador de mapa fragmentar**. Atualize os metadados suporte os mapas de fragmentar em Azure SQL Database.  Há duas maneiras que você pode realizar isso, usando o PowerShell ou c#. Ambas as opções são mostradas abaixo.

***Opção 1: Atualizar metadados usando o PowerShell***

1. Baixe o utilitário de linha de comando mais recente para NuGet do [aqui](http://nuget.org/nuget.exe) e salve em uma pasta. 

2. Abra um Prompt de comando, navegue para a mesma pasta e execute o comando:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. Navegue até a subpasta que contém a nova versão DLL de cliente que você acabou baixou, por exemplo:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`

4. Baixe o scriptlet de atualização de cliente elástica banco de dados no [Centro de Script](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)e salvá-lo na mesma pasta que contém a DLL.

5. A partir dessa pasta, execute ".\upgrade.ps1 PowerShell" no prompt de comando e siga os avisos.
 
***Opção 2: Atualizar metadados usando c#***

Como alternativa, crie um aplicativo do Visual Studio que abre sua ShardMapManager, itera todos os fragmentos e executa a atualização de metadados chamando os métodos [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) e [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) como neste exemplo: 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 
    
    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Essas técnicas para atualizações de metadados podem ser aplicadas várias vezes sem prejudicar. Por exemplo, se uma versão mais antiga do cliente inadvertidamente cria um fragmentar depois que você já tiver atualizado, você pode executar a atualização novamente em todos os fragmentos para garantir que a versão mais recente de metadados está presente em toda sua infraestrutura. 

**Observação:**  Novas versões da biblioteca cliente publicado acumulado continuar a trabalhar com versões anteriores dos metadados do Gerenciador de mapa fragmentar em DB do SQL Azure e vice-versa.   No entanto, para tirar proveito de alguns dos novos recursos no cliente mais recente, metadados precisam ser atualizado.   Observe que as atualizações de metadados não afetará quaisquer dados do usuário ou dados específicos do aplicativo, somente os objetos criada e usada pelo Gerenciador do mapa fragmentar.  E aplicativos continuam operando através a sequência de atualização descrita acima. 

## <a name="elastic-database-client-version-history"></a>Histórico de versão de cliente do banco de dados elástica 

Para o histórico de versão, vá para [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]  


<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
 