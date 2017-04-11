<properties 
    pageTitle="Autenticar com contrato móvel APIs REST"
    description="Descreve como autenticar com APIs do restante do Azure Mobile contrato" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="10/05/2016"
    ms.author="wesmc;ricksal"/>

# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Autenticar com contrato móvel APIs REST

## <a name="overview"></a>Visão geral

Este documento descreve como obter um Oauth AAD válido token para autenticar com as APIs REST do Mobile contrato. 

Será considerado que você tenha uma assinatura válida do Azure e você tiver criado um aplicativo móvel contrato usando um dos nossos [Tutoriais do desenvolvedor](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Autenticação

Um Microsoft Azure Active Directory com base OAuth token é usado para autenticação. 

Ordem a solicitação de autenticação uma API, um cabeçalho de autorização deve ser adicionado a cada solicitação que é da seguinte forma:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Tokens do Azure Active Directory expiram em 1 hora.

Há várias maneiras de obter um token. Como as APIs são geralmente chamadas de um serviço de nuvem, você deseja usar uma chave API. Uma chave de API na terminologia do Azure denomina-se uma senha principal do serviço. O procedimento a seguir descreve uma maneira de configurá-la manualmente.

### <a name="one-time-setup-using-script"></a>Configuração única (usando script)

Você deve seguir o conjunto de instruções abaixo para executar a configuração usando um script PowerShell que leva o tempo mínimo para a instalação, mas usa os padrões mais permitidos. Opcionalmente, você também pode siga as instruções na [configuração manual](mobile-engagement-api-authentication-manual.md) para fazer isso diretamente do portal do Azure e fazer mais refinada configuração. 

1. Obtenha a versão mais recente do PowerShell do Azure do [aqui](http://aka.ms/webpi-azps). Para obter mais informações sobre as instruções de download, você pode ver esse [link](../powershell-install-configure.md).  

2. Quando Azure PowerShell estiver instalado, use os seguintes comandos para garantir que você tenha o **módulo Azure** instalado:

    a. Verifique se que o módulo do PowerShell do Azure está disponível na lista de módulos disponíveis. 
    
        Get-Module –ListAvailable 

    ![Módulos Azure disponíveis][1]
        
    b. Se você não encontrar o módulo do PowerShell do Azure na lista acima, você precisará executar o seguinte:
        
        Import-Module Azure 
        
3. Login para o Gerenciador de recursos do Azure do PowerShell executando o seguinte comando e fornecendo seu nome de usuário e senha para sua conta do Azure: 
        
        Login-AzureRmAccount

4. Se você tiver várias assinaturas, em seguida, você deve executar o seguinte:

    a. Obter uma lista de todas as suas assinaturas e copie a SubscriptionId da assinatura que você deseja usar. Verifique se que esta assinatura é a mesma que tem o aplicativo de contrato de celular que você vai interagir com usando as APIs. 

        Get-AzureRmSubscription

    b. Execute o seguinte comando fornecendo a SubscriptionId para configurar a assinatura a ser usado.

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>

5. Copie o texto para o script de [Novo AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) em sua máquina local e salvá-lo como um cmdlet do PowerShell (por exemplo, `APIAuth.ps1`) e executá-lo `.\APIAuth.ps1`. 
    
6. O script solicitará que você forneça uma entrada de **principalName**. Forneça um nome adequado aqui que você deseja usar para criar seu aplicativo do Active Directory (por exemplo, APIAuth). 

7. Depois que o script for concluído, ele exibirá o seguinte quatro valores que você precisará autenticar programaticamente com AD então certifique-se para copiá-las. 
        
    **TenantId**, **SubscriptionId**, **ApplicationId**e **secreta**.

    Você usará TenantId como `{TENANT_ID}`, ApplicationId como `{CLIENT_ID}` e secreta como `{CLIENT_SECRET}`.

    > [AZURE.NOTE] Sua política de segurança padrão pode impedir a execução de um scripts do PowerShell. Em caso afirmativo, você temporariamente configurar sua política de execução para permitir a execução de scripts usando o seguinte comando:

        > Set-ExecutionPolicy RemoteSigned

8. Veja aqui como o conjunto de cmdlets do PS ficaria. 

    ![][3]

9. Fazer check-in do portal de gerenciamento do Azure que um novo aplicativo do AD foi criado com o nome fornecido para o script chamado **principalName** em **que aplicativos Mostrar minha empresa possui**.

    ![][4]

#### <a name="steps-to-get-a-valid-token"></a>Etapas para obter um token válido

1. Chamar a API com os seguintes parâmetros e certifique-se de substituir o LOCATÁRIO\_ID, cliente\_cliente e ID\_segredo:

    - **Solicitação de URL** como *https://login.microsoftonline.com/ {LOCATÁRIO\_ID} / oauth2/token*
    - **Cabeçalho de tipo de conteúdo HTTP** como *aplicativo/x-www-form-urlencoded*
    - **Corpo da solicitação HTTP** como *conceder\_tipo = cliente\_credenciais & client_id = {cliente\_ID} & client_secret = {cliente\_segredo} & resource=https%3A%2F%2Fmanagement.core.windows.net%2F*

    Este é um exemplo de solicitação:

        POST /{TENANT_ID}/oauth2/token HTTP/1.1
        Host: login.microsoftonline.com
        Content-Type: application/x-www-form-urlencoded
        grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
        urce=https%3A%2F%2Fmanagement.core.windows.net%2F

    Aqui está uma resposta de exemplo:

        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Content-Length: 1234
    
        {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
        5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}

    Este exemplo incluídos codificação de URL dos parâmetros POSTAGEM, `resource` valor é realmente `https://management.core.windows.net/`. Tenha cuidado para também URL codificar `{CLIENT_SECRET}` como ele pode conter caracteres especiais.

    > [AZURE.NOTE] Para testar, você pode usar uma ferramenta de cliente HTTP como [Fiddler](http://www.telerik.com/fiddler) ou [extensão carteiro Chrome](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) 

2. Agora em cada chamada de API, inclua o cabeçalho de solicitação de autorização:

        Authorization: Bearer {ACCESS_TOKEN}

    Se você receber um código de 401 status retornado, verifique o corpo de resposta, ele pode informar o token expirou. Nesse caso, obtenha um novo símbolo.

##<a name="using-the-apis"></a>Usando as APIs

Agora que você tem um token válido, você está pronto para fazer as chamadas de API.

1. Em cada solicitação de API, você precisará passar um token válido, ainda que você obteve na seção anterior.

2. Você precisará plug-in alguns parâmetros na solicitação URI que identifica seu aplicativo. Solicitação de URI a seguinte aparência

        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

    Para obter os parâmetros, clique no nome do seu aplicativo e clique em Painel de controle e você verá uma página com o seguinte com todos os 3 parâmetros.

    - **1**`{subscription-id}`
    - **2**`{app-collection}`
    - **3**`{app-resource-name}`
    - **4** o nome de seu grupo de recursos será **MobileEngagement** , a menos que você criou um novo. 

    ![Parâmetros de URI de API do contrato de celular][2]

>[AZURE.NOTE] <br/>
>1. Ignore o endereço da raiz de API como era para as APIs anterior.<br/>
>2. Se você criou o aplicativo usando o portal do Azure clássico, em seguida, você precisa usar o nome de recurso do aplicativo que é diferente do nome do aplicativo em si. Se você criou o aplicativo no Portal do Azure, em seguida, você deve usar o nome do aplicativo em si (não há nenhuma diferenciação entre o nome do recurso de aplicativo e o nome do aplicativo para aplicativos criados no novo portal).  

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png



