<properties
    pageTitle="Mover de dados e armazenamento de Blob do Azure usando AzCopy | Microsoft Azure"
    description="Mover de dados e armazenamento de Blob do Azure usando AzCopy"
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

# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Mover de dados e armazenamento de Blob do Azure usando AzCopy

AzCopy é um utilitário de linha de comando projetado para carregar, baixar e copiando dados para e do Microsoft Azure blob, arquivo e armazenamento de tabela.

Para obter instruções sobre como instalar AzCopy e informações adicionais em usá-lo com a plataforma Windows Azure, consulte [Introdução ao utilitário AzCopy de linha de comando](../storage/storage-use-azcopy.md).

Orientação sobre tecnologias usadas para mover dados para e/ou do armazenamento de Blob do Azure estão vinculados aqui:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Se você estiver usando máquina virtual que foi configurado com os scripts fornecidos pela [máquinas virtuais de ciência de dados no Azure](machine-learning-data-science-virtual-machines.md), AzCopy já está instalado na VM.

> [AZURE.NOTE] Para obter uma introdução completa ao Azure blob storage, consulte [Noções básicas de Blob do Azure](../storage/storage-dotnet-how-to-use-blobs.md) e [Azure Blob](https://msdn.microsoft.com/library/azure/dd179376.aspx)serviço.


## <a name="prerequisites"></a>Pré-requisitos

Este documento pressupõe que você tenha uma assinatura do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregar/baixar dados, você deve saber a sua chave de nome e uma conta da conta armazenamento do Azure.

- Para configurar uma assinatura do Azure, consulte [avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).

- Para obter instruções sobre como criar uma conta de armazenamento e para obter informações importantes e conta, consulte [contas de armazenamento do Azure sobre](../storage/storage-create-storage-account.md).


## <a name="run-azcopy-commands"></a>Executar comandos de AzCopy

Para executar comandos AzCopy, abra uma janela de comando e navegue até o diretório de instalação do AzCopy em seu computador, onde se encontra o AzCopy.exe executável. 

A sintaxe básica para comandos AzCopy é:

    AzCopy /Source:<source> /Dest:<destination> [Options]

>[AZURE.NOTE] Você pode adicionar o local de instalação do AzCopy ao caminho do sistema e executar os comandos de qualquer diretório. Por padrão, AzCopy é instalado *% \ ProgramFiles(x86) %\Microsoft SDKs\Azure\AzCopy* ou *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.

## <a name="upload-files-to-an-azure-blob"></a>Carregar arquivos para um blob do Microsoft Azure

Para carregar um arquivo, use o seguinte comando:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Baixar arquivos de um blob do Microsoft Azure

Para baixar um arquivo de um Azure blob, use o seguinte comando:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Transferir blobs entre Azure contêineres

Para transferir blobs entre Azure contêineres, use o seguinte comando:

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Dicas para usar AzCopy

> [AZURE.TIP]   
> 1. Quando o **upload de** arquivos, */S* carrega arquivos repetidamente. Sem esse parâmetro, arquivos em subdiretórios não são carregados.  
> 2. Quando o **download de** arquivo, */S* procura o contêiner repetidamente até que todos os arquivos na pasta especificada e seus subdiretórios, ou todos os arquivos que correspondem ao padrão especificado em determinado diretório e seus subdiretórios, são baixadas.  
> 3.  Você não pode especificar um **arquivo específico blob** baixar usando o parâmetro */Source* . Para baixar um arquivo específico, especifique o nome de arquivo do blob baixar usando o parâmetro */Pattern* . **/S** parâmetro pode ser usado com AzCopy procure uma repetidamente de padrão de nome de arquivo. Sem o parâmetro padrão, AzCopy baixa todos os arquivos nessa pasta.
