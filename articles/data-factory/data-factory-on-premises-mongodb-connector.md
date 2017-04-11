<properties 
    pageTitle="Mover dados do MongoDB usando dados Factory | Microsoft Azure" 
    description="Saiba mais sobre como mover dados do banco de dados do MongoDB usando fábrica de dados do Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/04/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Mover dados do MongoDB usando fábrica de dados do Azure

Este artigo descreve como você pode usar a atividade de cópia em uma fábrica de dados do Azure para mover dados de um banco de dados do MongoDB local para outro armazenamento de dados. Este artigo se baseia o artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com a atividade de cópia e as combinações de repositório de dados de origem/receptor que ofereça suporte a atividade de cópia.

Serviço de fábrica de dados suporta a conexão a fontes de MongoDB locais usando o Gateway de gerenciamento de dados. Consulte o artigo de [Gateway de gerenciamento de dados](data-factory-data-management-gateway.md) para saber mais sobre o Gateway de gerenciamento de dados e [mover dados do local para a nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo sobre como configurar o gateway um pipeline de dados para mover dados. 

> [AZURE.NOTE] Você precisa usar o gateway para se conectar à MongoDB, mesmo se ele estiver hospedado em Azure IaaS VMs. Se você estiver tentando se conectar a uma instância do MongoDB hospedado na nuvem, você também pode instalar a instância de gateway na VM IaaS.

Dados fábrica atualmente suporta apenas movendo os dados da MongoDB outros armazenamentos de dados, mas não para mover dados de outras fontes de dados para MongoDB.

## <a name="prerequisites"></a>Pré-requisitos
Para o serviço de fábrica de dados do Azure sejam capazes de se conectar ao seu banco de dados do MongoDB local, você deve instalar os seguintes componentes: 

- Gateway de gerenciamento de dados 2.0 ou acima na mesma máquina que hospeda o banco de dados ou em uma máquina separada para evitar que competem para recursos com o banco de dados. Gateway de gerenciamento de dados é um software que se conecta a fontes de dados locais aos serviços de nuvem de maneira segura e gerenciada. Consulte o artigo do [Gateway de gerenciamento de dados](data-factory-data-management-gateway.md) para obter detalhes sobre o Gateway de gerenciamento de dados.
  
    Quando você instala o gateway, ele instala automaticamente um driver ODBC do Microsoft MongoDB usado para conectar ao MongoDB. 

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil para criar um pipeline que copia dados de um banco de dados MongoDB para qualquer um dos armazenamentos de dados com suporte receptor é usar o Assistente de dados de cópia. Consulte [Tutorial: criar um pipeline usando o Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para um rápida passo a passo sobre como criar um pipeline usando o Assistente de dados de cópia. 

O exemplo a seguir fornece definições de JSON de exemplo que você pode usar para criar um pipeline usando o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de banco de dados MongoDB ao armazenamento de Blob do Azure. No entanto, os dados podem ser copiados para qualquer um dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.

## <a name="sample-copy-data-from-mongodb-to-azure-blob"></a>Exemplo: Copiar dados de MongoDB ao Azure Blob
Este exemplo mostra como copiar dados de um banco de dados local MongoDB para um armazenamento de Blob do Azure. No entanto, dados podem ser copiados **diretamente** a qualquer uma dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.  
 
O exemplo tem as seguintes entidades de fábrica de dados:

1.  Um serviço vinculado do tipo [OnPremisesMongoDb](#linked-service-properties).
2.  Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Uma entrada [dataset](data-factory-create-datasets.md) do tipo [MongoDbCollection](#dataset-type-properties).
4.  Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [MongoDbSource](#copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de um resultado de consulta no banco de dados MongoDB para um blob cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras. 

A primeira etapa, configure o gateway de gerenciamento de dados de acordo com as instruções no artigo [Gateway de gerenciamento de dados](data-factory-data-management-gateway.md) . 

**Serviço de MongoDB vinculado**

    { 
        "name": "OnPremisesMongoDbLinkedService", 
        "properties": 
        { 
            "type": "OnPremisesMongoDb", 
            "typeProperties": 
            { 
                "authenticationType": "<Basic or Anonymous>", 
                "server": "< The IP address or host name of the MongoDB server >",  
                "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>", 
                "username": "<username>", 
                "password": "<password>",
               "authSource": "< The database that you want to use to check your credentials for authentication. >",
                "databaseName": "<database name>",
                "gatewayName": "<mygateway>"
            } 
        }
    } 


**Serviço de armazenamento vinculado do Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**MongoDB dataset de entrada** Configuração "externo": "true" informa o serviço de fábrica de dados que a tabela é externa à fábrica dados e não é produzida por uma atividade na fábrica dados.
    
    {
         "name":  "MongoDbInputDataset",
        "properties": { 
            "type": "MongoDbCollection", 
            "linkedServiceName": "OnPremisesMongoDbLinkedService", 
            "typeProperties": { 
                "collectionName": "<Collection name>"   
            }, 
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        } 
    }



**Conjunto de dados de saída do Azure Blob**

Dados são gravados em um novo blob cada hora (frequência: horas, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa ano, mês, dia e partes de horas de início.

    {
        "name": "AzureBlobOutputDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%M"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%d"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%H"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Pipeline com atividade de cópia**

O pipeline contém uma atividade de cópia que está configurado para usar a entrada acima e conjuntos de dados de saída e está agendado para ser executado a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **MongoDbSource** e **receptor** tipo está definido como **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados na última hora para copiar.
    
    {
        "name": "CopyMongoDBToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "MongoDbSource",
                            "query": "$$Text.Format('select * from  MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "MongoDbInputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutputDataSet"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MongoDBToAzureBlob"
                }
            ],
            "start": "2016-06-01T18:00:00Z",
            "end": "2016-06-01T19:00:00Z"
        }
    }


## <a name="linked-service-properties"></a>Propriedades de serviço vinculada

A tabela a seguir fornece descrição para elementos JSON específicos ao serviço de **OnPremisesMongoDB** vinculado.

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- | 
| tipo | A propriedade type deve ser definida: **OnPremisesMongoDb** | Sim |
| servidor | Endereço IP ou host nome do servidor MongoDB. | Sim | 
| porta | Porta TCP que o servidor MongoDB usa para escutar conexões de cliente. | Opcional, valor padrão: 27017 |
| authenticationType | Básica ou anônima. | Sim | 
| nome de usuário | Conta de usuário para acessar o MongoDB. | Sim (se autenticação básica é usada).|
| senha | Senha do usuário. |   Sim (se autenticação básica é usada). | 
| authSource | Nome do banco de dados MongoDB que você deseja usar para verificar suas credenciais para autenticação. | Opcional (se a autenticação básica é usada). padrão: usa a conta de administrador e o banco de dados especificado usando databaseName propriedade. |  
| databaseName | Nome do banco de dados MongoDB que você deseja acessar. | Sim |
| gatewayName | Nome do gateway que acessa o repositório de dados. | Sim | 
| encryptedCredential | Credencial criptografada pelo gateway. | Opcional |


Consulte [definir credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para obter detalhes sobre como definir credenciais para uma fonte de dados MongoDB local.

## <a name="dataset-type-properties"></a>Propriedades de tipo de conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo de [conjuntos de dados criando](data-factory-create-datasets.md) . Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOB do Microsoft Azure, tabela do Microsoft Azure, etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção typeProperties de conjunto de dados do tipo **MongoDbCollection** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| NomeDaColeta | Nome da coleção no banco de dados MongoDB. | Sim | 

## <a name="copy-activity-type-properties"></a>Copiar propriedades de tipo de atividade

Para obter uma lista completa das seções & propriedades disponíveis para a definição de atividades, consulte o artigo [Criar canais](data-factory-create-pipelines.md) . Propriedades como nome, descrição, entrada e saída tabelas e política estão disponíveis para todos os tipos de atividades. 

Por outro lado, propriedades disponíveis na seção **typeProperties** da atividade variam com cada tipo de atividade. Atividade de cópia, eles variam dependendo dos tipos de fontes e receptores.

Quando a origem for do tipo **MongoDbSource** as seguintes propriedades estão disponíveis na seção de typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- |
| consulta | Use a consulta personalizada para ler dados. | Cadeia de caracteres de consulta SQL-92. Por exemplo: selecionar * de MyTable. | Não (se **NomeDaColeta** de **dataset** for especificado) | 

## <a name="schema-by-data-factory"></a>Esquema pela fábrica de dados
Serviço de dados fábrica Azure infere esquema de uma coleção de MongoDB usando os documentos de 100 mais recentes no conjunto de. Se esses 100 documentos não contêm o esquema completo, algumas colunas podem ser ignoradas durante a operação de cópia. 

## <a name="type-mapping-for-mongodb"></a>Digite o mapeamento para MongoDB

Conforme mencionado no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , atividade de cópia executa conversões de tipo automática de tipos de fonte para coletar tipos com a abordagem de 2 etapas a seguir:

1. Converter de tipos de fonte nativo para tipo .NET
2. Converter do tipo .NET para o tipo de receptor nativo

Ao mover dados para MongoDB os seguintes mapeamentos são usados de tipos de MongoDB para tipos .NET.

| Tipo de MongoDB | Tipo .NET framework |
| ------------------- | ------------------- | 
| Binário | Byte] |
| Booliano | Booliano |
| Data | DateTime |
| NumberDouble | Duplo |
| NumberInt | Int32 |
| NumberLong | Int64 |
| ID do objeto | Cadeia de caracteres |
| Cadeia de caracteres | Cadeia de caracteres |
| UUID | GUID | 
| Objeto | Renormalized em Nivelar colunas com "_" como separador aninhada |

> [AZURE.NOTE]  
> Para saber sobre o suporte para matrizes usando tabelas virtuais, consulte a seção [suporte para tipos complexos usando tabelas virtuais](#support-for-complex-types-using-virtual-tables) abaixo. 

Atualmente, não há suporte para os seguintes tipos de dados MongoDB: DBPointer, JavaScript, máx/mín chave, expressões regulares, símbolo, carimbo de hora, indefinido

## <a name="support-for-complex-types-using-virtual-tables"></a>Suporte para tipos complexos usando tabelas virtuais
Azure Data Factory usa um driver ODBC interno conectem e copie os dados de seu banco de dados MongoDB. Para obter tipos complexos como matrizes ou objetos com tipos diferentes nos documentos, o driver novamente normalizará dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver nessas colunas, o driver gera virtuais tabelas a seguir:

-   Uma **tabela base**, que contém os mesmos dados como a tabela real, exceto para as colunas de tipo complexo. A tabela base usa o mesmo nome como a tabela real que ele representa.
-   Uma **tabela virtual** para cada coluna tipo complexo, que se expande os dados aninhados. As tabelas virtuais são nomeadas usando o nome da tabela real, um separador "_" e o nome do objeto ou matriz.

Tabelas virtuais referem-se aos dados na tabela real, ativando o driver acessar os dados desordenados. Consulte a seção de exemplo abaixo detalhes. Você pode acessar o conteúdo das matrizes MongoDB consultando e associar as tabelas virtuais.

Você pode usar o [Assistente de cópia](data-factory-data-movement-activities.md#data-factory-copy-wizard) intuitiva exibir a lista de tabelas no banco de dados do MongoDB incluindo as tabelas virtuais e visualizar os dados dentro. Você também pode criar uma consulta no Assistente de cópia e validar para ver o resultado.

### <a name="example"></a>Exemplo

Por exemplo, "ExampleTable" abaixo é uma tabela de MongoDB que tenha uma coluna com uma matriz de objetos em cada célula – notas fiscais e uma coluna com uma matriz de tipos escalares – classificações. 

ID | Nome do cliente | Faturas | Nível de serviço | Classificações
--- | ------------- | -------- | ------------- | -------
1111 | ABC | [{invoice_id: item "123",: "torradeira", preço: desconto "456,": "0,2"}, {invoice_id: item "124,": "forno", preço: "1235", desconto: "0,2"}] | Prata | [5,6]
2222 | XYZ | [{invoice_id: item "135,": "fridge", preço: "12543", desconto: "0,0"}] | Ouro | [1,2]

O driver seria gerar várias tabelas virtuais para representar esta tabela única. A primeira tabela virtual é a tabela base denominada "ExampleTable", mostrado abaixo. A tabela base contém todos os dados da tabela original, mas os dados a partir das matrizes foi omitidos e são expandidos nas tabelas virtuais.

ID | Nome do cliente | Nível de serviço
--- | ------------- | -------------
1111 | ABC | Prata
2222 | XYZ | Ouro

As tabelas a seguir mostram as tabelas virtuais que representam as matrizes originais no exemplo. Estas tabelas contêm o seguinte: 

- Uma referência de volta para a coluna de chave primária original correspondente à linha da matriz original (via a coluna ID)
- Uma indicação da posição dos dados dentro da matriz original 
- Os dados expandidos para cada elemento dentro da matriz

Tabela "ExampleTable_Invoices":

ID | ExampleTable_Invoices_dim1_idx | invoice_id | item | preço | Desconto
--- | -------------- | ---------- | ---- | ----- | --------
1111 | 0 | 123 | torradeira | 456 | 0,2
1111 | 1 | 124 | forno | 1235 | 0,2
2222 | 0 | 135 | fridge | 12543 | 0,0

Tabela "ExampleTable_Ratings":

ID | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings
--- | ------------- | -------------
1111 | 0 | 5
1111 | 1 | 6
2222 | 0 | 1
2222 | 1 | 2

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Desempenho e ajuste  
Consulte o [guia de ajuste e desempenho de atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre principais fatores que desempenho de impacto de movimentação de dados (cópia atividade) em fábrica de dados do Azure e várias maneiras de otimizá-lo.

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo [mover dados entre locais e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo para criar um pipeline de dados que move dados de um armazenamento de dados local para um armazenamento de dados do Azure. 

