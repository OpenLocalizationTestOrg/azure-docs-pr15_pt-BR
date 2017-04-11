<properties
   pageTitle="Criar serviço principal com CLI Azure | Microsoft Azure"
   description="Descreve como usar CLI do Azure para criar um aplicativo do Active Directory e o serviço principal e conceder acesso aos recursos através do controle de acesso baseado em função. Ele mostra como autenticar o aplicativo com uma senha ou o certificado."
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
   ms.date="09/30/2016"
   ms.author="tomfitz"/>

# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>Usar CLI do Azure para criar um objeto de serviço para acessar recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)


Quando você tiver um aplicativo ou script que precise acessar recursos, provavelmente você não deseja executar esse processo em suas próprias credenciais. Você pode ter permissões diferentes que você deseja para o aplicativo, e você não desejar que o aplicativo para continuar usando suas credenciais se alterar suas responsabilidades. Em vez disso, você pode criar uma identidade do aplicativo que inclui as credenciais de autenticação e as atribuições de função. Sempre que o aplicativo é executado, ele autentica com essas credenciais. Este tópico mostra como usar [CLI do Azure para Mac, Linux e Windows](xplat-cli-install.md) para configurar um aplicativo seja executado em seu próprio credenciais e identidade.

Com o Azure CLI, você tem duas opções para autenticar seu aplicativo do AD:

 - senha
 - certificado

Este tópico mostra como usar as duas opções no Azure CLI. Se você pretende efetuar login no Azure de uma estrutura de programação (como Python, Ruby ou node), autenticação de senha pode ser a melhor opção. Antes de decidir se deseja usar uma senha ou um certificado, consulte a seção de [aplicativos de exemplo](#sample-applications) para obter exemplos de autenticar nas estruturas diferentes.

## <a name="active-directory-concepts"></a>Conceitos do Active Directory

Neste artigo, você criar dois objetos - o aplicativo do Active Directory (AD) e o serviço principal. O aplicativo do AD é a representação global de seu aplicativo. Ele contém as credenciais (uma id de aplicativo e uma senha ou certificado). O serviço principal é a representação local do seu aplicativo em um Active Directory. Ele contém a atribuição de função. Este tópico se concentra em um aplicativo de locatário do único onde o aplicativo destina-se para executar em apenas uma organização. Normalmente, você usa o único locatário aplicativos para aplicativos de linha de negócios que são executados dentro de sua organização. Em um aplicativo único locatário, você tem um aplicativo do AD e capital de um serviço.

Você pode estar se perguntando - por que eu preciso ambos os objetos? Essa abordagem faz mais sentido quando você considera locatários vários aplicativos. Normalmente você usa vários locatários aplicativos para aplicativos do software como serviço (SaaS), onde seu aplicativo é executado em muitas assinaturas diferentes. Para aplicativos de vários locatários, você tem um aplicativo do AD e vários objetos de serviço (um em cada Active Directory que concede acesso ao aplicativo). Para configurar um aplicativo de vários locatário, consulte [o guia do desenvolvedor autorização com a API do Gerenciador de recursos do Azure](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Permissões necessárias

Para concluir este tópico, você deve ter permissões suficientes em sua Azure Active Directory e sua assinatura do Azure. Especificamente, você deve ser capaz de criar um aplicativo no Active Directory e atribuir a entidade de serviço a uma função. 

No seu Active Directory, sua conta deve ser um administrador (como **Administrador Global** ou **Administrador de usuário**). Se sua conta for atribuída à função de **usuário** , você precisa ter um administrador elevar suas permissões.

Em sua assinatura, sua conta deve ter `Microsoft.Authorization/*/Write` acesso, que é concedido por meio a função de [proprietário](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrador de acesso do usuário](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . Se sua conta é atribuída a função **Colaborador** , você receberá um erro ao tentar atribuir a entidade de serviço a uma função. Novamente, o administrador de assinatura deve conceder acesso suficiente.

Agora, vá para uma seção para autenticação de [senha](#create-service-principal-with-password) ou [certificado](#create-service-principal-with-certificate) .

## <a name="create-service-principal-with-password"></a>Criar serviço principal com senha

Nesta seção, você execute as etapas para criar o aplicativo do AD com uma senha e atribuir a função de leitor ao serviço principal.

Vamos dar uma olhada estas etapas.

1. Entre em sua conta.

        azure login

1. Você tem duas opções para criar o aplicativo do AD. Você pode criar o aplicativo do AD e o serviço principal em uma etapa, ou criá-los separadamente. Criá-los em uma etapa se você não precisa especificar uma home page e identificador URIs para seu aplicativo. Criá-los separadamente se você precisar definir esses valores para um aplicativo web. Ambas as opções são mostradas nesta etapa.

     - Para criar o aplicativo do AD e serviço principal em uma etapa, forneça o nome do aplicativo e uma senha, conforme mostrado no seguinte comando:
     
            azure ad sp create -n exampleapp -p {your-password}     
     
     - Para criar o aplicativo do AD separadamente, forneça o nome do aplicativo, uma home page URI, identificador URIs e uma senha, conforme mostrado no seguinte comando:
     
            azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>

         O comando anterior retorna um valor de AppId. Para criar uma entidade de serviço, forneça esse valor como um parâmetro no comando a seguir:
     
            azure ad sp create -a <AppId>
     
     Se sua conta não tem as [permissões necessárias](#required-permissions) no Active Directory, você vê uma mensagem de erro indicando "Authentication_Unauthorized" ou "nenhuma assinatura encontrada no contexto".
    
     Para ambas as opções, o novo objeto de serviço é retornado. A **Id do objeto** é necessária ao conceder permissões. O guid listado com os **Nomes principais de serviço** é necessária ao fazer logon. Esse guid é o mesmo valor que a id de aplicativo. Em aplicativos de exemplo, esse valor é conhecido como a **ID do cliente**. 
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. Conceda as permissões de serviço principal na sua assinatura. Neste exemplo, você pode adicionar o capital de serviço à função **leitor** , que concede permissão para ler todos os recursos na assinatura. Para outras funções, consulte [RBAC: funções internas](./active-directory/role-based-access-built-in-roles.md). Para o parâmetro **ServicePrincipalName** , forneça a **ID do objeto** que você usou ao criar o aplicativo. 

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

     Se sua conta não tem permissões suficientes para atribuir uma função, você verá uma mensagem de erro. A mensagem informa que sua conta **não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' sobre escopo ' / assinaturas / {guid}'**. 

Pronto! Seu aplicativo de AD e capital de serviço estão configurados. A próxima seção mostra como fazer logon com a credencial através do Azure CLI. Se você quiser usar a credencial em seu aplicativo de código, você não precisa continuar com este tópico. Você pode saltar para os [aplicativos de exemplo](#sample-applications) para obter exemplos de log com sua id de aplicativo e a senha. 

### <a name="provide-credentials-through-azure-cli"></a>Fornecer credenciais por meio de CLI do Azure

Agora, você precisa fazer logon como o aplicativo para executar operações.

1. Sempre que você entrar como um objeto de serviço, você precisa fornecer a identificação de locatário do diretório para seu aplicativo do AD. Um locatário é uma instância do Active Directory. Para recuperar a id de locatário para sua assinatura atualmente autenticada, use:

        azure account show

     Que retorna:

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Se você precisar obter a id de locatário de outra assinatura, use o seguinte comando:

        azure account show -s {subscription-id}

2. Se você precisar recuperar a id do cliente a ser usado para efetuar login no, use:

        azure ad sp show -c exampleapp --json

     O valor a ser usado para fazer login é o guid listado nos nomes principais de serviço.

        [
          {
            "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "7132aca4-1bdb-4238-ad81-996ff91d8db4"
            ]
          }
        ]

3. Faça logon como o capital de serviço.

        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}

    Você será solicitado a senha. Fornece a senha que você especificou ao criar o aplicativo do AD.

        info:    Executing command login
        Password: ********

Agora, você está autenticado como de capital de serviço para o capital de serviço que você criou.

## <a name="create-service-principal-with-certificate"></a>Criar serviço principal com certificado

Nesta seção, você deve executar as etapas para:

- criar um certificado autoassinado
- criar o aplicativo do AD com o certificado e a entidade de serviço
- atribuir a função de leitor de capital de serviço

Para concluir essas etapas, você deve ter [OpenSSL](http://www.openssl.org/) instalado.

1. Crie um certificado autoassinado.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Combine as chaves pública e privadas.

        cat privkey.pem cert.pem > examplecert.pem

3. Abra o arquivo **examplecert.pem** e procure a longa sequência de caracteres entre **---início certificado---** e **---Encerrar certificado--**. Copie os dados de certificado. Você pode passar dados como um parâmetro ao criar o serviço principal.

1. Entre em sua conta.

        azure login

1. Você tem duas opções para criar o aplicativo do AD. Você pode criar o aplicativo do AD e o serviço principal em uma etapa, ou criá-los separadamente. Criá-los em uma etapa se você não precisa especificar uma home page e identificador URIs para seu aplicativo. Criá-los separadamente se você precisar definir esses valores para um aplicativo web. Ambas as opções são mostradas nesta etapa.

     - Para criar o aplicativo do AD e serviço principal em uma etapa, forneça o nome do aplicativo e os dados de certificado, conforme mostrado no seguinte comando:
     
            azure ad sp create -n exampleapp --cert-value <certificate data>
     
     - Para criar o aplicativo do AD separadamente, forneça o nome do aplicativo, uma home page URI, identificador URIs e os dados de certificado, conforme mostrado no seguinte comando:
     
            azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example --cert-value <certificate data>

         O comando anterior retorna um valor de AppId. Para criar uma entidade de serviço, forneça esse valor como um parâmetro no comando a seguir:
     
            azure ad sp create -a <AppId>
  
     Se sua conta não tem as [permissões necessárias](#required-permissions) no Active Directory, você vê uma mensagem de erro indicando "Authentication_Unauthorized" ou "nenhuma assinatura encontrada no contexto".
    
     Para ambas as opções, o novo objeto de serviço é retornado. O Id do objeto é necessária ao conceder permissões. O guid listado com os **Nomes principais de serviço** é necessária ao fazer logon. Esse guid é o mesmo valor que a id de aplicativo. Em aplicativos de exemplo, esse valor é conhecido como a **ID do cliente**. 
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Conceda as permissões de serviço principal na sua assinatura. Neste exemplo, você pode adicionar o capital de serviço à função **leitor** , que concede permissão para ler todos os recursos na assinatura. Para outras funções, consulte [RBAC: funções internas](./active-directory/role-based-access-built-in-roles.md). Para o parâmetro **ServicePrincipalName** , forneça a **ID do objeto** que você usou ao criar o aplicativo. 

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

     Se sua conta não tem permissões suficientes para atribuir uma função, você verá uma mensagem de erro. A mensagem informa que sua conta **não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' sobre escopo ' / assinaturas / {guid}'**. 

### <a name="provide-certificate-through-automated-azure-cli-script"></a>Fornecer certificado através de script de CLI Azure automatizado

Agora, você precisa fazer logon como o aplicativo para executar operações.

1. Sempre que você entrar como um objeto de serviço, você precisa fornecer a identificação de locatário do diretório para seu aplicativo do AD. Um locatário é uma instância do Active Directory. Para recuperar a id de locatário para sua assinatura atualmente autenticada, use:

        azure account show

     Que retorna:

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Se você precisar obter a id de locatário de outra assinatura, use o seguinte comando:

        azure account show -s {subscription-id}

1. Para recuperar a impressão digital do certificado e remover caracteres desnecessários, use:

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     Que retorna um valor de impressão digital semelhante a:

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Se você precisar recuperar a id do cliente a ser usado para efetuar login no, use:

        azure ad sp show -c exampleapp

     O valor a ser usado para fazer login é o guid listado nos nomes principais de serviço.

        [
          {
            "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "4fd39843-c338-417d-b549-a545f584a745",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "4fd39843-c338-417d-b549-a545f584a745"
            ]
          }
        ]

1. Faça logon como o capital de serviço.

        azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

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
- Para obter mais informações sobre como usar certificados e CLI do Azure, consulte [autenticação baseada em certificado com objetos de serviço do Azure da linha de comando do Linux](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx). 
