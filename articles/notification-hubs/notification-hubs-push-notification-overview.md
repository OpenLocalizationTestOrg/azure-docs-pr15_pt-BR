<properties
    pageTitle="Hubs de notificação do Azure"
    description="Saiba como usar notificações por push no Azure. Exemplos de código escritos em c# usando a API do .NET."
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="multiple"
    ms.devlang="multiple"
    ms.topic="hero-article"
    ms.date="08/25/2016"
    ms.author="yuaxu"/>


#<a name="azure-notification-hubs"></a>Hubs de notificação do Azure

##<a name="overview"></a>Visão geral

Azure Hubs de notificação fornecem uma infraestrutura de envio fácil de usar várias plataformas, dimensionada-out que permite que você envie notificações push móveis de qualquer back-end (na nuvem ou locais) qualquer plataforma móvel.

Com Hubs de notificação, você pode facilmente enviar as notificações por push entre plataformas e personalizadas, abstraindo os detalhes dos sistemas de notificação de plataforma diferente (PNS). Com uma única chamada de API, você pode direcionar usuários individuais ou segmentos de público inteiro contendo milhões de usuários, em todos os seus dispositivos.

Você pode usar Hubs de notificação para cenários corporativos e consumidor. Por exemplo:

- Envie notificações de notícias ruptura para milhões com baixa latência (Hubs de notificação alimenta aplicativos do Bing previamente instalados em todos os dispositivos Windows e do Windows Phone).
- Envie cupons baseada em local segmentos de usuário.
- Envie notificações de evento para usuários ou grupos para aplicativos de jogos/de Finanças de esportes.
- Notificar os usuários de eventos de empresa como novas mensagens/emails e vendas potenciais.
- Envie uma senhas necessária para autenticação multifator.



##<a name="what-are-push-notifications"></a>Quais são as notificações de envio por Push?

Smartphones e tablets podem "notificar" usuários quando ocorreu um evento. Essas notificações podem ter várias formas.

Em aplicativos da Windows Store e do Windows Phone, a notificação pode ser na forma de um _sistema_: uma janela restrita é exibida, com um som, para sinalizar uma nova notificação. Outros tipos de notificação que são suportados incluem notificações _de bloco_ _bruto_e _selo_ . Para obter mais informações sobre os tipos de notificações com suporte em dispositivos Windows, consulte [blocos, selos e notificações](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx).

Em dispositivos do Apple iOS, o envio da mesma forma notifica o usuário com uma caixa de diálogo solicitando que o usuário para exibir ou fechar a notificação. Clicando em **Exibir** abre o aplicativo que está recebendo a mensagem. Para obter mais informações sobre as notificações do iOS, consulte [iOS notificações](http://go.microsoft.com/fwlink/?LinkId=615245).

Notificações por push ajudam dispositivos móveis exibir novas informações ao restante com uso eficiente de energia. Notificações podem ser enviadas por sistemas de back-end para dispositivos móveis, mesmo quando correspondentes aplicativos em um dispositivo não estiver ativos. Notificações por push são um componente vital para os aplicativos do consumidor, onde eles são usados para aumentar o uso e envolvimento do aplicativo. Notificações também são úteis para empresas, quando informações atualizadas aumentam a capacidade de funcionário aos eventos de negócios.

Alguns exemplos específicos de cenários de contrato móvel são:

1.  Atualizando um bloco no Windows 8 ou Windows Phone com informações financeiras atuais.
2.  Um usuário com um brinde que algum item de trabalho foi atribuída ao usuário, em um aplicativo corporativo baseado em fluxo de trabalho de alerta.
3.  Exibir um selo com o número de vendas atuais lidera em um aplicativo CRM (como o Microsoft Dynamics CRM).

##<a name="how-push-notifications-work"></a>Como o trabalho de notificações de envio

Notificações por push são entregues por meio de infra-estruturas específico da plataforma chamadas _Sistemas de notificação de plataforma_ (PNS). Um PNS oferece barebones funções (ou seja, não há suporte para transmissão, personalização) e não ter nenhuma interface comum. Por exemplo, para enviar uma notificação para um aplicativo da Windows Store, um desenvolvedor deve contatar o WNS (serviço de notificação do Windows). Para enviar uma notificação para um dispositivo iOS, o desenvolvedor mesmo tem contatar APNS (Apple enviados por Push serviço notificação), e envie a mensagem uma segunda vez. Azure hubs de notificação ajudam fornecendo uma interface comum, juntamente com outros recursos de suporte para notificações por push entre cada plataforma.

Em um alto nível, no entanto, todos os sistemas de notificação de plataforma seguem o mesmo padrão:

1.  O aplicativo cliente contata a PNS para recuperar a sua _alça_. O tipo de identificador depende do sistema. Para o WNS, é um URI ou "canal de notificação". Para APNS, ele é um token.
2.  O aplicativo cliente armazena esta alça no _back-end_ do aplicativo para uso posterior. WNS, back-end normalmente é um serviço de nuvem. Apple, o sistema é chamado um _provedor_.
3.  Para enviar uma notificação de envio, o aplicativo back-end contata o PNS utilizando a alça para uma instância de aplicativo cliente específico de destino.
4.  O PNS encaminha a notificação no dispositivo especificado pelo identificador de.

![][0]

##<a name="the-challenges-of-push-notifications"></a>Os desafios de notificações de envio

Enquanto esses sistemas são muito poderosos, eles ainda deixam muito trabalho para o desenvolvedor do aplicativo para implementar cenários de notificação por push mesmo comuns, como transmissão ou enviando notificações por push para usuários segmentados.

Notificações por push são um dos recursos mais solicitados em serviços de nuvem para aplicativos móveis. O motivo para isso é a infraestrutura necessária para que eles funcionem relativamente complexo e, principalmente, não relacionados à lógica comercial principal do aplicativo. Estes são alguns dos desafios na criação de uma infraestrutura de envio sob demanda:

- **Dependência de plataforma.** Para enviar notificações para dispositivos em diferentes plataformas, várias interfaces devem ser codificadas no back-end. Não só são os detalhes de baixo nível diferentes, mas a apresentação da notificação (bloco, sistema ou selo) também é dependente de plataforma. Essas diferenças podem levar a complexo e difícil de manter o código de back-end.

- **Escala.** Dimensionamento este infraestrutura tem dois aspectos:
    + Por diretrizes PNS, tokens de dispositivo devem ser atualizados sempre que o aplicativo é iniciado. Isso leva a uma grande quantidade de tráfego (e acessos banco de dados resultante) apenas para manter os tokens de dispositivo atualizados. Quando o número de dispositivos cresce (possivelmente para milhões), o custo de criação e manutenção este infraestrutura é nonnegligible.

    + A maioria dos PNSs não dão suporte a transmissão em vários dispositivos. Ela segue que uma difusão para milhões de dispositivos resulta em milhões de chamadas para os PNSs. Ser capaz de dimensionar essas solicitações é trivial, porque geralmente os desenvolvedores de aplicativo desejam manter a latência total para baixo. Por exemplo, o último dispositivo a receber a mensagem não deve receber a notificação de 30 minutos após o envio de notificações, como em muitos casos ele seria prejudicar a finalidade ter notificações por push.
- **Roteamento.** PNSs oferece uma maneira de enviar uma mensagem para um dispositivo. No entanto, na maioria dos aplicativos notificações são direcionadas para os usuários e/ou grupos de interesse (por exemplo, todos os funcionários atribuídos a uma conta de cliente determinadas). Assim, para rotear as notificações para os dispositivos corretos, o aplicativo back-end deve manter um registro que associa grupos de interesse tokens de dispositivo. Essa sobrecarga adiciona o tempo total de custos de mercado e a manutenção de um aplicativo.

##<a name="why-use-notification-hubs"></a>Por que usar Hubs de notificação?

Notificação Hubs eliminam a complexidade: você não tem a gerenciar os desafios de notificações de envio. Em vez disso, você pode usar um Hub de notificação. Notificação Hubs usem uma infraestrutura de notificação de envio completo várias plataformas, dimensionada-out e reduzem consideravelmente o código de envio específicas que é executado no aplicativo back-end. Notificação Hubs implementam todas as funcionalidades de uma infraestrutura de envio. Dispositivos responsáveis apenas para registrar PNS alças e back-end é responsável por enviar mensagens de independente de plataforma para usuários ou grupos de interesse, conforme mostrado na figura a seguir:

![][1]


Hubs de notificação fornecem uma infraestrutura de notificação por push prontos para uso com as seguintes vantagens:

- **Várias plataformas.**
    +  Suporte para todas as principais plataformas móveis. Hubs de notificação podem enviar notificações por push para aplicativos da Windows Store, iOS, Android e Windows Phone.

    +  Hubs de notificação fornecem uma interface comum para enviar notificações para todas as plataformas com suporte. Protocolos específicos de plataforma não são necessários. O aplicativo back-end pode enviar notificações em formatos específicos de plataforma ou independente de plataforma. O aplicativo apenas se comunica com Hubs de notificação.

    +  Gerenciamento de identificador de dispositivo. Notificação Hubs mantém o registro de alça e comentários de PNSs.

- **Funciona com qualquer back-end**: nuvem ou no local, .NET, PHP, Java, nó, etc.

- **Escala.** Escala de hubs de notificação para milhões de dispositivos sem a necessidade de reformular ou fragmentar.


- **Rich definir padrões de entrega**:

    - *Transmissão*: permite transmitir praticamente simultâneas para milhões de dispositivos com uma única chamada de API.

    - *Difusão ponto a ponto/difusão seletiva*: Push a marcas que representa usuários individuais, incluindo todos os seus dispositivos; ou grupo mais largo; Por exemplo, fatores formulário separado (tablet versus telefone).

    - *Segmentação*: envio por Push para segmento complexo definido por expressões de marca (por exemplo, dispositivos em Nova York acompanhando Yankees).

    Cada dispositivo, ao enviar sua alça a um hub de notificação, pode especificar uma ou mais _marcas_. Para obter mais informações sobre [marcas]. Marcas não precisa ser previamente provisionado ou descartado. Marcas fornecem uma maneira simples para enviar notificações aos usuários ou grupos de interesse. Como marcas podem conter qualquer identificador de aplicativo específico (como IDs de usuário ou grupo), o seu uso libera o aplicativo back-end da carga de ter que armazenar e gerenciar os identificadores de dispositivo.

- **Personalização**: cada dispositivo pode ter um ou mais modelos, para obter a localização de cada dispositivo e personalização sem afetar o código de back-end.

- **Segurança**: acesso segredo (SAS) ou autenticação federada compartilhadas.

- **Telemetria Rich**: disponíveis no portal e programaticamente.


##<a name="integration-with-app-service-mobile-apps"></a>Integração com aplicativos móveis do serviço de aplicativo

Para facilitar uma experiência perfeita e Unificação nos serviços do Azure, o [Aplicativo de serviço móvel aplicativos] tem suporte interno para notificações de envio utilizando Hubs de notificação. [Aplicativos do aplicativo de serviço Mobile] oferece uma plataforma de desenvolvimento de aplicativos móveis altamente escaláveis, globalmente disponível para os desenvolvedores corporativos e integradores que traz um conjunto sofisticado de recursos para desenvolvedores móveis.

Os desenvolvedores de aplicativos Mobile podem utilizar Hubs de notificação com o fluxo de trabalho a seguir:

1. Recuperar o identificador PNS do dispositivo
2. Registrar o dispositivo e [modelos] com Hubs de notificação por meio conveniente API de registro de SDK do cliente de aplicativos móveis
    + Observe que aplicativos Mobile revelar ausente todas as marcas nos registros para fins de segurança. Trabalhar com Hubs de notificação do seu back-end diretamente para associar marcas dispositivos.
3. Enviar notificações do seu back-end do aplicativo com Hubs de notificação

Aqui estão alguns conveniências trouxe para desenvolvedores com essa integração:

- **SDKs de cliente móvel de aplicativos.** Estes SDKs várias plataformas fornecem APIs simples para o registro e falam com o hub de notificação vinculado com o aplicativo móvel automaticamente. Os desenvolvedores não precisam aprofundar por meio de credenciais de Hubs de notificação e trabalhar com um serviço adicional.
    + SDK do marca automaticamente o dispositivo determinado com aplicativos Mobile ID de usuário autenticado para habilitar o envio para o cenário de usuário.
    + SDK do automaticamente use a identificação de instalação de aplicativos móveis como GUID para registrar com Hubs de notificação, salvando desenvolvedores o problema de manter várias GUIDs de serviço.
    
- **Modelo de instalação.** Aplicativos Mobile funciona com o modelo de envio mais recente dos Hubs de notificação para representar todas as propriedades de envio associadas a um dispositivo em uma instalação de JSON que se alinha com serviços de notificação de envio e é fácil de usar.

- **Flexibilidade.** Os desenvolvedores sempre poderá optar funcione com Hubs de notificação diretamente mesmo com a integração no lugar.

- **Experiência integrada no [portal do Azure].** Enviar como um recurso é representado visualmente no aplicativos Mobile e desenvolvedores podem trabalhar facilmente com o hub de notificação associados por meio de aplicativos Mobile.



##<a name="next-steps"></a>Próximas etapas

Você pode encontrar mais informações sobre a notificação Hubs nestes tópicos:

+ **[Como os clientes estão usando Hubs de notificação]**

+ **[Guias e tutoriais de Hubs de notificação]**

+ **Tutoriais de notificação Hubs Introdução** ([iOS], [Android], [Universal do Windows], [o Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

O .NET relevantes gerenciado API referências para notificações por push estão localizadas aqui:

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [Como os clientes estão usando Hubs de notificação]: http://azure.microsoft.com/services/notification-hubs
  [Guias e tutoriais de Hubs de notificação]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Universal do Windows]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [Aplicativos móveis do serviço de aplicativo]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
  [modelos]: notification-hubs-templates-cross-platform-push-messages.md
  [Portal do Azure]: https://portal.azure.com
  [marcas]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
