<properties 
    pageTitle="Mover dados do Redshift Amazon usando dados Factory | Microsoft Azure" 
    description="Saiba mais sobre como mover dados de Redshift Amazon usando fábrica de dados do Azure." 
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
    ms.date="08/23/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Mover dados do Amazon Redshift utilizando fábrica de dados do Azure

Este artigo descreve como você pode usar a atividade de cópia em uma fábrica de dados do Azure para mover dados do Amazon Redshift aos dados de outra armazenar. Este artigo se baseia o artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com atividade de cópia e uma lista de armazenamentos de dados de origem/receptor.  

Fábrica de dados atualmente suporta apenas movendo os dados da Amazon Redshift outros armazenamentos de dados, mas não para mover dados de outras fontes de dados para o Amazon Redshift.

## <a name="prerequisites"></a>Pré-requisitos

- Se você estiver movendo dados para um armazenamento de dados local, o Gateway de gerenciamento de dados (use o endereço IP da máquina) conceder acesso aos Amazon Redshift cluster. Consulte [autorizar o acesso ao cluster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) para obter instruções. 
- Se você estiver movendo dados para um armazenamento de dados do Azure, consulte [Intervalos de IP de centro de dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653) para os intervalos de endereços do IP de computação (incluindo intervalos SQL) usados pelos centros de dados do Microsoft Azure. 

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil para criar um pipeline que copia dados da Amazon Redshift é usar o Assistente de dados de cópia. Consulte [Tutorial: criar um pipeline usando o Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para um rápida passo a passo sobre como criar um pipeline usando o Assistente de dados de cópia. 

O exemplo a seguir fornece definições de JSON de exemplo que você pode usar para criar um pipeline usando o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ele mostra como copiar dados de Amazon Redshift ao armazenamento de Blob do Azure. No entanto, os dados podem ser copiados para qualquer um dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores).

## <a name="sample-copy-data-from-amazon-redshift-to-azure-blob"></a>Exemplo: Copiar dados de Amazon Redshift ao Azure Blob
Este exemplo mostra como copiar dados de um banco de dados do Amazon Redshift para um armazenamento de Blob do Azure. No entanto, dados podem ser copiados **diretamente** a qualquer uma dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.  
 
O exemplo tem as seguintes entidades de fábrica de dados:

- Um serviço vinculado do tipo [AmazonRedshift](#linked-service-properties).
- Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Uma entrada [dataset](data-factory-create-datasets.md) do tipo [RelationalTable](#dataset-type-properties).
- Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](#copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de um resultado de consulta no Amazon Redshift para um blob cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras. 

**Serviço de Redshift Amazon vinculado**

    {
        "name": "AmazonRedshiftLinkedService",
        "properties":
        {
            "type": "AmazonRedshift",
            "typeProperties":
            {
                "server": "< The IP address or host name of the Amazon Redshift server >",
                "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
                "database": "<The database name of the Amazon Redshift database>",
                "username": "<username>",
                "password": "<password>"
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

**Amazon Redshift dataset de entrada**

Definindo **"externo": true** informa o serviço de fábrica de dados que o conjunto de dados externo à fábrica dados e não é produzido por uma atividade na fábrica dados. Defina esta propriedade como true em um conjunto de dados de entrada que não é produzido por uma atividade no pipeline.

    {
        "name": "AmazonRedshiftInputDataset",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "AmazonRedshiftLinkedService",
            "typeProperties": {
                "tableName": "<Table name>"
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
                "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
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

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendado para ser executado a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **RelationalSource** e **receptor** tipo está definido como **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados na última hora para copiar.
    
    {
        "name": "CopyAmazonRedshiftToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonRedshiftInputDataset"
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
                    "name": "AmazonRedshiftToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Propriedades de serviço vinculada

A tabela a seguir fornece descrição para elementos JSON específicos ao serviço de Redshift Amazon vinculado.

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- | 
| tipo | A propriedade de tipo deve ser definida como: **AmazonRedshift**. | Sim |
| servidor | Endereço IP ou host nome do servidor Redshift Amazon. | Sim |
| porta | O número da porta TCP que o servidor Amazon Redshift usa para escutar conexões de cliente. | Não, valor padrão: 5439 |
| banco de dados | Nome do banco de dados Redshift Amazon. | Sim |
| nome de usuário | Nome de usuário que tenha acesso ao banco de dados.| Sim |
| senha | Senha da conta de usuário.| Sim |


## <a name="dataset-type-properties"></a>Propriedades de tipo de conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo de [conjuntos de dados criando](data-factory-create-datasets.md) . Seções como estrutura, disponibilidade e política são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOB do Microsoft Azure, tabela do Microsoft Azure, etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção typeProperties de conjunto de dados do tipo **RelationalTable** (que inclui Amazon Redshift dataset) tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| nome de tabela | Nome da tabela no banco de dados Redshift Amazon vinculado serviço se refere. | Não (se a **consulta** de **RelationalSource** for especificada) | 

## <a name="copy-activity-type-properties"></a>Copiar propriedades de tipo de atividade

Para obter uma lista completa das seções & propriedades disponíveis para a definição de atividades, consulte o artigo [Criar canais](data-factory-create-pipelines.md) . Propriedades como nome, descrição, entrada e saída tabelas e políticas estão disponíveis para todos os tipos de atividades. 

Por outro lado, propriedades disponíveis na seção **typeProperties** da atividade variam com cada tipo de atividade. Atividade de cópia, eles variam dependendo dos tipos de fontes e receptores.

Quando a origem de atividade de cópia for do tipo **RelationalSource** (que inclui Amazon Redshift) as propriedades a seguir estão disponíveis na seção de typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- |
| consulta | Use a consulta personalizada para ler dados. | Cadeia de caracteres de consulta SQL. Por exemplo: selecionar * de MyTable. | Não (se o **nome de tabela** de **dataset** for especificado) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-amazon-redshift"></a>Digite o mapeamento para Amazon Redshift

Conforme mencionado no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , atividade de cópia executa conversões de tipo automática de tipos de fonte para coletar tipos com a abordagem de duas etapas a seguir:

1. Converter de tipos de fonte nativo para tipo .NET
2. Converter do tipo .NET para o tipo de receptor nativo

Ao mover dados para Amazon Redshift, os seguintes mapeamentos serão usados de tipos de Redshift Amazon para tipos .NET.

Tipo de Redshift Amazon | .NET com base em tipo
-------------------- | ---------------
SMALLINT | Int16
NÚMERO INTEIRO | Int32
BIGINT | Int64
DECIMAL | Decimal
REAL | Único
PRECISÃO DUPLA | Duplo
BOOLIANO | Cadeia de caracteres
CARACT | Cadeia de caracteres
VARCHAR | Cadeia de caracteres
DATA | DateTime
CARIMBO DE HORA | DateTime
TEXTO | Cadeia de caracteres



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Desempenho e ajuste  
Consulte o [guia de ajuste e desempenho de atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre principais fatores que desempenho de impacto de movimentação de dados (cópia atividade) em fábrica de dados do Azure e várias maneiras de otimizá-lo.

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos: 
- [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia. 