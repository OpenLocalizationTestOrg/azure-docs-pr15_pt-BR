<properties
    pageTitle="Como usar o armazenamento de arquivos do Azure do Python | Microsoft Azure"
    description="Saiba como usar o armazenamento de arquivos do Azure do Python para carregar, lista, download e excluir arquivos."
    services="storage"
    documentationCenter="python"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-azure-file-storage-from-python"></a>Como usar o armazenamento de arquivos do Azure do Python

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Visão geral

Este artigo mostrará como executar cenários comuns usando o armazenamento de arquivos. Os exemplos são gravados em Python e usam o [Microsoft Azure armazenamento SDK para Python]. Os cenários cobertos incluem carregando, listando, baixando e excluindo arquivos.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-share"></a>Criar um compartilhamento

O objeto **FileService** permite que você trabalhe com compartilhamentos, pastas e arquivos. O código a seguir cria um objeto **FileService** . Adicione o seguinte na parte superior de qualquer arquivo Python no qual você deseja acessar programaticamente o armazenamento do Azure.

    from azure.storage.file import FileService

O código a seguir cria um objeto de **FileService** usando a chave de nome e uma conta de conta do armazenamento.  Substitua 'Minha conta' e 'mykey' com seu nome de conta e chave.

    file_service = **FileService** (account_name='myaccount', account_key='mykey')

No exemplo de código a seguir, você pode usar um objeto **FileService** para criar o compartilhamento, se ele não existir.

    file_service.create_share('myshare')

## <a name="upload-a-file-into-a-share"></a>Carregar um arquivo em um compartilhamento

Um compartilhamento de armazenamento do Azure arquivo contém pelo menos, um diretório raiz onde podem residem os arquivos. Nesta seção, você aprenderá como carregar um arquivo de armazenamento local para o diretório raiz de um compartilhamento.

Para criar um arquivo e carregue os dados, use o **criar\_arquivo\_de\_caminho**, **criar\_arquivo\_de\_fluxo**, **criar\_arquivo\_de\_bytes** ou **criar\_arquivo\_de\_texto** métodos. Eles são métodos de alto nível que executam o agrupamento necessárias quando o tamanho dos dados excede 64 MB.

**criar\_arquivo\_de\_caminho** carrega o conteúdo de um arquivo do caminho especificado, e **criar\_arquivo\_de\_fluxo** carrega o conteúdo de um arquivo/fluxo já aberto. **criar\_arquivo\_de\_bytes** carrega uma matriz de bytes, e **criar\_arquivo\_de\_texto** carrega o valor de texto especificado usando a codificação especificada (padrões UTF-8).

O exemplo a seguir carrega o conteúdo do arquivo **sunset.png** no arquivo **myfile** .

    from azure.storage.file import ContentSettings
    file_service.create_file_from_path(
        'myshare',
        None, # We want to create this blob in the root directory, so we specify None for the directory_name
        'myfile',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png'))

## <a name="how-to-create-a-directory"></a>Como: criar um diretório

Você também pode organizar armazenamento colocando arquivos dentro subdiretórios em vez de ter todos eles no diretório raiz. O serviço de armazenamento de arquivos Azure permite criar quantas diretórios quanto sua conta permitir. O código a seguir irá criar uma subpasta chamada **sampledir** sob o diretório raiz.

    file_service.create_directory('myshare', 'sampledir')

## <a name="how-to-list-files-and-directories-in-a-share"></a>Como: arquivos e pastas em um compartilhamento de lista

Para listar os arquivos e pastas em um compartilhamento, use o **lista\_diretórios\_e\_arquivos** método. Esse método retorna um gerador. O seguinte código gera o **nome** de cada arquivo e pasta em um compartilhamento no console.

    generator = file_service.list_directories_and_files('myshare')
    for file_or_dir in generator:
        print(file_or_dir.name)

## <a name="download-files"></a>Baixar arquivos

Para baixar dados de um arquivo, use **obter\_arquivo\_para\_caminho**, **obter\_arquivo\_para\_fluxo**, **obter\_arquivo\_para\_bytes**, ou **obter\_arquivo\_para\_texto**. Eles são métodos de alto nível que executam o agrupamento necessárias quando o tamanho dos dados excede 64 MB.

O exemplo a seguir demonstra usando **obter\_arquivo\_para\_caminho** baixar o conteúdo do arquivo **myfile** e armazená-lo para o arquivo de **saída-sunset.png** .

    file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')

## <a name="delete-a-file"></a>Excluir um arquivo

Finalmente, para excluir um arquivo, chame **delete_file**.

    file_service.delete_file('myshare', None, 'myfile')

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as Noções básicas do armazenamento de arquivos, siga estes links para saber mais.

- [Central de desenvolvedores do Python](/develop/python/)
- [Serviços de armazenamento do Azure API REST](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog da equipe de armazenamento do Azure]
- [Armazenamento do Microsoft Azure SDK para Python]

[Blog da equipe de armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Armazenamento do Microsoft Azure SDK para Python]: https://github.com/Azure/azure-storage-python
