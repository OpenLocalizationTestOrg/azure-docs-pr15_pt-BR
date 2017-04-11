<properties
    pageTitle="Como instalar silenciosamente o Azure AD Application Proxy Connector | Microsoft Azure"
    description="Discute como executar uma instalação silenciosa do Azure AD Application Proxy Connector para fornecer acesso remoto seguro a seus aplicativos de local."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="how-to-silently-install-the-azure-ad-application-proxy-connector"></a>Como instalar silenciosamente o Azure conector de Proxy de aplicativo do AD

Você deseja ser capaz de enviar um script de instalação para vários servidores do Windows ou para os servidores do Windows que não têm a interface de usuário habilitado. Este tópico explica como criar um script do Windows PowerShell que permite a instalação autônoma instalar e registrar o Azure conector de Proxy de aplicativo do AD.

## <a name="enabling-access"></a>Habilitar acesso
Proxy do aplicativo funciona pela instalação de um serviço de Windows Server fino chamado o conector dentro da sua rede. Para o conector de Proxy do aplicativo trabalhá-lo deve ser registrado com o diretório do Azure AD usando uma senha e o administrador global. Normalmente, isso é inserido durante a instalação do conector em uma caixa de diálogo pop up. Como alternativa, você pode usar o Windows PowerShell para criar um objeto de credencial para inserir suas informações de registro, ou você pode criar seu próprios token e usá-lo para inserir suas informações de registro.

## <a name="step-1--install-the-connector-without-registration"></a>Etapa 1: Instalar o conector sem registro


Instale o conector MSIs sem registrar o conector da seguinte maneira:


1. Abra um prompt de comando.
2. Execute o seguinte comando no qual o /q significa instalação silenciosa - a instalação não solicitará que você aceite o contrato de licença de usuário final.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="step-2-register-the-connector-with-azure-active-directory"></a>Etapa 2: Registrar o conector com o Active Directory do Azure
Isso pode ser feito usando um dos seguintes métodos:


- Registrar o conector usando um objeto de credencial do Windows PowerShell
- Registrar o conector usando um token criado offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrar o conector usando um objeto de credencial do Windows PowerShell


1. Criar o objeto de credenciais do Windows PowerShell, execute o seguinte, onde "<username>"e"<password>" deve ser substituído com o nome de usuário e senha para seu diretório:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword

2. Vá para **C:\Program Files\Microsoft AAD aplicativo Proxy conector** e executar o script usando o objeto de credenciais do PowerShell que você criou, onde $cred é o nome do PowerShell credenciais de objeto que você criou:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred


### <a name="register-the-connector-using-a-token-created-offline"></a>Registrar o conector usando um token criado offline

1. Crie um símbolo de offline usando a classe de AuthenticationContext usando os valores no trecho de código:


        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





2. Depois que você tiver o token de criar uma SecureString usando o token: <br>
`$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. Execute o seguinte comando do Windows PowerShell, onde SecureToken é o nome do token criado anteriormente e tenantID é GUID do seu locatário: <br>
`RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



## <a name="see-also"></a>Consulte também

- [Habilitar o Proxy de aplicativo do Active Directory do Azure](active-directory-application-proxy-enable.md)
- [Publicar aplicativos usando seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
- [Ativar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Solucionar problemas com o Proxy de aplicativo](active-directory-application-proxy-troubleshoot.md)

Para as últimas notícias e atualizações, confira o [blog do Proxy de aplicativo](http://blogs.technet.com/b/applicationproxyblog/)
