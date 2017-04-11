<properties 
    pageTitle="Como autorizar contas de desenvolvedor usando OAuth 2.0 no gerenciamento de API do Azure" 
    description="Saiba como autorizar usuários usando OAuth 2.0 no gerenciamento de API." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Como autorizar contas de desenvolvedor usando OAuth 2.0 no gerenciamento de API do Azure

Várias APIs suportam [OAuth 2.0](http://oauth.net/2/) para proteger a API e garantir que somente os usuários válidos tenham acesso, e eles podem acessar apenas os recursos aos quais eles tem direito. Para poder usar o Console interativo de desenvolvedor do gerenciamento de API do Azure com tais APIs, o serviço permite que você configure sua instância de serviço para trabalhar com seu OAuth 2.0 API habilitado.

## <a name="prerequisites"> </a>Pré-requisitos

Este guia mostra como configurar sua instância de serviço de gerenciamento de API para usar OAuth 2.0 autorização para contas do desenvolvedor, mas não mostram como configurar um provedor de OAuth 2.0. A configuração para cada provedor OAuth 2.0 é diferente, embora as etapas são semelhantes, e as informações necessárias usadas na configuração OAuth 2.0 na sua instância do serviço de gerenciamento de API são as mesmas. Este tópico mostra exemplos usando o Active Directory do Azure como um provedor de OAuth 2.0.

>[AZURE.NOTE] Para obter mais informações sobre como configurar OAuth 2.0 usando o Azure Active Directory, consulte o exemplo de [Web App-GraphAPI-DotNet][] .

## <a name="step1"> </a>Configurar um servidor de autorização OAuth 2.0 no gerenciamento de API

Para começar, clique em **Gerenciar** no Portal de clássico do Azure para o serviço de gerenciamento de API. Isso o leva para o portal de fornecedor de gerenciamento de API.

![Portal do Publisher][api-management-management-console]

>[AZURE.NOTE] Se você ainda não tiver criado uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][] do tutorial de [Introdução ao gerenciamento de API do Azure][] .

Clique em **segurança** no menu de **Gerenciamento de API** à esquerda, clique **OAuth 2.0**e, em seguida, clique em **Adicionar autorização server**.

![OAuth 2.0][api-management-oauth2]

Após clicar em **Adicionar servidor de autorização**, o novo formulário de servidor de autorização é exibido.

![Novo servidor][api-management-oauth2-server-1]

Insira um nome e uma descrição opcional nos campos **nome** e **Descrição** . 

>[AZURE.NOTE] Esses campos são usados para identificar o servidor de autorização OAuth 2.0 dentro da instância atual de serviço de gerenciamento de API e seus valores não vêm do servidor OAuth 2.0.

Insira a **URL de página de registro de cliente**. Esta página é onde os usuários podem criar e gerenciar suas contas e varia dependendo do provedor OAuth 2.0 usado. A **URL de página de registro de cliente** aponta para a página que os usuários podem usar para criar e configurar suas próprias contas para provedores de OAuth 2.0 que oferecem suporte a gerenciamento de contas de usuário. Algumas organizações não configurar ou usar essa funcionalidade, mesmo se o provedor de OAuth 2.0 suportá-la. Se o seu provedor de OAuth 2.0 não tiver gerenciamento de usuários de contas configurado, digite uma URL de espaço reservado como a URL da sua empresa ou uma URL como `https://placeholder.contoso.com`.

A próxima seção do formulário contém as configurações de **código de autorização conceder tipos**, **URL de ponto de extremidade de autorização**e **método de solicitação de autorização** .

![Novo servidor][api-management-oauth2-server-2]

Especifica o **código de autorização conceder tipos** marcando os tipos de desejado. **Código de autorização** é especificado por padrão.

Insira a **URL do ponto de extremidade de autorização**. Azure Active Directory, essa URL será semelhante ao seguinte URL, onde `<client_id>` é substituída com a id do cliente que identifica seu aplicativo para o servidor OAuth 2.0.

    https://login.windows.net/<client_id>/oauth2/authorize

O **método de solicitação de autorização** Especifica como a solicitação de autorização é enviada para o servidor OAuth 2.0. **Obter** é selecionado por padrão.

A próxima seção é onde a **URL do ponto de extremidade de Token**, **métodos de autenticação de cliente**, **enviando o método de token de acesso**e **escopo padrão** são especificados.

![Novo servidor][api-management-oauth2-server-3]

Para um servidor do Azure Active Directory OAuth 2.0, a **URL do ponto de extremidade de Token** terá o seguinte formato, onde `<APPID>` tem o formato de `yourapp.onmicrosoft.com`.

    https://login.windows.net/<APPID>/oauth2/token

A configuração padrão para **os métodos de autenticação de cliente** é **básico**e **token de acesso enviando método** é o **cabeçalho de autorização**. Esses valores são configurados nesta seção do formulário, juntamente com o **escopo padrão**.

A seção de **credenciais do cliente** contém a **ID do cliente** e o **segredo cliente**, que são obtidos durante o processo de criação e configuração do seu servidor OAuth 2.0. Assim que o **Código do cliente** e o **cliente segredo** forem especificados, a **redirect_uri** para o **código de autorização** é gerado. Esse URI é usada para configurar a URL de resposta na configuração de servidor OAuth 2.0.

![Novo servidor][api-management-oauth2-server-4]

Se o **código de autorização conceder tipos** está definido como **senha de proprietário do recurso**, na seção de **credenciais de senha de proprietário do recurso** é usada para especificar as credenciais; Caso contrário, você pode deixar em branco.

![Novo servidor][api-management-oauth2-server-5]

Quando o formulário for concluído, clique em **Salvar** para salvar a configuração de servidor de autorização de API gerenciamento OAuth 2.0. Após a configuração do servidor é salva, você pode configurar APIs para usar essa configuração, conforme mostrado na próxima seção.

## <a name="step2"> </a>Configurar uma API para usar a autorização de usuário OAuth 2.0

Clique **APIs** no menu **Gerenciamento de API** à esquerda, clique no nome da API desejado, clique em **segurança**e, em seguida, marque a caixa **OAuth 2.0**.

![Autorização do usuário][api-management-user-authorization]

Selecione o **servidor de autorização** de desejado na lista suspensa e clique em **Salvar**.

![Autorização do usuário][api-management-user-authorization-save]

## <a name="step3"> </a>Testar a autorização de usuário OAuth 2.0 no Portal do desenvolvedor

Depois de ter configurado seu servidor de autorização OAuth 2.0 e configurado seu API para usar esse servidor, você poderá testá-lo indo para o Portal do desenvolvedor e chamando uma API.  Clique em **portal do desenvolvedor** no menu superior direito.

![Portal do desenvolvedor][api-management-developer-portal-menu]

Clique **APIs** no menu superior e selecione **API de eco**.

![API de eco][api-management-apis-echo-api]

>[AZURE.NOTE] Se você tiver apenas uma API configurada ou visível para sua conta, clicando em APIs leva você diretamente para as operações para essa API.

Selecione a operação de **Obter recursos** , clique em **Console aberto**e selecione **código de autorização** na lista suspensa.

![Console aberto][api-management-open-console]

Quando **o código de autorização** é selecionada, uma janela pop-up é exibida com o formulário de entrada do provedor OAuth 2.0. Neste exemplo, o formulário de entrada é fornecido pelo Active Directory do Azure.

>[AZURE.NOTE] Se você tiver pop-ups desativados você será solicitado a ativá-las pelo navegador. Depois de habilitá-las, selecione **o código de autorização** novamente e o formulário de entrada serão exibidas.

![Entrar][api-management-oauth2-signin]

Depois que você tiver conectado, os **cabeçalhos de solicitação** são preenchidos com um `Authorization : Bearer` cabeçalho que autoriza a solicitação.

![Token de cabeçalho de solicitação][api-management-request-header-token]

Neste ponto você pode configurar os valores desejados para os parâmetros restantes e enviar a solicitação. 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar o gerenciamento de API e OAuth 2.0, consulte o vídeo a seguir e que acompanha [artigo](api-management-howto-protect-backend-with-aad.md).

> [AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introdução ao gerenciamento de API do Azure]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Criar uma instância de serviço de gerenciamento de API]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[Web App-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

