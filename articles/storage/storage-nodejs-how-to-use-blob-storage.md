<properties
    pageTitle="Como usar o armazenamento de Blob do Node | Microsoft Azure"
    description="Armazene dados não estruturados na nuvem com o armazenamento de Blob do Azure (armazenamento de objeto)."
    services="storage"
    documentationCenter="nodejs"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>



# <a name="how-to-use-blob-storage-from-nodejs"></a>Como usar o armazenamento de Blob do Node

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Visão geral

Este artigo mostra como executar cenários comuns usando o armazenamento de Blob. Os exemplos são gravados por meio da API do Node. Os cenários cobertos incluem como carregar, lista, baixar e excluir blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Criar um aplicativo de Node

Para obter instruções sobre como criar um aplicativo de Node, consulte [criar um aplicativo da web Node no serviço de aplicativo do Azure], [criar e implantar um aplicativo Node para um serviço de nuvem do Azure] – usando o Windows PowerShell ou [Construir e implantar um aplicativo de web Node no Azure usando Web Matrix].

## <a name="configure-your-application-to-access-storage"></a>Configurar seu aplicativo para acessar o armazenamento

Para usar o armazenamento do Azure, é necessário o SDK de armazenamento do Azure para Node. js, que inclui um conjunto de bibliotecas de conveniência que se comunicar com os serviços de armazenamento restante.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Use o Gerenciador de pacote de nó (NPM) para obter o pacote

1.  Use uma interface de linha como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix), para navegar até a pasta onde você criou seu aplicativo de amostra.

2.  Na janela de comando, digite **npm instalar o armazenamento do azure** . Saída do comando é semelhante ao seguinte exemplo de código.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  Você pode executar manualmente o comando **ls** para verificar se um **nó\_módulos** pasta foi criada. Dentro dessa pasta, localize o pacote de **armazenamento do azure** , que contém as bibliotecas que você precisa para acessar o armazenamento.

### <a name="import-the-package"></a>Importar o pacote

Usando o bloco de notas ou outro editor de texto, adicione o seguinte à parte superior do arquivo **Server. js** do aplicativo onde você pretende usar o armazenamento:

    var azure = require('azure-storage');

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma conexão de armazenamento do Azure

O módulo Azure lerá as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY`, ou `AZURE_STORAGE_CONNECTION_STRING`, para informações necessárias para conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, especifique as informações da conta ao chamar **createBlobService**.

Para obter um exemplo de definir as variáveis de ambiente no [Portal do Azure](https://portal.azure.com) para um aplicativo web Azure, consulte [Node web app usando o serviço de tabela do Azure].

## <a name="create-a-container"></a>Criar um contêiner

O objeto **BlobService** permite que você trabalhar com contêineres e blobs. O código a seguir cria um objeto **BlobService** . Adicione o seguinte na parte superior da **Server. js**:

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE] Você pode acessar um blob anonimamente, usando **createBlobServiceAnonymous** e fornecendo o endereço do host. Por exemplo, use `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Para criar um novo recipiente, use **createContainerIfNotExists**. O exemplo de código a seguir cria um novo recipiente chamado 'mycontainer':

    blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
        if(!error){
          // Container exists and is private
        }
    });

Se o contêiner é recém-criado `result.created` for verdadeira. Se o contêiner já existir, `result.created` for falsa. `response`contém informações sobre a operação, incluindo as informações de ETag para o contêiner.

### <a name="container-security"></a>Segurança de contêiner

Por padrão, novos contêineres são particulares e não podem ser acessados anonimamente. Para tornar o contêiner público para que você possa acessar anonimamente, você pode definir o nível de acesso do contêiner **blob** ou **contêiner**.

* **blob** - permite o acesso anônimo de leitura a blob conteúdo e metadados dentro deste contêiner, mas não aos metadados de contêiner como listando todos os blobs dentro de um contêiner

* **contêiner** - permite o acesso de leitura anônimo blob conteúdo e metadados, bem como metadados de contêiner

O exemplo de código a seguir demonstra definindo o nível de acesso para **blob**:

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
        if(!error){
          // Container exists and allows
          // anonymous read access to blob
          // content and metadata within this container
        }
    });

Como alternativa, você pode modificar o nível de acesso de um contêiner, usando **setContainerAcl** para especificar o nível de acesso. O exemplo de código a seguir altera o nível de acesso ao contêiner:

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
      if(!error){
        // Container access level set to 'container'
      }
    });

O resultado contém informações sobre a operação, incluindo a **ETag** atual para o contêiner.

### <a name="filters"></a>Filtros

Você pode aplicar operações de filtragem opcionais para operações executadas usando **BlobService**. Operações de filtragem pode incluir log automaticamente repetindo, etc. Filtros são objetos que implementam um método com a assinatura:

    function handle (requestOptions, next)

Depois de fazer seu pré-processamento na lista Opções de solicitação, o método precisa chamar "next", passando um retorno de chamada com a assinatura a seguir:

    function (returnObject, finalCallback, next)

Esse retorno de chamada e, depois de processar a returnObject (a resposta da solicitação de servidor), o retorno de chamada precisa para invocar Avançar se existir para continuar processando outros filtros ou simplesmente invocar finalCallback para encerrar a chamada de serviço.

Dois filtros que implementam lógica de repetição são fornecidos com o SDK do Azure para Node, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. A seguir cria um objeto de **BlobService** que usa o **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobSvc = azure.createBlobService().withFilter(retryOperations);

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob em um contêiner

Há três tipos de blobs: bloquear blobs, blobs de página e acrescentar blobs. Bloco blobs permitem que mais eficiente carregar grandes de dados. Acrescentar blobs são otimizados para acrescentar operações. Página blobs são otimizados para operações de leitura/gravação. Para obter mais informações, consulte [Noções básicas sobre bloco Blobs, Blobs acrescentar e Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### <a name="block-blobs"></a>Blobs de bloco

Para carregar dados em um blob de bloco, use o seguinte:

* **createBlockBlobFromLocalFile** - cria um novo blob de bloco e carrega o conteúdo de um arquivo

* **createBlockBlobFromStream** - cria um novo blob de bloco e carrega o conteúdo de um fluxo

* **createBlockBlobFromText** - cria um novo blob de bloco e carrega o conteúdo de uma cadeia de caracteres

* **createWriteStreamToBlockBlob** - fornece um fluxo de gravação para um blob de bloco

O exemplo de código a seguir carrega o conteúdo do arquivo **Test. txt** em **myblob**.

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

O `result` retornado por esses métodos contém informações sobre a operação, como a **ETag** do blob.

### <a name="append-blobs"></a>Acrescentar blobs

Para carregar dados para um novo blob de acréscimo, use o seguinte:

* **createAppendBlobFromLocalFile** - cria um novo blob de acréscimo e carrega o conteúdo de um arquivo

* **createAppendBlobFromStream** - cria um novo blob de acréscimo e carrega o conteúdo de um fluxo

* **createAppendBlobFromText** - cria um novo blob de acréscimo e carrega o conteúdo de uma cadeia de caracteres

* **createWriteStreamToNewAppendBlob** - cria um novo blob de acréscimo e fornece um fluxo de gravação para ele

O exemplo de código a seguir carrega o conteúdo do arquivo **Test. txt** em **myappendblob**.

    blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

Para acrescentar um bloco para um blob de acréscimo existente, use o seguinte:

* **appendFromLocalFile** - acrescentar o conteúdo de um arquivo a um blob de acréscimo existente

* **appendFromStream** - acrescentar o conteúdo de um fluxo da um blob de acréscimo existente

* **appendFromText** - acrescentar o conteúdo de uma cadeia de caracteres para um blob de acréscimo existente

* **appendBlockFromStream** - acrescentar o conteúdo de um fluxo da um blob de acréscimo existente

* **appendBlockFromText** - acrescentar o conteúdo de uma cadeia de caracteres para um blob de acréscimo existente

> [AZURE.NOTE] appendFromXXX APIs fará algumas validação do lado do cliente para falhar rapidamente evitar a chamada de servidor de unncessary. não appendBlockFromXXX.

O exemplo de código a seguir carrega o conteúdo do arquivo **Test. txt** em **myappendblob**.

    blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
      if(!error){
        // text appended
      }
    });


### <a name="page-blobs"></a>Blobs de página

Para carregar dados em um blob de página, use o seguinte:

* **createPageBlob** - cria um novo blob de página de um tamanho específico

* **createPageBlobFromLocalFile** - cria um novo blob de página e carrega o conteúdo de um arquivo

* **createPageBlobFromStream** - cria um novo blob de página e carrega o conteúdo de um fluxo

* **createWriteStreamToExistingPageBlob** - fornece um fluxo de gravação para um blob de página existente

* **createWriteStreamToNewPageBlob** - cria um novo blob de página e, em seguida, fornece um fluxo de gravação para ele

O exemplo de código a seguir carrega o conteúdo do arquivo **Test. txt** em **mypageblob**.

    blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

> [AZURE.NOTE] Página blobs consistem em 512 bytes 'Páginas'. Você receberá um erro ao carregar dados com um tamanho que não é um múltiplo de 512.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Para listar os blobs em um contêiner, use o método **listBlobsSegmented** . Se você quiser retornar blobs com um prefixo específico, use **listBlobsSegmentedWithPrefix**.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
          // result.entries contains the entries
          // If not all blobs were returned, result.continuationToken has the continuation token.
      }
    });

O `result` contém um `entries` conjunto, que é uma matriz de objetos que descrevem cada blob. Se todos os blobs não podem ser retornados, o `result` também fornece uma `continuationToken`, que você pode usar como o segundo parâmetro para recuperar entradas adicionais.

## <a name="download-blobs"></a>Baixar blobs

Para baixar dados de um blob, use o seguinte:

* **getBlobToLocalFile** - grava o conteúdo de blob ao arquivo

* **getBlobToStream** - grava o conteúdo de blob um fluxo

* **getBlobToText** - grava o conteúdo de blob uma cadeia de caracteres

* **createReadStream** - fornece um fluxo de leiam o blob

O exemplo de código a seguir demonstra usando **getBlobToStream** para baixar o conteúdo do blob **myblob** e armazená-lo para o arquivo **txt** usando um fluxo:

    var fs = require('fs');
    blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
      if(!error){
        // blob retrieved
      }
    });

O `result` contém informações sobre o blob, incluindo informações de **ETag** .

## <a name="delete-a-blob"></a>Excluir um blob

Finalmente, para excluir um blob, chame **deleteBlob**. O exemplo de código a seguir exclui o blob denominado **myblob**.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
      if(!error){
        // Blob has been deleted
      }
    });

## <a name="concurrent-access"></a>Acesso simultâneo

Para dar suporte ao acesso simultâneo em um blob de vários clientes ou várias instâncias de processo, você pode usar **ETags** ou **concessões**.

* **Etag** - fornece uma maneira de detectar que o blob ou contêiner foi modificado por outro processo

* **Concessão** - fornece uma maneira de obter exclusivo, renovável, escrever ou excluir o acesso a um blob para um período de tempo

### <a name="etag"></a>ETag

Usar ETags se é preciso permitir que vários clientes ou instâncias gravar o bloco de página ou Blob Blob simultaneamente. A ETag permitirá determinar se o contêiner ou blob foi modificado desde inicialmente ler ou criou, que permite que você evitar substituir alterações confirmadas por outro cliente ou processo.

Você pode definir condições de ETag usando opcional `options.accessConditions` parâmetro. O exemplo de código a seguir carrega apenas o arquivo de **Test. txt** se o blob já existe e tem o valor de ETag contido por `etagToMatch`.

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
        if(!error){
        // file uploaded
      }
    });

Quando você estiver usando ETags, o padrão geral é:

1. Obter a ETag como resultado de uma operação de get, lista ou criar.

2. Execute uma ação, verificando que o valor de ETag não foi modificado.

Se o valor foi modificado, isso indica que outro cliente ou instância modificada o blob ou recipiente desde que você adquiriu o valor de ETag.

### <a name="lease"></a>Concessão

Você pode adquirir uma nova concessão por usando o método **acquireLease** , especificando o blob ou recipiente que você deseja obter uma concessão em. Por exemplo, o seguinte código adquire uma concessão em **myblob**.

    blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
      if(!error) {
        console.log('leaseId: ' + result.id);
      }
    });

Operações posteriores **myblob** devem fornecer a `options.leaseId` parâmetro. A concessão ID é retornado como `result.id` de **acquireLease**.

> [AZURE.NOTE] Por padrão, a duração da concessão é infinita. Você pode especificar uma duração de não infinita (entre 15 e 60 segundos), fornecendo a `options.leaseDuration` parâmetro.

Para remover uma concessão, use **releaseLease**. Para quebrar uma concessão, mas impedir que outras pessoas obtendo uma nova concessão até que a duração original expirou, use **breakLease**.

## <a name="work-with-shared-access-signatures"></a>Trabalhar com assinaturas de acesso compartilhado

Assinaturas de acesso compartilhado (SAS) são uma maneira segura para fornecer acesso granular blobs e contêineres sem fornecer o seu nome de conta de armazenamento ou teclas. Assinaturas de acesso compartilhado são usadas com frequência para fornecer acesso limitado aos seus dados, como permitir que um aplicativo móvel acessar blobs.

> [AZURE.NOTE] Enquanto você também pode permitir o acesso anônimo a blobs, acesso compartilhado assinaturas permitem que você forneça mais acesso controlado, como você deve gerar as associações de segurança.

Um aplicativo confiável como um serviço baseado em nuvem gera assinaturas de acesso compartilhado usando o **generateSharedAccessSignature** da **BlobService**e fornece-o a um aplicativo de confiança parcial ou não confiável como um aplicativo móvel. Acesso assinaturas são geradas usando uma política, que descreve o início e término durante o qual as assinaturas de acesso compartilhado são válidas compartilhado, bem como o nível de acesso concedido do suporte de assinaturas de acesso compartilhado.

O exemplo de código a seguir gera uma nova política de acesso compartilhado que permite que o portador de assinaturas de acesso compartilhado executar operações de leitura no blob **myblob** e expira 100 minutos após o horário em que ele é criado.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
    var host = blobSvc.host;

Observe que as informações do host devem ser fornecidas também, pois é necessária quando o proprietário de assinaturas de acesso compartilhado tenta acessar o contêiner.

O aplicativo cliente usa assinaturas de acesso compartilhado com **BlobServiceWithSAS** para executar operações contra o blob. A seguir obtém informações sobre **myblob**.

    var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
    sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
      if(!error) {
        // retrieved info
      }
    });

Desde que as assinaturas de acesso compartilhado geradas com acesso somente leitura, se uma tentativa para modificar o blob, um erro será retornado.

### <a name="access-control-lists"></a>Listas de controle de acesso

Você também pode usar uma lista de controle de acesso (ACL) para definir a política de acesso Associações de. Isso é útil se você quiser permitir que vários clientes acessar um contêiner, mas oferece políticas de acesso diferente para cada cliente.

Uma ACL é implementada usando uma matriz de políticas de acesso, com uma identificação associada a cada diretiva. O exemplo de código a seguir define duas diretivas, uma para 'Usuário1' e outro para 'Usuário2':

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
        Start: startDate,
        Expiry: expiryDate
      }
    };

O exemplo de código a seguir obtém a ACL atual para **mycontainer**e depois adiciona as novas diretivas usando **setBlobAcl**. Essa abordagem permite:

    var extend = require('extend');
    blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Quando a ACL estiver definida, você pode criar assinaturas de acesso compartilhado com base na identificação de uma política. O exemplo de código a seguir cria novas assinaturas de acesso compartilhado para 'Usuário2':

    blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes recursos.

-   [Armazenamento do Azure SDK para referência de API de nó][]
-   [Blog da equipe de armazenamento do Azure][]
-   Repositório de [SDK de armazenamento do Azure para nó][] no GitHub
-   [Central de desenvolvedores do Node](/develop/nodejs/)
-   [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md)

[Armazenamento do Azure SDK para nó]: https://github.com/Azure/azure-storage-node

[Criar um aplicativo da web de Node no serviço de aplicativo do Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
[Node web app usando o serviço de tabela do Azure]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md
[Construir e implantar um aplicativo de web Node para Azure usando Web Matrix]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure Portal]: https://portal.azure.com
[Criar e implantar um aplicativo Node para um serviço de nuvem do Azure]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Blog da equipe de armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Armazenamento do Azure SDK para referência de API de nó]: http://dl.windowsazure.com/nodestoragedocs/index.html
