<properties 
    pageTitle="Como criar APIs no gerenciamento de API do Azure" 
    description="Aprenda a criar e configurar APIs no gerenciamento de API do Azure." 
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

# <a name="how-to-create-apis-in-azure-api-management"></a>Como criar APIs no gerenciamento de API do Azure

Uma API API gerenciamento representa um conjunto de operações que podem ser chamados por aplicativos clientes. Novas APIs são criados no portal do publisher e, em seguida, as operações desejadas são adicionadas. Depois que as operações são adicionadas, a API é adicionada a um produto e pode ser publicada. Depois que uma API é publicada, pode ser assinado e usado por desenvolvedores.

Este guia mostra a primeira etapa do processo: como criar e configurar uma nova API de gerenciamento de API. Para obter mais informações sobre como adicionar operações e a publicação de um produto, consulte [como adicionar operações para uma API][] e [como criar e publicar um produto][].

## <a name="create-new-api"> </a>Criar uma nova API

APIs são criadas e configurados no portal do publisher. Para acessar o portal do publisher, clique em **Gerenciar** no Portal de clássico do Azure para o serviço de gerenciamento de API.

![Portal do Publisher][api-management-management-console]

>Se você ainda não tiver criado uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][] do tutorial de [Introdução ao gerenciamento de API do Azure][] .

Clique em **APIs** no menu de **Gerenciamento de API** à esquerda e clique em **Adicionar API**.

![Criar API][api-management-create-api]

Use a janela **Adicionar nova API** para configurar a nova API.

![Adicionar nova API][api-management-add-new-api]

Os campos a seguir são usados para configurar a nova API.

-   **Nome da Web API** fornece um nome exclusivo e descritivo para a API. Ela é exibida em portais de desenvolvedor e publisher.
-   **URL do serviço Web** referencia o serviço HTTP implementando API. Gerenciamento de API encaminha as solicitações para este endereço.
-   **URL da Web API sufixo** é acrescentado à URL base para o serviço de gerenciamento de API. A base URL é comum para todas as APIs hospedadas por uma instância de serviço de gerenciamento de API. Gerenciamento de API distingue APIs pelo seu sufixo e, portanto, o sufixo deve ser exclusivo para cada API para um determinado editor.
-   **Esquema de URL da Web API** determina quais protocolos podem ser usados para acessar a API. HTTPs é especificado por padrão.
-   Para opcionalmente adicionar essa nova API para um produto, clique em **produtos (opcionais)** suspensa e escolha um produto. Esta etapa pode ser repetida várias vezes para adicionar a API para vários produtos.

Quando os valores desejados estão configurados, clique em **Salvar**. Depois que a nova API é criada, a página de resumo para a API é exibida no portal do publisher.

![Resumo de API][api-management-api-summary]

## <a name="configure-api-settings"> </a>Configurações de configurar API

Você pode usar a guia **configurações** para verificar e edite a configuração para uma API. **Nome da Web API**, **URL do serviço Web**e **sufixo de URL da Web API** são inicialmente definida quando a API é criada e pode ser modificada aqui. **Descrição** fornece uma descrição opcional e **esquema de URL da Web API** determina quais protocolos podem ser usados para acessar a API.

![Configurações de API][api-management-api-settings]

Para configurar a autenticação de gateway para o serviço de back-end implementando API, selecione a guia **segurança** . Menu **com credenciais** suspenso pode ser usado para configurar a autenticação **básica HTTP** ou **certificados de cliente** . Para usar autenticação básica HTTP, basta inserir as credenciais desejadas. Para obter informações sobre como usar autenticação de certificado de cliente, consulte [como proteger serviços de back-end usando autenticação de certificado de cliente no gerenciamento de API do Azure][].

Na guia **segurança** também pode ser usada para configurar a **autorização do usuário** usando OAuth 2.0. Para obter mais informações, consulte [como autorizar contas de desenvolvedor usando OAuth 2.0 no gerenciamento de API do Azure][].

![Configurações de autenticação básica][api-management-api-settings-credentials]

Clique em **Salvar** para salvar as alterações feitas nas configurações de API.

## <a name="next-steps"> </a>Próximos passos

Depois que uma API é criada e as configurações definidas, as próximas etapas são para adicionar as operações à API, adicione a API a um produto e publicá-lo para que fique disponível para os desenvolvedores. Para obter mais informações, consulte os artigos a seguir.

-   [Como adicionar operações para uma API][]
-   [Como criar e publicar um produto][]





[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[Como adicionar operações para uma API]: api-management-howto-add-operations.md
[Como criar e publicar um produto]: api-management-howto-add-products.md

[Introdução ao gerenciamento de API do Azure]: api-management-get-started.md
[Criar uma instância de serviço de gerenciamento de API]: api-management-get-started.md#create-service-instance
[Como proteger serviços de back-end usando o cliente de autenticação de certificado no gerenciamento de API do Azure]: api-management-howto-mutual-certificates.md
[Como autorizar contas de desenvolvedor usando OAuth 2.0 no gerenciamento de API do Azure]: api-management-howto-oauth2.md