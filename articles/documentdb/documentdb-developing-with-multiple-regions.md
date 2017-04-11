<properties
   pageTitle="Desenvolvendo com várias regiões em DocumentDB | Microsoft Azure"
   description="Saiba como acessar seus dados em várias regiões do Azure DocumentDB, um serviço de banco de dados NoSQL totalmente gerenciado."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="kipandya"/>
   
# <a name="developing-with-multi-region-documentdb-accounts"></a>Desenvolvendo com contas de DocumentDB várias regiões

> [AZURE.NOTE] Distribuição global de bancos de dados de DocumentDB é geralmente disponível e automaticamente habilitado para todas as contas DocumentDB recém-criado. Estamos trabalhando para habilitar a distribuição global em todas as contas existentes, mas enquanto isso, se quiser que a distribuição global habilitada em sua conta, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e podemos será habilite-lo para você agora.

Para aproveitar a [distribuição global](documentdb-distribute-data-globally.md), aplicativos cliente podem especificar a lista de preferência ordenada das regiões a ser usado para executar operações de documento. Isso pode ser feito definindo a política de conexão. Com base na configuração de conta do Azure DocumentDB, disponibilidade regional atual e a lista de preferência especificada, o ponto de extremidade mais ideal será escolhido pelo SDK para executar a gravação e operações de leitura. 

Esta lista de preferência é especificada ao inicializar uma conexão usando o cliente de DocumentDB SDKs. SDK do aceite um parâmetro opcional "PreferredLocations" Isso é uma lista ordenada de regiões Azure.

O SDK enviará automaticamente a todas as gravações atuais escrever região. 

Todas as leituras serão enviadas para a primeira região disponível na lista PreferredLocations. Se a solicitação falhar, o cliente falhar para baixo na lista para a próxima região e assim por diante. 

O cliente que SDKs só tentará ler as regiões especificado em PreferredLocations. Portanto, por exemplo, se a conta de banco de dados está disponível em três regiões, mas somente o cliente especifica dois das regiões não seja de gravação para PreferredLocations, em seguida, nenhuma leitura será disponibilizada fora da região de gravação, mesmo no caso de failover.

O aplicativo pode verificar o ponto de extremidade de gravação atual e leia escolhido pelo SDK marcando duas propriedades, WriteEndpoint e ReadEndpoint, disponível na versão SDK 1.8 e acima do ponto de extremidade. 

Se a propriedade PreferredLocations não estiver definida, todas as solicitações serão atendidas da região da gravação atual. 


## <a name="net-sdk"></a>.NET SDK
O SDK pode ser usado sem fazer alterações de código. Nesse caso, o SDK automaticamente direciona ambas as leituras e gravações com a região atual de gravação. 

Na versão 1,8 e posterior do SDK do .NET, o parâmetro ConnectionPolicy para o construtor de DocumentClient tem uma propriedade chamada Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Essa propriedade será do tipo coleção `<string>` e deve conter uma lista de nomes de região. Os valores de cadeia de caracteres são formatados por coluna Nome região as [Regiões do Azure]  [ regions] página, sem espaços antes ou depois do primeiro e último caractere respectivamente.

A gravação atual e pontos de extremidade de leitura estão disponíveis em DocumentClient.WriteEndpoint e DocumentClient.ReadEndpoint respectivamente.

> [AZURE.NOTE] As URLs para os pontos de extremidade não devem ser consideradas como constantes de vida longa. O serviço pode atualizar esses a qualquer momento. O SDK manipula automaticamente essa alteração.

    // Getting endpoints from application settings or other configuration location
    Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
    string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

    //Setting read region selection preference 
    connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
    connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
    connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

    // initialize connection
    DocumentClient docClient = new DocumentClient(
        accountEndPoint,
        accountKey,
        connectionPolicy);

    // connect to DocDB 
    await docClient.OpenAsync().ConfigureAwait(false);


## <a name="nodejs-javascript-and-python-sdks"></a>NodeJS, JavaScript e Python SDKs
O SDK pode ser usado sem fazer alterações de código. Nesse caso, o SDK automaticamente direcionará leituras e gravações atuais escrever região. 

Na versão mais recente do cada SDK e 1,8, o parâmetro ConnectionPolicy para o construtor de DocumentClient uma nova propriedade chamada DocumentClient.ConnectionPolicy.PreferredLocations. Este parâmetro é uma matriz de cadeias de caracteres que usa uma lista de nomes de região. Os nomes são formatados por coluna Nome de região nas [Regiões do Azure]  [ regions] página. Você também pode usar as constantes predefinidas no objeto conveniência AzureDocuments.Regions

A gravação atual e pontos de extremidade de leitura estão disponíveis em DocumentClient.getWriteEndpoint e DocumentClient.getReadEndpoint respectivamente.

> [AZURE.NOTE] As URLs para os pontos de extremidade não devem ser consideradas como constantes de vida longa. O serviço pode atualizar esses a qualquer momento. O SDK tratará essa alteração automaticamente.

Abaixo está um exemplo de código para NodeJS/Javascript. Python e Java seguirá o mesmo padrão.

    // Creating a ConnectionPolicy object
    var connectionPolicy = new DocumentBase.ConnectionPolicy();
    
    // Setting read region selection preference, in the following order -
    // 1 - West US
    // 2 - East US
    // 3 - North Europe
    connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];
    
    // initialize the connection
    var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);


## <a name="rest"></a>RESTANTE 
Depois que uma conta de banco de dados foi disponibilizada em vários regiões, os clientes podem consultar sua disponibilidade executando uma solicitação GET na seguinte URI.

    https://{databaseaccount}.documents.azure.com/

O serviço retornará uma lista de regiões e seu correspondente DocumentDB ponto de extremidade URIs para as réplicas. Região de gravação atual será indicado na resposta. O cliente poderá selecionar o ponto de extremidade apropriado para todas as solicitações de API REST da seguinte maneira.

Resposta de exemplo

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


-   COLOCAR, POSTAGEM e excluir todas as solicitações devem ir para a gravação indicada URI
-   Obtém todos e outras solicitações de somente leitura (por exemplo, consultas) podem ir para qualquer ponto de extremidade da escolha do cliente

Grave solicitações para regiões de somente leitura falhará com código de erro HTTP 403 ("proibido").

Se a região de gravação for alterado após a fase de descoberta inicial do cliente, gravações subsequentes para a região de gravação anterior falhará com código de erro HTTP 403 ("proibido"). O cliente, em seguida, deve obter a lista das regiões novamente para obter a região de gravação atualizado.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os dados distribuindo globalmente com DocumentDB nos seguintes artigos:

- [Distribuir dados globalmente com DocumentDB](documentdb-distribute-data-globally.md)
- [Níveis de consistência](documentdb-consistency-levels.md)
- [Como funciona a produtividade com várias regiões](documentdb-manage.md#how-throughput-works-with-multiple-regions)
- [Adicionar regiões usando o portal do Azure](documentdb-portal-global-replication.md)

[regions]: https://azure.microsoft.com/regions/ 
