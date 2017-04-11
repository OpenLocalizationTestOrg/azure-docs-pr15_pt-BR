<properties
   pageTitle="Criar o serviço Azure principal com o PowerShell | Microsoft Azure"
   description="Descreve como usar o PowerShell do Azure para criar um aplicativo do Active Directory e o serviço principal e conceder acesso aos recursos através do controle de acesso baseado em função. Ele mostra como autenticar o aplicativo com uma senha ou o certificado."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Usar o PowerShell do Azure para criar um objeto de serviço para acessar recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)

Quando você tiver um aplicativo ou script que precise acessar recursos, provavelmente você não deseja executar esse processo em suas próprias credenciais. Você pode ter permissões diferentes que você deseja para o aplicativo, e você não desejar que o aplicativo para continuar usando suas credenciais se alterar suas responsabilidades. Em vez disso, você pode criar uma identidade do aplicativo que inclui as credenciais de autenticação e as atribuições de função. Sempre que o aplicativo é executado, ele autentica com essas credenciais. Este tópico mostra como usar o [PowerShell do Azure](powershell-install-configure.md) para configurar tudo o que precisa de um aplicativo seja executado em seu próprio credenciais e identidade.

Com o PowerShell, você tem duas opções para autenticar seu aplicativo do AD:

 - senha
 - certificado

Este tópico mostra como usar as duas opções no PowerShell. Se você pretende efetuar login no Azure de uma estrutura de programação (como Python, Ruby ou node), autenticação de senha pode ser a melhor opção. Antes de decidir se deseja usar uma senha ou um certificado, consulte a seção de [aplicativos de exemplo](#sample-applications) para obter exemplos de autenticar nas estruturas diferentes.

## <a name="active-directory-concepts"></a>Conceitos do Active Directory

Neste artigo, você criar dois objetos - o aplicativo do Active Directory (AD) e o serviço principal. O aplicativo do AD é a representação global de seu aplicativo. Ele contém as credenciais (uma id de aplicativo e uma senha ou certificado). O serviço principal é a representação local do seu aplicativo em um Active Directory. Ele contém a atribuição de função. Este tópico se concentra em um aplicativo de locatário do único onde o aplicativo destina-se para executar em apenas uma organização. Normalmente, você usa o único locatário aplicativos para aplicativos de linha de negócios que são executados dentro de sua organização. Em um aplicativo único locatário, você tem um aplicativo do AD e capital de um serviço.

Você pode estar se perguntando - por que eu preciso ambos os objetos? Essa abordagem faz mais sentido quando você considera locatários vários aplicativos. Normalmente você usa vários locatários aplicativos para aplicativos do software como serviço (SaaS), onde seu aplicativo é executado em muitas assinaturas diferentes. Para aplicativos de vários locatários, você tem um aplicativo do AD e vários objetos de serviço (um em cada Active Directory que concede acesso ao aplicativo). Para configurar um aplicativo de vários locatário, consulte [o guia do desenvolvedor autorização com a API do Gerenciador de recursos do Azure](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Permissões necessárias

Para concluir este tópico, você deve ter permissões suficientes em sua Azure Active Directory e sua assinatura do Azure. Especificamente, você deve ser capaz de criar um aplicativo no Active Directory e atribuir a entidade de serviço a uma função. 

No seu Active Directory, sua conta deve ser um administrador (como **Administrador Global** ou **Administrador de usuário**). Se sua conta for atribuída à função de **usuário** , você precisa ter um administrador elevar suas permissões.

Em sua assinatura, sua conta deve ter `Microsoft.Authorization/*/Write` acesso, que é concedido por meio a função de [proprietário](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrador de acesso do usuário](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . Se sua conta é atribuída a função **Colaborador** , você receberá um erro ao tentar atribuir a entidade de serviço a uma função. Novamente, o administrador de assinatura deve conceder acesso suficiente.

Agora, vá para uma seção para autenticação de [senha](#create-service-principal-with-password) ou [certificado](#create-service-principal-with-certificate) .

## <a name="create-service-principal-with-password"></a>Criar serviço principal com senha

Nesta seção, você deve executar as etapas para:

- criar o aplicativo do AD com uma senha
- criar a entidade de serviço
- atribuir a função de leitor de capital de serviço

Para executar essas etapas rapidamente, consulte os seguintes três cmdlets. 

     $app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
     New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
     New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Vamos dar uma olhada estas etapas mais atentamente para certificar-se de que você entende o processo.

1. Entre em sua conta.

        Add-AzureRmAccount

1. Crie um novo aplicativo do Active Directory, fornecendo um nome de exibição, o URI que descreva o seu aplicativo, os URIs que identifique seu aplicativo e a senha da sua identidade de aplicativo.

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "<Your_Password>"

     Para aplicativos de único locatário, os URIs não são validados.
     
     Se sua conta não tem as [permissões necessárias](#required-permissions) no Active Directory, você vê uma mensagem de erro indicando "Authentication_Unauthorized" ou "nenhuma assinatura encontrada no contexto".

1. Examine o novo objeto de aplicativo. 

        $app
        
     Observe em particular a propriedade **ApplicationId** , que é necessário para a criação de objetos de serviço, atribuições de função e adquirir o token de acesso.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}

2. Crie um principal de serviço do aplicativo passando a id de aplicativo do aplicativo do Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

3. Conceda as permissões de serviço principal na sua assinatura. Neste exemplo, você pode adicionar o capital de serviço à função **leitor** , que concede permissão para ler todos os recursos na assinatura. Para outras funções, consulte [RBAC: funções internas](./active-directory/role-based-access-built-in-roles.md). Para o parâmetro **ServicePrincipalName** , forneça a **ApplicationId** que você usou ao criar o aplicativo. 

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Se sua conta não tem permissões suficientes para atribuir uma função, você verá uma mensagem de erro. A mensagem informa que sua conta **não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' sobre escopo ' / assinaturas / {guid}'**. 

Pronto! Seu aplicativo de AD e capital de serviço estão configurados. A próxima seção mostra como fazer logon com a credencial através do PowerShell. Se você quiser usar a credencial em seu aplicativo de código, você pode saltar para os [aplicativos de exemplo](#sample-applications). 

### <a name="provide-credentials-through-powershell"></a>Fornecer credenciais através do PowerShell

Agora, você precisa fazer logon como o aplicativo para executar operações.

1. Crie um objeto de **PSCredential** que contém suas credenciais executando o comando **Get-Credential** . É necessário o **ApplicationId** antes de executar este comando portanto, verifique se você tem disponível para colar.

        $creds = Get-Credential

2. Você será solicitado que você digite suas credenciais. O nome de usuário, use o **ApplicationId** que você usou ao criar o aplicativo. Para a senha, use um que você especificou ao criar a conta.

     ![inserir credenciais](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

2. Sempre que você entrar como um objeto de serviço, você precisa fornecer a identificação de locatário do diretório para seu aplicativo do AD. Um locatário é uma instância do Active Directory. Se você tiver apenas uma assinatura, você pode usar:

        $tenant = (Get-AzureRmSubscription).TenantId
    
     Se você tiver mais de uma assinatura, especifique a assinatura onde reside a seu Active Directory. Para obter mais informações, consulte [como Azure assinaturas são associadas com o Active Directory do Azure](./active-directory/active-directory-how-subscriptions-associated-directory.md).

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

4. Faça logon como o capital de serviço especificando que essa conta é uma entidade de serviço e fornecendo o objeto de credenciais. 

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     Agora, você está autenticado como o serviço principal para o aplicativo do Active Directory que você criou.

### <a name="save-access-token-to-simplify-log-in"></a>Salvar o token de acesso para simplificar login

Para evitar fornecer as credenciais de principal de serviço sempre que ele precisa fazer logon no, você pode salvar o token de acesso.

1. Para usar o token de acesso atual em uma sessão posterior, salve o perfil.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Abra o perfil e examine seu conteúdo. Observe que ele contém um token de acesso. 
        
2. Em vez de manualmente efetuar login novamente, basta carrega o perfil.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] O token de acesso expira, portanto usar um perfil salvo funciona somente para desde o token é válido.
        
## <a name="create-service-principal-with-certificate"></a>Criar serviço principal com certificado

Nesta seção, você deve executar as etapas para:

- criar um certificado autoassinado
- criar o aplicativo do AD com o certificado
- criar a entidade de serviço
- atribuir a função de leitor de capital de serviço

Para executar essas etapas com Azure PowerShell 2.0 rapidamente no Windows 10 ou no Windows Server 2016 Technical Preview, consulte os seguintes cmdlets. 

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Vamos dar uma olhada estas etapas mais atentamente para certificar-se de que você entende o processo. Este artigo também mostra como realizar as tarefas em versões anteriores do Azure PowerShell ou sistemas operacionais.

### <a name="create-the-self-signed-certificate"></a>Criar o certificado auto-assinado

A versão do PowerShell disponível com o Windows 10 e Windows Server 2016 Technical Preview tem um cmdlet **New-SelfSignedCertificate** atualizado para gerar um certificado auto-assinado. Sistemas operacionais anteriores têm o cmdlet New-SelfSignedCertificate, mas ela não oferece os parâmetros necessários para este tópico. Em vez disso, você precisa importar um módulo para gerar o certificado. Este tópico mostra as duas abordagens para gerar o certificado baseado no sistema operacional que você tem. 

- Se você tiver o **Windows 10 ou no Windows Server 2016 Technical Preview**, execute o seguinte comando para criar um certificado autoassinado: 

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
- Se você **não tiver o Windows 10 ou Windows Server 2016 Technical Preview**, você precisará baixar o [auto-assinado gerador de certificado](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) do Microsoft Script Center. Extrair seu conteúdo e importe o cmdlet que é necessário.
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
    
     Em seguida, gere o certificado.
    
        $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"

Você tem seu certificado e pode prosseguir com a criação de seu aplicativo do AD.

### <a name="create-the-active-directory-app-and-service-principal"></a>Criar o aplicativo do Active Directory e o serviço principal

1. Recupere o valor da chave do certificado.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Entrar sua conta do Azure.

        Add-AzureRmAccount

3. Crie um novo aplicativo do Active Directory, fornecendo um nome de exibição, o URI que descreva o seu aplicativo, os URIs que identifique seu aplicativo e a senha da sua identidade de aplicativo.

     Se você tiver o Azure PowerShell 2.0 uso (agosto 2016 ou posterior), o seguinte cmdlet:

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Se você tiver Azure PowerShell 1.0, use o seguinte cmdlet:
    
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Para aplicativos de único locatário, os URIs não são validados.
    
    Se sua conta não tem as [permissões necessárias](#required-permissions) no Active Directory, você vê uma mensagem de erro indicando "Authentication_Unauthorized" ou "nenhuma assinatura encontrada no contexto".
        
    Examine o novo objeto de aplicativo. 

        $app

    Observe que a propriedade **ApplicationId** , que é necessário para a criação de objetos de serviço, atribuições de função e adquirir tokens de acesso.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}


5. Crie um principal de serviço do aplicativo passando a id de aplicativo do aplicativo do Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

6. Conceda as permissões de serviço principal na sua assinatura. Neste exemplo, você pode adicionar o capital de serviço à função **leitor** , que concede permissão para ler todos os recursos na assinatura. Para outras funções, consulte [RBAC: funções internas](./active-directory/role-based-access-built-in-roles.md). Para o parâmetro **ServicePrincipalName** , forneça a **ApplicationId** que você usou ao criar o aplicativo.

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Se sua conta não tem permissões suficientes para atribuir uma função, você verá uma mensagem de erro. A mensagem informa que sua conta **não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' sobre escopo ' / assinaturas / {guid}'**.

Pronto! Seu aplicativo de AD e capital de serviço estão configurados. A próxima seção mostra como fazer logon com certificado através do PowerShell.

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornecer certificado por meio de script do PowerShell automatizado

Sempre que você entrar como um objeto de serviço, você precisa fornecer a identificação de locatário do diretório para seu aplicativo do AD. Um locatário é uma instância do Active Directory. Se você tiver apenas uma assinatura, você pode usar:

    $tenant = (Get-AzureRmSubscription).TenantId
    
Se você tiver mais de uma assinatura, especifique a assinatura onde reside a seu Active Directory. Para obter mais informações, consulte [administrar seu diretório Azure AD](./active-directory/active-directory-administer.md).

    $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

Para autenticar no script, especifique a conta é um serviço principal e forneça a impressão digital do certificado, a id do aplicativo e identificação de locatário. Para automatizar o script, você pode armazenar esses valores como variáveis de ambiente e recuperá-los durante a execução, ou você pode incluí-las no script.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant

Agora, você está autenticado como o serviço principal para o aplicativo do Active Directory que você criou.

## <a name="sample-applications"></a>Aplicativos de exemplo

Os aplicativos de exemplo a seguir mostram como efetuar login como o capital de serviço.

**.NET**

- [Implantar um SSH habilitado máquina virtual com um modelo com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Gerenciar recursos do Azure e grupos de recursos com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Introdução aos recursos - implantar usando o modelo do Gerenciador de recursos Azure - em Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Introdução aos recursos - gerenciar o grupo de recursos - em Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Implantar um SSH habilitado máquina virtual com um modelo em Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Gerenciando o recurso Azure e grupos de recursos com Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node**

- [Implantar um SSH habilitado máquina virtual com um modelo no Node](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Gerenciar recursos do Azure e grupos de recursos com Node](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Implantar um SSH habilitado máquina virtual com um modelo em Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Gerenciando o recurso Azure e grupos de recursos com Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Próximas etapas
  
- Para obter etapas detalhadas sobre como integrar um aplicativo do Azure para gerenciar recursos, consulte [o guia do desenvolvedor autorização com a API do Gerenciador de recursos do Azure](resource-manager-api-authentication.md).
- Para obter uma explicação mais detalhada de aplicativos e objetos de serviço, consulte [objetos de aplicativo e Principal do serviço](./active-directory/active-directory-application-objects.md). 
- Para obter mais informações sobre autenticação do Active Directory, consulte [Cenários de autenticação do Azure AD](./active-directory/active-directory-authentication-scenarios.md).



