<properties
    pageTitle="Usar o Azure cofre principais de um aplicativo Web | Microsoft Azure"
    description="Use este tutorial para ajudá-lo a aprender a usar o Azure chave Cofre de um aplicativo web."
    services="key-vault"
    documentationCenter=""
    authors="adhurwit"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="adhurwit"/>

# <a name="use-azure-key-vault-from-a-web-application"></a>Usar o Azure cofre principais de um aplicativo Web #

## <a name="introduction"></a>Introdução  
Use este tutorial para ajudá-lo a aprender a usar o Azure chave Cofre de um aplicativo web no Azure. Ele orienta o processo de acessar um segredo de um compartimento de chave do Azure para que ele pode ser usado em seu aplicativo web.

**Tempo estimado para concluir:** 15 minutos


Para obter informações gerais sobre o Azure chave cofre, consulte [o que é Azure chave cofre?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você deve ter o seguinte:

- Um URI para um segredo em um cofre de chave do Azure
- Uma ID de cliente e um segredo de cliente para um aplicativo web registrado com o Active Directory do Azure que tenha acesso ao seu Cofre de chave
- Um aplicativo web. Estamos será mostrando as etapas para um aplicativo do ASP.NET MVC implantado no Azure como um aplicativo Web.

> [AZURE.NOTE]  É essencial que você concluiu as etapas listadas na [Introdução ao Azure chave cofre](key-vault-get-started.md) para este tutorial, para que você tenha o URI um segredo e a ID do cliente e segredo cliente para um aplicativo web.

O aplicativo web que acessarão o Cofre de chave é aquele que está registrado no Active Directory do Azure e tem acesso ao seu Cofre de chave. Se esse não for o caso, volte para registrar um aplicativo do tutorial de Introdução e repita as etapas listadas.

Este tutorial é projetado para desenvolvedores da web que compreender as Noções básicas de criação de aplicativos da web no Azure. Para obter mais informações sobre Azure Web Apps, consulte [Visão geral de aplicativos Web](../app-service-web/app-service-web-overview.md).



## <a id="packages"></a>Adicionar pacotes Nuget ##
Há dois pacotes que seu aplicativo web precisa ter instalado.

- Biblioteca de autenticação do Active Directory - contém métodos para interagir com o Active Directory do Azure e gerenciando identidade de usuário
- Biblioteca de Cofre de chave Azure - contém métodos para interagir com Cofre de chave do Azure


Ambos esses pacotes podem ser instalados usando o Package Manager Console usando o comando pacote de instalação.

    // this is currently the latest stable version of ADAL
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault


## <a id="webconfig"></a>Modificar Web. config ##
Há três configurações de aplicativo que precisam ser adicionadas ao arquivo Web. config da seguinte maneira.

    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


Se você não fizer para hospedar seu aplicativo como um aplicativo Web do Azure, você deve adicionar os valores reais ClientId, segredo cliente e segredo URI para Web. config. Caso contrário, deixe esses valores fictícios porque adicionaremos os valores reais no Portal do Azure para um nível de segurança adicional.


## <a id="gettoken"></a>Adicionar método para obter um acesso Token ##
Para poder usar a API do Cofre de chave é necessário um token de acesso. O cliente do cofre chave manipula chamadas à API do cofre chave, mas você precisa fornecer uma função que obtém o token de acesso.  

Este é o código para obter um acesso token de Active Directory do Azure. Este código poderá entrar em qualquer lugar no seu aplicativo. Como o adicionar uma classe utilitários ou EncryptionHelper.  

    //add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Threading.Tasks;
    using System.Web.Configuration;

    //this is an optional property to hold the secret after it is retrieved
    public static string EncryptSecret { get; set; }

    //the method that will be provided to the KeyVaultClient
    public static async Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

> [AZURE.NOTE] 
> Usando uma identificação de cliente e segredo cliente é a maneira mais fácil para autenticar um aplicativo do Azure AD. E usá-lo em seu aplicativo web permite uma separação de tarefas e mais controle sobre o gerenciamento de chaves. Mas ele dependem colocando o segredo de cliente em suas configurações de configuração que algumas podem ser tão arriscadas como colocar o segredo que você deseja proteger em suas configurações. Veja abaixo para obter mais informações sobre como usar um ID do cliente e um certificado em vez do código do cliente e segredo cliente para autenticar o aplicativo do Azure AD.



## <a id="appstart"></a>Recupere o segredo em Iniciar aplicativo ##
Agora, precisamos código para chamar a API do cofre chave e recuperar o segredo. O código a seguir pode ser colocado em qualquer lugar, desde que ele é chamado antes de você precisa usá-lo. Ter coloquei este código no evento Iniciar aplicativo do global. asax para que ele é executado uma vez em Iniciar e disponibiliza o segredo para o aplicativo.

    //add these using statements
    using Microsoft.Azure.KeyVault;
    using System.Web.Configuration;

    // I put my GetToken method in a Utils class. Change for wherever you placed your method.
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

    var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;

    //I put a variable in a Utils class to hold the secret for general  application use.
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>Adicionar configurações de aplicativo no Portal do Azure (opcional) ##
Se você tiver um aplicativo Web do Azure agora você pode adicionar os valores reais para o AppSettings no Portal do Azure. Fazendo isso, os valores reais não estarão no Web. config mas protegida através do Portal onde você tem recursos de controle de acesso em separado. Esses valores substituirão os valores que você inseriu na Web. config. Certifique-se de que os nomes são os mesmos.

![Configurações do aplicativo exibidas no Portal do Azure][1]


## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Autenticar com um certificado em vez de um segredo de cliente
Outra maneira de autenticar um aplicativo do Azure AD está usando uma ID de cliente e um certificado em vez de uma identificação de cliente e segredo cliente. A seguir estão as etapas para usar um certificado em um aplicativo Web do Azure:

1. Obter ou criar um certificado
2. Associar o certificado de um aplicativo do Azure AD
3. Adicionar código ao seu aplicativo Web para usar o certificado
4. Adicionar um certificado ao seu aplicativo Web


**Obter ou criar um certificado** Para nossas finalidades faremos um certificado de teste. Aqui estão alguns comandos que você pode usar em um Prompt de comando do desenvolvedor para criar um certificado. Altere o diretório para onde deseja que os arquivos de certificado a ser criado.

    makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2015 -e 07/31/2016 -r
    pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

Anote a data de término e a senha para a. pfx (neste exemplo: 07/31/2016 e test123). Você precisará deles abaixo.

Para obter mais informações sobre como criar um certificado de teste, consulte [como: criar seu próprio teste certificado](https://msdn.microsoft.com/library/ff699202.aspx)


**Associar o certificado com um aplicativo do Azure AD** Agora que você tem um certificado, é necessário associá-lo a um aplicativo do Azure AD. Mas o Portal de gerenciamento do Azure não suporta isso agora. Em vez disso, você precisa usar o Powershell. Estes são os comandos que você precisa executar:

    $x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2

    PS C:\> $x509.Import("C:\data\KVWebApp.cer")

    PS C:\> $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())

    PS C:\> $now = [System.DateTime]::Now

    # this is where the end date from the cert above is used
    PS C:\> $yearfromnow = [System.DateTime]::Parse("2016-07-31")

    PS C:\> $adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -KeyValue $credValue -KeyType "AsymmetricX509Cert" -KeyUsage "Verify" -StartDate $now -EndDate $yearfromnow

    PS C:\> $sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId

    PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToSecrets all -ResourceGroupName 'contosorg'

    # get the thumbprint to use in your app settings
    PS C:\>$x509.Thumbprint

Depois de executar estes comandos, você pode ver o aplicativo no Azure AD. Se você não vir o aplicativo no primeiro, pesquise "Aplicativos minha empresa possui" em vez de "Aplicativos minha empresa usa".

Para saber mais sobre objetos de aplicativo do Azure AD e ServicePrincipal, consulte [objetos de aplicativo e serviço Principal](../active-directory/active-directory-application-objects.md)



**Adicionar código para seu aplicativo Web para usar o certificado** Agora, podemos adicionará código para seu aplicativo Web para acessar o certificado e usá-lo para autenticação.

Primeiro, há código para acessar o certificado.

    public static class CertificateHelper
    {
        public static X509Certificate2 FindCertificateByThumbprint(string findValue)
        {
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            try
            {
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                    findValue, false); // Don't validate certs, since the test root isn't installed.
                if (col == null || col.Count == 0)
                    return null;
                return col[0];
            }
            finally
            {
                store.Close();
            }
        }
    }


Observe que StoreLocation é CurrentUser em vez de LocalMachine. E se podemos está fornecendo 'false' para o método de localizar porque estamos usando um certificado de teste.


Em seguida é o código que usa o CertificateHelper e cria um ClientAssertionCertificate que é necessário para autenticação.

    public static ClientAssertionCertificate AssertionCert { get; set; }

    public static void GetCert()
    {
        var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
        AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
    }


Veja aqui o novo código para obter o token de acesso. Isso substitui o método de GetToken acima. Posso ter dado a ele um nome diferente para sua conveniência.

    public static async Task<string> GetAccessToken(string authority, string resource, string scope)
    {
        var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
        var result = await context.AcquireTokenAsync(resource, AssertionCert);
        return result.AccessToken;
    }

Posso ter coloque todo esse código classe de utilitários do meu projeto Web App para facilidade de uso.

A última alteração de código está no método Application_Start. Primeiro, precisamos chamar o método GetCert() para carregar o ClientAssertionCertificate. E, em seguida, vamos alterar o método de retorno que fornecemos ao criar um novo KeyVaultClient. Observe que isso substitui o código que tivemos acima.

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**Adicionar um certificado para o seu aplicativo Web por meio do Portal do Azure** Adicionar um certificado para seu aplicativo Web é um processo de duas etapas simples. Primeiro, acesse o Portal do Azure e navegue até seu aplicativo Web. Na lâmina configurações para o aplicativo Web, clique na entrada para "domínios personalizados e SSL". Na lâmina que abre você poderá carregar o certificado que você criou acima, KVWebApp.pfx, certifique-se de que você se lembrar da senha para o pfx.

![Adicionando um certificado para um aplicativo Web no Portal do Azure][2]


A última coisa que você precisa fazer é adicionar uma configuração de aplicativo ao seu aplicativo Web que tem o site nome\_carga\_certificados e um valor de *. Isso garante que todos os certificados são carregados. Se você quisesse carregar somente os certificados que você carregou, então você pode inserir uma lista separada por vírgulas de suas impressões digitais.

Para saber mais sobre como adicionar um certificado para um aplicativo Web, consulte [Usando certificados em aplicativos de sites do Azure](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)


**Adicionar um certificado ao Cofre de chave como um segredo** Ao invés de carregar seu certificado para o serviço Web App diretamente, você pode armazená-la em Cofre de chave como um segredo e implantá-lo de lá. Esse é um processo de duas etapas descrito a seguir na postagem no blog, [Implantando o certificado de aplicativo do Azure Web através do Cofre de chave](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)



## <a id="next"></a>Próximas etapas ##


Para programação referências, consulte [Azure chave cofre cliente API referência c#](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
