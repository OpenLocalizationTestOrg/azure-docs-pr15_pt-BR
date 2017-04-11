<properties 
    pageTitle="Trabalhar com dados geoespaciais em Azure DocumentDB | Microsoft Azure" 
    description="Compreenda como criar, índice e espaciais objetos com Azure DocumentDB de consulta." 
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="10/25/2016" 
    ms.author="arramac"/>
    
# <a name="working-with-geospatial-data-in-azure-documentdb"></a>Trabalhar com dados geoespaciais em DocumentDB do Azure

Este artigo é uma introdução à funcionalidade geoespaciais na [DocumentDB do Azure](https://azure.microsoft.com/services/documentdb/). Depois lendo este artigo, você será capaz de responder às seguintes perguntas:

- Como armazena dados espaciais no Azure DocumentDB?
- Como posso consultar dados geoespaciais em DocumentDB Azure no SQL e LINQ?
- Como habilitar ou desabilitar a indexação espacial no DocumentDB?

Consulte este [projeto Github](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) para exemplos de código.

## <a name="introduction-to-spatial-data"></a>Introdução aos dados espaciais

Dados espaciais descreve a posição e a forma dos objetos no espaço. Na maioria dos aplicativos, elas correspondem aos objetos da Terra, ou seja, os dados geoespaciais. Dados espaciais podem ser usados para representar o local de uma pessoa, um local de interesse ou o limite de uma cidade ou uma Lucerne. Casos comuns de uso frequentemente envolvem consultas proximidade, por exemplo, o "encontrar todos os restaurantes próximos meu local atual". 

### <a name="geojson"></a>GeoJSON
DocumentDB oferece suporte a indexação e consulta de dados de ponto de geoespaciais representado usando a [especificação de GeoJSON](http://geojson.org/geojson-spec.html). Estruturas de dados de GeoJSON são sempre objetos JSON válidos, para que eles podem ser armazenados e consultados usando DocumentDB sem qualquer ferramentas especializadas ou bibliotecas. SDK do DocumentDB fornecem auxiliar classes e métodos que tornam mais fácil trabalhar com dados espaciais. 

### <a name="points-linestrings-and-polygons"></a>Pontos, linestrings e polígonos
Um **ponto** indica uma única posição no espaço. Os dados geoespaciais, um ponto representa o local exato, o que poderia ser um endereço de uma loja de supermercado, um quiosque, um automóvel ou uma cidade.  Um ponto é representado em par GeoJSON (e DocumentDB) usando suas coordenadas ou longitude e latitude. Aqui está um exemplo JSON para um ponto.

**Pontos DocumentDB**

    {
       "type":"Point",
       "coordinates":[ 31.9, -4.8 ]
    }

>[AZURE.NOTE] A especificação de GeoJSON Especifica longitude primeiro e latitude segundo. Como em outros aplicativos de mapeamento, longitude e latitude são ângulos em são representados em termos de graus. Valores de longitude são medidas a partir o primeiro meridiano e estão entre -180 e 180.0 graus e latitude valores são medidas a partir o equador e entre -90,0 e 90,0 graus. 
>
> DocumentDB interpretará coordenadas conforme representado por sistema de referência WGS 84. Veja abaixo para obter mais detalhes sobre os sistemas de coordenadas de referência.

Isso pode ser inserido em um documento DocumentDB, conforme mostrado neste exemplo de um perfil de usuário que contém os dados de local:

**Usar o perfil com a localização armazenada em DocumentDB**

    {
       "id":"documentdb-profile",
       "screen_name":"@DocumentDB",
       "city":"Redmond",
       "topics":[ "NoSQL", "Javascript" ],
       "location":{
          "type":"Point",
          "coordinates":[ 31.9, -4.8 ]
       }
    }

Além dos pontos, GeoJSON também suporta LineStrings e polígonos. **LineStrings** representar uma série de dois ou mais pontos no espaço e segmentos de linha que conexão-las. Dados geoespaciais, linestrings são usadas para representar rodovias ou rios. Um **polígono** é um limite de pontos conectados que formam um LineString fechada. Polígonos são usados para representar formações naturais como Lagos ou jurisdições políticas como estados e de cidades. Aqui está um exemplo de um polígono no DocumentDB. 

**Polígonos em DocumentDB**

    {
       "type":"Polygon",
       "coordinates":[
           [ 31.8, -5 ],
           [ 31.8, -4.7 ],
           [ 32, -4.7 ],
           [ 32, -5 ],
           [ 31.8, -5 ]
       ]
    }

>[AZURE.NOTE] A especificação de GeoJSON requer que para polígonos válidos, o último par de coordenadas fornecido deve ser a mesma que o primeiro, para criar uma forma fechada.
>
>Pontos dentro de um polígono devem ser especificados na ordem no sentido anti-horário. Um polígono especificado na ordem no sentido horário representa o inverso da região dentro dela.

Além de ponto, LineString e polígono, GeoJSON também especifica a representação como agrupar vários locais geoespaciais, bem como associar propriedades aleatório a localização geográfica como um **recurso**. Desde que esses objetos são JSON válido, eles podem todos ser armazenados e processados em DocumentDB. No entanto DocumentDB só oferece suporte a indexação automática de pontos.

### <a name="coordinate-reference-systems"></a>Coordenar sistemas de referência

Como a forma da Terra é irregular, coordenadas de dados geoespaciais é representado em muitos sistemas de coordenadas referência (CRS), cada uma com seus próprios quadros de referência e unidades de medida. Por exemplo, o "nacional grade de Britain" é um sistema de referência é muito preciso para o Reino Unido, mas não fora dela. 

O CRS mais populares em uso hoje é o sistema Geodésico Mundial [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). Dispositivos GPS e vários serviços de mapeamento, inclusive mapas do Google e APIs de mapas do Bing usam WGS 84. DocumentDB oferece suporte a indexação e consulta de dados geoespaciais usando somente o CRS WGS 84. 

## <a name="creating-documents-with-spatial-data"></a>Criando documentos com dados espaciais
Quando você criar documentos que contêm valores de GeoJSON, eles são automaticamente indexados com um índice espacial de acordo com a política de indexação da coleção. Se você estiver trabalhando com um SDK DocumentDB em um idioma dinamicamente digitado como Python ou Node, você deve criar GeoJSON válida.

**Criar um documento com os dados geoespaciais em Node**

    var userProfileDocument = {
       "name":"documentdb",
       "location":{
          "type":"Point",
          "coordinates":[ -122.12, 47.66 ]
       }
    };

    client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
        // additional code within the callback
    });

Se você estiver trabalhando com o .NET (ou Java) SDKs, você pode usar as novas classes de ponto e polígono dentro do namespace Microsoft.Azure.Documents.Spatial para incorporar informações de local dentro de seus objetos de aplicativo. Essas classes ajudam a simplificar a serialização e desserialização de dados espaciais em GeoJSON.

**Criar documento com os dados geoespaciais no .NET**

    using Microsoft.Azure.Documents.Spatial;
    
    public class UserProfile
    {
        [JsonProperty("name")]
        public string Name { get; set; }

        [JsonProperty("location")]
        public Point Location { get; set; }
        
        // More properties
    }
    
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
        new UserProfile 
        { 
            Name = "documentdb", 
            Location = new Point (-122.12, 47.66) 
        });

Se você não tiver as informações de latitude e longitude, mas tiver o endereços físicos ou o nome do local como cidade ou país/região, você pode pesquisar as coordenadas reais usando um serviço de geocodificação como serviços de REST de mapas do Bing. Saiba mais sobre geocodificação Bing Maps [aqui](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Consultando tipos espaciais

Agora que estamos tirou uma olhada em como inserir dados geoespaciais, vamos dar uma olhada em como consultar esses dados usando DocumentDB usando SQL e LINQ.

### <a name="spatial-sql-built-in-functions"></a>Espaciais funções internas do SQL
DocumentDB é compatível com as seguintes funções internas de abrir geoespaciais Consortium OGC () para consultar geoespaciais. Para obter mais detalhes sobre o conjunto completo de funções internas na linguagem SQL, consulte [DocumentDB de consulta](documentdb-sql-query.md).

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descrição</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Retorna a distância entre as duas expressões de ponto GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Retorna uma expressão booliana que indica se o ponto de GeoJSON especificado no primeiro argumento é dentro do polígono GeoJSON no segundo argumento.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Retorna um valor booliano que indica se a expressão especificada de ponto ou polígono GeoJSON é válida.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Retorna um valor JSON contendo um booliano valor se a expressão especificada de ponto ou polígono GeoJSON é válida e inválido, além do motivo como um valor de cadeia de caracteres.</td>
</tr>
</table>

Funções espaciais podem ser usadas para executar consultas de proximidade em dados espaciais. Por exemplo, aqui está uma consulta que retorna todos os documentos de família que estão dentro de 30 km do local especificado usando a função interna ST_DISTANCE. 

**Consulta**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Se você incluir indexação espacial em sua política de indexação, em seguida, "consultas distância" serão atendidas com eficiência por meio do índice. Para obter mais detalhes sobre indexação espacial, consulte a seção abaixo. Se você não tiver um índice espacial para caminhos especificado, você ainda pode executar consultas espaciais especificando `x-ms-documentdb-query-enable-scan` cabeçalho de solicitação com o valor definido como "true". No .NET, isso pode ser feito passando o argumento **FeedOptions** opcional para consultas com [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) definido como true. 

ST_WITHIN pode ser usado para verificar se um ponto está dentro de um polígono. Polígonos são usados para representar limites como códigos postais, limites de estado ou formações naturais. Novamente se você incluir indexação espacial em sua política de indexação, em seguida, "comem" consultas serão atendidas com eficiência por meio do índice. 

Argumentos de polígono em ST_WITHIN podem conter apenas um único toque, ou seja, os polígonos não devem conter furos neles. 

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Resultados**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] Semelhante a como incompatíveis works tipos na consulta de DocumentDB, se o valor de local especificado em um argumento é inválido ou formado, em seguida, ele irá avaliar **indefinido** e avaliado documento para ser ignorado dos resultados da consulta. Se sua consulta não retornar resultados, execute o ST_ISVALIDDETAILED para a depuração, por que o tipo de spatail é inválido.     

DocumentDB também dá suporte à execução de consultas inversas, ou seja, você pode indexar polígonos ou linhas em DocumentDB, e consultar as áreas que contêm um ponto especificado. Esse padrão geralmente é usado em logística para identificar, por exemplo, quando um caminhão entra ou sai de uma determinada área. 

**Consulta**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Resultados**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID e ST_ISVALIDDETAILED podem ser usados para verificar se um objeto espacial é válido. Por exemplo, a seguinte consulta verifica a validade de um ponto com uma valor de latitude de intervalo (-132.8) de ausência. ST_ISVALID retorna apenas um valor booliano e ST_ISVALIDDETAILED retorna o booleanos e uma cadeia de caracteres que contém o motivo por que ele é considerado inválido.

**Consulta**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Resultados**

    [{
      "$1": false
    }]

Essas funções também podem ser usadas para validar polígonos. Por exemplo, aqui usamos ST_ISVALIDDETAILED para validar um polígono que não é fechado. 

**Consulta**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Resultados**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
### <a name="linq-querying-in-the-net-sdk"></a>LINQ consultando no SDK do .NET

O SDK do .NET DocumentDB também provedores stub métodos `Distance()` e `Within()` para uso em expressões LINQ. O provedor DocumentDB LINQ converte essas chamadas de método das chamadas de função interna SQL equivalentes (ST_DISTANCE e ST_WITHIN respectivamente). 

Aqui está um exemplo de uma consulta LINQ que localiza todos os documentos no conjunto de DocumentDB cujo valor "local" é dentro de um raio de 30km especificado aponte usando LINQ.

**Consulta LINQ para distância**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Da mesma forma, aqui está uma consulta para encontrar todos os documentos cujo "local" está dentro do caixa/polígono especificado. 

**LINQ consultar dentro**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Agora que estamos tirou uma olhada em como consultar documentos usando LINQ e SQL, vamos dar uma olhada em como configurar DocumentDB para indexação espacial.

## <a name="indexing"></a>Indexação

Conforme descrito em papel [Esquema independente indexação com DocumentDB do Azure](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) , podemos projetado mecanismo de banco de dados do DocumentDB seja verdadeiramente independente de esquema e forneça suporte de primeira classe para JSON. O mecanismo de banco de dados de gravação otimizado do DocumentDB nativamente entende espaciais dados (pontos, polígonos e linhas) representados no padrão GeoJSON.

Em resumo, a geometria é projetada de coordenadas geodésico em um plano 2D depois dividida células usando uma **quadtree**de cada vez. Essas células são mapeadas para 1D baseado no local da célula dentro de uma **curva de preenchimento de espaço de Hilbert**, que preserva a localidade de pontos. Além disso, quando dados de localização são indexados, ela passa por um processo conhecido como **mosaico**, ou seja, todas as células que formam uma interseção um local são identificadas e armazenadas como chaves no índice DocumentDB. Em tempo de consulta, argumentos como pontos e polígonos são também mosaico para extrair os intervalos de ID de célula relevantes e usados para recuperar dados do índice.

Se você especificar uma política de indexação que inclui índice espacial para / * (todos os caminhos), e em seguida, todos os pontos encontrados dentro do conjunto são indexados para consultas espaciais eficientes (ST_WITHIN e ST_DISTANCE). Índices espaciais não têm um valor de precisão e sempre usar um valor de precisão padrão.

>[AZURE.NOTE] DocumentDB oferece suporte a indexação automática de pontos, polígonos e LineStrings

O trecho JSON a seguir mostra uma política de indexação com indexação espacial habilitada, ou seja, qualquer ponto de GeoJSON encontrado nos documentos para consultar espacial de índice. Se você estiver modificando a política de indexação no portal do Azure, você pode especificar o seguinte JSON para política de indexação para Habilitar indexação em seu conjunto espacial.

**Coleção indexação política JSON com Spatial habilitado para pontos e polígonos**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Aqui está um trecho de código em .NET que mostra como criar um conjunto com indexação espacial ativado para todos os caminhos que contêm pontos. 

**Criar um conjunto com indexação espacial**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

E aqui está como você pode modificar um conjunto existente para aproveitar a indexação espacial quaisquer pontos que estão armazenados em documentos.

**Modificar um conjunto existente com indexação espacial**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [AZURE.NOTE] Se o valor de GeoJSON dentro do documento local for inválido ou formado, em seguida, ele será não obter indexado para consultar espacial. Você pode validar valores de local usando ST_ISVALID e ST_ISVALIDDETAILED.
>
> Se sua definição de conjunto inclui uma chave de partição, não é relatado andamento da transformação de indexação. 

## <a name="next-steps"></a>Próximas etapas
Agora que você já learnt sobre como começar com o suporte de geoespaciais no DocumentDB, você pode:

- Iniciar a codificação com as [amostras de código do .NET geoespaciais no Github](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
- Obter mãos em com geoespaciais consultando no [Espaço de consulta de DocumentDB](http://www.documentdb.com/sql/demo#geospatial)
- Saiba mais sobre [DocumentDB consulta](documentdb-sql-query.md)
- Saiba mais sobre [Políticas de indexação de DocumentDB](documentdb-indexing-policies.md)
