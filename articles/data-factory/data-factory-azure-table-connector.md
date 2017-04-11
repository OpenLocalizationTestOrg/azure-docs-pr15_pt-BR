<properties 
    pageTitle="Mover dados para/de tabela do Azure | Microsoft Azure" 
    description="Saiba como mover dados de/para o armazenamento de tabela do Azure usando fábrica de dados do Azure." 
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
    ms.date="09/13/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Mover de dados e tabela de Azure usando fábrica de dados do Azure

Este artigo descreve como você pode usar a atividade de cópia em uma fábrica de dados do Azure para mover dados de/para tabela do Azure de/para outro armazenamento de dados. Este artigo se baseia o artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral das combinações de repositório de dados com suporte e movimentação de dados com a atividade de cópia.

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil para criar um pipeline que copia dados de/para o armazenamento de tabela do Azure é usar o Assistente de dados de cópia. Consulte [Tutorial: criar um pipeline usando o Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para um rápida passo a passo sobre como criar um pipeline usando o Assistente de dados de cópia. 

Os exemplos a seguir fornecem definições de JSON de exemplo que você pode usar para criar um pipeline usando o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados entre o armazenamento de tabela do Azure e o banco de dados do Azure Blob. No entanto, dados podem ser copiados **diretamente** de qualquer uma das fontes a qualquer uma dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.

## <a name="sample-copy-data-from-azure-table-to-azure-blob"></a>Exemplo: Copiar dados de tabela do Azure para Azure Blob

Mostra o exemplo a seguir:

1.  Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (usado para blob & tabela).
2.  Uma entrada [dataset](data-factory-create-datasets.md) do tipo [Azure](#azure-table-dataset-type-properties).
3.  Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
3.  O [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [AzureTableSource](#azure-table-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

O exemplo copia dados pertencentes à partição padrão em uma tabela do Azure para um blob cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras.

**Serviço de armazenamento do Azure vinculado:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Azure fábrica de dados compatível com dois tipos de serviços de armazenamento do Azure vinculado: **AzureStorage** e **AzureStorageSas**. Para o primeiro, especifique a cadeia de conexão que inclui a chave da conta e para o posterior, você especificar o Uri de assinatura de acesso compartilhado (SAS). Consulte a seção de [Serviços vinculadas](#linked-services) para obter detalhes.  

**Azure dataset de entrada de tabela:**

O exemplo supõe que você criou uma tabela "MinhaTabela" na tabela do Azure.
 
Configuração "externo": "true" informa o serviço de fábrica de dados que o conjunto de dados externo à fábrica dados e não é produzido por uma atividade na fábrica dados.

    {
      "name": "AzureTableInput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**Azure Blob dataset de saída:**

Dados são gravados em um novo blob cada hora (frequência: horas, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa ano, mês, dia e partes de horas de início. 

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Pipeline com a atividade de cópia:**

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendado para ser executado a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **AzureTableSource** e **receptor** tipo está definido como **BlobSink**. A consulta SQL especificada com a propriedade **AzureTableSourceQuery** seleciona os dados da partição padrão cada hora para copiar.

    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                {
                    "name": "AzureTabletoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                        {
                            "name": "AzureTableInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "AzureTableSource",
                            "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },              
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 0,
                        "timeout": "01:00:00"
                    }
                }
             ]  
        }
    }

## <a name="sample-copy-data-from-azure-blob-to-azure-table"></a>Exemplo: Copiar dados do Azure Blob tabela do Azure

Mostra o exemplo a seguir:

1.  Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (usado para blob & tabela)
3.  Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Uma saída [dataset](data-factory-create-datasets.md) do tipo [Azure](#azure-table-dataset-type-properties). 
4.  O [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [AzureTableSink](#azure-table-copy-activity-type-properties). 


As cópias de amostra série temporal dados a partir de um Azure blob para um Azure tabela por hora. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras.

**Serviço de armazenamento do Azure (para tabela do Azure & Blob) vinculado:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Azure fábrica de dados compatível com dois tipos de serviços de armazenamento do Azure vinculado: **AzureStorage** e **AzureStorageSas**. Para o primeiro, especifique a cadeia de conexão que inclui a chave da conta e para o posterior, você especificar o Uri de assinatura de acesso compartilhado (SAS). Consulte a seção de [Serviços vinculadas](#linked-services) para obter detalhes. 

**Azure Blob entrado conjunto de dados:**

Dados são obtidos de um novo blob cada hora (frequência: horas, intervalo: 1). O nome de caminho e do arquivo de pasta para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa ano, mês e dia parte da hora de início e nome do arquivo usa a parte de hora de início. "externo": "true" configuração informa o serviço de fábrica de dados que o conjunto de dados externo à fábrica dados e não é produzido por uma atividade na fábrica dados.
    
    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
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
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**Tabela do Microsoft Azure dataset de saída:**

O exemplo copia dados a uma tabela chamada "Minha tabela" na tabela do Azure. Crie uma tabela do Azure com o mesmo número de colunas, como você espera arquivo CSV de Blob para conter. Novas linhas são adicionadas à tabela de cada hora. 

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Pipeline com a atividade de cópia:**

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendado para ser executado a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **BlobSource** e **receptor** tipo está definido como **AzureTableSink**. 


    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureTableOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "AzureTableSink",
                "writeBatchSize": 100,
                "writeBatchTimeout": "01:00:00"
              }
            },
            "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },                      
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
          ]
       }
    }

## <a name="linked-services"></a>Serviços de vinculadas
Há dois tipos de serviços vinculados, que você pode usar para vincular um armazenamento de blob do Microsoft Azure uma fábrica dados Azure. Eles são: **AzureStorage** vinculado serviço e **AzureStorageSas** vinculado. O serviço de armazenamento do Azure vinculado fornece fábrica dados com acesso global ao armazenamento do Azure. Enquanto o SAS de armazenamento do Azure (assinatura de acesso compartilhado) vinculado o serviço fornece a fábrica de dados com acesso restrito/ligadas ao tempo ao armazenamento do Azure. Não há nenhum outras diferenças entre esses dois serviços vinculadas. Escolha o serviço vinculado que atenda às suas necessidades. As seções a seguir fornecem mais detalhes sobre esses dois serviços vinculados.

[AZURE.INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="azure-table-dataset-type-properties"></a>Azure propriedades de tipo de conjunto de dados de tabela

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo de [conjuntos de dados criando](data-factory-create-datasets.md) . Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOB do Microsoft Azure, tabela do Microsoft Azure, etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção de **typeProperties** para o conjunto de dados do tipo **Azure** tem as seguintes propriedades.

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| nome de tabela | Nome da tabela na instância do banco de dados do Azure tabela vinculado serviço se refere. | Sim. Quando um nome de tabela é especificado sem um azureTableSourceQuery, todos os registros da tabela são copiados para o destino. Se um azureTableSourceQuery também é especificado, os registros da tabela que satisfaz a consulta são copiados para o destino. |

### <a name="schema-by-data-factory"></a>Esquema pela fábrica de dados
Para armazenamentos de dados sem esquema como tabela do Azure, o serviço de dados fábrica infere o esquema de uma das seguintes maneiras:

1.  Se você especificar a estrutura dos dados usando a propriedade de **estrutura** na definição do dataset, o serviço de dados fábrica honra essa estrutura como o esquema. Nesse caso, se uma linha não contiver um valor de uma coluna, um valor nulo é fornecido para ele.
2. Se você não especificar a estrutura dos dados usando a propriedade de **estrutura** na definição do dataset, Data Factory infere o esquema usando a primeira linha dos dados. Nesse caso, se a primeira linha não contiver o esquema completo, algumas colunas são perdidas no resultado da operação de cópia.

Portanto, para fontes de dados sem esquema, a prática recomendada é especificar a estrutura dos dados usando a propriedade de **estrutura** .

## <a name="azure-table-copy-activity-type-properties"></a>Propriedades de tipo de atividade de cópia de tabela Azure

Para obter uma lista completa das seções & propriedades disponíveis para a definição de atividades, consulte o artigo [Criar canais](data-factory-create-pipelines.md) . Propriedades como nome, descrição, entrada e saída conjuntos de dados e políticas estão disponíveis para todos os tipos de atividades. 

Por outro lado, propriedades disponíveis na seção typeProperties da atividade variam com cada tipo de atividade. Atividade de cópia, eles variam dependendo dos tipos de fontes e receptores.

**AzureTableSource** compatível com as seguintes propriedades typeProperties seção:

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | -------- 
azureTableSourceQuery | Use a consulta personalizada para ler dados. | Cadeia de caracteres de consulta de tabela do Microsoft Azure. Veja exemplos da próxima seção. | Não. Quando um nome de tabela é especificado sem um azureTableSourceQuery, todos os registros da tabela são copiados para o destino. Se um azureTableSourceQuery também é especificado, os registros da tabela que satisfaz a consulta são copiados para o destino.
azureTableSourceIgnoreTableNotFound | Indica se assimilar a exceção da tabela não existe. | TRUE<br/>FALSO | Não |

### <a name="azuretablesourcequery-examples"></a>exemplos de azureTableSourceQuery

Se a coluna de tabela do Azure é do tipo de cadeia de caracteres: 

    azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"

Se a coluna de tabela do Azure for do tipo datetime: 

    "azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"


**AzureTableSink** compatível com as seguintes propriedades typeProperties seção:


Propriedade | Descrição | Valores permitidos | Necessário  
-------- | ----------- | -------------- | -------- 
azureTableDefaultPartitionKeyValue | Partição chave valor padrão que pode ser usado o receptor de. | Um valor de cadeia de caracteres. | Não 
azureTablePartitionKeyName | Especifique o nome da coluna cujos valores são usados como chaves de partição. Se não especificado, AzureTableDefaultPartitionKeyValue será usado como a chave de partição. | Um nome de coluna. | Não |
azureTableRowKeyName | Especifique o nome da coluna cujos valores de coluna são usados como chave de linha. Se não especificado, use um GUID para cada linha. | Um nome de coluna. | Não  
azureTableInsertType | O modo de inserir dados em tabela do Microsoft Azure.<br/><br/>Essa propriedade controla se linhas existentes na tabela de saída com chaves de partição e linha correspondentes tem seus valores substituídos ou mesclados. <br/><br/>Para saber mais sobre como funcionam essas configurações (direta e substituir), consulte os tópicos de [Inserir ou entidade de mesclagem](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [Inserir ou substituir entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx) . <br/><br> Esta configuração se aplica no nível de linha, não no nível de tabela, e nenhuma opção exclui linhas na tabela de saída que não existem na entrada. | Mesclar (padrão)<br/>Substituir | Não 
writeBatchSize | Insere dados na tabela Azure quando a writeBatchSize ou writeBatchTimeout é alcançado. | Número inteiro (número de linhas)| Não (padrão: 10000) 
writeBatchTimeout | Insere dados na tabela Azure quando a writeBatchSize ou writeBatchTimeout é alcançado | período de tempo<br/><br/>Exemplo: "00:20:00" (20 minutos) | Não (o padrão para o tempo limite padrão de cliente de armazenamento valor 90 sec)

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Mapear uma coluna de origem para uma coluna de destino usando o tradutor propriedade JSON antes de poder usar a coluna de destino como o azureTablePartitionKeyName.

No exemplo a seguir, a coluna de origem DivisionID é mapeada para a coluna de destino: DivisionID.  

    "translator": {
        "type": "TabularTranslator",
        "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
    } 

O DivisionID é especificado como a chave da partição. 

    "sink": {
        "type": "AzureTableSink",
        "azureTablePartitionKeyName": "DivisionID",
        "writeBatchSize": 100,
        "writeBatchTimeout": "01:00:00"
    }


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-azure-table"></a>Digite o mapeamento para a tabela do Microsoft Azure

Conforme mencionado no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , atividade de cópia executa conversões de tipo automática de tipos de fonte para coletar tipos com a abordagem de duas etapas a seguir.

1. Converter de tipos de fonte nativo para tipo .NET
2. Converter do tipo .NET para o tipo de receptor nativo

Ao mover dados para & de tabela do Azure, os seguintes [mapeamentos definidas pelo serviço de tabela do Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) são usados de tipos de OData de tabela do Azure para o tipo de .NET e vice-versa. 

| Tipo de dados OData | Tipo .NET | Detalhes |
| --------------- | --------- | ------- |
| Edm.Binary | byte] | Uma matriz de bytes até 64 KB. |
| Edm.Boolean | bool | Um valor booliano. |
| Edm.DateTime | DateTime | Um valor de 64 bits expresso como tempo Universal Coordenado (UTC). O intervalo de DateTime suportados começando com meia-noite, 1 de janeiro de 1601 A.D. (C.E.), UTC. O intervalo termina em 31 de dezembro de 9999. |
| Edm.Double | duplo | Um valor de ponto flutuante de 64 bits. |
| Edm.Guid | GUID | Um identificador exclusivo de 128 bits. |
| Edm.Int32 | Int32 ou int | Um inteiro de 32 bits. |
| Edm.Int64 | Int64 ou longo | Um inteiro de 64 bits. |
| Edm.String | Cadeia de caracteres | Um valor codificado em UTF-16. Valores de cadeia de caracteres podem ser até 64 KB. |

### <a name="type-conversion-sample"></a>Exemplo de conversão de tipo

O exemplo a seguir é para copiar dados de um Blob do Azure para tabela do Azure com conversões de tipo. 

Suponha que o conjunto de dados Blob está no formato CSV e contém três colunas. Uma delas é uma coluna de datetime com um formato de datetime personalizadas usando nomes abreviados francês para o dia da semana. 

Defina o conjunto de dados de origem de Blob da seguinte maneira, juntamente com as definições de tipo para as colunas.
    
    {
        "name": " AzureBlobInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability":
            {
                "frequency": "Hour",
                "interval": 1,
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

Considerando o mapeamento do tipo de tipo de OData de tabela do Azure para o tipo de .NET, você deve definir a tabela na tabela do Azure com o esquema a seguir. 

**Azure esquema de tabela:**

Nome da coluna | Tipo
----------- | --------
ID de usuário | Edm.Int64
nome | Edm.String 
lastlogindate | Edm.DateTime

Em seguida, defina o conjunto de dados de tabela do Azure da seguinte maneira. Não é necessário especificar seção de "estrutura" com as informações de tipo, desde que o tipo de informação já está especificado no armazenamento de dados subjacente.

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

Nesse caso, Data Factory automaticamente conversões de tipo incluindo o campo Datetime com o formato datetime personalizadas usando a cultura "fr-fr" ao mover dados do Blob para tabela do Azure.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Desempenho e ajuste  
Para saber mais sobre principais fatores que desempenho de impacto de movimentação de dados (cópia atividade) em fábrica de dados do Azure e várias maneiras de otimizá-lo, consulte [guia de ajuste e desempenho de atividade de cópia](data-factory-copy-activity-performance.md).







