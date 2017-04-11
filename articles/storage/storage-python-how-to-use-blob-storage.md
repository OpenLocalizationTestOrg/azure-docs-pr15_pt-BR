<properties
    pageTitle="Como usar o armazenamento de Blob do Azure (armazenamento de objeto) do Python | Microsoft Azure"
    description="Armazene dados não estruturados na nuvem com o armazenamento de Blob do Azure (armazenamento de objeto)."
    services="storage"
    documentationCenter="python"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="how-to-use-azure-blob-storage-from-python"></a>Como usar o armazenamento de Blob do Azure do Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Visão geral

Armazenamento de Blob do Azure é um serviço que armazena os dados não estruturados na nuvem como objetos/blobs. Armazenamento de blob pode armazenar qualquer tipo de texto ou dados binários, como um documento, arquivo de mídia ou instalador do aplicativo. Armazenamento de blob também é conhecido como armazenamento do objeto.

Este artigo mostrará como executar cenários comuns usando o armazenamento de Blob. Os exemplos são gravados em Python e usam o [Microsoft Azure armazenamento SDK para Python]. Os cenários cobertos incluem carregando, listando, baixando e excluindo blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-container"></a>Criar um contêiner

Com base no tipo de blob que você gostaria de usar, criar um objeto **BlockBlobService**, **AppendBlobService**ou **PageBlobService** . O código a seguir usa um objeto de **BlockBlobService** . Adicione o seguinte na parte superior de qualquer arquivo Python no qual você deseja acessar programaticamente armazenamento de Blob do Azure bloco.

    from azure.storage.blob import BlockBlobService

O código a seguir cria um objeto de **BlockBlobService** usando a chave de nome e uma conta de conta do armazenamento.  Substitua 'Minha conta' e 'mykey' com seu nome de conta e chave.

    block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

No exemplo de código a seguir, você pode usar um objeto **BlockBlobService** para criar o contêiner se ele não existir.

    block_blob_service.create_container('mycontainer')

Por padrão, o novo contêiner é particular, portanto, você deve especificar sua chave de acesso de armazenamento (conforme você realizou anteriormente) para baixar blobs desse recipiente. Se você quiser disponibilizar os blobs dentro do contêiner para todos, você pode criar o contêiner e passar o nível de acesso público usando o código a seguir.

    from azure.storage.blob import PublicAccess
    block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)

Como alternativa, você pode modificar um contêiner após ter criado usando o código a seguir.

    block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)

Após essa alteração, qualquer pessoa na Internet pode ver blobs em um contêiner de público, mas somente você pode modificar ou excluí-las.

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob em um contêiner

Para criar um blob de bloco e carregar dados, use o **criar\_blob\_de\_caminho**, **criar\_blob\_de\_fluxo**, **criar\_blob\_de\_bytes** ou **criar\_blob\_de\_texto** métodos. Eles são métodos de alto nível que executam o agrupamento necessárias quando o tamanho dos dados excede 64 MB.

**criar\_blob\_de\_caminho** carrega o conteúdo de um arquivo do caminho especificado, e **criar\_blob\_de\_fluxo** carrega o conteúdo de um arquivo/fluxo já aberto. **criar\_blob\_de\_bytes** carrega uma matriz de bytes, e **criar\_blob\_de\_texto** carrega o valor de texto especificado usando a codificação especificada (padrões UTF-8).

O exemplo a seguir carrega o conteúdo do arquivo **sunset.png** para o blob **myblob** .

    from azure.storage.blob import ContentSettings
    block_blob_service.create_blob_from_path(
        'mycontainer',
        'myblockblob',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png')
                )

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Para listar os blobs em um contêiner, use o **lista\_blobs** método. Esse método retorna um gerador. O seguinte código gera o **nome** de cada blob em um contêiner no console.

    generator = block_blob_service.list_blobs('mycontainer')
    for blob in generator:
        print(blob.name)

## <a name="download-blobs"></a>Baixar blobs

Para baixar dados de um blob, use **obter\_blob\_para\_caminho**, **obter\_blob\_para\_fluxo**, **obter\_blob\_para\_bytes**, ou **obter\_blob\_para\_texto**. Eles são métodos de alto nível que executam o agrupamento necessárias quando o tamanho dos dados excede 64 MB.

O exemplo a seguir demonstra usando **obter\_blob\_para\_caminho** baixar o conteúdo do blob **myblob** e armazená-lo para o arquivo de **saída-sunset.png** .

    block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')

## <a name="delete-a-blob"></a>Excluir um blob

Finalmente, para excluir um blob, chame **delete_blob**.

    block_blob_service.delete_blob('mycontainer', 'myblockblob')

## <a name="writing-to-an-append-blob"></a>Gravar um blob de acréscimo

Um blob de acréscimo é otimizado para operações de acréscimo, como log. Como um blob de bloco, um blob de acréscimo é composto dos blocos, mas quando você adiciona um novo bloco para um blob de acréscimo, sempre é acrescentado ao final do blob. Você não pode atualizar ou excluir um bloco existente em um blob de acréscimo. O bloco de IDs para um blob de acréscimo não estão expostos como elas são para um blob de bloco.

Cada bloco em um blob de acréscimo pode ser um tamanho diferente, até no máximo 4 MB, e um blob de acréscimo pode incluir um máximo de 50.000 blocos. O tamanho máximo de um blob de acréscimo, portanto, é um pouco mais de 195 GB (4 MB X 50.000 blocos).

O exemplo a seguir cria um novo blob de acréscimo e acrescenta alguns dados, simular uma operação de log simples.

    from azure.storage.blob import AppendBlobService
    append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

    # The same containers can hold all types of blobs
    append_blob_service.create_container('mycontainer')

    # Append blobs must be created before they are appended to
    append_blob_service.create_blob('mycontainer', 'myappendblob')
    append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

    append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu as Noções básicas do armazenamento de Blob, siga estes links para saber mais.

- [Central de desenvolvedores do Python](/develop/python/)
- [Serviços de armazenamento do Azure API REST](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog da equipe de armazenamento do Azure]
- [Armazenamento do Microsoft Azure SDK para Python]

[Blog da equipe de armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Armazenamento do Microsoft Azure SDK para Python]: https://github.com/Azure/azure-storage-python
