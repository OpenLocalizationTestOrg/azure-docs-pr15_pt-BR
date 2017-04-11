<properties
    pageTitle="Mover dados para/Azure dados Lucerne Store | Fábrica de dados do Azure"
    description="Saiba como mover dados de/para armazenamento de Lucerne de dados do Azure usando fábrica de dados do Azure"
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
    ms.date="09/27/2016"
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-azure-data-lake-store-using-azure-data-factory"></a>Mover de dados e armazenamento de Lucerne de dados do Azure usando fábrica de dados do Azure
Este artigo descreve como você pode usar a atividade de cópia em uma fábrica de dados do Azure para mover dados de/para o armazenamento do Azure dados Lucerne de/para outro armazenamento de dados. Este artigo se baseia o artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com a atividade de cópia e as combinações de repositório de dados com suporte.

> [AZURE.NOTE]
> Crie uma conta de armazenamento do Azure dados Lucerne antes de criar um pipeline com uma atividade de cópia para mover dados de/para uma loja Lucerne de dados do Azure. Para saber sobre armazenamento de Lucerne de dados do Azure, consulte [Introdução ao armazenamento de Lucerne de dados do Azure](../data-lake-store/data-lake-store-get-started-portal.md).
>  
> Examine a [criar seu primeiro tutorial de pipeline](data-factory-build-your-first-pipeline.md) para obter etapas detalhadas para criar uma fábrica de dados, serviços vinculados, conjuntos de dados e um pipeline. Use os trechos JSON com Editor de fábrica de dados ou Visual Studio ou Azure PowerShell para criar as entidades de fábrica de dados.

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil para criar um pipeline que copia dados de/para o armazenamento do Azure dados Lucerne é usar o Assistente de dados de cópia. Consulte [Tutorial: criar um pipeline usando o Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para um rápida passo a passo sobre como criar um pipeline usando o Assistente de dados de cópia. 

Os exemplos a seguir fornecem definições de JSON de exemplo que você pode usar para criar um pipeline usando o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados para e do armazenamento de Lucerne de dados do Azure e armazenamento de Blob do Azure. No entanto, dados podem ser copiados **diretamente** de qualquer uma das fontes a qualquer uma dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.  


## <a name="sample-copy-data-from-azure-blob-to-azure-data-lake-store"></a>Exemplo: Copiar dados do Azure Blob para armazenamento de Lucerne de dados do Azure
Mostra o exemplo a seguir:

1.  Um serviço vinculado do tipo [AzureStorage](#azure-storage-linked-service-properties).
2.  Um serviço vinculado do tipo [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
3.  Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](#azure-blob-dataset-type-properties).
4.  Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4.  Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que usa [BlobSource](#azure-blob-copy-activity-type-properties) e [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties).

O exemplo copia dados de série de tempo de um armazenamento de Blob do Azure para armazenamento de Lucerne de dados do Azure cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras.


**Serviço de armazenamento vinculado do Azure:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Lucerne de dados do Azure vinculado serviço:**

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

### <a name="to-create-azure-data-lake-linked-service-using-data-factory-editor"></a>Para criar o Azure Lucerne vinculadas serviço de dados usando o Editor de fábrica de dados
O procedimento a seguir fornece etapas para criar um serviço de armazenamento de Lucerne do Azure dados vinculados usando o Editor de fábrica de dados.

1. Clique em **armazenam novos dados** na barra de comandos e selecione **Azure dados Lucerne loja**.
2. No editor de JSON, para a propriedade **dataLakeStoreUri** , insira o URI para a Lucerne de dados.
3. Clique em botão de **Autorizar** na barra de comando. Você verá uma janela pop-up.

    ![Autorize botão](./media/data-factory-azure-data-lake-connector/authorize-button.png)

4. Use suas credenciais para entrar e a propriedade de **autorização** no JSON deve ser atribuída a um valor agora.
5. (opcional) Especificar valores para parâmetros opcionais como **accountName**, **subscriptionID**e **resourceGroupName** no JSON (ou) excluir essas propriedades do JSON.
6. Clique em **implantar** na barra de comando para implantar o serviço vinculado.

> [AZURE.IMPORTANT] O código de autorização gerado usando o botão **Autorizar** expira depois de um dia. **Autorize de novo** usando o **Autorizar** botão quando o **token expira** e reimplantação o serviço vinculada. Consulte a seção de [Serviço de repositório de Lucerne vinculadas dados do Azure](#azure-data-lake-store-linked-service-properties) para obter detalhes. 



**Azure Blob entrado conjunto de dados:**

Dados são obtidos de um novo blob cada hora (frequência: horas, intervalo: 1). O nome de caminho e do arquivo de pasta para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa ano, mês e dia parte da hora de início e nome do arquivo usa a parte de hora de início. "externo": "true" configuração informa o serviço de fábrica de dados que a tabela é externa à fábrica dados e não é produzida por uma atividade na fábrica dados.

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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


**Lucerne de dados do Azure dataset de saída:**

O exemplo copia dados para um armazenamento Lucerne de dados do Azure. Novos dados são cópias dados Lucerne armazenam cada hora.

    {
        "name": "AzureDataLakeStoreOutput",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/output/"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Pipeline com uma atividade de cópia:**

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendado para ser executado a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **BlobSource** e **receptor** tipo está definido como **AzureDataLakeStoreSink**.

    {  
        "name":"SamplePipeline",
        "properties":
        {  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline with copy activity",
            "activities":
            [  
                {
                    "name": "AzureBlobtoDataLake",
                    "description": "Copy Activity",
                    "type": "Copy",
                    "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                    ],
                    "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "treatEmptyAsNull": true,
                            "blobColumnSeparators": ","
                        },
                        "sink": {
                            "type": "AzureDataLakeStoreSink"
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

## <a name="sample-copy-data-from-azure-data-lake-store-to-azure-blob"></a>Exemplo: Copiar dados de armazenamento de Lucerne de dados do Azure para Azure Blob
Mostra o exemplo a seguir:

1.  Um serviço vinculado do tipo [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
2.  Um serviço vinculado do tipo [AzureStorage](#azure-storage-linked-service-properties).
3.  Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4.  Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](#azure-blob-dataset-type-properties).
5.  Um [pipeline](data-factory-create-pipelines.md) com uma atividade de cópia que usa [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) e [BlobSink](#azure-blob-copy-activity-type-properties)

O exemplo copia dados da série de tempo de uma loja Lucerne de dados do Azure para um Azure blob cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras.

**Armazenamento de Lucerne do Azure dados vinculados serviço:**

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

> [AZURE.NOTE] Consulte as etapas no exemplo anterior para obter a URL de autorização.  

**Serviço de armazenamento vinculado do Azure:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Lucerne de dados do Azure dataset de entrada:**

Definindo **"externo": true** informa o serviço de fábrica de dados que a tabela é externa à fábrica dados e não é produzida por uma atividade na fábrica dados.

    {
        "name": "AzureDataLakeStoreInput",
        "properties":
        {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/input/",
                "fileName": "SearchLog.tsv",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
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

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendado para ser executado a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **AzureDataLakeStoreSource** e **receptor** tipo está definido como **BlobSink**.


    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                {
                    "name": "AzureDakeLaketoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                      {
                        "name": "AzureDataLakeStoreInput"
                      }
                    ],
                    "outputs": [
                      {
                        "name": "AzureBlobOutput"
                      }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "AzureDataLakeStoreSource",
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


## <a name="azure-data-lake-store-linked-service-properties"></a>Propriedades de dados Lucerne Store vinculadas serviço Azure

Você pode vincular uma conta de armazenamento do Azure para uma fábrica Azure dados usando um serviço de armazenamento do Azure vinculado. A tabela a seguir fornece descrição para elementos JSON específicos ao serviço de armazenamento do Azure vinculado.

| Propriedade | Descrição | Necessário |
| :-------- | :----------- | :-------- |
| tipo | A propriedade type deve ser definida: **AzureDataLakeStore** | Sim |
| dataLakeStoreUri | Especifica informações sobre a conta de armazenamento de Lucerne de dados do Azure. É no seguinte formato: https://<Azure Data Lake account name>.azuredatalakestore.net/webhdfs/v1 | Sim |
| autorização | Clique em botão de **Autorizar** no **Editor de fábrica de dados** e insira suas credenciais que atribui a URL de autorização geradas automaticamente a essa propriedade.  | Sim |
| identificação de sessão | Id da sessão OAuth da sessão autorização oauth. Cada id de sessão é exclusiva e pode ser usado somente uma vez. Esta configuração é gerada automaticamente quando você usa o Editor de fábrica de dados. | Sim |  
| accountName | Nome de conta de Lucerne de dados | Não |
| subscriptionId | Assinatura do Azure ID. | Não (se não especificado, assinatura da fábrica dados é usada). |
| resourceGroupName |  Nome do grupo de recursos do Azure | Não (se não especificado, grupo de recursos da fábrica dados é usado). |

## <a name="token-expiration"></a>Expiração do token 
O código de autorização que gerar usando o botão **Autorizar** expira depois de um dia. Consulte a tabela a seguir para os tempos de expiração para diferentes tipos de contas de usuário. Você pode ver o seguinte erro mensagem quando a autenticação **token expira**: "Erro na operação de credencial: invalid_grant - AADSTS70002: erro ao validar credenciais. AADSTS70008: A concessão de acesso fornecido expirou ou foi revogada. ID do rastreamento: ID de correlação d18629e8-af88-43c5-88e3-d8419eb1fca1: carimbo de hora fac30a0c-6be6-4e02-8d69-a776d2ffefd7: 2015-12-15 21-09-31Z ".


| Tipo de usuário | Expira após |
| :-------- | :----------- | 
| Contas de usuário não gerenciadas pelo Active Directory do Azure (@hotmail.com, @live.com, etc.). | 12 horas |
| Contas de usuários gerenciadas pelo Azure Active Directory (AAD) | 14 dias após a última fatia executar. <br/><br/>90 dias, se uma fatia com base em serviço vinculado baseado em OAuth é executado pelo menos uma vez a cada 14 dias. |

Se você alterar sua senha antes deste horário de expiração do token, o token expira imediatamente e você verá o erro mencionado nesta seção. 

Para evitar/resolver esse erro, autorize de novo usando o **Autorizar** botão quando o **token expira** e reimplantação o serviço vinculada. Você também pode gerar valores para as propriedades de **identificação de sessão** e **autorização** por programação usando o código na seção a seguir:

### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Para gerar programaticamente valores de ID de sessão e autorização 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Consulte os tópicos [Classes AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)e [AuthorizationSessionGetResponse classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) para obter detalhes sobre as classes de fábrica de dados usadas no código. Adicione uma referência a versão de **2.9.10826.1824** do **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll** para a classe de WindowsFormsWebAuthenticationDialog usada no código. 
 

## <a name="azure-data-lake-dataset-type-properties"></a>Propriedades de tipo de Dataset Lucerne de dados do Azure

Para obter uma lista completa das seções JSON & propriedades disponíveis para a definição de conjuntos de dados, consulte o artigo de [conjuntos de dados criando](data-factory-create-datasets.md) . Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOB do Microsoft Azure, tabela do Microsoft Azure, etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local, etc., formatar os dados no repositório de dados. A seção typeProperties de conjunto de dados do tipo **AzureDataLakeStore** dataset tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| :-------- | :----------- | :-------- |
| folderPath | Repositório de caminho para o contêiner e a pasta na Lucerne de dados do Azure. | Sim |
| nome do arquivo | Nome do arquivo no repositório do Azure dados Lucerne. nome do arquivo é opcional e diferencia maiusculas de minúsculas. <br/><br/>Se você especificar um nome de arquivo, a atividade (incluindo a cópia) funciona no arquivo específico.<br/><br/>Quando o nome do arquivo não for especificada, cópia inclui todos os arquivos na folderPath de conjunto de dados de entrada.<br/><br/>Quando o nome do arquivo não for especificado para um conjunto de dados de saída, o nome do arquivo gerado seria no seguinte neste formato: dados. <Guid>. txt (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | Não |
| partitionedBy | partitionedBy é uma propriedade opcional. Você pode usá-lo para especificar um folderPath dinâmico e o nome do arquivo de dados da série de tempo. Por exemplo, folderPath pode ser parametrizada para cada hora dos dados. Consulte a seção de [usando partitionedBy propriedade](#using-partitionedby-property) para obter detalhes e exemplos. | Não |
| formato | Os seguintes tipos de formato são suportados: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade de **tipo** em Formatar como um desses valores. Consulte as seções [Especificando TextFormat](#specifying-textformat), [Especificando AvroFormat](#specifying-avroformat), [Especificando JsonFormat](#specifying-jsonformat), [Especificando OrcFormat](#specifying-orcformat)e [Especificando ParquetFormat](#specifying-parquetformat) para obter detalhes. Se você quiser copiar arquivos como-está entre armazenamentos baseados em arquivos (cópia binário), você pode ignorar a seção de formato em ambas as definições de conjunto de dados de entrada e saída.| Não
| compactação | Especifica o tipo e o nível de compactação para os dados. Tipos suportados são: **GZip**, **Deflate**e **BZip2** e níveis com suporte são: **Optimal** e **mais rápido**. Atualmente, as configurações de compactação não há suporte para dados em **AvroFormat** ou **OrcFormat**. Para obter mais informações, consulte a seção de [suporte de compactação](#compression-support) .  | Não |

### <a name="using-partitionedby-property"></a>Usando a propriedade partitionedBy
Você pode especificar um folderPath dinâmico e o nome do arquivo de dados da série de tempo com a seção **partitionedBy** , macros de dados fábrica e as variáveis do sistema: SliceStart e SliceEnd, que indicam as horas de início e término de uma fatia de dados fornecidos.

Consulte os artigos [Criar conjuntos de dados](data-factory-create-datasets.md) e [agendamento & execução](data-factory-scheduling-and-execution.md) para compreender mais detalhes sobre conjuntos de dados de série de tempo, agendamento e fatias.

#### <a name="sample-1"></a>Exemplo 1

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy":
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

Neste exemplo, {fatia} é substituída pelo valor da variável de sistema de Data Factory SliceStart no formato (YYYYMMDDHH) especificado. O SliceStart refere-se para começar a hora da fatia. O folderPath é diferente para cada fatia. Por exemplo: wikisampledataout/wikidatagateway/2014100103 ou wikisampledataout/wikidatagateway/2014100104

#### <a name="sample-2"></a>Exemplo 2

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],

Neste exemplo, ano, mês, dia e hora da SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades folderPath e fileName.

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]
 

### <a name="compression-support"></a>Suporte de compactação  
Processamento de grandes conjuntos de dados pode causar gargalos de rede e e/s. Portanto, dados compactados em armazenamentos podem não apenas acelerar a transferência de dados pela rede e economize espaço em disco, mas também trazer melhorias de desempenho significativas no processamento de grande volume. Atualmente, a compactação tem suporte para armazenamentos de dados baseado em arquivo como Azure Blob ou sistema de arquivos local.  

Para especificar a compactação de um conjunto de dados, use a propriedade de **compactação** no dataset JSON como no exemplo seguinte:   

    {  
        "name": "AzureDatalakeStoreDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureDatalakeStore",  
            "linkedServiceName": "DataLakeStoreLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  
 
Seção **compactação** tem duas propriedades:  
  
- **Tipo:** o codec de compactação, que pode ser **GZIP**, **Deflate** ou **BZIP2**.  
- **Nível:** a taxa de compactação, que pode ser **Optimal** ou **mais rápido**. 
    - **Mais rápido:** A operação de compactação deve concluir assim que possível, mesmo se o arquivo resultante não é compactado ideal. 
    - **Optimal**: A operação de compactação deve ser otimizada compactada, mesmo se a operação demora mais para ser concluída. 
    
    Para obter mais informações, consulte o tópico de [Nível de compactação](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . 

Suponha que o conjunto de dados de amostra é usado como a saída de uma atividade de cópia. A atividade de cópia compacta os dados de saída com codec GZIP usando melhor proporção e grava os dados compactados em um arquivo denominado pagecounts.csv.gz no repositório do Azure dados Lucerne.   

Quando você define a propriedade compactação em um dataset entrado JSON, o pipeline de lê dados compactados da origem. Quando você define a propriedade em um conjunto de dados de saída JSON, a atividade de cópia pode escrever dados compactados para o destino. Aqui estão alguns cenários de exemplo: 

- Dados de leitura GZIP compactada de uma loja de Lucerne de dados do Azure, descompactá-lo e gravar dados de resultado em um banco de dados do SQL Azure. Você pode definir o conjunto de dados do Azure dados Lucerne Store entrado com a compactação de propriedade JSON nesse caso. 
- Leia os dados de um arquivo de texto sem formatação local do sistema de arquivos, compactá-lo usando o formato GZip e gravar os dados compactados em uma loja de Lucerne de dados do Azure. Você pode definir um conjunto de dados de Lucerne de Azure dados de saída com a compactação de propriedade JSON nesse caso.  
- Ler um dados compactados GZIP de uma loja de Lucerne de dados do Azure, descompactá-lo, compactar usando BZIP2 e gravar dados de resultado em uma loja de Lucerne de dados do Azure. Você definir o conjunto de tipo de compactação como GZIP e BZIP2 para entrada e saída conjuntos de dados respectivamente.   


## <a name="azure-data-lake-copy-activity-type-properties"></a>Propriedades de tipo de atividade de cópia Lucerne dados Azure  
Para obter uma lista completa das seções & propriedades disponíveis para a definição de atividades, consulte o artigo [Criar canais](data-factory-create-pipelines.md) . Propriedades como nome, descrição, entrada e saída tabelas e política estão disponíveis para todos os tipos de atividades.

Por outro lado, propriedades disponíveis na seção typeProperties da atividade variam com cada tipo de atividade. Para atividade de cópia, eles podem variar dependendo dos tipos de fontes e receptores

**AzureDataLakeStoreSource** compatível com a seção de **typeProperties** seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- |
| recursiva | Indica se os dados são lidos repetidamente das pastas sub ou somente na pasta especificada. | Verdadeiro (valor padrão), FALSO | Não |



**AzureDataLakeStoreSink** compatível com a seção de **typeProperties** seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Especifica o comportamento de copiar. | **PreserveHierarchy:** preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem a pasta de origem é idêntico ao caminho relativo do arquivo de destino para pasta de destino.<br/><br/>**FlattenHierarchy:** todos os arquivos da pasta de origem são criados no primeiro nível da pasta de destino. Os arquivos de destino são criados com nome gerado automaticamente.<br/><br/>**MergeFiles:** mescla todos os arquivos da pasta de origem para um arquivo. Se o nome de arquivo/Blob for especificado, o nome de arquivo mesclado seria o nome especificado. Caso contrário, seria o nome de arquivo gerado automaticamente. | Não |


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Desempenho e ajuste  
Consulte o [guia de ajuste e desempenho de atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre principais fatores que desempenho de impacto de movimentação de dados (cópia atividade) em fábrica de dados do Azure e várias maneiras de otimizá-lo.
