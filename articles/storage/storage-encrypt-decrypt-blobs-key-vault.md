<properties
    pageTitle="Tutorial: Criptografar e descriptografar blobs no armazenamento do Microsoft Azure usando o Azure chave cofre | Microsoft Azure"
    description="Este tutorial orienta como criptografar e descriptografar um blob usando criptografia do lado do cliente para armazenamento do Microsoft Azure com o Azure chave cofre."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="required"
    ms.date="10/18/2016"
    ms.author="lakasa;robinsh"/>

# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Tutorial: Criptografar e descriptografar blobs no armazenamento do Microsoft Azure usando Cofre de chave do Azure

## <a name="introduction"></a>Introdução

Este tutorial aborda como fazer uso da criptografia de armazenamento do lado do cliente com o Azure chave cofre. Ele o guiará pelo como criptografar e descriptografar um blob em um aplicativo de console usando essas tecnologias.

**Tempo estimado para concluir:** 20 minutos

Para obter informações gerais sobre o Azure chave cofre, consulte [o que é Azure chave cofre?](../key-vault/key-vault-whatis.md).

Para obter informações gerais sobre criptografia do lado do cliente para o armazenamento do Azure, consulte [criptografia do lado do cliente e compartimento de chave do Azure para armazenamento do Microsoft Azure](storage-client-side-encryption.md).


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você deve ter o seguinte:

- Uma conta de armazenamento do Azure
- Visual Studio 2013 ou superior
- PowerShell Azure


## <a name="overview-of-client-side-encryption"></a>Visão geral da criptografia do lado do cliente

Para obter uma visão geral de criptografia do lado do cliente de armazenamento do Azure, consulte [criptografia do lado do cliente e compartimento de chave do Azure para armazenamento do Microsoft Azure](storage-client-side-encryption.md)

Aqui está uma breve descrição de como funciona a criptografia do cliente:

1. O SDK do cliente de armazenamento do Azure gera uma chave de criptografia do conteúdo (CEK), que é uma chave simétrica do uso de uma vez.
2. Dados de cliente são criptografados usando este CEK.
3. O CEK é fornecida (criptografados) usando a chave de criptografia de chave (KEK). O KEK é identificado por um identificador de chave e pode ser um par de chaves assimétrico ou uma chave simétrica e podem ser gerenciada localmente ou armazenado no Azure chave cofre. O cliente de armazenamento em si nunca tem acesso ao KEK. Ele apenas chama o algoritmo de quebra automática de chave que é fornecido pela chave cofre. Os clientes podem escolher usar provedores personalizados para chave disposição/desempacotamento se desejarem.
4. Os dados criptografados, em seguida, são carregados para o serviço de armazenamento do Azure.


## <a name="set-up-your-azure-key-vault"></a>Configurar seu Cofre de chave do Azure
Para continuar com este tutorial, você precisa fazer as seguintes etapas são descritas no tutorial [Introdução ao Azure Cofre de chave](../key-vault/key-vault-get-started.md):

- Crie um chave cofre.
- Adicione uma chave ou segredo ao Cofre de chave.
- Registre um aplicativo com o Active Directory do Azure.
- Autorize o aplicativo para usar a tecla ou segredo.

Anote o ClientID e ClientSecret geradas ao registrar um aplicativo com o Active Directory do Azure.

Crie ambas as chaves no cofre de chave. Vamos supor para o restante do tutorial que você usou os seguintes nomes: ContosoKeyVault e TestRSAKey1.


## <a name="create-a-console-application-with-packages-and-appsettings"></a>Criar um aplicativo de console com pacotes e AppSettings

No Visual Studio, crie um novo aplicativo de console.

Adicione pacotes de nuget necessárias no Console do Gerenciador de pacote.

    Install-Package WindowsAzure.Storage

    // This is the latest stable release for ADAL.
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault
    Install-Package Microsoft.Azure.KeyVault.Extensions


Adicione AppSettings ao App.

    <appSettings>
        <add key="accountName" value="myaccount"/>
        <add key="accountKey" value="theaccountkey"/>
        <add key="clientId" value="theclientid"/>
        <add key="clientSecret" value="theclientsecret"/>
        <add key="container" value="stuff"/>
    </appSettings>

Adicione o seguinte `using` instruções e verifique se você adicionar uma referência a System. Configuration ao projeto.

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.Azure.KeyVault;
    using System.Threading;     
    using System.IO;


## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Adicionar um método para obter um símbolo para seu aplicativo de console

O seguinte método é usado por classes chave cofre que precisam se autenticar para acessar seu cofre chave.

    private async static Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(
            ConfigurationManager.AppSettings["clientId"],
            ConfigurationManager.AppSettings["clientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

## <a name="access-storage-and-key-vault-in-your-program"></a>Compartimento de chave em seu programa e armazenamento de acesso

Na função principal, adicione o seguinte código.

    // This is standard code to interact with Blob storage.
    StorageCredentials creds = new StorageCredentials(
        ConfigurationManager.AppSettings["accountName"],
        ConfigurationManager.AppSettings["accountKey"]);
    CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
    CloudBlobClient client = account.CreateCloudBlobClient();
    CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
    contain.CreateIfNotExists();

    // The Resolver object is used to interact with Key Vault for Azure Storage.
    // This is where the GetToken method from above is used.
    KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);


> [AZURE.NOTE] Modelos de objeto do cofre chave
>
>É importante entender o que há realmente dois chave cofre modelos de objetos a serem consideradas: uma baseia a API REST (namespace KeyVault) e a outra é uma extensão de criptografia do lado do cliente.

> O cliente do cofre chave interage com a API REST e entende JSON Web chaves e senhas para os dois tipos de itens que estão contidas em Cofre de chave.

> As extensões de cofre chave são classes que parecem criadas especificamente para criptografia do lado do cliente no armazenamento do Azure. Eles contêm uma interface para teclas (IKey) e classes com base no conceito de uma resolução de chave. Há duas implementações de IKey que você precisa saber: RSAKey e SymmetricKey. Agora que ocorram coincidir com as mudanças que estão contidas em um cofre de chave, mas neste momento são classes independentes (para que a chave e segredo recuperados pelo cliente chave cofre não implemente IKey).


## <a name="encrypt-blob-and-upload"></a>Criptografar blob e carregar
Adicione o seguinte código para criptografar um blob e carregue-o à sua conta de armazenamento do Azure. O método **ResolveKeyAsync** usado retorna um IKey.


    // Retrieve the key that you created previously.
    // The IKey that is returned here is an RsaKey.
    // Remember that we used the names contosokeyvault and testrsakey1.
    var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();


    // Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    // Reference a block blob.
    CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

    // Upload using the UploadFromStream method.
    using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
        blob.UploadFromStream(stream, stream.Length, null, options, null);


A seguir é uma captura de tela a partir do [Portal clássico do Azure](https://manage.windowsazure.com) para um blob que foram criptografado usando a criptografia do lado do cliente com uma chave armazenada em chave cofre. A propriedade **KeyId** é o URI da chave no cofre de chave que atua como o KEK. A propriedade **EncryptedKey** contém a versão criptografada do CEK.

![Captura de tela mostrando os metadados de Blob que inclui metadados de criptografia][1]

> [AZURE.NOTE] Se você examinar o construtor de BlobEncryptionPolicy, você verá que ele pode aceitar uma chave e/ou uma resolução. Lembre-se de que agora, você não pode usar uma resolução de criptografia porque faz não esteja atualmente oferece suporte uma chave padrão.



## <a name="decrypt-blob-and-download"></a>Descriptografar blob e baixar
Descriptografia é realmente quando usando as classes de resolução faz sentido. A ID da chave usada para criptografia é associada ao blob em seus metadados, portanto, não há motivo para recuperar a chave e lembre-se a associação entre chave e blob. Você só precisa certificar-se de que a chave permanece em chave cofre.   

A chave privada de uma chave de RSA permanece em Cofre de chave, portanto, para descriptografia ocorra, a chave criptografada dos metadados de blob que contém o CEK é enviada ao Cofre de chave para descriptografia.

Adicione o seguinte para descriptografar o blob que você acabou de ser carregado.

    // In this case, we will not pass a key and only pass the resolver because
    // this policy will only be used for downloading / decrypting.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
        blob.DownloadToStream(np, null, options, null);


> [AZURE.NOTE] Existem alguns dos outros tipos de resolvedores para facilitar o gerenciamento de chaves, incluindo: AggregateKeyResolver e CachingKeyResolver.


## <a name="use-key-vault-secrets"></a>Use segredos Cofre de chave
A maneira de usar um segredo com criptografia do lado do cliente é por meio da classe SymmetricKey porque um segredo é essencialmente uma chave simétrica. Mas, conforme mencionado acima, um segredo em chave cofre não mapear exatamente para um SymmetricKey. Há algumas coisas para compreender:


- A chave em um SymmetricKey deve ser um comprimento fixo: 128, 192, 256, 384 ou 512 bits.
- A chave em um SymmetricKey deve ser codificada em Base64.
- Um segredo Cofre de chave que será usado como um SymmetricKey precisa ter um tipo de conteúdo de "application/octet-stream" em chave cofre.

Aqui está um exemplo do PowerShell de criação de um segredo no cofre de chave que podem ser usados como um SymmetricKey.
Observação: O valor codificado, $key, é apenas a fins de demonstração. Em seu próprio código, você desejará gerar esta tecla.

    // Here we are making a 128-bit key so we have 16 characters.
    //  The characters are in the ASCII range of UTF8 so they are
    //  each 1 byte. 16 x 8 = 128.
    $key = "qwertyuiopasdfgh"
    $b = [System.Text.Encoding]::UTF8.GetBytes($key)
    $enc = [System.Convert]::ToBase64String($b)
    $secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

    // Substitute the VaultName and Name in this command.
    $secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"

Em seu aplicativo de console, você pode usar a mesma chamada como antes de recuperar esse segredo como um SymmetricKey.

    SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
        "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
        CancellationToken.None).GetAwaiter().GetResult();

É isso. Aproveite!

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar o armazenamento do Microsoft Azure com c#, consulte [Biblioteca de cliente de armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Para saber mais sobre a API REST Blob, consulte [API REST do Blob serviço](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Para as informações mais recentes sobre o armazenamento do Microsoft Azure, acesse o [Blog da equipe de armazenamento do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/).


<!--Image references-->
[1]: ./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png
