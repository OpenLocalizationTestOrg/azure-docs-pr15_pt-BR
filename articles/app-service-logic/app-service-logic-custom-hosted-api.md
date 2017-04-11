<properties
    pageTitle="Chamar uma API personalizada nos aplicativos de lógica"
    description="Usando seu API personalizado hospedado no serviço de aplicativo com aplicativos de lógica"
    authors="stepsic-microsoft-com"
    manager="dwrede"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="stepsic"/>

# <a name="using-your-custom-api-hosted-on-app-service-with-logic-apps"></a>Usando seu API personalizado hospedado no serviço de aplicativo com aplicativos de lógica

Embora os aplicativos de lógica tenha um conjunto sofisticado de 40 + conectores para uma variedade de serviços, talvez você queira chamar em sua própria API personalizado que pode executar seu próprio código. Uma das maneiras mais fácil e mais flexível para hospedar seu próprios web personalizado da API é usar o serviço de aplicativo. Este artigo discute como ligar para qualquer web API hospedado em um aplicativo API do serviço de aplicativo, a web app ou o aplicativo móvel.

Para obter informações sobre a criação de APIs como um disparador ou ação dentro de aplicativos de lógica, confira [neste artigo](app-service-logic-create-api-app.md).

## <a name="deploy-your-web-app"></a>Implantar o seu aplicativo Web

Primeiro, você precisará implantar sua API como um aplicativo Web no aplicativo de serviço. Implantação básica de rosto as instruções aqui: [criar um aplicativo web do ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).  Enquanto você pode telefonar para qualquer API a partir de um aplicativo de lógica, para a melhor experiência, que recomendamos que você adicione metadados de Swagger para integrar-se facilmente com ações de aplicativos de lógica.  Você pode obter detalhes sobre como [Adicionar swagger](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui).

### <a name="api-settings"></a>Configurações de API

Em ordem para o designer de aplicativos lógica para analisar seu Swagger, é importante que você habilite CORS e definir as propriedades de APIDefinition de seu aplicativo web.  Isso é muito fácil definir dentro do Portal do Azure.  Basta abrir a lâmina de configurações de seu aplicativo Web, sob a seção API configurar a 'definição de API' para a URL de seu arquivo de swagger.json (é geralmente https://{name}.azurewebsites.net/swagger/docs/v1) e adicionar uma política CORS para ' *' para permitir solicitações de aplicativos lógica Designer.

## <a name="calling-into-the-api"></a>Chamando a API

Quando dentro do portal de aplicativos de lógica, se você tiver definido CORS e as propriedades de definição de API você deve conseguir facilmente adicione ações personalizadas API dentro de seu fluxo.  No designer, você pode selecionar para navegar em seus sites de assinatura para listar os sites com uma URL de swagger definida.  Você também pode usar o HTTP + Swagger ação apontar para um swagger e ações disponíveis e entradas de lista.  Por fim, você sempre pode criar uma solicitação usando a ação de HTTP para chamar qualquer API, mesmo aqueles que não têm ou expor um documento swagger.

Se você deseja proteger sua API, há duas maneiras diferentes de fazer isso:

1. Nenhuma alteração de código necessária - Azure Active Directory pode ser usada para proteger sua API sem a necessidade de qualquer alteração de código ou reimplantação.
1. Impor autenticação básica, AAD Auth ou autenticação de certificado no código do seu API.

## <a name="securing-calls-to-your-api-without-a-code-change"></a>Protegendo chamadas para seu API sem uma alteração de código

Nesta seção, você vai criar dois aplicativos Azure Active Directory – uma para o aplicativo de lógica e outra para seu aplicativo Web.  Você vai autenticar chamadas para seu aplicativo Web usando a entidade de serviço (id do cliente e segredo) associada com o aplicativo AAD para o aplicativo de lógica. Finalmente, você vai incluir o aplicativo IDs em sua definição de aplicativo de lógica.

### <a name="part-1-setting-up-an-application-identity-for-your-logic-app"></a>Parte 1: Configurar uma identidade de aplicativo para o aplicativo de lógica

Este é o que o aplicativo de lógica usa para autenticar do active directory. Você só *precisa* fazê-lo uma vez para seu diretório. Por exemplo, você pode optar por usar a mesma identidade para todos os seus aplicativos de lógica, embora você também pode criar identidades exclusivas por aplicativo lógica se desejar. Você pode fazer isso na interface do usuário ou usar o PowerShell.

#### <a name="create-the-application-identity-using-the-azure-classic-portal"></a>Criar a identidade do aplicativo usando o portal de clássico Azure

1. Navegue para o [Active directory no portal de clássico do Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) e selecione o diretório que você usa para o aplicativo Web
2. Clique na guia **aplicativos**
3. Clique em **Adicionar** na barra de comandos na parte inferior da página
4. Nomeie sua identidade usar, clique na seta próxima
5. Colocar em uma cadeia de caracteres exclusiva formatada como um domínio em duas caixas de texto e clique na marca de seleção
6. Clique na guia **Configurar** para este aplicativo
7. Copiar a **ID do cliente**, ele será usado em seu aplicativo de lógica
8. Na seção **teclas** clique em **Selecionar duração** e escolha ano 1 ou 2 anos
9. Clique no botão **Salvar** na parte inferior da tela (talvez seja necessário aguardar alguns segundos)
10. Certifique-se agora copiar a chave na caixa. Isso também será usado em seu aplicativo de lógica

#### <a name="create-the-application-identity-using-powershell"></a>Criar a identidade do aplicativo usando o PowerShell
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. Certifique-se de copiar o **Locatário ID**, a **ID do aplicativo** e a senha usada

### <a name="part-2-protect-your-web-app-with-an-aad-app-identity"></a>Parte 2: Proteger seu aplicativo Web com uma identidade de aplicativo AAD

Se o seu aplicativo Web já está implantado apenas você pode ativá-la no portal. Caso contrário, você pode fazer a ativação de autorização parte da sua implantação do Gerenciador de recursos do Azure.

#### <a name="enable-authorization-in-the-azure-portal"></a>Ativar a autorização no Portal do Azure

1. Navegue até o Web app e clique nas **configurações** na barra de comando.
2. Clique em **Autorização/autenticação**.
3. **Ativá-la.**

Neste ponto, um aplicativo é criado automaticamente para você. Você precisa de ID do cliente do aplicativo para parte 3, portanto você precisará:

1. Vá para o [Active directory no portal de clássico do Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) e selecione seu diretório.
2. Procure o aplicativo na caixa de pesquisa
3. Clique na lista
4. Clique na guia **Configurar**
5. Você deve ver o **Código do cliente**

#### <a name="deploying-your-web-app-using-an-arm-template"></a>Implantando seu aplicativo Web usando um modelo ARM

Primeiro, você precisa criar um aplicativo para o aplicativo Web. Isso deve ser diferente do aplicativo que é usado para o aplicativo de lógica. Comece seguindo as etapas acima na parte 1, mas agora para o uso da **home page** e **IdentifierUris** a https:// real**URL** de seu aplicativo Web.

>[AZURE.NOTE]Quando você cria o aplicativo para o aplicativo Web, você deve usar a [abordagem de portal clássica Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory), como o PowerShell commandlet não configurar as permissões necessárias para entrar os usuários em um site.

Uma vez que você tiver o cliente de ID e ID de locatário, inclua o seguinte como um recurso de sub do aplicativo Web no seu modelo de implantação:

```
"resources" : [
    {
        "apiVersion" : "2015-08-01",
        "name" : "web",
        "type" : "config",
        "dependsOn" : [
            "[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
        ],
        "properties" : {
            "siteAuthEnabled": true,
            "siteAuthSettings": {
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

Para executar uma implantação automaticamente que implante um aplicativo da Web em branco e um aplicativo de lógica junto que usam AAD, clique no botão a seguir:

[![Implantar para o Azure](./media/app-service-logic-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Para o modelo completo, consulte [chamadas de aplicativo de lógica em uma API personalizado hospedado no aplicativo de serviço e protegidos por AAD](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).


### <a name="part-3-populate-the-authorization-section-in-the-logic-app"></a>Parte 3: Preencher a seção de autorização no aplicativo lógica

Na seção **autorização** da ação **HTTP** :`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Elemento | Descrição |
|---------|-------------|
| tipo | Tipo de autenticação. Para autenticação de ActiveDirectoryOAuth, o valor é ActiveDirectoryOAuth. |
| locatário | O identificador de locatário usado para identificar o locatário do AD. |
| público-alvo | Necessário. O recurso que você está se conectando. |
| clientID | O identificador de cliente para o aplicativo do Azure AD. |
| segredo | Necessário. Segredo do cliente que está solicitando o token. |

O modelo acima já tem essa configurar, mas se você estiver criando o aplicativo de lógica diretamente, você precisará incluir a seção de autorização completo.

## <a name="securing-your-api-in-code"></a>Protegendo seu API no código

### <a name="certificate-auth"></a>Autenticação de certificado

Você pode usar certificados de cliente para validar as solicitações de entrada para o seu aplicativo Web. Consulte [Como para configurar comum para autenticação TLS Web App](../app-service-web/app-service-web-configure-tls-mutual-auth.md) para como configurar seu código.

Na seção *autorização* você deve fornecer: `{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`.

| Elemento | Descrição |
|---------|-------------|
| tipo | Necessário. Tipo de autenticação. Para certificados de cliente SSL, o valor deve ser ClientCertificate. |
| PFX | Necessário. Codificado na Base 64 o conteúdo do arquivo PFX. |
| senha | Necessário. Senha para acessar o arquivo PFX. |

### <a name="basic-auth"></a>Autenticação básica

Você pode usar autenticação básica (por exemplo, nome de usuário e senha) para validar as solicitações de entrada. Autenticação básica é um padrão comum e você poderá fazer isso em qualquer idioma que você criar seu aplicativo em.

Na seção *autorização* , você deve fornecer: `{"type": "basic","username": "test","password": "test"}`.

| Elemento | Descrição |
|---------|-------------|
| tipo | Necessário. Tipo de autenticação. Para a autenticação básica, o valor deve ser Basic. |
| nome de usuário | Necessário. Nome de usuário para autenticar. |
| senha | Necessário. Senha para autenticação. |

### <a name="handle-aad-auth-in-code"></a>Alça AAD auth no código

Por padrão, a autenticação do Active Directory do Azure que você habilitar no Portal não autorização refinada. Por exemplo, ele não bloquear sua API para um usuário específico ou um aplicativo, mas apenas para um locatário específico.

Se você quiser restringir a API apenas lógica aplicativo, por exemplo, no código, você pode extrair o cabeçalho que contém a JWT e verifique quem o chamador é, rejeitar todas as solicitações que não correspondem.

Indo além disso, se você quiser implementar-inteiramente em seu próprio código e não aproveitar o recurso de Portal, você pode ler este artigo: [Uso do Active Directory para autenticação em um serviço de aplicativo do Azure](../app-service-web/web-sites-authentication-authorization.md).

Você ainda precisa executar as etapas acima para criar uma identidade de aplicativo para o aplicativo de lógica e usá-lo para chamar a API.
