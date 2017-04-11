<properties
    pageTitle="Mover de dados e armazenamento de Blob do Azure usando Python | Microsoft Azure"
    description="Mover de dados e armazenamento de Blob do Azure usando Python"
    services="machine-learning,storage"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />

# <a name="move-data-to-and-from-azure-blob-storage-using-python"></a>Mover de dados e armazenamento de Blob do Azure usando Python

Este tópico descreve como listar, carregar e baixar blobs usando a API de Python. Com a API de Python fornecidas no SDK do Azure, você pode:

- Criar um contêiner
- Carregar um blob em um contêiner
- Baixar blobs
- Listar os blobs em um contêiner
- Excluir um blob

Para obter mais informações sobre como usar a API de Python, consulte [como usar o serviço de armazenamento de Blob do Python](../storage/storage-python-how-to-use-blob-storage.md).

Orientação sobre tecnologias usadas para mover dados para e/ou do armazenamento de Blob do Azure estão vinculados aqui:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Se você estiver usando máquina virtual que foi configurado com os scripts fornecidos pela [máquinas virtuais de ciência de dados no Azure](machine-learning-data-science-virtual-machines.md), AzCopy já está instalado na VM.

> [AZURE.NOTE] Para obter uma introdução completa ao Azure blob storage, consulte [Noções básicas de Blob do Azure](../storage/storage-dotnet-how-to-use-blobs.md) e [Azure Blob](https://msdn.microsoft.com/library/azure/dd179376.aspx)serviço.


## <a name="prerequisites"></a>Pré-requisitos

Este documento pressupõe que você tenha uma assinatura do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregar/baixar dados, você deve saber a sua chave de nome e uma conta da conta armazenamento do Azure.

- Para configurar uma assinatura do Azure, consulte [avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).

- Para obter instruções sobre como criar uma conta de armazenamento e para obter informações importantes e conta, consulte [contas de armazenamento do Azure sobre](../storage/storage-create-storage-account.md).


## <a name="upload-data-to-blob"></a>Carregar dados Blob

Adicione o trecho a seguir, na parte superior de qualquer código Python no qual você deseja acessar programaticamente o armazenamento do Azure:

    from azure.storage.blob import BlobService

O objeto **BlobService** permite que você trabalhar com contêineres e blobs. O código a seguir cria um objeto de BlobService usando a chave de nome e uma conta de conta do armazenamento. Substitua o nome da conta e chave da conta com sua conta real e chave.

    blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Use os seguintes métodos para carregar dados em um blob:

1. colocar\_bloco\_blob\_de\_caminho (carrega o conteúdo de um arquivo do caminho especificado)
2. colocar\_block_blob\_de\_arquivo (carrega o conteúdo de um arquivo/fluxo já aberto)
3. colocar\_bloco\_blob\_de\_bytes (uploads uma matriz de bytes)
4. colocar\_bloco\_blob\_de\_texto (carrega o valor de texto especificado usando a codificação especificada)

O código de exemplo a seguir carrega um arquivo local para um contêiner:

    blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

O código de exemplo a seguir carrega todos os arquivos (excluindo diretórios) em um diretório local ao armazenamento de blob:

    from azure.storage.blob import BlobService
    from os import listdir
    from os.path import isfile, join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"     

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
    # find all files in the LOCAL_DIRECT (excluding directory)
    local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

    file_num = len(local_file_list)
    for i in range(file_num):
        local_file = join(LOCAL_DIRECT, local_file_list[i])
        blob_name = local_file_list[i]
        try:
            blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
        except:
            print "something wrong happened when uploading the data %s"%blob_name


## <a name="download-data-from-blob"></a>Baixar dados do Blob

Use os seguintes métodos para baixar dados de um blob:
1. obter\_blob\_para\_caminho
2. obter\_blob\_para\_arquivo
3. obter\_blob\_para\_bytes
4. obter\_blob\_para\_texto

Esses métodos que executam o agrupamento necessárias quando o tamanho dos dados excede 64 MB.

O código de exemplo a seguir downloads o conteúdo de um blob em um contêiner para um arquivo local:

    blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

O código de exemplo a seguir baixa todos os blobs de um contêiner. Ele usa lista\_blobs para obter uma lista de blobs disponíveis no contêiner e downloads-las em um diretório local.

    from azure.storage.blob import BlobService
    from os.path import join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"     

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # List all blobs and download them one by one
    blobs = blob_service.list_blobs(CONTAINER_NAME)
    for blob in blobs:
        local_file = join(LOCAL_DIRECT, blob.name)
        try:
            blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
        except:
            print "something wrong happened when downloading the data %s"%blob.name
