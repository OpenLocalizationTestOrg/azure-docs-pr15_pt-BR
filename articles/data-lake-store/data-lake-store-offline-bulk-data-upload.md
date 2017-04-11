<properties
   pageTitle="Carregar grandes quantidades de dados no repositório de Lucerne de dados usando métodos offline | Microsoft Azure"
   description="Use a ferramenta de AdlCopy para copiar dados de Blobs de armazenamento do Azure para armazenamento de Lucerne de dados"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/07/2016"
   ms.author="nitinme"/>

# <a name="use-azure-import-export-service-for-offline-copy-of-data-to-data-lake-store"></a>Usar o serviço de importação-exportação do Azure para cópia offline de dados para armazenamento de Lucerne de dados

Neste artigo, você aprenderá sobre como copiar grandes conjuntos de dados (> 200GB) em uma loja de Lucerne de dados do Azure usando métodos de cópia offline, como o [Serviço de importação/exportação do Azure](../storage/storage-import-export-service.md). Especificamente, o arquivo usado como exemplo neste artigo é 339,420,860,416 bytes ou seja cerca de 319GB no disco. Vamos chamar 319GB.tsv neste arquivo.

Serviço de importação/exportação Azure permite transferir grandes quantidades de dados com segurança ao Azure blob storage por remessa unidades de disco rígido a um centro de dados do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).

- **Conta de armazenamento do azure**.

- **Conta do azure dados Lucerne Analytics (opcional)** - consulte [Introdução ao Azure dados Lucerne Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) para obter instruções sobre como criar uma conta de armazenamento de Lucerne de dados.


## <a name="preparing-the-data"></a>Preparar os dados

Antes de usar o serviço de importação/exportação, podemos deve quebrar o arquivo de dados para ser transferidos **em cópias que têm menos de 200 GB** de tamanho. Isso ocorre porque a ferramenta de importação não funciona com arquivos maiores que 200GB. Em conformidade com isso, neste tutorial, podemos dividir o arquivo em blocos de 100GB bytes cada. Você pode fazer isso facilmente usando [Cygwin](https://cygwin.com/install.html). Cygwin compatível com o comando Linux que permite que os usuários fazer isso facilmente. Para nosso caso, usamos o comando a seguir.

    split -b 100m 319GB.tsv

A operação de divisão cria arquivos com os nomes abaixo.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Prepare discos com dados

Siga as instruções no [Serviço de importação/exportação Azure usando](../storage/storage-import-export-service.md) (sob a seção **preparar as unidades** ) para preparar sua unidades de disco rígido. Aqui está o fluxo geral sobre como preparar a unidade.

1. Adquirir um disco rígido que atende ao requisito a ser usado para o serviço de importação/exportação de Auzre.

2. Identificar uma conta de armazenamento do Azure onde os dados serão copiados uma vez ele si enviados para o Centro de dados do Azure.

3. Use a [Ferramenta de importação/exportação do Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), um utilitário de linha de comando. Aqui está um trecho de exemplo de como usar a ferramenta.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Consulte [Usando o serviço de importação/exportação do Azure](../storage/storage-import-export-service.md) para trechos de amostra mais sobre como usar a **Ferramenta de importação/exportação do Azure**.

4. O comando acima cria um arquivo do diário no local especificado. Você usará esse arquivo de diário para criar um trabalho de importação do [Portal clássico do Azure](https://manage.windowsazure.com).

## <a name="create-an-import-job"></a>Criar um trabalho de importação

Agora você pode criar um trabalho de importação usando as instruções em [Usando o serviço de importação/exportação do Azure](../storage/storage-import-export-service.md) (sob a seção de **criar o trabalho de importação** ). Para este trabalho de importação, com outros detalhes, também fornecem o arquivo de diário criado ao preparar as unidades de disco.

## <a name="physically-ship-the-disks"></a>Enviar física discos

Agora você pode entregar física discos a uma central de dados Azure onde os dados são copiados nos Blobs de armazenamento do Azure você forneceu ao criar o trabalho de importação. Além disso, ao criar o trabalho, se você tiver optado para fornecer as informações de acompanhamento posterior, você pode agora voltar ao seu trabalho de importação e atualizado o número de controle.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Copiar dados de Blobs de armazenamento do Azure para armazenamento de Lucerne de dados do Azure

Depois que o status do trabalho de importação mostra concluído, você pode verificar se os dados estão disponíveis nas bolhas de armazenamento do Azure especificadas. Você pode usar uma variedade de métodos para mover dados dos Blobs de armazenamento do Azure para armazenamento de Lucerne de dados do Azure. Para todas as opções disponíveis para carregar dados, consulte [dados de Ingesting dados Lucerne loja](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store).

Nesta seção, fornecemos as definições de JSON que você pode usar para criar um pipeline de fábrica de dados do Azure para copiar dados. Você pode usar essas definições de JSON do [portal do Azure](../data-factory/data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](../data-factory/data-factory-copy-activity-tutorial-using-powershell.md).

### <a name="source-linked-service-azure-storage-blob"></a>Fonte vinculado serviço de armazenamento do Azure Blob)

````
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
````

### <a name="target-linked-service-azure-data-lake-store"></a>Destino vinculado serviço (Azure dados Lucerne Store)

````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>Conjunto de dados de entrada
````
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
````
### <a name="output-data-set"></a>Conjunto de dados de saída
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>Pipeline (atividade de cópia)
````
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
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
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
````
Para obter mais informações sobre como usar fábrica de dados do Azure para mover dados do Azure Blob de armazenamento e o repositório de Lucerne de dados do Azure, consulte [mover dados do Azure Blob de armazenamento para armazenamento de Lucerne de dados do Azure usando fábrica de dados do Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store).

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Reconstruir os arquivos de dados no repositório de Lucerne de dados do Azure

Podemos iniciado com um arquivo que foi 319GB de tamanho e dividimos para baixo em arquivos de tamanho menor de forma que podem ser transferido usando o serviço de importação/exportação do Azure. Agora que os dados estiverem no repositório de Lucerne de dados do Azure, podemos reconstrcut o arquivo ao seu tamanho original. Você pode usar os seguintes cmldts PowerShell do Azure para fazê-lo.

````
# Login to our account
Login-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext –SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv”
````

## <a name="next-steps"></a>Próximas etapas

- [Proteger os dados no repositório de Lucerne de dados](data-lake-store-secure-data.md)
- [Usar a análise de dados Azure Lucerne com dados Lucerne Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com dados Lucerne Store](data-lake-store-hdinsight-hadoop-use-portal.md)
