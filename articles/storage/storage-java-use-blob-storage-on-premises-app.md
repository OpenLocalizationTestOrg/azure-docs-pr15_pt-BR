<properties
    pageTitle="Aplicativo com armazenamento de blob (Java) local | Microsoft Azure"
    description="Aprenda a criar um aplicativo de console que carrega uma imagem para Azure e exibe a imagem em seu navegador. Exemplos de código em Java."
    services="storage"
    documentationCenter="java"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="on-premises-application-with-blob-storage"></a>Aplicativo de local com armazenamento de blob

## <a name="overview"></a>Visão geral

O exemplo a seguir mostra como você pode usar o armazenamento do Azure para armazenar imagens no Azure. O código neste artigo é para um aplicativo de console que carrega uma imagem para Azure e, em seguida, cria um arquivo HTML que exibe a imagem em seu navegador.

## <a name="prerequisites"></a>Pré-requisitos

- Um Kit de desenvolvedor de Java (JDK), versão 1,6 ou posterior, está instalado.
- O SDK do Azure está instalado.
- O JAR para as bibliotecas do Azure para Java e JARs qualquer dependência aplicável, são instalados e estão no caminho compilação usado pelo seu compilador Java. Para obter informações sobre como instalar as bibliotecas do Azure para Java, consulte [o SDK do Azure para Java de Download](java-download-azure-sdk.md).
- Uma conta de armazenamento do Azure tiver sido configurada. O nome de conta e chave da conta para a conta de armazenamento serão usados pelo código neste artigo. Veja [como criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account) para obter informações sobre como criar uma conta de armazenamento e [Exibir e copiar teclas de acesso de armazenamento](storage-create-storage-account.md#view-and-copy-storage-access-keys) para obter informações sobre como recuperar a chave da conta.

- Você criou um arquivo de imagem local chamado armazenados no caminho c:\\myimages\\image1.jpg. Como alternativa, modifique o construtor de   **FileInputStream** no exemplo para usar um nome de caminho e do arquivo de imagem diferente.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="to-use-azure-blob-storage-to-upload-a-file"></a>Usar o armazenamento de blob do Microsoft Azure para carregar um arquivo

Um procedimento passo a passo é apresentado aqui. Se você gostaria de saltar, todo o código é apresentado posteriormente neste artigo.

Começa o código, incluindo importações para as classes de armazenamento do Azure principais, as classes de cliente do Azure blob, as classes Java IO e a classe de **URISyntaxException** .

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

Declare uma classe denominada **StorageSample**e incluir o colchete aberto, **{**.

    public class StorageSample {

Dentro da classe **StorageSample** , declare uma variável de cadeia de caracteres que conterão o protocolo de ponto de extremidade padrão, o nome da sua conta de armazenamento e sua chave de acesso de armazenamento, conforme especificado em sua conta de armazenamento do Azure. Substitua os valores de espaço reservado **seu\_conta\_nome** e **seu\_conta\_chave** com sua própria conta chave de nome e uma conta, respectivamente.

    public static final String storageConnectionString =
           "DefaultEndpointsProtocol=http;" +
               "AccountName=your_account_name;" +
               "AccountKey=your_account_name";

Adicionar na sua declaração para **principal**, inclua um bloco **try** e inclua os colchetes abrir necessários, **{**.

    public static void main(String[] args)
    {
        try
        {

Declare variáveis do tipo a seguir (as descrições são para como eles são usados neste exemplo):

-   **CloudStorageAccount**: usado para inicializar o objeto de conta com seu nome de conta de armazenamento do Azure e chave e para criar o objeto de cliente do blob.
-   **CloudBlobClient**: usada para acessar o serviço de blob.
-   **CloudBlobContainer**: usado para criar um contêiner de blob, os blobs no contêiner de lista e exclua o contêiner.
-   **CloudBlockBlob**: usado para carregar um arquivo de imagem local para o contêiner.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

Atribua um valor para a variável de **conta** .

    account = CloudStorageAccount.parse(storageConnectionString);

Atribua um valor para a variável **serviceClient** .

    serviceClient = account.createCloudBlobClient();

Atribua um valor para a variável de **contêiner** . Podemos receberá uma referência a um contêiner chamado **gettingstarted**.

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

Crie o contêiner. Esse método criará o contêiner se ele não existir (e retornar **verdadeiro**). Se o contêiner existir, ele retornará **false**. Uma alternativa para **createIfNotExists** é o método **create** (que retornará um erro se o contêiner já existir).

    container.createIfNotExists();

Configurar o acesso anônimo para o contêiner.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Obtenha uma referência para o blob de bloco, que representará o blob no armazenamento do Azure.

    blob = container.getBlockBlobReference("image1.jpg");

Use o construtor de **arquivo** para obter uma referência para o arquivo criado localmente que você fará o upload. Certifique-se de que você criou este arquivo antes de executar o código.

    File fileReference = new File ("c:\\myimages\\image1.jpg");

Carregue o arquivo local por meio de uma chamada para o método **CloudBlockBlob.upload** . O primeiro parâmetro para o método **CloudBlockBlob.upload** é um objeto de **FileInputStream** que representa o arquivo local que será carregado para o armazenamento do Azure. O segundo parâmetro é o tamanho, em bytes, do arquivo.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Chamar uma função auxiliar chamada **MakeHTMLPage**, para tornar uma página básica de HTML que contém uma ** &lt;imagem&gt; ** elemento com a fonte definida como o blob que está agora na sua conta de armazenamento do Azure. O código para **MakeHTMLPage** será abordado neste artigo.

    MakeHTMLPage(container);

Imprima uma mensagem de status e informações sobre a página HTML criada.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Feche o bloco **try** , inserindo um colchete de fechamento: **}**

Lidar com as seguintes exceções:

-   **FileNotFoundException**: pode ser lançada pelos construtores **FileInputStream** ou **FileOutputStream** .
-   **StorageException**: pode ser lançada pela biblioteca cliente Azure armazenamento.
-   **URISyntaxException**: pode ser lançada pelo método **ListBlobItem.getUri** .
-   **Exceção**: manipulação de exceção genérica.

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Feche o **principal** , inserindo um colchete de fechamento: **}**

Crie um método chamado **MakeHTMLPage** para criar uma página HTML básica. Este método tem um parâmetro de tipo **CloudBlobContainer**, que será usado para percorrer a lista de blobs carregados. Este método irá gerar exceções do tipo **FileNotFoundException**, que pode ser lançada pelo construtor de **FileOutputStream** e **URISyntaxException**, que pode ser lançada pelo método **ListBlobItem.getUri** . Incluir o colchete de abertura, **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Crie um arquivo local chamado **index**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Gravar para o arquivo local, adicionando na ** &lt;html&gt;**, ** &lt;cabeçalho&gt;**, e ** &lt;corpo&gt; ** elementos.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Percorrer a lista de blobs carregados. Para cada blob, na página HTML, crie um ** &lt;img&gt; ** elemento que tem o atributo **src** enviado para o URI do blob conforme ela existe em sua conta de armazenamento do Azure.
Embora você tenha adicionado apenas uma imagem neste exemplo, se você adicionou mais, este código seria iteramos todos eles.

Para simplificar, este exemplo pressupõe que cada blob carregado é uma imagem. Se você atualizou blobs diferente de imagens ou blobs de página em vez de blobs do bloco, ajuste o código conforme necessário.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Fechar o ** &lt;corpo&gt; ** elemento e a ** &lt;html&gt; ** elemento.

    stream.println("</body>");
    stream.println("</html>");

Feche o arquivo local.

    stream.close();

Feche **MakeHTMLPage** , inserindo um colchete de fechamento: **}**

Feche **StorageSample** , inserindo um colchete de fechamento: **}**

O que vem a seguir é o código completo para este exemplo. Lembre-se de modificar os valores de espaço reservado **seu\_conta\_nome** e **seu\_conta\_chave** para usar seu nome de conta e chave da conta, respectivamente.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                       "AccountName=your_account_name;" +
                       "AccountKey=your_account_name";

        public static void main(String[] args) {
            try {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExists();

                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");

                File fileReference = new File("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            } catch (FileNotFoundException fileNotFoundException) {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            } catch (StorageException storageException) {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            } catch (URISyntaxException uriSyntaxException) {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            } catch (Exception e) {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
        {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

Além de carregar seu arquivo de imagem local para armazenamento do Azure, o código de exemplo cria um namedindex.html arquivo local, que pode ser aberto no seu navegador para ver a sua imagem carregada.

Como o código contém seu nome de conta e chave da conta, certifique-se de que seu código-fonte é seguro.

## <a name="to-delete-a-container"></a>Para excluir um contêiner

Porque cobrado para armazenamento, talvez você queira excluir o contêiner **gettingstarted** após terminar de experiências com este exemplo. Para excluir um contêiner, use o método **CloudBlobContainer.delete** .

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Para chamar o método **CloudBlobContainer.delete** , o processo de inicialização objetos **CloudStorageAccount**, **ClodBlobClient**e **CloudBlobContainer** é a mesma mostrada para o método **createIfNotExist** . A seguir está um exemplo completo que exclui o recipiente chamado **gettingstarted**.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

    public class DeleteContainer {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                   "AccountName=your_account_name;" +
                   "AccountKey=your_account_key";

        public static void main(String[] args)
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();

                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

Para obter uma visão geral de outras classes de armazenamento de blob e métodos, consulte [como usar o armazenamento de Blob do Java](storage-java-how-to-use-blob-storage.md).

## <a name="next-steps"></a>Próximas etapas

Siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- [Armazenamento do Azure SDK para Java](https://github.com/azure/azure-storage-java)
- [Referência SDK do cliente de armazenamento do Azure](http://dl.windowsazure.com/storage/javadoc/)
- [Serviços de armazenamento do Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog da equipe de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
