<properties 
    pageTitle="Solicitar unidades em DocumentDB | Microsoft Azure" 
    description="Saiba mais sobre como compreender, especificar e estimar os requisitos de unidade de solicitação em DocumentDB." 
    services="documentdb" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="syamk"/>

#<a name="request-units-in-documentdb"></a>Solicitar unidades em DocumentDB
Agora disponível: DocumentDB [Calculadora de unidade de solicitação](https://www.documentdb.com/capacityplanner). Saiba mais em [Estimating sua produtividade precisa](documentdb-request-units.md#estimating-throughput-needs).

![Calculadora de produtividade][5]

##<a name="introduction"></a>Introdução
Este artigo fornece uma visão geral de unidades de solicitação no [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). 

Após ler este artigo, você poderá responder às seguintes perguntas:  

-   O que são unidades de solicitação e solicitar encargos?
-   Como posso especificar a capacidade de unidade de solicitação para um conjunto?
-   Como estimar que necessidades de unidade de solicitação do meu aplicativo?
-   O que acontece se eu exceder a capacidade de unidade de solicitação para um conjunto?


##<a name="request-units-and-request-charges"></a>Unidades de solicitação e encargos de solicitação
DocumentDB oferece desempenho de forma rápido e previsível por *reserva de* recursos para atender a que necessidades de produtividade do seu aplicativo.  Porque o aplicativo carregar e acessar padrões alterar ao longo do tempo, DocumentDB permite que você facilmente aumentar ou diminuir a quantidade de produtividade reservada disponível para seu aplicativo.

Com DocumentDB, produtividade reservada é especificada em termos de unidades de solicitação de processamento por segundo.  Você pode pensar unidades de solicitação como moeda de produtividade, por meio da qual você *reservar* uma quantidade de garantia de unidades de solicitação disponíveis para seu aplicativo em por segundo.  Cada operação em DocumentDB - escrevendo um documento, executar uma consulta, atualizando um documento consome CPU, memória e IOPS.  Ou seja, cada operação gera um *encargo de solicitação*, que é expresso em *unidades de solicitação*.  Noções básicas sobre os fatores que afetam os encargos de unidade de solicitação, juntamente com os requisitos de produtividade do seu aplicativo, permite que você executar o aplicativo como custo efetivamente possível. 

##<a name="specifying-request-unit-capacity"></a>Especifica a capacidade de unidade de solicitação
Ao criar um conjunto de DocumentDB, você pode especificar o número de unidades de solicitação por segundo (RUs) reservada para o conjunto.  Depois que o conjunto é criado, a alocação completa de RUs especificado é reservada para uso da coleção.  Cada coleção certamente tem dedicada e isolada características de produtividade.  

É importante observar que DocumentDB opera em um modelo de reserva; Isto é, você será cobrado para produtividade *reservada* para a coleção, independentemente de quanto do que produtividade é ativamente *usada*.  Lembre-se, no entanto, que como carregar seu aplicativo, dados e alteração de padrões de uso, de que você pode facilmente dimensionar para a quantidade reservados RUs pelos DocumentDB SDKs ou usando o [Portal do Azure](https://portal.azure.com).  Para obter mais informações para produtividade de escala para cima e para baixo, consulte [níveis de desempenho de DocumentDB](documentdb-performance-levels.md).

##<a name="request-unit-considerations"></a>Considerações de unidade de solicitação
Ao estimar o número de unidades de solicitação reservar para seu conjunto de DocumentDB, é importante levar as seguintes variáveis em consideração:

- **Tamanho do documento**. Como tamanhos do documento aumentam as unidades consumidas para ler ou escrever que os dados também aumentará.
- **Contagem de propriedade de documento**. Supondo indexação de padrão de todas as propriedades, as unidades consumidas para escrever um documento aumentará como aumenta a contagem de propriedade.
- **Consistência de dados**. Ao usar níveis de consistência de dados de forte ou envelhecimento limitada, unidades adicionais serão consumidas para ler documentos.
- **Propriedades de indexado**. Uma política de índice em cada coleção determina quais propriedades são indexadas por padrão. Você pode reduzir o consumo de unidade solicitação limitando o número de propriedades indexadas ou ativando a indexação lenta.
- **Indexação de documentos**. Por padrão a que cada documento será automaticamente indexado, você irá consumir menos unidades de solicitação se você optar por não indexar alguns dos seus documentos.
- **Padrões de consulta**. A complexidade de uma consulta afeta quantas unidades solicitar são consumidas para uma operação. O número de predicados, natureza predicados, projeções, número de UDFs e o tamanho do conjunto de dados de origem todos influenciar o custo das operações de consulta.
- **Uso de script**.  Como com consultas, procedimentos armazenados e disparadores consumam unidades de solicitação com base na complexidade das operações estão sendo executadas. À medida que você desenvolve seu aplicativo, inspecione o cabeçalho de carga de solicitação para entender melhor como a cada operação está consumindo a capacidade de unidade de solicitação.

##<a name="estimating-throughput-needs"></a>Estimativa necessidades de produtividade
Uma unidade de solicitação é uma medida normalizada de custo de processamento de solicitação. Uma unidade única solicitação representa a capacidade de processamento necessária para ler (via link auto ou id) um único documento JSON 1KB consiste 10 valores de propriedade unique (excluindo propriedades do sistema). Uma solicitação para criar (inserir), substituir ou excluir o mesmo documento irá consumir mais processamento do serviço e, portanto, mais unidades de solicitação.   

> [AZURE.NOTE] A linha de base da unidade 1 solicitação para um documento de 1KB corresponde a um GET simple por link auto ou id do documento.

###<a name="use-the-request-unit-calculator"></a>Usar a Calculadora de unidade de solicitação
Para ajudar clientes finos ajustar suas estimativas de produtividade, há uma [Calculadora de unidade de solicitação](https://www.documentdb.com/capacityplanner) do baseada na web para ajudar a estimar os requisitos de unidade de solicitação para operações típicas, incluindo:

- Documento cria (gravações)
- Leituras de documento
- Exclui do documento
- Atualizações de documento

A ferramenta também inclui suporte para estimar necessidades de armazenamento de dados com base em documentos de exemplo fornecidos.

Usando a ferramenta é simple:

1. Carregar um ou mais documentos JSON representantes.

    ![Carregar documentos para a Calculadora de unidade de solicitação][2]

2. Para estimar os requisitos de armazenamento de dados, insira o número total de documentos que você espera armazenar.

3. Insira o número de documento criar, ler, atualizar e excluir operações exigem (em uma base por segundo). Para estimar as cobranças de unidade de solicitação de operações de atualização de documento, carregue uma cópia do documento de exemplo da etapa 1 acima que inclui atualizações de campo típico.  Por exemplo, se atualizações de documento normalmente modificam duas propriedades chamadas lastLogin e userVisits, em seguida, simplesmente copiar o documento de amostra, atualizar os valores para essas duas propriedades e carregue o documento copiado.

    ![Insira requisições de produtividade na Calculadora de unidade de solicitação][3]

4. Clique em calcular e examine os resultados.

    ![Solicitar os resultados de Calculadora de unidade][4]

>[AZURE.NOTE]Se você tiver tipos de documento que serão muito diferente em termos de tamanho e o número de propriedades indexadas, carregue uma amostra de cada *tipo* de documento típico a ferramenta e, em seguida, calcular os resultados.

###<a name="use-the-documentdb-request-charge-response-header"></a>Use o cabeçalho de resposta charge DocumentDB solicitação
Cada resposta do serviço DocumentDB inclui um cabeçalho personalizado (x-ms-solicitação-cobrança) que contém as unidades de solicitação consumidas para a solicitação. Esse cabeçalho é também acessível pelo SDK do DocumentDB. No SDK do .NET, RequestCharge é uma propriedade do objeto ResourceResponse.  Para consultas, o navegador de consulta DocumentDB no portal do Azure fornece informações de custo de solicitação para consultas executadas.

![Examinando encargos RU no Explorador de consulta][1]

Com isso em mente, um método para estimar a quantidade de produtividade reservada exigida pelo seu aplicativo é gravar o encargo unitário de solicitação associado ao executar operações típicas em relação a um documento representante usado pelo seu aplicativo e, em seguida, estimando o número de operações você espera executar cada segundo.  Certifique-se medir e incluir consultas típicas e o uso de script de DocumentDB também.

>[AZURE.NOTE]Se você tiver tipos de documento que serão muito diferente em termos de tamanho e o número de propriedades indexadas, registre o encargo unitário de solicitação de operação aplicável associado a cada *tipo* de documento típico.

Por exemplo:

1. Registrar o encargo de unidade de solicitação de criação (inserindo) um documento típico. 
2. Registre o encargo de unidade de solicitação de leitura de um documento típico.
3. Registro a carga de unidade de solicitação de atualização de um documento típico.
3. Registre o encargo de unidade de solicitação de consultas de documento típico e comuns.
4. Registrar o encargo de unidade de solicitação de quaisquer scripts personalizados (procedimentos armazenados, disparadores, funções definidas pelo usuário) utilizado pelo aplicativo
5. Calcule as unidades de solicitação necessários dados o número estimado de operações que você prevê para executar cada segundo.

##<a name="a-request-unit-estimation-example"></a>Um exemplo de solicitação de estimativa de unidade
Considere o seguinte documento ~ 1KB:

    {
     "id": "08259",
    "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
    "tags": [
        {
        "name": "cereals ready-to-eat"
        },
        {
        "name": "kellogg"
        },
        {
        "name": "kellogg's crispix"
        }
    ],
    "version": 1,
    "commonName": "Includes USDA Commodity B855",
    "manufacturerName": "Kellogg, Co.",
    "isFromSurvey": false,
    "foodGroup": "Breakfast Cereals",
    "nutrients": [
        {
        "id": "262",
        "description": "Caffeine",
        "nutritionValue": 0,
        "units": "mg"
        },
        {
        "id": "307",
        "description": "Sodium, Na",
        "nutritionValue": 611,
        "units": "mg"
        },
        {
        "id": "309",
        "description": "Zinc, Zn",
        "nutritionValue": 5.2,
        "units": "mg"
        }
    ],
    "servings": [
        {
        "amount": 1,
        "description": "cup (1 NLEA serving)",
        "weightInGrams": 29
        }
    ]
    }

>[AZURE.NOTE]Documentos são minified em DocumentDB, para que o sistema calculada tamanho do documento acima é um pouco menor que 1KB.


A tabela a seguir mostra a solicitação aproximada encargos de unidade para operações típicas neste documento (o encargo unitário solicitação aproximada pressupõe que o nível de consistência de conta estiver definido como "Sessão" e que todos os documentos são automaticamente indexados):

Operação|Solicitar encargo unitário 
---|---
Criar documento|~ 15 RU 
Ler documento|~ 1 RU
Documento de consulta por id|~2.5 RU

Além disso, esta tabela mostra solicitação aproximada encargos de unidade para consultas típicos usados no aplicativo:

Consulta|Solicitar encargo unitário|# documentos retornados
---|---|--- 
Selecione alimentos por id|~2.5 RU|1 
Selecione alimentos pelo fabricante|~ 7 RU|7
Selecionar grupo de alimentos e ordem por peso|~ 70 RU|100
Selecionar 10 alimentos superiores em um grupo de alimentos|~ 10 RU|10

>[AZURE.NOTE]Encargos de RU variam com base no número de documentos retornados.

Com essas informações, podemos estimar os requisitos de RU para este aplicativo dado o número de operações e consultas que esperamos por segundo:

Operação/consulta|Número estimado por segundo|RUs necessárias 
---|---|--- 
Criar documento|10|150 
Ler documento|100|100 
Selecione alimentos pelo fabricante|25|175 
Selecione por grupo de alimentos|10|700 
Selecione os 10 principais|15|Total de 150|155|1275

Nesse caso, podemos esperar um requisito de produtividade média de 1,275 RU/s.  Arredondamento até 100 a mais próximo, vamos provisionar 1.300 RU/s para conjunto deste aplicativo.

##<a id="RequestRateTooLarge"></a>Limites de produtividade reservadas excedente
Lembre-se de que o consumo de unidade de solicitação é avaliado como uma taxa por segundo. Para aplicativos que excedeu a taxa de unidade de solicitação provisionado para um conjunto, solicitações a esse conjunto serão reduzidas até que a taxa cai abaixo do nível reservado. Quando ocorre um controle, o servidor será preventivamente encerrar a solicitação com RequestRateTooLargeException (código de status HTTP 429) e retornar o cabeçalho x-ms-Repetir-após-ms indicando a quantidade de tempo, em milissegundos, que o usuário deve aguardar antes de tentar novamente a solicitação.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Se você estiver usando as consultas SDK do cliente .NET e LINQ, em seguida, na maioria das vezes que você nunca tenha que lidar com essa exceção, como a versão atual do SDK do cliente .NET implicitamente captura esta resposta, respeita o servidor especificado depois de Repetir cabeçalho e reenviar a solicitação. A menos que sua conta está sendo acessada simultaneamente por vários clientes, a próxima tentativa será bem-sucedida.

Se você tiver mais de um cliente cumulativa operando acima da taxa de solicitação, o comportamento de repetir padrão não pode ser suficiente, e o cliente irá acionar uma DocumentClientException com código de status 429 ao aplicativo. Em casos como esta, você pode considerar a manipulação de comportamento de repetição e lógica do erro do seu aplicativo manipulando rotinas ou aumentar a produtividade reservada para o conjunto.

##<a name="next-steps"></a>Próximas etapas

Para saber mais sobre reservada produtividade com bancos de dados do Azure DocumentDB, explore estes recursos:
 
- [DocumentDB preços](https://azure.microsoft.com/pricing/details/documentdb/)
- [Gerenciando capacidade de DocumentDB](documentdb-manage.md) 
- [Dados de modelagem em DocumentDB](documentdb-modeling-data.md)
- [Níveis de desempenho de DocumentDB](documentdb-partition-data.md)

Para saber mais sobre DocumentDB, consulte a [documentação](https://azure.microsoft.com/documentation/services/documentdb/)do Azure DocumentDB. 

Para começar a usar escala e testes de desempenho com DocumentDB, consulte [desempenho e escala de teste com DocumentDB do Azure](documentdb-performance-testing.md).


[1]: ./media/documentdb-request-units/queryexplorer.png 
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png
