<properties
    pageTitle="Como usar o armazenamento de Blob do Azure do iOS | Microsoft Azure"
    description="Armazene dados não estruturados na nuvem com o armazenamento de Blob do Azure (armazenamento de objeto)."
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="micurd"/>

# <a name="how-to-use-blob-storage-from-ios"></a>Como usar o armazenamento de Blob do iOS

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Visão geral

Este artigo mostrará como executar cenários comuns usando o armazenamento de Blob do Microsoft Azure. Os exemplos são gravados em objetivo-C e usam a [Biblioteca de cliente de armazenamento do Azure para iOS](https://github.com/Azure/azure-storage-ios). Os cenários cobertos incluem **Carregando**, **Listando**, **baixando**e **Excluindo** blobs. Para obter mais informações sobre blobs, consulte a seção [Próximas etapas](#next-steps) . Você também pode baixar o [aplicativo de amostra](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) para ver rapidamente o uso de armazenamento do Azure em um aplicativo do iOS.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importar a biblioteca de iOS de armazenamento do Azure para seu aplicativo

Você pode importar a biblioteca de iOS de armazenamento do Azure para seu aplicativo usando o [CocoaPod de armazenamento do Azure](https://cocoapods.org/pods/AZSClient) ou importando o arquivo **Framework** .

## <a name="cocoapod"></a>CocoaPod

1. Se você ainda não fez isso, [Instale CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) em seu computador abrindo uma janela de terminal e executando o seguinte comando

        sudo gem install cocoapods

2. Próximo, no diretório do projeto (o diretório que contém seu `.xcodeproj` arquivo), crie um novo arquivo chamado `Podfile`(sem extensão de arquivo). Adicione o seguinte ao `Podfile` e salvar

        pod 'AZSClient'

3. Na janela terminal, navegue até o diretório do projeto e execute o seguinte comando

        pod install

4. Se seu `.xcodeproj` estiver aberto no Xcode, fechá-lo. No diretório do projeto, abra o arquivo de projeto recém-criado que terá a `.xcworkspace` extensão. Este é o arquivo que você trabalhará partir de agora em.

## <a name="framework"></a>Framework
Para poder usar a biblioteca de iOS de armazenamento do Azure, primeiro você precisará criar o arquivo framework.

1. Primeiro, baixe ou clonar o [azure-armazenamento-ios repo](https://github.com/azure/azure-storage-ios).

2. Vá para o *azure-armazenamento-ios* -> *biblioteca* -> *Biblioteca de cliente de armazenamento do Azure*e abra `AZSClient.xcodeproj` no Xcode.

3. No canto esquerdo superior da Xcode, altere o esquema active de "Biblioteca de cliente de armazenamento do Azure" para "Framework".

4. Crie o projeto (⌘ + B). Isso criará uma `AZSClient.framework` arquivo na sua área de trabalho.

Você pode importar o arquivo framework para seu aplicativo fazendo o seguinte:

1. Criar um novo projeto ou abra o seu projeto existente no Xcode.

2. Clique no seu projeto no painel de navegação esquerdo e clique na guia *Geral* na parte superior do editor de projeto.

3. Na seção *vinculada estruturas e bibliotecas* , clique no botão Adicionar (+).

4. Clique em *Adicionar outro...*. Navegue até e adicione o `AZSClient.framework` arquivo que você acabou de criar.

5. Na seção *vinculada estruturas e bibliotecas* , clique no botão Adicionar (+) novamente.

6. Na lista de bibliotecas já fornecido, procure por `libxml2.2.dylib` e adicioná-la ao seu projeto.

7. Clique na guia *Criar configurações* na parte superior do editor de projeto.

8. Na seção *Caminhos de pesquisa* , clique duas vezes em *Caminhos de pesquisa de Framework* e adicione o caminho para sua `AZSClient.framework` arquivo.

## <a name="import-statement"></a>Declaração de importação
Você precisa incluir a seguinte instrução de importação no arquivo onde você deseja chamar a API de armazenamento do Azure.

    // Include the following import statement to use blob APIs.
    #import <AZSClient/AZSClient.h>

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Operações assíncronas
> [AZURE.NOTE] Todos os métodos que executam uma solicitação ao serviço são operações assíncronas. Os exemplos de código, você encontrará que esses métodos têm um manipulador de conclusão. Código de dentro do manipulador de conclusão será executado **depois que** a solicitação seja concluída. Código após o manipulador de conclusão será executado **enquanto** a solicitação está sendo feito.

## <a name="create-a-container"></a>Criar um contêiner
Cada blob em armazenamento Azure deve residem em um contêiner. O exemplo a seguir mostra como criar um contêiner, chamado *newcontainer*, na sua conta de armazenamento se ele ainda não exista. Ao escolher um nome para seu contêiner, lembre-se das regras de nomenclatura mencionadas acima.

    -(void)createContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

      // Create container in your Storage account if the container doesn't already exist
      [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
         if (error){
             NSLog(@"Error in creating container.");
         }
      }];
    }

Você pode confirmar que isso funciona olhando para o [Gerenciador de armazenamento do Microsoft Azure](http://storageexplorer.com) e verificando que *newcontainer* está na lista de contêineres para a sua conta de armazenamento.

## <a name="set-container-permissions"></a>Definir permissões de contêiner
Permissões de um contêiner são configuradas para acesso **privado** por padrão. Entretanto, contêineres fornecem algumas opções diferentes para acesso de contêiner:

- **Particular**: contêiner e blob dados podem ser lidos pelo somente o proprietário da conta.

- **Blob**: dados Blob dentro deste recipiente podem ser lidos por meio de solicitação anônima, mas dados do contêiner não estão disponíveis. Clientes não é possível enumerar blobs dentro do contêiner via solicitação anônima.

- **Contêiner**: contêiner e blob dados podem ser lidos por meio de solicitação anônima. Clientes podem enumerar blobs dentro do contêiner via solicitação anônima, mas não é possível enumerar contêineres dentro da conta de armazenamento.

O exemplo a seguir mostra como criar um contêiner com permissões de acesso de **contêiner** que permitirão acesso público e somente leitura para todos os usuários na Internet:

    -(void)createContainerWithPublicAccess{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob em um contêiner
Conforme mencionado na seção [conceitos de serviços de Blob](#blob-service-concepts) , o armazenamento de Blob oferece três tipos diferentes de blobs: bloquear blobs, acrescentar blobs e blobs de página. Neste momento, a biblioteca de iOS de armazenamento do Azure suporta apenas blobs de bloco. Na maioria dos casos, o blob de bloco é o tipo recomendado para usar.

O exemplo a seguir mostra como carregar um blob de bloco de um NSString. Se já existe um blob com o mesmo nome neste contêiner, o conteúdo desse blob será substituído.

    -(void)uploadBlobToContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
             else{
                 // Create a local blob object
                 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                 // Upload blob to Storage
                 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                     if (error){
                         NSLog(@"Error in creating blob.");
                     }
                 }];
             }
         }];
    }

Você pode confirmar que isso funciona olhando para o [Gerenciador de armazenamento do Microsoft Azure](http://storageexplorer.com) e verificando que o contêiner, *containerpublic*, contém o blob, *sampleblob*. Neste exemplo, usamos um contêiner de público, então você também pode verificar se isso funcionou indo para os blobs URI:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Além de carregar um blob de bloco de um NSString, existem métodos semelhantes para NSData, NSInputStream ou um arquivo local.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner
O exemplo a seguir mostra como todos os blobs em um contêiner de lista. Ao executar essa operação, lembre-se dos parâmetros a seguir:     

- **continuationToken** - o token de continuação representa onde a operação de listagem deve começar. Se nenhum token for fornecido, ele listará blobs desde o começo. Qualquer número de blobs pode ser listado, de zero até um máximo de conjunto. Mesmo se esse método retorna resultados de zero, se `results.continuationToken` não é zero, pode haver mais bolhas no serviço que não foram listadas.
- **prefixo** - você pode especificar o prefixo a ser usado para listagem de blob. Somente as bolhas que começam com esse prefixo serão listadas.
- **useFlatBlobListing** - conforme mencionado na seção [nomenclatura e referência contêineres e blobs](#naming-and-referencing-containers-and-blobs) , embora o serviço de Blob é um esquema de armazenamento simples, você pode criar uma hierarquia virtual nomeando blobs com informações de caminho. No entanto, não plana listagem não é suportada atualmente; Isso é em breve. Por agora, esse valor deve ser`YES`
- **blobListingDetails** - você pode especificar quais itens para incluir ao listando blobs
    - `AZSBlobListingDetailsNone`: Somente confirmadas blobs de lista e não retornam blob metadados.
    - `AZSBlobListingDetailsSnapshots`: Lista compromisso blobs e blob instantâneos.
    - `AZSBlobListingDetailsMetadata`: Metadados de blob recuperar para cada blob retornado na listagem.
    - `AZSBlobListingDetailsUncommittedBlobs`: Liste blobs dedicadas e não confirmados.
    - `AZSBlobListingDetailsCopy`: Inclua copiar propriedades na listagem.
    - `AZSBlobListingDetailsAll`: Lista todos os blobs confirmadas disponíveis, blobs não confirmados e instantâneos e retornar todos os status de cópia e de metadados para esses blobs.
- **maxResults** - o número máximo de resultados para retornar para a operação. Use -1 para não definir um limite.
- **completionHandler** - o bloco de código seja executado com os resultados da operação de listagem.

Neste exemplo, um método auxiliar é usado para chamada repetidamente a lista blobs método sempre que um token de continuação é retornado.

    -(void)listBlobsInContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        //List all blobs in container
        [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
            if (error != nil){
                NSLog(@"Error in creating container.");
            }
        }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
        [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
            if (error)
            {
                completionHandler(error);
            }
            else
            {
                for (int i = 0; i < results.blobs.count; i++) {
                    NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
                }
                if (results.continuationToken)
                {
                    [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
                }
                else
                {
                    completionHandler(nil);
                }
            }
        }];
    }


## <a name="download-a-blob"></a>Baixe um blob

O exemplo a seguir mostra como baixar um blob para um objeto de NSString.

    -(void)downloadBlobToString{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
            else{
                NSLog(@"%@",text);
            }
        }];
    }

## <a name="delete-a-blob"></a>Excluir um blob

O exemplo a seguir mostra como excluir um blob.

    -(void)deleteBlob{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
            if (error) {
                NSLog(@"Error in deleting blob.");
            }
        }];
    }

## <a name="delete-a-blob-container"></a>Excluir um contêiner de blob

O exemplo a seguir mostra como excluir um contêiner.

    -(void)deleteContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      // Delete container
      [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
         if(error){
             NSLog(@"Error in deleting container");
         }
      }];
    }

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como usar o armazenamento de Blob do iOS, siga estes links para saber mais sobre a biblioteca do iOS e o serviço de armazenamento.

- [Biblioteca de cliente de armazenamento Azure para iOS](https://github.com/azure/azure-storage-ios)
- [IOS de armazenamento Azure documentação de referência](http://azure.github.io/azure-storage-ios/)
- [Serviços de armazenamento do Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog da equipe de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage)

Se você tiver dúvidas sobre esta biblioteca à vontade postar nosso [Fórum do MSDN Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) ou [Estouro de pilha](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Se você tiver sugestões de recurso de armazenamento do Azure, envie aos [Comentários de armazenamento do Azure](https://feedback.azure.com/forums/217298-storage/).
