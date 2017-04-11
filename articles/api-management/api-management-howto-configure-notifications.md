<properties 
    pageTitle="Como configurar notificações e modelos de email no gerenciamento de API do Azure" 
    description="Aprenda a configurar notificações e modelos de gerenciamento de API do Azure de email." 
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

# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Como configurar notificações e modelos de email no gerenciamento de API do Azure

Gerenciamento de API fornece a capacidade de configurar notificações de eventos específicos e configurar os modelos de email que são usados para se comunicar com os administradores e desenvolvedores de uma instância de gerenciamento de API. Este tópico mostra como configurar notificações de eventos disponíveis e fornece uma visão geral de configuração de modelos de email usados para esses eventos.

## <a name="publisher-notifications"> </a>Configurar notificações do publisher

Para configurar notificações, clique em **Gerenciar** no Portal de clássico do Azure para o serviço de gerenciamento de API. Isso o leva para o portal de fornecedor de gerenciamento de API.

![Portal do Publisher][api-management-management-console]

>Se você ainda não tiver criado uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][] do tutorial de [Introdução ao gerenciamento de API do Azure][] .

Clique em **notificações** no menu **Gerenciamento de API** à esquerda para exibir as notificações disponíveis.

![Notificações do Publisher][api-management-publisher-notifications]

A seguinte lista de eventos pode ser configurada para notificações.

-   **Solicitações de assinatura (que exigem aprovação)** - os destinatários de email especificada e usuários receberá notificações por email sobre solicitações de assinatura para produtos de API que exigem aprovação.
-   **Novas assinaturas** - os destinatários de email especificada e usuários receberá notificações por email sobre novas assinaturas de API do produto.
-   **Solicitações de galeria de aplicativos** - os destinatários de email especificada e usuários receberá notificações por email quando novos aplicativos forem enviados à Galeria de aplicativo.
-   **Cco** - os destinatários de email especificada e os usuários receberão um email cópias ocultas de todos os emails enviados para os desenvolvedores.
-   **Nova questão ou comentário** - os destinatários de email especificada e usuários receberá notificações de email quando uma nova questão ou comentário é enviado no portal do desenvolvedor.
-   **Mensagem de encerrar conta** - os destinatários de email especificada e usuários receberá notificações por email quando uma conta é fechada.
-   **Limite de cota de assinatura Approaching** - os seguintes destinatários de email e usuários receberá notificações por email quando o uso de assinatura obtém próximo cota de uso.

Para cada evento, você pode especificar os destinatários de email usando a caixa de texto de endereço de email ou você pode selecionar usuários de uma lista.

Para especificar os endereços de email para ser notificado, insira-os na caixa de texto endereço de email. Se você tiver vários endereços de email, separe-os usando vírgulas.

![Destinatários de notificação][api-management-email-addresses]

Para especificar os usuários para ser notificado, clique em **Adicionar destinatário**, marque a caixa ao lado de usuários para ser notificado e clique em **Okey**.

>Observe que somente os administradores são exibidos na lista.

Depois de configurar os destinatários de notificação, clique em **Salvar** para aplicar os destinatários de notificação atualizadas.

>Se você navegar fora da guia **Notificações do Publisher** portal do publisher alerta se há alterações não salvas.

## <a name="email-templates"> </a>Configurar modelos de email

Gerenciamento de API fornece modelos de email para as mensagens de email enviadas durante a administração e o uso do serviço. Os seguintes modelos de email são fornecidos.

-   Envio de galeria de aplicativo aprovado
-   Carta de encerramento do desenvolvedor
-   Limite de cota de desenvolvedor se aproximando de notificação
-   Convidar usuário
-   Novo comentário adicionado a um problema
-   Nova questão recebida
-   Nova assinatura ativada
-   Confirmação de assinatura renovada
-   Recusa a solicitação de assinatura
-   Solicitação de inscrição recebida

Esses modelos podem ser modificados conforme desejado.

Para exibir e configurar os modelos de email para a sua instância de gerenciamento de API, clique em **notificações** no menu **Gerenciamento de API** à esquerda e selecione a guia **Modelos de Email** .

![Modelos de email][api-management-email-templates]

Para exibir ou modificar um modelo específico, selecione-o na lista suspensa de **modelos** .

![Lista de modelos de email][api-management-email-templates-list]

Cada modelo de email possui um assunto em texto sem formatação e a definição de corpo no formato HTML. Cada item pode ser personalizado conforme desejado.

![Editor de modelo de email][api-management-email-template]

A lista de **parâmetros** contém uma lista de parâmetros, que, quando inseridos no assunto ou no corpo, será substituído o valor designado quando o email é enviado. Para inserir um parâmetro, coloque o cursor onde deseja que o parâmetro para ir e clique na seta à esquerda do nome do parâmetro.

Clique em **Visualizar** ou **Enviar um teste** para ver como o email será pesquisar ou enviar um email de teste.

>Observe que os parâmetros não são substituídos por valores reais quando visualizar ou enviar um teste.
>
>Para salvar as alterações no modelo de email, clique em **Salvar**ou cancelar as alterações clique em **Cancelar**.



[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Introdução ao gerenciamento de API do Azure]: api-management-get-started.md
[Criar uma instância de serviço de gerenciamento de API]: api-management-get-started.md#create-service-instance