<properties
    pageTitle="Gerenciar o acesso anônimo de leitura para contêineres e blobs | Microsoft Azure"
    description="Saiba como disponibilizar contêineres e blobs para acesso anônimo e como acessá-los por programação."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Gerenciar o acesso anônimo de leitura para contêineres e blobs

## <a name="overview"></a>Visão geral

Por padrão, somente o proprietário da conta de armazenamento pode acessar os recursos de armazenamento dentro dessa conta. Para o armazenamento de Blob somente, você pode definir permissões de um contêiner permitir acesso anônimo de leitura para o contêiner e seus blobs, para que você pode conceder acesso aos recursos sem compartilhar sua chave da conta.

Acesso anônimo é melhor para cenários onde você deseja que determinados blobs sempre esteja disponível para acesso anônimo de leitura. Para um controle mais refinados, você pode criar uma assinatura de acesso compartilhado, que permite o acesso de representante restringido usando permissões diferentes e em um intervalo de tempo especificado. Para obter mais informações sobre como criar assinaturas de acesso compartilhado, consulte [Assinaturas usando de acesso compartilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Conceder permissões de usuários anônimos para contêineres e blobs

Por padrão, um contêiner e qualquer blobs dentro dela podem ser acessadas somente pelo proprietário da conta de armazenamento. Para dar permissões de leitura para um contêiner e seus blobs de usuários anônimos, você pode definir as permissões de contêiner para permitir acesso público. Usuários anônimos podem ler blobs dentro de um contêiner acessível publicamente sem a solicitação de autenticação.

Os contêineres fornecem as seguintes opções para gerenciar o acesso de contêiner:

- **Acesso de leitura público completo:** Dados de contêiner e blob podem ser lidos por meio de solicitação anônima. Clientes podem enumerar blobs dentro do contêiner via solicitação anônima, mas não é possível enumerar contêineres dentro da conta de armazenamento.

- **Acesso para blobs só de leitura de público:** Dados de blob dentro deste recipiente podem ser lidos por meio de solicitação anônima, mas dados do contêiner não estão disponíveis. Clientes não é possível enumerar blobs dentro do contêiner via solicitação anônima.

- **Nenhum public o acesso de leitura:** Dados de contêiner e blob podem ser lidos pelo somente o proprietário da conta.

Você pode definir permissões de contêiner das seguintes maneiras:

- A partir do [Portal do Azure](https://portal.azure.com).
- Programaticamente, usando a biblioteca de cliente do armazenamento ou a API REST.
- Usando o PowerShell. Para saber sobre como definir permissões de contêiner do Azure PowerShell, consulte [Usando o PowerShell do Azure com o armazenamento do Azure](storage-powershell-guide-full.md#how-to-manage-azure-blobs).

### <a name="setting-container-permissions-from-the-azure-portal"></a>Definindo permissões de contêiner do Portal do Azure

Para definir permissões de recipiente a partir do [Portal do Azure](https://portal.azure.com), siga estas etapas:

1. Navegue até o painel de controle para sua conta de armazenamento.
2. Selecione o nome de contêiner na lista. Clicando no nome expõe os blobs no contêiner de escolhido
3. Selecione a **política de acesso** da barra de ferramentas.
4. No campo **tipo de acesso** , selecione o nível de permissões desejado, conforme mostrado na captura de tela abaixo.

    ![Caixa de diálogo de metadados de contêiner editar](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="setting-container-permissions-programmatically-using-net"></a>Definindo permissões de contêiner programaticamente usando .NET

Para definir permissões para um contêiner usando a biblioteca de cliente .NET, recupere permissões existentes do contêiner chamando o método **GetPermissions** . Defina a propriedade **PublicAccess** para o objeto **BlobContainerPermissions** que é retornado pelo método **GetPermissions** . Finalmente, chame o método **SetPermissions** com as permissões atualizadas.

O exemplo a seguir define as permissões do contêiner para acesso de leitura público completa. Para definir permissões para o público acesso de leitura para blobs somente, defina a propriedade de **PublicAccess** para **BlobContainerPublicAccessType.Blob**. Para remover todas as permissões para usuários anônimos, defina a propriedade como **BlobContainerPublicAccessType.Off**.

    public static void SetPublicContainerPermissions(CloudBlobContainer container)
    {
        BlobContainerPermissions permissions = container.GetPermissions();
        permissions.PublicAccess = BlobContainerPublicAccessType.Container;
        container.SetPermissions(permissions);
    }

## <a name="access-containers-and-blobs-anonymously"></a>Acessar contêineres e blobs anonimamente

Um cliente que acessa contêineres e blobs anonimamente pode usar construtores que não necessitam de credenciais. Os exemplos a seguir mostram algumas maneiras diferentes para referenciar recursos de serviço de Blob anonimamente.

### <a name="create-an-anonymous-client-object"></a>Criar um objeto de cliente anônimo

Você pode criar um novo objeto de cliente de serviço para acesso anônimo, fornecendo o ponto de extremidade do serviço Blob da conta. No entanto, você também deve saber o nome de um contêiner nessa conta que está disponível para acesso anônimo.

    public static void CreateAnonymousBlobClient()
    {
        // Create the client object using the Blob service endpoint.
        CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

        // Read the container's properties. Note this is only possible when the container supports full public read access.
        container.FetchAttributes();
        Console.WriteLine(container.Properties.LastModified);
        Console.WriteLine(container.Properties.ETag);
    }

### <a name="reference-a-container-anonymously"></a>Fazer referência a um contêiner de maneira anônima

Se você tiver a URL para um contêiner anonimamente disponível, você pode usá-lo para referenciar o contêiner diretamente.

    public static void ListBlobsAnonymously()
    {
        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

        // List blobs in the container.
        foreach (IListBlobItem blobItem in container.ListBlobs())
        {
            Console.WriteLine(blobItem.Uri);
        }
    }


### <a name="reference-a-blob-anonymously"></a>Referenciar um blob anonimamente

Se você tiver a URL para um blob que está disponível para acesso anônimo, você pode fazer referência ao blob diretamente usando essa URL:

    public static void DownloadBlobAnonymously()
    {
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
        blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
    }

## <a name="features-available-to-anonymous-users"></a>Recursos disponíveis para usuários anônimos

A tabela a seguir mostra quais operações podem ser chamadas por usuários anônimos quando ACL de um contêiner estiver configurado para permitir acesso público.

| Operação de restante                                         | Permissão com acesso de leitura público completa | Permissão com acesso de leitura público para blobs somente |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| Lista contêineres                                        | Somente o proprietário                              | Somente o proprietário                                        |
| Criar contêiner                                       | Somente o proprietário                              | Somente o proprietário                                        |
| Obter propriedades de contêiner                               | Todos os                                     | Somente o proprietário                                        |
| Obter metadados de contêiner                                 | Todos os                                     | Somente o proprietário                                        |
| Definir metadados de contêiner                                 | Somente o proprietário                              | Somente o proprietário                                        |
| Obtenha o contêiner ACL                                      | Somente o proprietário                              | Somente o proprietário                                        |
| Definir o contêiner ACL                                      | Somente o proprietário                              | Somente o proprietário                                        |
| Excluir contêiner                                       | Somente o proprietário                              | Somente o proprietário                                        |
| Blobs de lista                                             | Todos os                                     | Somente o proprietário                                        |
| Colocar Blob                                               | Somente o proprietário                              | Somente o proprietário                                        |
| Obter Blob                                               | Todos os                                     | Todos os                                               |
| Obter propriedades de Blob                                    | Todos os                                     | Todos os                                               |
| Definir propriedades de Blob                                    | Somente o proprietário                              | Somente o proprietário                                        |
| Obter metadados de Blob                                      | Todos os                                     | Todos os                                               |
| Definir metadados de Blob                                      | Somente o proprietário                              | Somente o proprietário                                        |
| Colocar o bloco                                              | Somente o proprietário                              | Somente o proprietário                                        |
| Obter a lista de bloqueios (somente blocos confirmados)                 | Todos os                                     | Todos os                                               |
| Obter a lista de bloqueios (somente blocos não confirmados ou todos os blocos) | Somente o proprietário                              | Somente o proprietário                                        |
| Colocar a lista de bloqueios                                         | Somente o proprietário                              | Somente o proprietário                                        |
| Excluir Blob                                            | Somente o proprietário                              | Somente o proprietário                                        |
| Copiar Blob                                              | Somente o proprietário                              | Somente o proprietário                                        |
| Blob de instantâneo                                          | Somente o proprietário                              | Somente o proprietário                                        |
| Concessão Blob                                             | Somente o proprietário                              | Somente o proprietário                                        |
| Colocar a página                                               | Somente o proprietário                              | Somente o proprietário                                        |
| Obter intervalos de página                                        | Todos os                                     | Todos os                                                  |
| Acrescentar Blob                                            | Somente o proprietário                              | Somente o proprietário                                                  |


## <a name="see-also"></a>Consulte também

- [Autenticação para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [Usando assinaturas de acesso compartilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md)
- [Delegar acesso com uma assinatura de acesso compartilhado](https://msdn.microsoft.com/library/azure/ee395415.aspx)
