<properties
   pageTitle="Carregar os dados com fábrica de dados do Azure | Microsoft Azure"
   description="Saiba como carregar os dados com fábrica de dados do Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/13/2016"
   ms.author="mausher;barbkess"/>

# <a name="load-data-with-azure-data-factory"></a>Carregar os dados com dados Azure fábrica 

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)  
- [Fábrica de dados](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
- [BCP](sql-data-warehouse-load-with-bcp.md)  

Este tutorial mostra como criar um pipeline em fábrica de dados do Azure para mover dados do Azure armazenamento de Blob depósito de dados do SQL Azure. Com as etapas a seguir, você irá:

+ Configure dados de exemplo em um Blob de armazenamento do Azure.
+ Conecte recursos a fábrica de dados do Azure.
+ Crie um pipeline para mover dados do armazenamento de Blobs depósito de dados do SQL.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## <a name="before-you-begin"></a>Antes de começar

Para se familiarizar com fábrica de dados do Azure, consulte [Introdução à fábrica de dados do Azure][].

### <a name="create-or-identify-resources"></a>Criar ou identificar recursos

Antes de iniciar este tutorial, você precisa ter os seguintes recursos:

   + **Azure armazenamento de Blob**: Este tutorial usa Azure armazenamento de Blob como fonte de dados para o pipeline de fábrica de dados do Azure e então você precisa ter um disponível para armazenar os dados de exemplo. Se você não tenha um, saiba como [criar uma conta de armazenamento][].

   + **Depósito de dados do SQL**: Este tutorial move os dados do Azure armazenamento de Blob SQL Data Warehouse e então precisam ter um data warehouse online que é carregado com os dados de exemplo AdventureWorksDW. Se você ainda não tiver um depósito de dados, saiba como [provisionar um][Create a SQL Data Warehouse]. Se você tiver um depósito de dados, mas não provisioná-lo com os dados de exemplo, você poderá [carregá-lo manualmente][Load sample data into SQL Data Warehouse].

   + **Fábrica de dados do Azure**: fábrica de dados do Azure conclui a carga real e então você precisa ter um que você pode usar para criar o pipeline de movimentação de dados. Se você não tenha um, saiba como criar uma etapa 1 de [Introdução ao Azure Data Factory (Editor de fábrica de dados)][].

   + **AZCopy**: É necessário AZCopy para copiar os dados de exemplo do seu cliente local para seu Blob de armazenamento do Azure. Para obter instruções de instalação, consulte a [documentação de AZCopy][].

## <a name="step-1-copy-sample-data-to-azure-storage-blob"></a>Etapa 1: Copiar dados de exemplo para Azure armazenamento de Blob

Quando você tiver tudo pronto, você está pronto para copiar dados de exemplo para seu Blob de armazenamento do Azure.

1. [Baixar dados de exemplo][]. Estes dados adiciona outra três anos de dados de vendas com os dados de amostra AdventureWorksDW.

2. Use este comando AZCopy para copiar os três anos de dados para sua Blob de armazenamento do Azure.

    ````
    AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
    ````


## <a name="step-2-connect-resources-to-azure-data-factory"></a>Etapa 2: Conectar recursos a fábrica de dados do Azure

Agora que os dados no lugar, podemos criar o pipeline de fábrica de dados do Azure para mover os dados do armazenamento de blob do Microsoft Azure para SQL Data Warehouse.

Para começar, abra o [portal do Azure][] e selecione sua fábrica de dados no menu à esquerda.

### <a name="step-21-create-linked-service"></a>Etapa 2.1: Criar serviço vinculado

Vincule a sua conta de armazenamento do Azure e SQL Data Warehouse a sua fábrica de dados.  

1. Primeiro, comece o processo de registro clicando em seção 'Serviços vinculada' da sua fábrica de dados e clique em 'armazenam novos dados'. Escolha um nome para registrar seu armazenamento do azure em, selecione o armazenamento do Azure como tipo e insira seu nome de conta e chave da conta.

2. Para registrar o SQL Data Warehouse navegue até a seção 'Autor e implantar', selecione 'Novo armazenamento de dados' e 'Depósito de dados do SQL Azure'. Copie e cole neste modelo e em seguida, preencha as informações específicas.

    ```JSON
    {
        "name": "<Linked Service Name>",
        "properties": {
            "description": "",
            "type": "AzureSqlDW",
            "typeProperties": {
                 "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
             }
        }
    }
    ```

### <a name="step-22-define-the-dataset"></a>Etapa 2.2: Definir o conjunto de dados

Após criar os serviços vinculados, teremos que definir os conjuntos de dados.  Aqui, isso significa definindo a estrutura dos dados que estão sendo movidos do seu armazenamento para seu data warehouse.  Você pode ler mais sobre a criação de

1. Inicie esse processo por meio de navegação para a seção 'Autor e implantar' da sua fábrica de dados.

2. Clique em 'Novo dataset' e 'Armazenamento de Blob do Azure' para vincular seu armazenamento à sua fábrica de dados.  Você pode usar o roteiro para definir seus dados no armazenamento de Blob do Azure abaixo:

    ```JSON
    {
        "name": "<Dataset Name>",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "<linked storage name>",
            "typeProperties": {
                "folderPath": "<containter name>",
                "fileName": "FactInternetSales.csv",
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
    ```

3. Agora, definimos nosso dataset para depósito de dados do SQL. Vamos começar da mesma maneira, clicando 'Novo dataset' e 'Depósito de dados do SQL Azure'.

    ```JSON
    {
        "name": "DWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": "AzureSqlDWLinkedService",
            "typeProperties": {
                "tableName": "FactInternetSales"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

## <a name="step-3-create-and-run-your-pipeline"></a>Etapa 3: Criar e executar o pipeline

Finalmente, configurar e executar o pipeline em fábrica de dados do Azure.  Esta é a operação que conclui a movimentação de dados reais.  Você pode encontrar um modo de exibição completo das operações que você pode completar com SQL Data Warehouse e fábrica de dados do Azure [aqui][Move data to and from Azure SQL Data Warehouse using Azure Data Factory].

Na seção 'Autor e implantar', clique 'Mais comandos' e 'Novo Pipeline'.  Depois de criar o pipeline, você pode usar o abaixo código para transferir os dados para sua data warehouse:

```JSON
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "skipHeaderLineCount": 1
                },
                "sink": {
                    "type": "SqlDWSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:10"
                }
            },
            "inputs": [
              {
                "name": "<Storage Dataset>"
              }
            ],
            "outputs": [
              {
                "name": "<Data Warehouse Dataset>"
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
            "name": "Sample Copy",
            "description": "Copy Activity"
          }
        ],
        "start": "<Date YYYY-MM-DD>",
        "end": "<Date YYYY-MM-DD>",
        "isPaused": false
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais, comece a exibição:

- [Caminho de aprendizagem de fábrica de dados do azure][].
- [Conector de depósito de dados do SQL azure][]. Este é o tópico de referência de núcleo para usar o Azure dados fábrica com depósito de dados do SQL Azure.


Estes tópicos fornecem informações detalhadas sobre fábrica de dados do Azure. Eles discutam Azure SQL Database ou HDInsight, mas as informações também se aplica ao depósito de dados do SQL Azure.

- [Tutorial: Introdução ao Azure dados fábrica][] Este é o tutorial de núcleo para processar dados com fábrica de dados do Azure. Neste tutorial, você irá criar primeiro pipeline que usa HDInsight para transformar e analisar os logs da web mensalmente. Observe que não há nenhuma atividade de cópia neste tutorial.
- [Tutorial: copiar dados de Blob de armazenamento do Azure para o banco de dados do Azure SQL][]. Neste tutorial, você cria um pipeline em fábrica de dados do Azure para copiar dados do Azure armazenamento de Blob para Azure SQL Database.

<!--Image references-->

<!--Article references-->
[Documentação de AZCopy]: ../storage/storage-use-azcopy.md
[Conector de depósito de dados do SQL Azure]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Criar uma conta de armazenamento]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Introdução ao Azure Data Factory (Editor de fábrica de dados)]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Introdução ao Azure dados Factory]: ../data-factory/data-factory-introduction.md
[Load sample data into SQL Data Warehouse]: sql-data-warehouse-load-sample-databases.md
[Move data to and from Azure SQL Data Warehouse using Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[Tutorial: Copiar dados do Azure armazenamento de Blob para banco de dados do SQL Azure]: ../data-factory/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[Tutorial: Introdução ao fábrica de dados do Azure]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Caminho de aprendizagem do Azure fábrica de dados]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[Portal do Azure]: https://portal.azure.com
[Baixar dados de exemplo]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv
