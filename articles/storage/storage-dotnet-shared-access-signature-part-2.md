<properties
    pageTitle="Criar e usar um com o armazenamento de Blob | Microsoft Azure"
    description="Este tutorial mostra como criar assinaturas de acesso compartilhado para usar com o armazenamento de Blob e como usá-los dos seus aplicativos de cliente."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Compartilhado assinaturas do Access, parte 2: Criar e usar um com o armazenamento de Blob

## <a name="overview"></a>Visão geral

[Parte 1](storage-dotnet-shared-access-signature-part-1.md) deste tutorial explorado compartilhados assinaturas de acesso (SAS) e explicação das práticas recomendadas para usá-los. Parte 2 mostra como gerar e, em seguida, usar assinaturas de acesso compartilhado com o armazenamento de Blob. Os exemplos são gravados em c# e usam a biblioteca de cliente de armazenamento do Azure para .NET. Os cenários cobertos incluem esses aspectos de trabalho com assinaturas de acesso compartilhado:

- Gerar uma assinatura de acesso compartilhado em um contêiner
- Gerar uma assinatura de acesso compartilhado em um blob
- Criar uma política de acesso armazenadas para gerenciar assinaturas em recursos de um contêiner
- Teste as assinaturas de acesso compartilhado de um aplicativo cliente

## <a name="about-this-tutorial"></a>Sobre este Tutorial

Neste tutorial, nos concentraremos na criação de assinaturas de acesso compartilhado para contêineres e blobs criando dois aplicativos de console. O aplicativo de console primeiro gera assinaturas de acesso compartilhado em um contêiner e em um blob. Este aplicativo tem conhecimento das chaves de conta de armazenamento. O aplicativo de console do segundo, que irá atuar como um aplicativo cliente, acessa o contêiner e recursos de blob usando as assinaturas de acesso compartilhado criadas com o aplicativo primeiro. Este aplicativo usa as assinaturas de acesso compartilhado somente para autenticar seu acesso ao contêiner e recursos de blob - ele não tem conhecimento das chaves de conta.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Parte 1: Criar um aplicativo de Console para gerar assinaturas de acesso compartilhado

Primeiro, certifique-se de que você tenha a biblioteca de cliente de armazenamento do Azure para .NET instalado. Você pode instalar o [pacote do NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "NuGet pacote") que contém os conjuntos mais atualizados para a biblioteca de cliente; Este é o método recomendado para garantir que você tenha as correções mais recentes. Você também pode baixar a biblioteca de cliente como parte da versão mais recente do [SDK do Azure para .NET](https://azure.microsoft.com/downloads/).

No Visual Studio, crie um novo aplicativo de console do Windows e nomeie- **GenerateSharedAccessSignatures**. Adicione referências a **Microsoft.WindowsAzure.Configuration.dll** e **Microsoft.WindowsAzure.Storage.dll**, usando uma das seguintes abordagens:

-   Se você deseja instalar o pacote NuGet, primeiro instale o [Cliente do NuGet](https://docs.nuget.org/consume/installing-nuget). No Visual Studio, selecione **projeto | Gerenciar pacotes NuGet**, pesquise online de **Armazenamento do Azure**e siga as instruções de instalação.
-   Como alternativa, localize os conjuntos em sua instalação do SDK do Azure e adicione referências a eles.

Na parte superior do arquivo Program.cs, adicione as seguintes instruções **usando** :

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Edite o arquivo App para que ele contenha uma configuração com uma cadeia de conexão que aponta para sua conta de armazenamento. Seu arquivo App. config deve ser semelhante a esta:

    <configuration>
      <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings>
    </configuration>

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Gerar uma assinatura de acesso compartilhado URI para um contêiner

Em primeiro lugar, vamos adicionar um método para gerar uma assinatura de acesso compartilhado em um contêiner de novo. Nesse caso a assinatura não está associada uma política de acesso armazenadas, para que ele executa no URI as informações indicando seu tempo de expiração e as permissões que ele concede.

Primeiro, adicione o código para o método **Main ()** para autenticar o acesso à sua conta de armazenamento e criar um novo recipiente:

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Insert calls to the methods created below here...

        //Require user input before closing the console window.
        Console.ReadLine();
    }

Em seguida, adicione um novo método que gera a assinatura de acesso compartilhado para o contêiner e retorna a assinatura URI:

    static string GetContainerSasUri(CloudBlobContainer container)
    {
        //Set the expiry time and permissions for the container.
        //In this case no start time is specified, so the shared access signature becomes valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;

        //Generate the shared access signature on the container, setting the constraints directly on the signature.
        string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

Adicione as seguintes linhas na parte inferior do método **Main ()** , antes da chamada para **Console.ReadLine()**, para chamar **GetContainerSasUri()** e escrever a assinatura URI na janela de console:

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

Compilar e executar para a assinatura de acesso compartilhado URI para o novo contêiner de saída. O URI será semelhante à seguinte URI:       

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

Depois de executar o código, a assinatura de acesso compartilhado que você criou no contêiner será válida para próximas 24 horas. A assinatura concede uma permissão de cliente para blobs de lista no contêiner e gravar um blob novo para o contêiner.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Gerar uma assinatura de acesso compartilhado URI para um Blob

Em seguida, vamos escrever código semelhante para criar um novo blob dentro do contêiner e gerar uma assinatura de acesso compartilhado para ele. Essa assinatura de acesso compartilhado não está associada uma política de acesso armazenadas, para que ele inclui a hora de início, hora de vencimento e informações de permissão no URI.

Adicionar um novo método que cria um novo blob e escrever algum texto a ele, em seguida, gera uma assinatura de acesso compartilhado e retorna a assinatura URI:

    static string GetBlobSasUri(CloudBlobContainer container)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Set the expiry time and permissions for the blob.
        //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
        //The shared access signature will be valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

        //Generate the shared access signature on the blob, setting the constraints directly on the signature.
        string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

Na parte inferior do método **Main ()** , adicione as seguintes linhas para chamar **GetBlobSasUri()**, antes da chamada para **Console.ReadLine()**e escrever a assinatura de acesso compartilhado URI na janela de console:    

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();


Compilar e executar para a assinatura de acesso compartilhado URI para o novo blob de saída. O URI será semelhante à seguinte URI:

    https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

### <a name="create-a-stored-access-policy-on-the-container"></a>Criar uma política de acesso armazenadas no contêiner

Agora vamos criar uma política de acesso armazenadas no contêiner, que irá definir as restrições de assinaturas acesso compartilhado que estão associadas ele.

No exemplos anteriores, nós especificados a hora de início (implicitamente ou explicitamente), o tempo de expiração e as permissões a assinatura de acesso compartilhado URI em si. Nos exemplos a seguir, podemos, especifique esses a política de acesso armazenados e não a assinatura de acesso compartilhado. Isso nos permite alterar essas restrições sem reemitindo a assinatura de acesso compartilhado.

É possível ter um ou mais das restrições sobre a assinatura de acesso compartilhado e o resto na política de acesso armazenadas. Entretanto, você pode apenas especificar a hora de início, hora de vencimento e permissões em um só lugar ou outro; Por exemplo, você não pode especificar permissões na assinatura de acesso compartilhado e também especificá-las na política de acesso armazenadas.

Observe que quando você adiciona uma política de acesso a um contêiner, você deve obter permissões existentes do contêiner, adicionar a nova política de acesso e defina permissões do contêiner.

Adicione um novo método que cria uma nova política de acesso armazenadas em um contêiner e retorna o nome da política:

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
        string policyName)
    {
        //Create a new shared access policy and define its constraints.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
        };

        //Get the container's existing permissions.
        BlobContainerPermissions permissions = container.GetPermissions();

        //Add the new policy to the container's permissions, and set the container's permissions.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

Na parte inferior do método **Main ()** , antes da chamada para **Console.ReadLine()**, adicione as seguintes linhas desmarcar primeiro qualquer políticas de acesso existente e então chamar o método **CreateSharedAccessPolicy()** :    

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

Observe que quando você limpa as políticas de acesso em um contêiner, você deve primeiro obter permissões existentes do contêiner, e em seguida, desmarque as permissões e defina as permissões novamente.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Gerar uma assinatura de acesso compartilhado URI no contêiner que usa uma política de acesso

Em seguida, vamos criar outra assinatura de acesso compartilhado no contêiner que criamos anteriormente, mas desta vez que estamos associará a assinatura a política de acesso que criamos no exemplo anterior.

Adicione um novo método para gerar outra assinatura de acesso compartilhado no contêiner:

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Generate the shared access signature on the container. In this case, all of the constraints for the
        //shared access signature are specified on the stored access policy.
        string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

Na parte inferior do método **Main ()** , antes da chamada para **Console.ReadLine()**, adicione as seguintes linhas para chamar o método **GetContainerSasUriWithPolicy** :

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Gerar uma assinatura de acesso compartilhado URI no Blob que usa uma política de acesso

Por fim, vamos adicionar um método semelhante para criar outro blob e gerar uma assinatura de acesso compartilhado que está associada a uma política de acesso.

Adicione um novo método para criar um blob e gerar uma assinatura de acesso compartilhado:

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a shared access signature. " +
        "A stored access policy defines the constraints for the signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Generate the shared access signature on the blob.
        string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

Na parte inferior do método **Main ()** , antes da chamada para **Console.ReadLine()**, adicione as seguintes linhas para chamar o método **GetBlobSasUriWithPolicy** :    

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

O método **Main ()** agora deve parecer com isso integralmente. Execute-o para escrever a assinatura de acesso compartilhado URIs para a janela de console, copie e cole-os em um arquivo de texto para uso na segunda parte deste tutorial.

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Generate a SAS URI for the container, without a stored access policy.
        Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, without a stored access policy.
        Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
        Console.WriteLine();

        //Clear any existing access policies on container.
        BlobContainerPermissions perms = container.GetPermissions();
        perms.SharedAccessPolicies.Clear();
        container.SetPermissions(perms);

        //Create a new access policy on the container, which may be optionally used to provide constraints for
        //shared access signatures on the container and the blob.
        string sharedAccessPolicyName = "tutorialpolicy";
        CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

        //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        Console.ReadLine();
    }

Quando você executa o aplicativo de console GenerateSharedAccessSignatures, você verá a saída semelhante à seguinte na janela do console. Estas são as assinaturas de acesso compartilhado que você usará na parte 2 do tutorial.

![SAS-console-saída-1][sas-console-output-1]

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Parte 2: Criar um aplicativo de Console para testar as assinaturas de acesso compartilhado

Para testar as assinaturas de acesso compartilhado criadas nos exemplos anteriores, vamos criar um aplicativo de console segundo que usa as assinaturas para executar operações no contêiner e em um blob.

> [AZURE.NOTE] Se mais de 24 horas passaram desde que você concluiu a primeira parte do tutorial, as assinaturas geradas não será mais válidas. Nesse caso, você deve executar o código no aplicativo do console primeiro para gerar assinaturas de acesso compartilhado clara para uso na segunda parte do tutorial.

No Visual Studio, crie um novo aplicativo de console do Windows e nomeie- **ConsumeSharedAccessSignatures**. Adicione referências a **Microsoft.WindowsAzure.Configuration.dll** e **Microsoft.WindowsAzure.Storage.dll**, como você fez anteriormente.

Na parte superior do arquivo Program.cs, adicione as seguintes instruções **usando** :

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

No corpo do método **Main ()** , adicione as seguintes constantes e atualize seus valores para as assinaturas de acesso compartilhado geradas no parte 1 do tutorial.

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Adicionar um método para tentar operações de contêiner usando uma assinatura de acesso compartilhado

Em seguida, vamos adicionar um método que testa algumas operações de contêiner representante usando uma assinatura de acesso compartilhado no contêiner. Observe que a assinatura de acesso compartilhado é usada para retornar uma referência ao contêiner, autenticar o acesso ao contêiner com base na assinatura sozinha.

Adicione o seguinte método para Program.cs:

    static void UseContainerSAS(string sas)
    {
        //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Create a list to store blob URIs returned by a listing operation on the container.
        List<ICloudBlob> blobList = new List<ICloudBlob>();

        //Write operation: write a new blob to the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //List operation: List the blobs in the container.
        try
        {
            foreach (ICloudBlob blob in container.ListBlobs())
            {
                blobList.Add(blob);
            }
            Console.WriteLine("List operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("List operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Get a reference to one of the blobs in the container and read it.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        Console.WriteLine();

        //Delete operation: Delete a blob in the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Atualize o método de **Main ()** para chamar **UseContainerSAS()** com ambas as assinaturas de acesso compartilhado que você criou no contêiner:

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        Console.ReadLine();
    }


### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Adicionar um método para tentar operações de Blob usando uma assinatura de acesso compartilhado

Por fim, vamos adicionar um método que testa algumas operações de blob representante usando uma assinatura de acesso compartilhado em blob. Nesse caso, usamos o construtor **CloudBlockBlob(String)**, passando a assinatura de acesso compartilhado, para retornar uma referência para o blob. Nenhuma outra autenticação é necessária; ele se baseia na assinatura sozinha.

Adicione o seguinte método para Program.cs:

    static void UseBlobSAS(string sas)
    {
        //Try performing blob operations using the SAS provided.

        //Return a reference to the blob using the SAS URI.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        //Write operation: Write a new blob to the container.
        try
        {
            string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Read the contents of the blob.
        try
        {
            MemoryStream msRead = new MemoryStream();
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                {
                    string line;
                    while ((line = reader.ReadLine()) != null)
                    {
                        Console.WriteLine(line);
                    }
                }
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Delete operation: Delete the blob.
        try
        {
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Atualize o método de **Main ()** para chamar **UseBlobSAS()** com ambas as assinaturas de acesso compartilhado que você criou em blob:

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

Execute o aplicativo de console e observe a saída para ver quais operações são permitidas para quais assinaturas. A saída na janela do console terá aparência semelhante à seguinte:

![SAS-console-saída-2][sas-console-output-2]

## <a name="next-steps"></a>Próximas etapas

[Assinaturas de acesso compartilhado, parte 1: Noções básicas sobre o modelo SAS](storage-dotnet-shared-access-signature-part-1.md)

[Gerenciar o acesso anônimo de leitura para contêineres e blobs](storage-manage-access-to-resources.md)

[Delegar acesso com uma assinatura de acesso compartilhado (API REST)](http://msdn.microsoft.com/library/azure/ee395415.aspx)

[Apresentando o fila SAS e tabela](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG
