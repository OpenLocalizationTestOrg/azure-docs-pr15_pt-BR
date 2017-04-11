<properties 
    pageTitle="Mover dados do serviço de armazenamento simples Amazon usando dados Factory | Microsoft Azure" 
    description="Saiba mais sobre como mover dados do Amazon Simple Storage Service (S3) usando fábrica de dados do Azure." 
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
    ms.date="10/24/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Mover dados do Amazon Simple Storage Service usando fábrica de dados do Azure

Este artigo descreve como você pode usar a atividade de cópia em uma fábrica de dados do Azure para mover dados do serviço de armazenamento simples (S3) Amazon para outro dados armazenar. Este artigo se baseia o artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) que apresenta uma visão geral de movimentação de dados e uma lista de armazenamentos de dados de origem/receptor compatível com atividade de cópia.  

Fábrica de dados atualmente suporta apenas movendo os dados da Amazon S3 outros armazenamentos de dados, mas não para mover dados de outras fontes de dados para o Amazon S3.

## <a name="required-permissions"></a>Permissões necessárias

Para copiar dados de Amazon S3, verifique se que você possui a concessão as permissões abaixo:

- **S3:GetObject** e **s3:GetObjectVersion** para operações de objeto do Amazon S3
- **S3:ListBucket** e **s3:ListAllMyBuckets** (usado no Assistente de cópia somente) para Amazon S3 Balde de operações 

Você pode encontrar a lista completa de permissões Amazon S3 com detalhes de [Especificando permissões em uma política](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil para criar um pipeline que copia dados da Amazon S3 é usar o Assistente de dados de cópia. Consulte [Tutorial: criar um pipeline usando o Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para um rápida passo a passo sobre como criar um pipeline usando o Assistente de dados de cópia. 

O exemplo a seguir fornece definições de JSON de exemplo que você pode usar para criar um pipeline usando o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ele mostra como copiar dados de Amazon S3 ao armazenamento de Blob do Azure. No entanto, os dados podem ser copiados para qualquer um dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores).

## <a name="sample-copy-data-from-amazon-s3-to-azure-blob"></a>Exemplo: Copiar dados de Amazon S3 ao Azure Blob
Este exemplo mostra como copiar dados de um S3 Amazon para um armazenamento de Blob do Azure. No entanto, dados podem ser copiados **diretamente** a qualquer uma dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.  
 
O exemplo tem as seguintes entidades de fábrica de dados:

- Um serviço vinculado do tipo [AwsAccessKey](#linked-service-properties).
- Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AmazonS3](#dataset-type-properties).
- Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [FileSystemSource](#copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de Amazon S3 para um Azure blob cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras. 

**Serviço de Amazon S3 vinculado**

    {
        "name": "AmazonS3LinkedService",
        "properties": {
            "type": "AwsAccessKey",
            "typeProperties": {
                "accessKeyId": "<access key id>",
                "secretAccessKey": "<secret access key>"
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

**Amazon S3 dataset de entrada**

Definindo **"externo": true** informa o serviço de fábrica de dados que o conjunto de dados externo à fábrica dados e não é produzido por uma atividade na fábrica dados. Defina esta propriedade como true em um conjunto de dados de entrada que não é produzido por uma atividade no pipeline.

    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
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
                "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendado para ser executado a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **FileSystemSource** e **receptor** tipo está definido como **BlobSink**. 
    
    {
        "name": "CopyAmazonS3ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "FileSystemSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonS3InputDataset"
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
                    "name": "AmazonS3ToBlob"
                }
            ],
            "start": "2014-08-08T18:00:00Z",
            "end": "2014-08-08T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Propriedades de serviço vinculada

A tabela a seguir fornece descrição para elementos específicos do JSON para Amazon S3 (**AwsAccessKey**) vinculados serviço.

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------- | ------- |  
| accessKeyID | ID da chave de acesso secreta. | cadeia de caracteres | Sim |
| secretAccessKey | A tecla de acesso secreta em si. | Cadeia de caracteres secreta criptografada | Sim | 


## <a name="dataset-type-properties"></a>Propriedades de tipo de conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo de [conjuntos de dados criando](data-factory-create-datasets.md) . Seções como estrutura, disponibilidade e política são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOB do Microsoft Azure, tabela do Microsoft Azure, etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção typeProperties de conjunto de dados do tipo **AmazonS3** (que inclui Amazon S3 dataset) tem as seguintes propriedades

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------- | ------ | 
| bucketName | O nome de Balde S3. | Cadeia de caracteres | Sim |
| chave | A chave de objeto S3. | Cadeia de caracteres | Não | 
| prefixo | Prefixo para a chave de objeto S3. Objetos cujas chaves começam com esse prefixo são selecionados. Se aplica somente quando a chave está vazia. | Cadeia de caracteres | Não | 
| Versão | A versão do objeto de S3 se S3 controle de versão está habilitado. | Cadeia de caracteres | Não |  
| formato | Os seguintes tipos de formato são suportados: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade de **tipo** em Formatar como um desses valores. Consulte as seções [Especificando TextFormat](#specifying-textformat), [Especificando AvroFormat](#specifying-avroformat), [Especificando JsonFormat](#specifying-jsonformat), [Especificando OrcFormat](#specifying-orcformat)e [Especificando ParquetFormat](#specifying-parquetformat) para obter detalhes. Se você quiser copiar arquivos como-está entre armazenamentos baseados em arquivos (cópia binário), você pode ignorar a seção de formato em ambas as definições de conjunto de dados de entrada e saída.| Não
| compactação | Especifica o tipo e o nível de compactação para os dados. Tipos suportados são: **GZip**, **Deflate**e **BZip2** e níveis com suporte são: **Optimal** e **mais rápido**. Atualmente, as configurações de compactação não há suporte para dados em **AvroFormat** ou **OrcFormat**. Para obter mais informações, consulte a seção de [suporte de compactação](#compression-support) .  | Não |

> [AZURE.NOTE] bucketName + tecla Especifica o local do objeto S3 onde Balde é o contêiner raiz para objetos S3 e chave é o caminho completo para o objeto S3.

### <a name="sample-dataset-with-prefix"></a>Conjunto de dados de exemplo com prefixo

    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "prefix": "testFolder/test",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }

### <a name="sample-data-set-with-version"></a>Conjunto de dados de exemplo (com versão)

    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "version": "WBeMIxQkJczm0CJajYkHf0_k6LhBmkcL",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }


### <a name="dynamic-paths-for-s3"></a>Caminhos de dinâmicos para S3

No exemplo, usamos valores fixos para propriedades de chave e bucketName no dataset Amazon S3. 

    "key": "testFolder/test.orc",
    "bucketName": "testbucket",

Você pode ter dados fábrica calcular a tecla e bucketName dinamicamente em tempo de execução usando variáveis de sistema como SliceStart.

    "key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
    "bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"

Você pode fazer o mesmo para a propriedade de prefixo de um conjunto de dados Amazon S3. Consulte [funções de fábrica de dados e variáveis de sistema](data-factory-functions-variables.md) para obter uma lista de funções suportadas e variáveis. 


[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## <a name="copy-activity-type-properties"></a>Copiar propriedades de tipo de atividade

Para obter uma lista completa das seções & propriedades disponíveis para a definição de atividades, consulte o artigo [Criar canais](data-factory-create-pipelines.md) . Propriedades como nome, descrição, entrada e saída tabelas e políticas estão disponíveis para todos os tipos de atividades. 

Por outro lado, propriedades disponíveis na seção **typeProperties** da atividade variam com cada tipo de atividade. Atividade de cópia, eles variam dependendo dos tipos de fontes e receptores.

Quando a fonte na atividade de cópia for do tipo **FileSystemSource** (que inclui Amazon S3), as seguintes propriedades estão disponíveis na seção de typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- | 
| recursiva | Especifica se a repetidamente lista S3 objetos sob o diretório. | Verdadeiro/Falso | Não | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Desempenho e ajuste  
Consulte o [guia de ajuste e desempenho de atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre principais fatores que desempenho de impacto de movimentação de dados (cópia atividade) em fábrica de dados do Azure e várias maneiras de otimizá-lo.

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos: 
- [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia. 
