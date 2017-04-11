<properties 
    pageTitle="Partição e a escala do Azure DocumentDB | Microsoft Azure"      
    description="Saiba mais sobre como partição funciona no Azure DocumentDB, como configurar a partição e partição chaves e como escolher a chave da partição certo para o seu aplicativo."         
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/> 

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="arramac"/> 

# <a name="partitioning-and-scaling-in-azure-documentdb"></a>Partição e dimensionamento em DocumentDB do Azure
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) destina-se para ajudá-lo a atingir o desempenho de forma rápido e previsível e escala perfeitamente junto com seu aplicativo que ela aumenta. Este artigo fornece uma visão geral de como partição funciona no DocumentDB e descreve como você pode configurar conjuntos de DocumentDB para dimensionar efetivamente seus aplicativos.

Após ler este artigo, você poderá responder às seguintes perguntas:   

- Como funciona partição no Azure DocumentDB?
- Como configurar partição no DocumentDB
- O que são chaves de partição e como escolher a chave da partição certo para meu aplicativo?

Para começar com o código, faça o download do projeto de [Exemplo do Driver de teste de desempenho de DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

## <a name="partitioning-in-documentdb"></a>Partição em DocumentDB

Em DocumentDB, você pode armazenar e consultar sem esquema JSON documentos com tempos de resposta de ordem de milissegundos em qualquer escala. DocumentDB fornece contêineres para armazenar chamados de **conjuntos**de dados. Coleções são recursos lógicos e podem abranger uma ou mais partições físicas ou servidores. O número de partições é determinado pelo DocumentDB de acordo com o tamanho do armazenamento e a produtividade provisionada da coleção. Todas as partições no DocumentDB tem um valor fixo de armazenamento reserva SSD associado a ele e é replicada para alta disponibilidade. Gerenciamento de partição totalmente é gerenciado pela DocumentDB do Azure e você não precisa escrever código complexo ou gerenciar suas partições. Conjuntos de DocumentDB são **praticamente ilimitado** em termos de produtividade e de armazenamento. 

Partição é totalmente transparente para seu aplicativo. DocumentDB suporta leituras rápidas e gravações, lógica transações consultas SQL e LINQ, JavaScript, com base, níveis de consistência e controle de acesso refinadas via API REST chamadas para um recurso de conjunto único. O serviço trata distribuindo dados entre partições e roteamento de solicitações de consulta para a direita partição. 

Como isso funciona? Quando você cria uma coleção no DocumentDB, você notará que há um valor de configuração de **propriedade de chave de partição** que você pode especificar. Esta é a propriedade JSON (ou caminho) dentro de seus documentos que podem ser usados pela DocumentDB para distribuir seus dados entre vários servidores ou partições. DocumentDB será o valor de chave de partição de hash e use o resultado hash para determinar a partição na qual o documento JSON será armazenado. Todos os documentos com a mesma chave de partição serão armazenados na mesma partição. 

Por exemplo, considere um aplicativo que armazena dados sobre funcionários e seus departamentos em DocumentDB. Vamos escolher `"department"` como a propriedade de chave de partição, para dimensionar dados por departamento. Cada documento no DocumentDB deve conter um obrigatório `"id"` propriedade que deve ser exclusiva para cada documento com o valor de chave de partição mesmo, por exemplo, `"Marketing`". Todos os documentos armazenados em uma coleção devem ter uma combinação exclusiva de chave de partição e id, por exemplo, `{ "Department": "Marketing", "id": "0001" }`, `{ "Department": "Marketing", "id": "0002" }`, e `{ "Department": "Sales", "id": "0001" }`. Em outras palavras, a propriedade composta de (id da chave, partição) é a chave primária para o conjunto.

### <a name="partition-keys"></a>Chaves de partição
A escolha da chave de partição é uma decisão importante que você precisará fazer no tempo de design. Você deve escolher um nome de propriedade JSON que tem uma ampla variedade de valores e é provável que tenha distribuído uniformemente os padrões de acesso. A chave de partição especificada como um caminho JSON, por exemplo, `/department` representa o departamento de propriedade. 

A tabela a seguir mostra exemplos de definições de chave de partição e os valores JSON correspondente a cada um.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Caminho de chave de partição</strong></p></td>
            <td valign="top"><p><strong>Descrição</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/Department</p></td>
            <td valign="top"><p>Corresponde ao valor JSON de doc.department onde o documento é o documento.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>nome/propriedades /</p></td>
            <td valign="top"><p>Corresponde ao valor JSON de doc.properties.name onde o documento é o documento (propriedade aninhado).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ID</p></td>
            <td valign="top"><p>Corresponde ao valor JSON de doc.id (chave de id e partição são a mesma propriedade).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ "nome do departamento"</p></td>
            <td valign="top"><p>Corresponde ao valor JSON de documento ["nome do departamento"] onde o documento é o documento.</p></td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] A sintaxe de caminho de chave de partição é semelhante a especificação de caminho para indexação caminhos de política com a diferença de chave que o caminho corresponde à propriedade em vez do valor, ou seja, não há nenhuma curinga no final. Por exemplo, você deve especificar/departamento /? para indexar os valores em departamento, mas Especifica /department como a definição de chave de partição. O caminho da chave partição implicitamente é indexado e não pode ser excluído da indexação usando indexação substituições de política de.

Vamos dar uma olhada em como a opção de chave de partição afeta o desempenho do seu aplicativo.

### <a name="partitioning-and-provisioned-throughput"></a>Produtividade partição e provisionada
DocumentDB destina-se a desempenho previsível. Quando você cria uma coleção, você reservar produtividade em termos de ** [unidades de solicitação](documentdb-request-units.md) (RU) por segundo**. Cada solicitação é atribuída um encargo unitário solicitação proporcional para a quantidade de recursos do sistema como CPU e IO consumida pela operação. Uma leitura de um documento de 1 kB com consistência de sessão consome 1 unidade de solicitação. Uma leitura é 1 RU independentemente o número de itens armazenados ou o número de solicitações simultâneas em execução ao mesmo tempo. Documentos maiores exigem maiores unidades de solicitação, dependendo do tamanho. Se você souber o tamanho das suas entidades e o número de leituras que você precisa de suporte para seu aplicativo, você pode provisionar o valor exato da taxa de transferência necessária para seu aplicativo do Leia necessidades. 

Quando DocumentDB armazena documentos, ele distribui-los uniformemente entre partições com base no valor de chave de partição. A taxa de transferência também é distribuída uniformemente entre os disponíveis ou seja partições a produtividade por partição = (taxa de transferência total por conjunto) / (número de partições). 

>[AZURE.NOTE] Para obter a taxa de transferência completa da coleção, você deve escolher uma chave de partição que permite que você distribua uniformemente solicitações entre um número de valores-chave distinct partição.

## <a name="single-partition-and-partitioned-collections"></a>Única partição e conjuntos de particionado
DocumentDB suporta a criação de conjuntos de partição única e particionadas. 

- **Coleções de Partitioned** pode abranger várias partições e grandes quantidades de armazenamento e produtividade de suporte. Você deve especificar uma chave de partição para o conjunto.
- **Coleções de partição única** têm opções de preço inferiores e a capacidade de consulta e realizar transações em todos os dados do conjunto. Eles têm os limites de armazenamento e escalabilidade de uma única partição. Você não precisa especificar uma chave de partição para esses conjuntos. 

![Conjuntos de particionado no DocumentDB][2] 

Cenários que não precisa grandes volumes de armazenamento ou produtividade, conjuntos de única partição são uma boa opção. Observe que conjuntos de partição única têm escalabilidade e limites de armazenamento de uma única partição, ou seja até 10 GB de armazenamento e até 10.000 unidades de solicitação por segundo. 

Conjuntos de particionada podem oferecer suporte a grandes quantidades de armazenamento e a produtividade. No entanto, as ofertas padrão são configuradas para armazenar até 250 GB de armazenamento e dimensionar até 250.000 unidades de solicitação por segundo. Se precisar de mais armazenamento ou produtividade por conjunto, contate [O suporte do Azure](documentdb-increase-limits.md) para que essas aumento de sua conta.

A tabela a seguir lista diferenças em trabalhando com uma partição única e conjuntos de particionada:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Conjunto de partição única</strong></p></td>
            <td valign="top"><p><strong>Coleção particionada</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Chave da partição</p></td>
            <td valign="top"><p>Nenhum</p></td>
            <td valign="top"><p>Necessário</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Chave primária de documento</p></td>
            <td valign="top"><p>"id"</p></td>
            <td valign="top"><p>chave composta &lt;chave da partição&gt; e "id"</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Armazenamento mínima</p></td>
            <td valign="top"><p>0 GB</p></td>
            <td valign="top"><p>0 GB</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Armazenamento máximo</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>Ilimitado (250 GB por padrão)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Taxa de transferência mínima</p></td>
            <td valign="top"><p>400 unidades de solicitação por segundo</p></td>
            <td valign="top"><p>10.000 unidades de solicitação por segundo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Taxa de transferência máxima</p></td>
            <td valign="top"><p>10.000 unidades de solicitação por segundo</p></td>
            <td valign="top"><p>Ilimitado (unidades de solicitação 250.000 por segundo por padrão)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Versões de API</p></td>
            <td valign="top"><p>Todos os</p></td>
            <td valign="top"><p>API de 2015-12-16 e versões mais recentes</p></td>
        </tr>
    </tbody>
</table>

## <a name="working-with-the-sdks"></a>Trabalhando com o SDK do

Azure DocumentDB adicionou suporte para a partição automática com a [API REST versão 2015-12-16](https://msdn.microsoft.com/library/azure/dn781481.aspx). Para criar conjuntos de particionado, você deve baixar versões SDK 1.6.0 ou mais recentes em uma das plataformas suportadas SDK (.NET, Node, Java, Python). 

### <a name="creating-partitioned-collections"></a>Criar coleções particionadas

O exemplo a seguir mostra um trecho de .NET para criar uma coleção para armazenar dados de telemetria do dispositivo de 20.000 unidades de solicitação por segundo de produtividade. O SDK define o valor de OfferThroughput (que por sua vez define o `x-ms-offer-throughput` cabeçalho de solicitação na API REST). Aqui, definimos a `/deviceId` como a chave de partição. A opção de chave de partição é salva junto com o restante dos metadados conjunto como nome e política de indexação.

Neste exemplo, escolhemos `deviceId` como sabemos que (a) uma vez que existem em um grande número de dispositivos, gravações podem ser distribuídas uniformemente em partições e permitindo nos dimensionar o banco de dados para incluir grandes volumes de dados e (b) muitas das solicitações como buscar a leitura mais recente para um dispositivo limitam-se a um único deviceId e podem ser recuperadas de uma única partição.

    DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
    await client.CreateDatabaseAsync(new Database { Id = "db" });

    // Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
    // spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
    // sorting against any number or string property.
    DocumentCollection myCollection = new DocumentCollection();
    myCollection.Id = "coll";
    myCollection.PartitionKey.Paths.Add("/deviceId");

    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri("db"),
        myCollection,
        new RequestOptions { OfferThroughput = 20000 });
        

> [AZURE.NOTE] Para criar conjuntos de particionada, especifique um valor de produtividade de unidades de solicitação de > 10.000 por segundo. Como produtividade é múltiplos de 100, isso deve ser 10,100 ou superior.

Este método faz uma API REST chamada para DocumentDB e o serviço será provisionar um número de partições com base na taxa de transferência solicitada. Você pode alterar a taxa de transferência de um conjunto de como seu desempenho necessidades evoluem. Consulte [Níveis de desempenho](documentdb-performance-levels.md) para obter mais detalhes.

### <a name="reading-and-writing-documents"></a>Ler e gravar documentos

Agora, vamos inserir dados em DocumentDB. Aqui está uma classe de exemplo contendo um dispositivo de leitura e uma chamada para CreateDocumentAsync para inserir um novo dispositivo em uma coleção de leitura.

    public class DeviceReading
    {
        [JsonProperty("id")]
        public string Id;

        [JsonProperty("deviceId")]
        public string DeviceId;

        [JsonConverter(typeof(IsoDateTimeConverter))]
        [JsonProperty("readingTime")]
        public DateTime ReadingTime;

        [JsonProperty("metricType")]
        public string MetricType;

        [JsonProperty("unit")]
        public string Unit;

        [JsonProperty("metricValue")]
        public double MetricValue;
      }

    // Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new DeviceReading
        {
            Id = "XMS-001-FE24C",
            DeviceId = "XMS-0001",
            MetricType = "Temperature",
            MetricValue = 105.00,
            Unit = "Fahrenheit",
            ReadingTime = DateTime.UtcNow
        });


Vamos ler o documento por sua chave de partição e id, atualizá-lo e como uma etapa final, exclua-o por id e chave da partição. Observe que as leituras incluam um valor de PartitionKey (correspondente para o `x-ms-documentdb-partitionkey` cabeçalho de solicitação na API REST).

    // Read document. Needs the partition key and the ID to be specified
    Document result = await client.ReadDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

    DeviceReading reading = (DeviceReading)(dynamic)result;

    // Update the document. Partition key is not required, again extracted from the document
    reading.MetricValue = 104;
    reading.ReadingTime = DateTime.UtcNow;

    await client.ReplaceDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      reading);

    // Delete document. Needs partition key
    await client.DeleteDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });



### <a name="querying-partitioned-collections"></a>Consultar coleções particionadas

Quando você consulta dados em conjuntos de particionada, DocumentDB roteia automaticamente a consulta para as partições correspondente aos valores de chave de partição especificados no filtro (se houver alguma). Por exemplo, esta consulta é roteada para apenas a partição que contém a chave da partição "XMS-0001".

    // Query using partition key
    IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"))
        .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");

A seguinte consulta não tem um filtro na chave partição (DeviceId) e é leque exibindo a todas as partições onde ele é executado no índice da partição. Observe que você tenha que especificar o EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` na API REST) ter o SDK para executar uma consulta em partições.

    // Query across partition keys
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true })
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);

### <a name="parallel-query-execution"></a>Execução paralela da consulta

SDK do DocumentDB 1.9.0 e acima opções de execução de consulta paralela suporte, que permitem que você executar consultas de baixa latência em conjuntos de particionada, mesmo quando eles precisam um grande número de partições de toque. Por exemplo, a seguinte consulta está configurada para executar em paralelo em partições.

    // Cross-partition Order By Queries
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
        .OrderBy(m => m.MetricValue);

Você pode gerenciar a execução da consulta paralela ajustando parâmetros a seguir:

- Definindo `MaxDegreeOfParallelism`, você pode controlar o grau de paralelismo ou seja, o número máximo de conexões de rede simultâneas para partições da coleção. Se você definir como -1, o grau de paralelismo é gerenciado pelo SDK. Se o `MaxDegreeOfParallelism` não é especificado ou definida como 0, o que é o valor padrão, haverá uma conexão de rede única para partições da coleção.
- Definindo `MaxBufferedItemCount`, você pode compensar utilização de memória de lado de latência e o cliente de consulta. Se você omitir esse parâmetro ou defini-lo como -1, o número de itens em buffer durante a execução de consulta paralela é gerenciado pelo SDK.

Considerando o mesmo estado da coleção, uma consulta paralela retornará resultados na mesma ordem como em execução serial. Ao executar uma consulta de partição de cruz que inclui a classificação (ORDER BY e/ou superior), o SDK DocumentDB problemas a consulta em paralelo em partições e mescla resultados parcialmente classificados no lado do cliente para produzir resultados globalmente ordenados.

### <a name="executing-stored-procedures"></a>Executar procedimentos armazenados

Você também pode executar transações atômicas contra documentos com a mesma ID de dispositivo, por exemplo, se você estiver mantendo agregações ou o estado mais recente de um dispositivo em um único documento. 

    await client.ExecuteStoredProcedureAsync<DeviceReading>(
        UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
        new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
        "XMS-001-FE24C");

Na próxima seção, examinaremos como você pode mover para conjuntos de particionada de conjuntos de partição única.

<a name="migrating-from-single-partition"></a>
### <a name="migrating-from-single-partition-to-partitioned-collections"></a>Migrando do partição única para conjuntos de particionado
Quando um aplicativo usando um conjunto de partição única precisa produtividade superior (> 10.000 RU/s) ou maior armazenamento de dados (> 10GB), você pode usar a [Ferramenta de migração de dados de DocumentDB](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) para migrar os dados da coleção de partição única para um conjunto de particionada. 

Migrar de um conjunto de partição única para um conjunto de particionado

1. Exporte dados do conjunto de partição única para JSON. Consulte [Exportar para arquivo JSON](documentdb-import-data.md#export-to-json-file) para obter detalhes adicionais.
2. Importe os dados em um conjunto de particionada criado com uma definição de chave de partição e mais de 10.000 unidades de solicitação por segunda produtividade, conforme mostrado no exemplo abaixo. Consulte [importação DocumentDB](documentdb-import-data.md#DocumentDBSeqTarget) para obter detalhes adicionais.

![Migração de dados para um conjunto de particionados no DocumentDB][3]  

>[AZURE.TIP] Para tempos de importação, considere aumentar o número de solicitações paralelas como 100 ou superior para tirar proveito da taxa maior disponível para conjuntos de particionada. 

Agora que estamos concluiu as Noções básicas, vamos analisar algumas considerações de design importantes ao trabalhar com chaves de partição em DocumentDB.

## <a name="designing-for-partitioning"></a>Projetando para partição
A escolha da chave de partição é uma decisão importante que você precisará fazer no tempo de design. Esta seção descreve algumas das vantagens e desvantagens envolvidas no selecionando uma chave de partição para seu conjunto.

### <a name="partition-key-as-the-transaction-boundary"></a>Chave de partição como o limite de transação
Sua opção de chave de partição deve saldo a necessidade de habilitar o uso de transações em relação a exigência para distribuir suas entidades em várias chaves de partição para garantir uma solução scalable. Em um extremo, você pode definir a mesma chave de partição para todos os seus documentos, mas isso pode limitar a escalabilidade de sua solução. No outro extremo, você poderia atribuir uma chave de partição exclusivo para cada documento, que seria altamente escaláveis, mas prefere impedir usando transações de documento cruzada por meio de procedimentos armazenados e disparadores. Uma chave de partição ideal é um que permite que você use consultas eficientes e que tem cardinalidade suficiente para garantir que sua solução é scalable. 

### <a name="avoiding-storage-and-performance-bottlenecks"></a>Evitando gargalos de armazenamento e desempenho 
Também é importante escolher uma propriedade que permite que as gravações sejam distribuídos entre um número de valores distintos. Solicitações para a mesma chave de partição não podem exceder a taxa de transferência de uma única partição e serão reduzidas. Portanto é importante escolher uma chave de partição que não resultam em **"pontos de acesso"** dentro de seu aplicativo. O tamanho total de armazenamento para documentos com a mesma chave de partição não também pode exceder 10 GB de armazenamento. 

### <a name="examples-of-good-partition-keys"></a>Exemplos de chaves de partição boa
Aqui estão alguns exemplos de como escolher a chave da partição do aplicativo:

* Se você estiver implementando um perfil de usuário back-end, a ID de usuário é uma boa opção para chave da partição.
* Se você está armazenando dados IoT estado do dispositivo por exemplo, uma identificação de dispositivo é uma boa opção para chave da partição.
* Se você estiver usando DocumentDB para registrar dados de série temporal, a ID de nome do host ou processo é uma boa opção para chave da partição.
* Se você tiver uma arquitetura de vários locatário, a identificação de locatário é uma boa opção para chave da partição.

Observe que em alguns casos de uso (como os perfis de usuário e IoT descritos acima), a chave da partição pode ser iguais a sua id (chave de documento). Em outros, como os dados da série de tempo, você pode ter uma chave de partição diferente a identificação.

### <a name="partitioning-and-loggingtime-series-data"></a>Dados partição e registro em log/série temporal
Um dos casos mais comuns de uso da DocumentDB é para o registro em log e telemetria. É importante escolher uma chave de partição boa desde que talvez você precise grandes volumes de dados de leitura/gravação. A opção será dependem de sua leitura e gravação taxas e tipos de consultas que você pretende executar. Aqui estão algumas dicas sobre como escolher uma chave de partição boa.

- Se o seu caso de uso envolve uma pequena taxa de grava acculumating por um longo período de tempo e necessidade de consulta por intervalos de carimbos de hora e outros filtros, por exemplo, usando um acúmulo do carimbo de hora data como uma chave de partição é uma boa abordagem. Isso permite a consulta sobre todos os dados de uma data de uma única partição. 
- Se sua carga de trabalho é pesado de gravação, que é geralmente mais comuns, você deve usar uma chave de partição que não esteja baseada em carimbo de hora, para que DocumentDB pode distribuir gravações uniformemente em um número de partições. Aqui um nome de host, ID de processo, ID da atividade ou outra propriedade com alta cardinalidade é uma boa opção. 
- Uma terceira abordagem é um híbrido um onde você tem vários conjuntos, uma para cada dia/mês e a chave da partição é uma propriedade granular como o nome do host. Isso tem o benefício que você pode definir diferentes níveis de desempenho com base na janela de tempo, por exemplo, o conjunto para o mês atual é provisionado com maior produtividade desde que ele serve leituras e gravações, enquanto meses anteriores com menor produtividade desde que eles servem somente leitura.

### <a name="partitioning-and-multi-tenancy"></a>Partição e multilocação
Se você estiver implementando um aplicativo de vários locatário usando DocumentDB, existem dois padrões principais para implementar aluguel com DocumentDB – chave de uma partição por locação e uma coleção por locatário. Aqui estão os prós e contras para cada:

* Uma chave de partição por locação: nesse modelo, locatários são proximidade da localização em um único conjunto. Mas consultas e inserções para documentos dentro de um locatário único podem ser executadas em relação a uma única partição. Você também pode implementar lógica de transações em todos os documentos dentro de um locatário. Como vários locatários compartilham um conjunto, você pode salvar os custos de armazenamento e produtividade pool de recursos para locatários dentro de uma única coleção em vez de provisionamento espaço extra para cada site principal. A desvantagem é que você não tenha isolamento de desempenho por locatário. Aumentos de desempenho/produtividade se aplicam aos vs a coleção inteira aumenta alvo locatários.
* Um conjunto por locação: cada locatário tem seu próprio conjunto. Nesse modelo, você pode reservar desempenho por locatário. Com novo consumo com base em modelo de preços do DocumentDB, esse modelo é mais econômico para aplicativos de vários locatários com um pequeno número de locatários.

Você também pode usar uma abordagem de combinação/hierárquico que collocates locatários pequenos e migra locatários maiores para sua própria coleção.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, vamos descritas como partição funciona no Azure DocumentDB, como você pode criar conjuntos de particionada e como você pode escolher uma chave de partição boa para seu aplicativo. 

-   Execute testes de desempenho com DocumentDB e de escala. Consulte [desempenho e escala de teste com DocumentDB do Azure](documentdb-performance-testing.md) para uma amostra.
-   Introdução a codificação com os [SDKs](documentdb-sdk-dotnet.md) ou a [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx)
-   Saiba mais sobre [transferência provisionada em DocumentDB](documentdb-performance-levels.md)
-   Se você quiser personalizar como o seu aplicativo executa partição, você pode conectar sua própria implementação de partição do lado do cliente. Consulte [suporte de partição do lado do cliente](documentdb-sharding.md).

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png
[3]: ./media/documentdb-partition-data/documentdb-migration-partitioned-collection.png  

 
