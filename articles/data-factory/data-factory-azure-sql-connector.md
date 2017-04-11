<properties 
    pageTitle="Mover dados ao/do Azure SQL Database | Microsoft Azure" 
    description="Saiba como mover dados de/para o banco de dados do SQL Azure usando fábrica de dados do Azure." 
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
    ms.date="09/20/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Mover dados para e do banco de dados do SQL Azure usando fábrica de dados do Azure
Este artigo descreve como você pode usar a atividade de cópia em uma fábrica de dados do Azure para mover dados de/para o banco de dados do Azure SQL de/para outro armazenamento de dados. Este artigo se baseia o artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com atividade de cópia e combinações de repositório de dados com suporte. 

## <a name="supported-sources-and-sinks"></a>Receptores e fontes compatíveis
Consulte a tabela de [armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para uma lista de fontes de dados compatíveis como fontes ou receptores com a atividade de cópia. Você pode mover dados de qualquer armazenamento de dados de origem com suporte para o banco de dados do Azure SQL ou de banco de dados do SQL Azure para qualquer armazenamento de dados com suporte receptor.

## <a name="create-pipeline"></a>Crie pipeline
Você pode criar um pipeline com uma atividade de cópia que move dados de/para um banco de dados do SQL Azure usando ferramentas diferentes/APIs.  

- Assistente de cópia
- Portal do Azure
- O Visual Studio
- PowerShell Azure
- API .NET
- API REST

Consulte o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para instruções passo a passo para criar um pipeline com uma atividade de cópia de maneiras diferentes.   

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil para criar um pipeline que copia dados de/para o banco de dados do SQL Azure é usar o Assistente de dados de cópia. Consulte [Tutorial: criar um pipeline usando o Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para um rápida passo a passo sobre como criar um pipeline usando o Assistente de dados de cópia. 

Os exemplos a seguir fornecem definições de JSON de exemplo que você pode usar para criar um pipeline usando o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados para e do banco de dados do SQL Azure e armazenamento de Blob do Azure. No entanto, dados podem ser copiados **diretamente** de qualquer uma das fontes a qualquer uma dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.

## <a name="sample-copy-data-from-azure-sql-database-to-azure-blob"></a>Exemplo: Copiar dados de banco de dados do Azure SQL Azure Blob

A mesma define as entidades de fábrica de dados a seguir:

1. Um serviço vinculado do tipo [AzureSqlDatabase](#azure-sql-linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureSqlTable](#azure-sql-dataset-type-properties). 
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [SqlSource](#azure-sql-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de série temporal (por hora, diariamente, etc) de uma tabela no banco de dados do SQL Azure para um blob cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras.  

**Serviço de vinculadas SQL Azure**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

Consulte a seção de [Serviço vinculada do SQL Azure](#azure-sql-linked-service-properties) para a lista de propriedades suportadas por esse serviço vinculado. 

**Serviço de armazenamento vinculado do Azure Blob**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Consulte o artigo do [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) para a lista de propriedades suportadas por esse serviço vinculado. 

**Dataset de entrada de SQL Azure**

O exemplo supõe que você criou uma tabela "MinhaTabela" no SQL Azure e contém uma coluna chamada "timestampcolumn" para os dados da série de tempo. 

Configuração "externo": "true" informa o serviço de fábrica de dados do Azure que o conjunto de dados externo à fábrica dados e não é produzido por uma atividade na fábrica dados.

    {
      "name": "AzureSqlInput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
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

Consulte a seção de [Propriedades de tipo de conjunto de dados do SQL Azure](#azure-sql-dataset-type-properties) para a lista de propriedades suportadas por esse tipo de conjunto de dados.  

**Conjunto de dados de saída do Azure Blob**

Dados são gravados em um novo blob cada hora (frequência: horas, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa ano, mês, dia e partes de horas de início. 

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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

Consulte a seção de [Propriedades de tipo de conjunto de dados do Azure Blob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) para a lista de propriedades suportadas por esse tipo de conjunto de dados.  

**Pipeline com atividade de cópia**

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendado para ser executado a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **SqlSource** e **receptor** tipo está definido como **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora para copiar.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureSQLInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

No exemplo, **sqlReaderQuery** é especificado para o SqlSource. A atividade de cópia executa essa consulta a fonte de banco de dados do Azure SQL para obter os dados. Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado use parâmetros).

Se você não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na seção estrutura do dataset JSON são usadas para criar uma consulta para executar no banco de dados do SQL Azure. Por exemplo: `select column1, column2 from mytable`. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela. 


Consulte a seção de [Fonte Sql](#sqlsource) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) para a lista de propriedades com suporte no SqlSource e BlobSink. 


## <a name="sample-copy-data-from-azure-blob-to-azure-sql-database"></a>Exemplo: Copiar dados do Azure Blob para banco de dados do SQL Azure

O exemplo define as entidades de fábrica de dados a seguir:  

1.  Um serviço vinculado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2.  Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4.  Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties).

As cópias de amostra série temporal dados (por hora, diariamente, etc) do Azure blob a uma tabela no SQL Azure cada hora de banco de dados. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras. 


**Serviço de vinculadas SQL Azure**
    
    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

Consulte a seção de [Serviço vinculada do SQL Azure](#azure-sql-linked-service-properties) para a lista de propriedades suportadas por esse serviço vinculado. 

**Serviço de armazenamento vinculado do Azure Blob**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Consulte o artigo do [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) para a lista de propriedades suportadas por esse serviço vinculado.

**Dataset de entrada Azure Blob**

Dados são obtidos de um novo blob cada hora (frequência: horas, intervalo: 1). O nome de caminho e do arquivo de pasta para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa ano, mês e dia parte da hora de início e nome do arquivo usa a parte de hora de início. "externo": "true" configuração informa o serviço de dados fábrica que essa tabela é externa à fábrica dados e não é produzida por uma atividade na fábrica dados.

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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

Consulte a seção de [Propriedades de tipo de conjunto de dados do Azure Blob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) para a lista de propriedades suportadas por esse tipo de conjunto de dados.

**Conjunto de dados de saída SQL Azure**

O exemplo copia dados a uma tabela chamada "Minha tabela" no SQL Azure. Crie a tabela no SQL Azure com o mesmo número de colunas, como você espera arquivo CSV de Blob para conter. Novas linhas são adicionadas à tabela de cada hora. 

    {
      "name": "AzureSqlOutput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

Consulte a seção de [Propriedades de tipo de conjunto de dados do SQL Azure](#azure-sql-dataset-type-properties) para a lista de propriedades suportadas por esse tipo de conjunto de dados.

**Pipeline com atividade de cópia**

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendado para ser executado a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **BlobSource** e **receptor** tipo está definido como **SqlSink**.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource",
                "blobColumnSeparators": ","
              },
              "sink": {
                "type": "SqlSink"
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

Consulte a seção de [Receptor de Sql](#sqlsink) e [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) para a lista de propriedades com suporte no SqlSink e BlobSource. 


## <a name="azure-sql-linked-service-properties"></a>Propriedades de serviço de vinculadas SQL Azure
Os exemplos, você usou um serviço vinculado do tipo **AzureSqlDatabase** para vincular a um banco de dados do SQL Azure a uma fábrica de dados. A tabela a seguir fornece descrição para elementos JSON específicas para o serviço SQL Azure vinculado.

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| tipo | A propriedade type deve ser definida: **AzureSqlDatabase** | Sim |
| connectionString | Especifica as informações necessárias para se conectar à instância do banco de dados do Azure SQL para a propriedade connectionString. | Sim |

> [AZURE.NOTE] Configure o [Firewall de banco de dados do SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) o servidor de banco de dados para [Permitir que os serviços do Azure acessar o servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Além disso, se você está copiando dados banco de dados do Azure SQL incluindo Azure fora de fontes de dados locais com o gateway de fábrica de dados, configure intervalo de endereço IP apropriado para o computador que está enviando dados para o banco de dados do Azure SQL. 

## <a name="azure-sql-dataset-type-properties"></a>Propriedades de tipo de conjunto de dados SQL Azure
Os exemplos, você usou um dataset do tipo **AzureSqlTable** para representar uma tabela em um banco de dados do SQL Azure. 

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo de [conjuntos de dados criando](data-factory-create-datasets.md) . Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOB do Microsoft Azure, tabela do Microsoft Azure, etc.). 

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção de **typeProperties** para o conjunto de dados do tipo **AzureSqlTable** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| nome de tabela | Nome da tabela na instância do Azure SQL Database vinculado serviço se refere. | Sim |

## <a name="azure-sql-copy-activity-type-properties"></a>Propriedades do tipo de atividade da cópia do Azure SQL
Para obter uma lista completa das seções & propriedades disponíveis para a definição de atividades, consulte o artigo [Criar canais](data-factory-create-pipelines.md) . Propriedades como nome, descrição, entrada e saída tabelas e política estão disponíveis para todos os tipos de atividades.

> [AZURE.NOTE] A atividade de cópia leva apenas uma entrada e produz somente uma saída.

Por outro lado, propriedades disponíveis na seção **typeProperties** da atividade variam com cada tipo de atividade. Atividade de cópia, eles variam dependendo dos tipos de fontes e receptores. 

Se você estiver movendo dados de um banco de dados do SQL Azure, você definir o tipo de fonte a atividade de cópia para **SqlSource**. Da mesma forma, se você estiver movendo dados para um banco de dados do SQL Azure, você definir o tipo de receptor a atividade de cópia para **SqlSink**. Esta seção fornece uma lista de propriedades com suporte no SqlSource e SqlSink. 

### <a name="sqlsource"></a>SqlSource

Atividade de cópia, quando a origem for do tipo **SqlSource**, as seguintes propriedades estão disponíveis na seção de **typeProperties** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Use a consulta personalizada para ler dados. | Cadeia de caracteres de consulta SQL. Exemplo: `select * from MyTable`.  | Não |
| sqlReaderStoredProcedureName | Nome do procedimento armazenado que lê os dados da tabela de origem. | Nome do procedimento armazenado. | Não |
| storedProcedureParameters | Parâmetros para o procedimento armazenado. | Pares de nome/valor. Nomes e capitalização de parâmetros devem corresponder os nomes e a capitalização dos parâmetros de procedimento armazenado. | Não |

Se a **sqlReaderQuery** for especificado para o SqlSource, a atividade de cópia executa essa consulta a fonte de banco de dados do Azure SQL para obter os dados. Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado use parâmetros). 

Se você não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na seção estrutura do dataset JSON são usadas para criar uma consulta (`select column1, column2 from mytable`) para executar no banco de dados do SQL Azure. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela. 

> [AZURE.NOTE] Quando você usa **sqlReaderStoredProcedureName**, ainda é necessário especificar um valor para a propriedade de **nome de tabela** no dataset JSON. Não há nenhum validações executadas contra esta tabela apesar. 

### <a name="sqlsource-example"></a>Exemplo de SqlSource

    "source": {
        "type": "SqlSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**A definição do procedimento armazenado:** 

    CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
    (
        @stringData varchar(20),
        @id int
    )
    AS
    SET NOCOUNT ON;
    BEGIN
         select *
         from dbo.UnitTestSrcTable
         where dbo.UnitTestSrcTable.stringData != stringData
        and dbo.UnitTestSrcTable.id != id
    END
    GO


### <a name="sqlsink"></a>SqlSink 

**SqlSink** compatível com as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- |
| writeBatchTimeout | Tempo de espera para a operação de inserção do lote seja concluída antes que ela expire. | período de tempo<br/><br/> Exemplo: "00: 30:00" (30 minutos). | Não | 
| writeBatchSize | Insere dados na tabela SQL quando o tamanho de buffer atinge writeBatchSize. | Número inteiro (número de linhas)| Não (padrão: 10000)
| sqlWriterCleanupScript | Especifica uma consulta para atividade de cópia executar, de forma que os dados de uma fatia específica é limpos. Para obter mais informações, consulte a [seção de repetição](#repeatability-during-copy). | Uma instrução de consulta.  | Não |
| sliceIdentifierColumnName | Especifique um nome de coluna para atividade de cópia preencher com identificador de fatia gerado automaticamente, que é usado para limpar dados de uma fatia específico quando executar novamente. Para obter mais informações, consulte a [seção de repetição](#repeatability-during-copy). | Nome da coluna de uma coluna com tipo de dados de binary(32). | Não |
| sqlWriterStoredProcedureName | Nome do procedimento armazenado dados upserts (atualizações/inserções) na tabela de destino. | Nome do procedimento armazenado. | Não |
| storedProcedureParameters | Parâmetros para o procedimento armazenado. | Pares de nome/valor. Nomes e capitalização de parâmetros devem corresponder os nomes e a capitalização dos parâmetros de procedimento armazenado. | Não | 
| sqlWriterTableType | Especifique um nome de tipo de tabela para ser usado no procedimento armazenado. Atividade de cópia disponibiliza dados movidos em uma tabela temporária com esse tipo de tabela. Código do procedimento armazenado pode mesclar os dados sejam copiados com dados existentes. | Um nome de tipo de tabela. | Não |

#### <a name="sqlsink-example"></a>Exemplo de SqlSink

    "sink": {
        "type": "SqlSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00",
        "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
        "sqlWriterTableType": "CopyTestTableType",
        "storedProcedureParameters": {
            "id": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" },
            "decimalData": { "value": "1", "type": "Decimal" }
        }
    }

## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade do banco de dados de destino
Esta seção fornece um exemplo para copiar dados de uma tabela de origem sem uma coluna de identidade para uma tabela de destino com uma coluna de identidade. 

**Tabela de origem:** 

    create table dbo.SourceTbl
    (
           name varchar(100),
           age int
    )

**Tabela de destino:**

    create table dbo.TargetTbl
    (
           id int identity(1,1),
           name varchar(100),
           age int
    )


Observe que a tabela de destino tem uma coluna de identidade. 

**Definição de JSON de conjunto de dados de origem**

    {
        "name": "SampleSource",
        "properties": {
            "type": " SqlServerTable",
            "linkedServiceName": "TestIdentitySQL",
            "typeProperties": {
                "tableName": "SourceTbl"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }

**Definição de JSON do dataset de destino**

    {
        "name": "SampleTarget",
        "properties": {
            "structure": [
                { "name": "name" },
                { "name": "age" }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "TestIdentitySQLSource",
            "typeProperties": {
                "tableName": "TargetTbl"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": false,
            "policy": {}
        }   
    }


Observe que, como sua tabela de origem e destino tem esquema diferente (destino tem uma coluna adicional com identidade). Neste cenário, você precisa especificar propriedade **estrutura** na definição de conjunto de dados de destino, que não inclui a coluna de identidade. 

Em seguida, você mapear colunas de conjunto de dados de origem para colunas no dataset de destino. Consulte a seção de [amostras de mapeamento de coluna](#column-mapping-samples) para um exemplo. 

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)] 

[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]
 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-sql-server--azure-sql-database"></a>Digite o mapeamento para SQL Server e o banco de dados do SQL Azure

Conforme mencionado na atividade de cópia do artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) executa conversões de tipo automática de tipos de fonte para coletar tipos com a abordagem de 2 etapas a seguir:

1. Converter de tipos de fonte nativo para tipo .NET
2. Converter do tipo .NET para o tipo de receptor nativo

Ao mover dados para & do SQL Azure, SQL server, Sybase os seguintes mapeamentos são usados de tipo SQL para tipo .NET e vice-versa.

O mapeamento é igual o mapeamento de tipo de dados do SQL Server para ADO.NET.

| Tipo de mecanismo de banco de dados do SQL Server | Tipo .NET framework |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binário | Byte] |
| bit | Booliano |
| CARACT | Cadeia de caracteres, [] CARACT |
| Data | DateTime |
| DateTime | DateTime |
| datetime2 | DateTime |
| DateTimeOffset | DateTimeOffset |
| Decimal | Decimal |
| Atributo FILESTREAM (varbinary(max)) | Byte] |
| Sobrepor | Duplo |
| imagem | Byte] | 
| int | Int32 | 
| dinheiro | Decimal |
| nchar | Cadeia de caracteres, [] CARACT |
| ntext | Cadeia de caracteres, [] CARACT |
| numérico | Decimal |
| nvarchar | Cadeia de caracteres, [] CARACT |
| real | Único |
| rowversion | Byte] |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | Decimal | 
| sql_variant | Objeto * |
| texto | Cadeia de caracteres, [] CARACT |
| tempo | Período de tempo |
| carimbo de hora | Byte] |
| tinyint | Byte |
| Identificador exclusivo | GUID |
| varbinary |  Byte] |
| varchar | Cadeia de caracteres, [] CARACT |
| XML | XML |


[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Desempenho e ajuste  
Consulte o [guia de ajuste e desempenho de atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre principais fatores que desempenho de impacto de movimentação de dados (cópia atividade) em fábrica de dados do Azure e várias maneiras de otimizá-lo.