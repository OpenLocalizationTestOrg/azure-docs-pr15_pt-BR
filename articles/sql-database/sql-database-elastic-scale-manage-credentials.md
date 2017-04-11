<properties 
    pageTitle="Gerenciamento de credenciais na biblioteca de cliente do banco de dados elástica | Microsoft Azure" 
    description="Como definir o nível certo de credenciais de administrador para somente leitura para os aplicativos de banco de dados elástica" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Credenciais usadas para acessar a biblioteca de cliente elástica banco de dados

A [biblioteca de cliente do banco de dados elástica](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) usa três tipos diferentes de credenciais para acessar o [Gerenciador de mapa fragmentar](sql-database-elastic-scale-shard-map-management.md). Dependendo da necessidade, use a credencial com o menor nível de acesso possível.

* **Credenciais de gerenciamento**: para criar ou manipular um gerente de mapa fragmentar. (Consulte o [Glossário](sql-database-elastic-scale-glossary.md)). 
* **Credenciais de acesso**: para acessar um Gerenciador de mapa fragmentar existente para obter informações sobre fragmentos.
* **Credenciais de Conexão**: para se conectar ao fragmentos. 

Consulte também [bancos de dados de gerenciamento e logon no Azure SQL Database](sql-database-manage-logins.md). 
 
## <a name="about-management-credentials"></a>Sobre as credenciais de gerenciamento

Gerenciamento credenciais são usadas para criar um objeto de [**ShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) para aplicativos que manipular fragmentar mapas. (Por exemplo, consulte [Adicionar um fragmentar usando ferramentas de banco de dados elástica](sql-database-elastic-scale-add-a-shard.md) e [Roteamento dependentes de dados](sql-database-elastic-scale-data-dependent-routing.md)) O usuário da biblioteca cliente dimensionamento Elástico cria os usuários SQL e logon SQL e garante que cada recebe as permissões de leitura/gravação sobre o banco de dados de mapa de fragmentar global e todos fragmentar bancos de dados também. Essas credenciais são usadas para manter o mapa de fragmentar global e os mapas de fragmentar local quando alterações para o mapa de fragmentar são executadas. Por exemplo, use as credenciais de gerenciamento para criar o objeto do Gerenciador de mapa fragmentar (usando [**GetSqlShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx): 

    // Obtain a shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmAdminConnectionString, 
            ShardMapManagerLoadPolicy.Lazy 
    ); 

A variável **smmAdminConnectionString** é uma cadeia de conexão que contém as credenciais de gerenciamento. O ID de usuário e senha fornece acesso de leitura/gravação ao banco de dados de mapa de fragmentar e fragmentos individuais. A cadeia de conexão de gerenciamento também inclui o nome do servidor e o nome do banco de dados para identificar o banco de dados de mapa de fragmentar global. Aqui está uma cadeia de conexão típica para essa finalidade:

     "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

Não use valores na forma de "username@server"—instead simplesmente usar o valor "username".  Isso ocorre porque as credenciais devem trabalhar contra o banco de dados do fragmentar mapa manager e fragmentos individuais, que podem estar em servidores diferentes.

## <a name="access-credentials"></a>Credenciais de acesso
  
Ao criar um fragmentar Gerenciador de mapa em um aplicativo que não administrar fragmentar mapas, use credenciais com permissões somente leitura no mapa fragmentar global. As informações recuperadas do mapa fragmentar global nessas credenciais são usadas para o [roteamento de dependentes de dados](sql-database-elastic-scale-data-dependent-routing.md) e para preencher o cache de mapa fragmentar no cliente. As credenciais são fornecidas através do mesmo padrão de chamada para **GetSqlShardMapManager** , conforme mostrado acima: 

    // Obtain shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmReadOnlyConnectionString, 
            ShardMapManagerLoadPolicy.Lazy
    );  

Observe o uso do **smmReadOnlyConnectionString** para refletir o uso de credenciais diferentes para esse acesso em nome de usuários **não administradores** : essas credenciais não devem fornecer permissões de gravação no mapa fragmentar global. 

## <a name="connection-credentials"></a>Credenciais de Conexão 

Credenciais adicionais são necessários ao usar o método [**OpenConnectionForKey**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) para acessar um fragmentar associada com uma chave de fragmentação. Essas credenciais precisará fornecer as permissões de acesso somente leitura a tabelas de mapa fragmentar local que reside na fragmentar. Isso é necessário para executar a validação de conexão para o roteamento de dependentes de dados sobre o fragmentar. Este trecho de código permite o acesso a dados no contexto de roteamento dependentes de dados: 
 
    using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
    targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

Neste exemplo, **smmUserConnectionString** contém a cadeia de conexão para as credenciais de usuário. Para DB do SQL Azure, aqui está uma cadeia de conexão típica credenciais de usuário: 

    "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

Como com as credenciais de administrador, não valores na forma de "username@server". Em vez disso, basta use "username".  Observe também que a cadeia de conexão não contém um nome de servidor e banco de dados. Isso ocorre porque a chamada **OpenConnectionForKey** redirecionará automaticamente a conexão para a fragmentar correta com base na chave. Portanto, o nome do banco de dados e o nome do servidor não são fornecidas. 

## <a name="see-also"></a>Consulte também
[Gerenciar bancos de dados e logon no banco de dados do SQL Azure](sql-database-manage-logins.md)

[Proteger seu banco de dados do SQL](sql-database-security.md)

[Introdução ao trabalhos elástica banco de dados](sql-database-elastic-jobs-getting-started.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 