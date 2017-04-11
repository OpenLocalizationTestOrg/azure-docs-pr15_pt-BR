<properties 
    pageTitle="Dimensionamento Elástico do SQL Azure perguntas Frequentes | Microsoft Azure" 
    description="Perguntas frequentes sobre o dimensionamento de Elástico de banco de dados do SQL Azure." 
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
    ms.date="05/03/2016" 
    ms.author="ddove"/>

# <a name="elastic-database-tools-faq"></a>Ferramentas de banco de dados elástica perguntas Frequentes 

#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Se eu tiver um único-locatário por fragmentar e nenhuma chave de fragmentação, como preencher a chave de fragmentação para as informações de esquema?

O objeto de informações de esquema só é usado para dividir cenários de mala direta. Se um aplicativo for naturalmente único-locatário, ele não exige a ferramenta de mesclagem de divisão e, portanto, não é necessário para preencher o objeto de informações de esquema.

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Eu já provisionado um banco de dados e eu já tenho um gerente de mapa fragmentar, como registrar esse novo banco de dados como uma fragmentar?

Consulte **[Adicionando um fragmentar a um aplicativo usando a biblioteca de cliente elástica banco de dados](sql-database-elastic-scale-add-a-shard.md)**. 

#### <a name="how-much-do-elastic-database-tools-cost"></a>Quanto ferramentas de banco de dados elástica custa?

Usando a biblioteca de cliente do banco de dados elástica não aumentam os custos. Os custos são acumulados somente para os bancos de dados do SQL Azure que você usa para fragmentos e o Gerenciador de mapa de fragmentar, bem como as funções da web/trabalhador que provisionar para a ferramenta de mesclagem de divisão.

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Por que minhas credenciais não estão funcionando quando adicionar um fragmentar de um servidor diferente?
Não use credenciais na forma de "usuário ID=username@servername”, em vez disso, basta usar" ID de usuário = username ".  Além disso, certifique-se de que o logon "username" tem permissões na fragmentar.

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Eu preciso para criar um gerente de mapa fragmentar e preencher fragmentos sempre inicia o meus aplicativos?

Não — a criação de Gerenciador de mapa de fragmentar (por exemplo, **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) é uma operação única.  Seu aplicativo deve usar a chamada **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** em tempo de inicialização do aplicativo.  Há deve apenas uma tal chamada por domínio do aplicativo.

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Tenho dúvidas sobre como usar ferramentas de banco de dados elástica, como obter resposta? 

Por favor, comunique-se no [Fórum do Azure SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Quando eu recebo uma conexão de banco de dados usando uma chave de fragmentação, eu ainda pode consultar dados para outras chaves de fragmentação no mesmo fragmentar.  Isso é por design?

As APIs de escala elástica oferecem uma conexão ao banco de dados correto para a sua chave de fragmentação, mas não fornecem fragmentação chave filtragem.  Adicione cláusulas **WHERE** à sua consulta para restringir o escopo para a chave de fragmentação fornecido, se necessário.

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>Posso usar uma edição de banco de dados do Azure diferente para cada fragmentar no meu conjunto fragmentar?

Sim, um fragmentar é um banco de dados individual e, portanto, um fragmentar poderia ser uma edição Premium e outro ser um Standard edition. Além disso, a edição de um fragmentar pode dimensionar para cima ou para baixo várias vezes durante a vida útil da fragmentar.

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Faz a disposição de ferramenta de mesclagem de divisão (ou excluir) um banco de dados durante uma operação de divisão ou mesclagem? 

Não. Para **Dividir** as operações, o banco de dados de destino deve existir com o esquema apropriado e ser registrado com o Gerenciador de mapa fragmentar.  Para operações de **mala direta** , você deve excluir a fragmentar do Gerenciador de mapa de fragmentar e exclua o banco de dados.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 