<properties 
    pageTitle="Mover dados para/de DocumentDB | Microsoft Azure" 
    description="Saiba como mover dados para/da coleção DocumentDB do Azure usando fábrica de dados do Azure" 
    services="data-factory, documentdb" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="multiple" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-documentdb-using-azure-data-factory"></a>Mover de dados e DocumentDB usando fábrica de dados do Azure

Este artigo descreve como você pode usar a atividade de cópia em uma fábrica de dados do Azure para mover dados do Azure DocumentDB dos dados de outra armazenam e mover dados do DocumentDB para outro armazenamento de dados. Este artigo se baseia o artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com atividade de cópia e combinações de repositório de dados com suporte.

Os exemplos a seguir mostram como copiar dados de e DocumentDB do Azure e armazenamento de Blob do Azure. No entanto, dados podem ser copiados **diretamente** de qualquer uma das fontes a qualquer uma dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.  

> [AZURE.NOTE] Copiando dados de dados de IaaS no local/Azure armazena ao Azure DocumentDB e vice-versa são compatíveis com o Gateway de gerenciamento de dados versão 2.1 e acima.

## <a name="sample-copy-data-from-documentdb-to-azure-blob"></a>Exemplo: Copiar dados de DocumentDB ao Azure Blob

O exemplo abaixo mostra:

1. Um serviço vinculado do tipo [DocumentDb](#azure-documentdb-linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [DocumentDbCollectionSource](#azure-documentdb-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados no Azure DocumentDB ao Azure Blob. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras.

**Serviço de DocumentDB vinculado Azure:**

    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Armazenamento de Blob do Azure vinculado serviço:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure dataset de entrada de banco de dados do documento:**

O exemplo supõe que você tem um conjunto nomeado **pessoa** em um banco de dados do Azure DocumentDB.
 
Configuração "externo": "true" e especificando externalData política informações no serviço de fábrica de dados do Azure que a tabela é externa à fábrica dados e não produzidos por uma atividade na fábrica dados.

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }


**Azure Blob dataset de saída:**

Dados são copiados para um novo blob cada hora com o caminho para o blob refletindo datetime específico com detalhamento de hora.

    {
      "name": "PersonBlobTableOut",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

Documento JSON de amostra no conjunto de pessoa em um banco de dados de DocumentDB: 

    {
      "PersonId": 2,
      "Name": {
        "First": "Jane",
        "Middle": "",
        "Last": "Doe"
      }
    }

DocumentDB suporta consultar documentos usando um SQL como sintaxe sobre os documentos JSON hierárquicos. 

Exemplo: Selecione Person.Name.Middle Person.PersonId, Person.Name.First como nome, como MiddleName Person.Name.Last como LastName de pessoa

O pipeline de seguir copia dados da coleção pessoa no banco de dados DocumentDB para um blob do Microsoft Azure. Como parte da atividade de cópia a entrada e saída conjuntos de dados foram especificados.  
    
    {
      "name": "DocDbToBlobPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                "nestingSeparator": "."
              },
              "sink": {
                "type": "BlobSink",
                "blobWriterAddHeader": true,
                "writeBatchSize": 1000,
                "writeBatchTimeout": "00:00:59"
              }
            },
            "inputs": [
              {
                "name": "PersonDocumentDbTable"
              }
            ],
            "outputs": [
              {
                "name": "PersonBlobTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromDocDbToBlob"
          }
        ],
        "start": "2015-04-01T00:00:00Z",
        "end": "2015-04-02T00:00:00Z"
      }
    }

## <a name="sample-copy-data-from-azure-blob-to-azure-documentdb"></a>Exemplo: Copiar dados do Azure Blob para DocumentDB do Azure

O exemplo abaixo mostra:

1. Um serviço vinculado do tipo [DocumentDb](#azure-documentdb-linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).


O exemplo copia dados do Azure blob para DocumentDB do Azure. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras.

**Armazenamento de Blob do Azure vinculado serviço:**
    
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Serviço de DocumentDB vinculado Azure:**
    
    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Azure Blob entrado conjunto de dados:**

    {
      "name": "PersonBlobTableIn",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "MiddleName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "fileName": "input.csv",
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**DocumentDB Azure dataset de saída:**

O exemplo copia dados para um conjunto nomeado "Pessoa".

    {
      "name": "PersonDocumentDbTableOut",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "Name.First",
            "type": "String"
          },
          {
            "name": "Name.Middle",
            "type": "String"
          },
          {
            "name": "Name.Last",
            "type": "String"
          }
        ],
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

O pipeline de seguir copia dados do Azure Blob à coleção pessoa na DocumentDB. Como parte da atividade de cópia a entrada e saída conjuntos de dados foram especificados. 
    
    {
      "name": "BlobToDocDbPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "DocumentDbCollectionSink",
                "nestingSeparator": ".",
                "writeBatchSize": 2,
                "writeBatchTimeout": "00:00:00"
              }
              "translator": {
                  "type": "TabularTranslator",
                  "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
              }
            },
            "inputs": [
              {
                "name": "PersonBlobTableIn"
              }
            ],
            "outputs": [
              {
                "name": "PersonDocumentDbTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromBlobToDocDb"
          }
        ],
        "start": "2015-04-14T00:00:00Z",
        "end": "2015-04-15T00:00:00Z"
      }
    }
 
Se a entrada de blob de amostra é como 

    1,John,,Doe

Em seguida, a saída JSON em DocumentDB será como:

    {
      "Id": 1,
      "Name": {
        "First": "John",
        "Middle": null,
        "Last": "Doe"
      },
      "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
    }
    
DocumentDB é um repositório de NoSQL para documentos JSON, onde estruturas aninhadas são permitidas. Azure fábrica de dados permite que o usuário indicar hierarquia via **nestingSeparator**, que é "." Neste exemplo. Com o separador, a atividade de cópia gerará o objeto "Nome" com elementos de três filhos primeiro, meio e o último, de acordo com "Name.First", "Name.Middle" e ". Sobrenome" na definição da tabela.

## <a name="azure-documentdb-linked-service-properties"></a>Propriedades de serviço vinculada DocumentDB Azure

A tabela a seguir fornece descrição para elementos JSON específicos ao serviço do Azure DocumentDB vinculado. 

| **Propriedade** | **Descrição** | **Necessário** |
| -------- | ----------- | --------- |
| tipo | A propriedade type deve ser definida: **DocumentDb** | Sim |
| connectionString | Especifica as informações necessárias para se conectar ao banco de dados do Azure DocumentDB. | Sim |

## <a name="azure-documentdb-dataset-type-properties"></a>Propriedades de tipo de DocumentDB Dataset Azure

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados Consulte o artigo de [conjuntos de dados criando](data-factory-create-datasets.md) . Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOB do Microsoft Azure, tabela do Microsoft Azure, etc.).
 
A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção de typeProperties para o conjunto de dados do tipo **DocumentDbCollection** tem as seguintes propriedades.

| **Propriedade** | **Descrição** | **Necessário** |
| -------- | ----------- | -------- |
| NomeDaColeta | Nome da coleção de documento DocumentDB. | Sim |


Exemplo:

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

### <a name="schema-by-data-factory"></a>Esquema pela fábrica de dados
Para armazenamentos de dados sem esquema como DocumentDB, o serviço de dados fábrica infere o esquema de uma das seguintes maneiras:  

1.  Se você especificar a estrutura dos dados usando a propriedade de **estrutura** na definição do dataset, o serviço de dados fábrica honra essa estrutura como o esquema. Nesse caso, se uma linha não contiver um valor de uma coluna, um valor nulo será fornecido para ele.
2.  Se você não especificar a estrutura dos dados usando a propriedade de **estrutura** na definição do dataset, o serviço de dados fábrica infere o esquema usando a primeira linha dos dados. Nesse caso, se a primeira linha não contiver o esquema completo, algumas colunas será ausentes no resultado da operação de cópia.

Portanto, para fontes de dados sem esquema, a prática recomendada é especificar a estrutura dos dados usando a propriedade de **estrutura** .

## <a name="azure-documentdb-copy-activity-type-properties"></a>Propriedades de tipo de atividade de cópia DocumentDB Azure

Para obter uma lista completa das seções & propriedades disponíveis para a definição de atividades consulte o artigo [Criar canais](data-factory-create-pipelines.md) . Propriedades como nome, descrição, entrada e saída tabelas e política estão disponíveis para todos os tipos de atividades.
 
**Observação:** A atividade de cópia leva apenas uma entrada e produz somente uma saída.

Propriedades disponíveis na seção typeProperties da atividade por outro lado variam com cada tipo de atividade e no caso de atividade de cópia variar dependendo dos tipos de fontes e receptores.

No caso de atividade de cópia quando origem for do tipo **DocumentDbCollectionSource** as seguintes propriedades estão disponíveis na seção de **typeProperties** :

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| ------------ | --------------- | ------------------ | ------------ |
| consulta | Especifique a consulta para ler dados. | Compatível com DocumentDB de cadeia de caracteres de consulta. <br/><br/>Exemplo:`SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` | Não <br/><br/>Se não especificado, a instrução SQL que é executada:`select <columns defined in structure> from mycollection` 
| nestingSeparator | Caractere especial para indicar que o documento estiver aninhado | Qualquer caractere. <br/><br/>DocumentDB é um repositório de NoSQL para documentos JSON, onde estruturas aninhadas são permitidas. Azure fábrica de dados permite que o usuário indicar hierarquia via nestingSeparator, que é "." nos exemplos acima. Com o separador, a atividade de cópia gerará o objeto "Nome" com elementos de três filhos primeiro, meio e o último, de acordo com "Name.First", "Name.Middle" e ". Sobrenome" na definição da tabela. | Não

**DocumentDbCollectionSink** compatível com as seguintes propriedades:

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | Um caractere especial no nome da coluna de origem para indicar que esse documento aninhado é necessária. <br/><br/>Por exemplo acima: `Name.First` na saída tabela produz a seguinte estrutura JSON no documento DocumentDB:<br/><br/>"Nome": {<br/>  "Primeiro": "João"<br/>}, | Caractere que é usado para separar níveis de aninhamento.<br/><br/>Valor padrão é `.` (ponto). | Caractere que é usado para separar níveis de aninhamento. <br/><br/>Valor padrão é `.` (ponto). | Não | 
| writeBatchSize | Número de solicitações paralelas DocumentDB serviço para criar documentos.<br/><br/>Você pode ajustar o desempenho ao copiar dados de/para DocumentDB usando essa propriedade. Você pode esperar um melhor desempenho ao aumentar writeBatchSize porque mais solicitações paralelas para DocumentDB são enviadas. No entanto você precisará evitar a limitação que pode lançar a mensagem de erro: "Solicitar a taxa for grande".<br/><br/>A limitação é decidida por um número de fatores, incluindo o tamanho dos documentos, número de termos nos documentos, indexação política de conjunto de destino, etc. Para operações de cópia, você pode usar um conjunto de melhor (por exemplo, S3) tenham produtividade mais disponível (2.500 solicitação unidades/segundo). | Número inteiro | Não (padrão: 10000) |
| writeBatchTimeout | Tempo de espera para a operação a ser concluída antes que ela expire. | período de tempo<br/><br/> Exemplo: "00: 30:00" (30 minutos). | Não |
 
## <a name="appendix"></a>Apêndice
1. **Pergunta:**  
    faz a atualização de suporte de atividade de cópia dos registros existentes?

    **Resposta:**  
    não.

2. **Pergunta:**  
    como faz uma nova tentativa de uma cópia de DocumentDB lidar com já copiou registros?

    **Resposta:**  
    se registros têm um campo de "ID" e a operação de cópia tenta inserir um registro com a mesma ID, a operação de cópia gera um erro.  
 
3. **Pergunta:** 
    Data Factory suporta [intervalo ou dados baseada em hash partição](https://azure.microsoft.com/documentation/articles/documentdb-partition-data/)? 

    **Resposta:** 
    não. 
4. **Pergunta:** 
    posso especificar mais de um conjunto de DocumentDB para uma tabela?
    
    **Resposta:** 
    não. Somente uma coleção pode ser especificada neste momento.
     
## <a name="performance-and-tuning"></a>Desempenho e ajuste  
Consulte o [guia de ajuste e desempenho de atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre principais fatores que desempenho de impacto de movimentação de dados (cópia atividade) em fábrica de dados do Azure e várias maneiras de otimizá-lo.
