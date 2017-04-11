<properties 
    pageTitle="Windows Phone Silverlight alcance SDK integração" 
    description="Como integrar alcance Azure contrato móvel com aplicativos do Windows Phone Silverlight"                    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article"
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-reach-sdk-integration"></a>Windows Phone Silverlight alcance SDK integração

Você deve seguir o procedimento de integração descrito a [Integração do Windows Phone Silverlight contrato SDK](mobile-engagement-windows-phone-integrate-engagement.md) antes de seguir este guia.

##<a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Incorporar o SDK do contrato atinja seu projeto do Silverlight do Windows Phone

Você não tem algo a adicionar. `EngagementReach`referências e recursos já estão em seu projeto.

> [AZURE.TIP]  Você pode personalizar imagens localizadas na `Resources` pasta do seu projeto, especialmente no ícone de marca (esse padrão para o ícone de contrato).

##<a name="add-the-capabilities"></a>Adicione os recursos

O SDK do contrato atinja necessidades alguns recursos adicionais.

Abrir sua `WMAppManifest.xml` de arquivo e certifique-se de que os seguintes recursos são declarados:

-   `ID_CAP_PUSH_NOTIFICATION`
-   `ID_CAP_WEBBROWSERCOMPONENT`

Primeiro é usado pelo serviço MPNS para permitir a exibição de notificação do sistema. Segundo é usado para incorporar uma tarefa de navegador o SDK.

Editar o `WMAppManifest.xml` arquivo e adicionar dentro do `<Capabilities />` marca:

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

##<a name="enable-the-microsoft-push-notification-service"></a>Ativar o serviço de notificação de Push da Microsoft

Para poder usar o **Serviço de notificação por Push da Microsoft** (chamado MPNS) sua `WMAppManifest.xml` arquivo deve ter uma `<App />` marca com uma `Publisher` atributo definido como o nome do seu projeto.

##<a name="initialize-the-engagement-reach-sdk"></a>Inicializar o alcance de contrato SDK

### <a name="engagement-configuration"></a>Configuração de contrato

A configuração de contrato centralizada no `Resources\EngagementConfiguration.xml` arquivo do seu projeto.

Edite esse arquivo para especificar a configuração de alcance:

-   *Opcional*, indique se o envio nativo (MPNS) está ativado ou não entre `<enableNativePush>` e `</enableNativePush>` marcas (`true` por padrão).
-   *Opcional*, indicar o nome do canal push entre `<channelName>` e `</channelName>` marcas, fornecem o mesmo que seu aplicativo pode usar ou deixá-lo vazio no momento.

Se você quiser especificá-lo em tempo de execução, você pode chamar o seguinte método antes da inicialização do agente de contrato:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */
    
    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [AZURE.TIP] Você pode especificar o nome do canal MPNS push do seu aplicativo. Por padrão, o contrato cria um nome com base no appId. Você não precisa especificar o nome por conta própria, exceto se você planeja usar o canal de envio fora do contrato.

### <a name="engagement-initialization"></a>Inicialização de contrato

Modificar o `App.xaml.cs`:

-   Adicionar ao seu `using` instruções:

        using Microsoft.Azure.Engagement;

-   Inserir `EngagementReach.Instance.Init` logo após `EngagementAgent.Instance.Init` na `Application_Launching` :

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

-   Inserir `EngagementReach.Instance.OnActivated` no `Application_Activated` método:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

> [AZURE.IMPORTANT] O `EngagementReach.Instance.Init` é executado em um thread dedicado. Você não tenha fazê-lo.

##<a name="app-store-submission-considerations"></a>Considerações de envio do aplicativo store

Microsoft impõe algumas regras ao usar as notificações de envio:

Da documentação do Microsoft [Diretivas de aplicativo] , seção 2,9:

1) Você deve perguntar ao usuário para aceitar para receber notificações por push. Em seguida, em suas configurações, adicione uma forma de desativar as notificações de envio.

O objeto EngagementReach fornece dois métodos para gerenciar o opt-in/opt-out, `EnableNativePush()` e `DisableNativePush()`. Por exemplo, você pode criar uma opção nas configurações com um botão de alternância para desabilitar ou habilitar MPNS.

Você também pode optar por desativar MPNS por meio da configuração de contrato\<windows phone-sdk-alcance-configuração\>.

> 2.9.1) o aplicativo primeiro deve descrever as notificações para ser fornecido e **obter a permissão do usuário express (opt-in)**e **forneça um mecanismo através do qual o usuário pode deixar de receber notificações por push**. Todas as notificações fornecidas usando o serviço de notificação por Push do Microsoft deve ser consistentes com a descrição fornecida para o usuário e deve estar em conformidade com todos os aplicável [Diretivas de aplicativo]  [ Content Policies] e [Requisitos adicionais para tipos específicos de aplicativo].

2) Você não deve usar muitas notificações de envio. Contrato tratará notificações para você.

> 2.9.2) o aplicativo e seu uso do serviço de notificação de envio do Microsoft devem não excessiva utilizem capacidade de rede ou largura de banda do serviço de notificação de envio do Microsoft, caso contrário indevidamente sobrecarregam um Windows Phone ou outro dispositivo Microsoft ou serviço com excessiva as notificações por push, conforme determinado pela Microsoft seu critério razoável e deve não prejudicar ou interferir com qualquer redes Microsoft ou servidores ou qualquer servidores de terceiros ou conectado ao serviço de notificação de envio da Microsoft.

3) Não dependem MPNS para enviar informações de criticals. Contrato usa MPNS, portanto, essa regra também se aplica a campanhas criadas dentro do contrato front-end.

> 2.9.3) serviço de notificações por Push a Microsoft não pode ser usado para enviar notificações de missão crítica ou caso contrário, poderiam afetar questões de vida ou morte, incluindo, sem notificações críticas limitação relacionados a um dispositivo médico ou condição. MICROSOFT ISENTA QUAISQUER GARANTIAS QUE O USO DO SERVIÇO DE NOTIFICAÇÕES PUSH DA MICROSOFT OU ENTREGA MICROSOFT NOTIFICAÇÃO SERVIÇO DE NOTIFICAÇÕES DE ENVIO SERÃO SEM INTERRUPÇÕES, SEM ERRO, OU CASO CONTRÁRIO, GARANTE QUE OCORREM EM TEMPO REAL.

**Não podemos garantir que seu aplicativo passará o processo de validação se você não respeita essas recomendações.**

##<a name="handle-data-push-optional"></a>Manipular o envio de dados (opcional)

Se quiser que o aplicativo seja capaz de receber alcance coloca de dados, você precisa implementar dois eventos da classe EngagementReach:

    EngagementReach.Instance.DataPushStringReceived += (body) =>
    {
       Debug.WriteLine("String data push message received: " + body);
       return true;
    };
    
    EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
    {
       Debug.WriteLine("Base64 data push message received: " + encodedBody);
       // Do something useful with decodedBody like updating an image view
       return true;
    };

Você pode ver que o retorno de chamada de cada método retorna um booliano. Contrato envia comentários para seu back-end após distribuir o envio de dados. Se o retorno de chamada retorna false, o `exit` comentários será enviada. Caso contrário, ele será `action`. Se nenhum retorno de chamada estiver definido para os eventos, o `drop` comentários serão retornado contratos.

> [AZURE.WARNING] Contrato não é capaz de receber comentários de múltiplos para um envio de dados. Se você planeja configurar vários manipuladores em um evento, lembre-se de que os comentários corresponderão à última um enviadas. Nesse caso, recomendamos sempre retorna o mesmo valor para evitar comentários confuso em front-end.

##<a name="customize-ui-optional"></a>Personalizar a interface do usuário (opcional)

### <a name="first-step"></a>Primeira etapa

Nós permitem que você personalize o alcance da interface do usuário.

Para fazer isso, você precisa criar uma subclasse do `EngagementReachHandler` classe.

**Código de exemplo:**

    using Microsoft.Azure.Engagement;
    
    namespace Example
    {
       internal class ExampleReachHandler : EngagementReachHandler
       {
          // Override EngagementReachHandler methods depending on your needs
       }
    }

Em seguida, defina o conteúdo da `EngagementReach.Instance.Handler` campo com seu objeto personalizado no seu `App.xaml.cs` classe dentro a `Application_Launching` método.

**Código de exemplo:**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [AZURE.NOTE] Por padrão, o compromisso utiliza sua própria implementação dos `EngagementReachHandler`. Você não precisa criar seu próprio e se você fizer isso, você não precisa substituir cada método. O comportamento padrão é selecionar o objeto de base do contrato.

### <a name="layouts"></a>Layouts

Por padrão, o alcance usará os recursos incorporados da DLL para exibir as páginas e notificações.

No entanto, você pode decidir usar seus próprios recursos para refletir sua marca nesses componentes.

Você pode substituir `EngagementReachHandler` métodos na sua subclasse para informar ao contrato usar layouts:

**Código de exemplo:**

    // In your subclass of EngagementReachHandler
    
    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }
    
    public override string GetPollUri()
    {
       // return the path of your own xaml
    }
    
    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [AZURE.TIP] O `CreateNotification` método pode retornar nulo. A notificação não será exibida e a campanha de alcance será ignorada.

Para simplificar a implementação de layout, também fornecemos nossa própria xaml que pode servir como base para seu código. Eles estão localizados no arquivamento SDK do contrato (/ src/alcance /).

> [AZURE.WARNING] As fontes que fornecemos são os mesmos exatos que usamos. Portanto se desejar modificá-los diretamente, não se esqueça de alterar o espaço para nome e o nome.

### <a name="notification-position"></a>Posição de notificação

Por padrão, uma notificação do aplicativo é exibida na parte inferior esquerda do aplicativo. Você pode alterar esse comportamento, substituindo o `GetNotificationPosition` método do `EngagementReachHandler` objeto.

    // In your subclass of EngagementReachHandler
    
    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

Atualmente, você pode escolher entre o `BOTTOM` (padrão) e `TOP` posições.

### <a name="launch-message"></a>Iniciar mensagem

Quando um usuário clica em uma notificação do sistema (um brinde), contrato inicia o aplicativo, carregar o conteúdo das mensagens de envio e exibir a página da campanha correspondente.

Há um atraso entre o início do aplicativo e a exibição da página (dependendo da velocidade da sua rede).

Para indicar ao usuário que algo está carregando, você deve fornecer um visual informações, como uma barra de progresso ou um indicador de progresso. Contrato não pode manipular que em si, mas oferece alguns manipuladores para você.

Para implementar o retorno de chamada, faça:

    /* The application has launched and the content is loading.
     * You should display an indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
    
    /* The application has finished loading the content and the page
     * is about to be displayed.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
    
    /* The content has been loaded, but an error has occurred.
     * You can provide an information to the user.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Você pode definir o retorno de chamada na sua `Application_Launching` método de sua `App.xaml.cs` arquivo, preferência antes o `EngagementReach.Instance.Init()` chamar.

> [AZURE.TIP] Cada manipulador é chamado pelo segmento de interface do usuário. Você não precisa se preocupar ao usar uma MessageBox ou algo relacionados a interface do usuário.

[Políticas de aplicativo]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[Requisitos adicionais para os tipos de aplicativo específico]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx
 
