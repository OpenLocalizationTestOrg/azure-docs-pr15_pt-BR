<properties 
    pageTitle="Integração do SDK do Windows Universal alcance de aplicativos" 
    description="Como integrar alcance Azure contrato móvel com aplicativos Universal do Windows"
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

# <a name="windows-universal-apps-reach-sdk-integration"></a>Integração do SDK do Windows Universal alcance de aplicativos

Você deve seguir o procedimento de integração descrito a [Integração do Windows Universal contrato SDK](mobile-engagement-windows-store-integrate-engagement.md) antes de seguir este guia.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Incorporar o SDK do contrato chegar a seu projeto Universal do Windows

Você não tem algo a adicionar. `EngagementReach`referências e recursos já estão em seu projeto.

> [AZURE.TIP] Você pode personalizar imagens localizadas na `Resources` pasta do seu projeto, especialmente no ícone de marca (esse padrão para o ícone de contrato). Na Universal de aplicativos você também pode mover a `Resources` pasta em seu projeto compartilhado para compartilhar o seu conteúdo entre aplicativos, mas você terá que manter o `Resources\EngagementConfiguration.xml` de arquivos no seu local padrão que ele seja plataforma dependente.

## <a name="enable-the-windows-notification-service"></a>Habilitar o serviço de notificação do Windows

### <a name="windows-8x-and-windows-phone-81-only"></a>Windows 8. x e Windows Phone 8.1 somente

Para poder usar o **Serviço de notificação do Windows** (referido como WNS) no seu `Package.appxmanifest` de arquivos no `Application UI` clique em `All Image Assets` na caixa esquerda bot. Na parte direita da caixa do `Notifications`, altere `toast capable` de `(not set)` para `(Yes)`.

### <a name="all-platforms"></a>Todas as plataformas

Você precisa sincronizar seu aplicativo para sua conta da Microsoft e a plataforma de contrato. Para isso, você precisa criar uma conta ou faça logon no [Centro de desenvolvimento do windows](https://dev.windows.com). Depois que criar um novo aplicativo e encontre o SID e chave secreta. Em frontend o contrato, vá em sua configuração de aplicativo no `native push` e colar suas credenciais. Depois disso, clique com botão direito no seu projeto, selecione `store` e `Associate App with the Store...`. Basta selecionar o aplicativo que você tenha criar antes de sincronizá-la.

## <a name="initialize-the-engagement-reach-sdk"></a>Inicializar o alcance de contrato SDK

Modificar o `App.xaml.cs`:

-   Inserir `EngagementReach.Instance.Init` logo após `EngagementAgent.Instance.Init` no seu `InitEngagement` método:

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
          EngagementReach.Instance.Init(e);
        }

    O `EngagementReach.Instance.Init` é executado em um thread dedicado. Você não tenha fazê-lo.

> [AZURE.NOTE] Se você estiver usando as notificações por push em outro lugar em seu aplicativo você precisa [compartilhar seu canal de envio](#push-channel-sharing) com contrato atinja.

## <a name="integration"></a>Integração

Contrato fornece duas maneiras de adicionar as faixas do aplicativo de alcance e intersticiais modos de exibição para anúncios e votações em seu aplicativo: a integração de sobreposição e a integração de manual de modos de exibição da web. Você não deve combinar ambas as abordagem na mesma página.

A opção entre a integração de duas poderia ser resumida desta forma:

-   Você pode escolher a integração de sobreposição se já herda suas páginas do agente `EngagementPage`, ele é apenas uma questão de substituição `EngagementPage` por `EngagementPageOverlay` e `xmlns:engagement="using:Microsoft.Azure.Engagement"` por `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` em suas páginas.
-   Você pode escolher os modos de exibição de web integração manual se quiser posicionar precisamente a IU atinja em suas páginas ou se você não quiser adicionar outro nível de herança às suas páginas. 

### <a name="overlay-integration"></a>Integração de sobreposição

Sobreposição de contrato dinamicamente adiciona os elementos de interface do usuário usados para exibir campanhas de alcance em sua página. Se a sobreposição não atender às sua layout você deve considerar os modos de exibição de web integração manual em vez disso.

Em sua alteração de arquivo. XAML `EngagementPage` referência a`EngagementPageOverlay`

-   Adicione a suas declarações de namespaces:

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

-   Substituir `engagement:EngagementPage` com `engagement:EngagementPageOverlay`:

**Com EngagementPage:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
        
            <!-- Your layout -->
        </engagement:EngagementPage>

**Com EngagementPageOverlay:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
        
            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

Em seguida, no seu arquivo. cs marcar sua página no `EngagementPageOverlay` em vez de `EngagementPage` e importar `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

-   Substituir `EngagementPage` com `EngagementPageOverlay`:

**Com EngagementPage:**

            using Microsoft.Azure.Engagement;
            
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**Com EngagementPageOverlay:**

            using Microsoft.Azure.Engagement.Overlay;
            
            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


Sobreposição de contrato adiciona uma `Grid` elemento na parte superior de sua página composta seu layout e os dois `WebView` elementos uma faixa de opções e o outro para o modo de exibição intersticial.

Você pode personalizar os elementos de sobreposição diretamente no `EngagementPageOverlay.cs` arquivo.

### <a name="web-views-manual-integration"></a>Integração de manual de modos de exibição da Web

Alcance irá procurar suas páginas para os dois `WebView` elementos responsáveis por exibir faixa de opções e o modo de exibição intersticial. A única coisa que você precisa fazer é adicionar esses dois `WebView` elementos em algum lugar em suas páginas, aqui está um exemplo:

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


Neste exemplo o `WebView` elementos são alongados para caber seus contêineres que automaticamente tamanhos-las novamente em caso de alteração de tamanho de rotação ou janela de tela.

> [AZURE.WARNING] É importante manter a mesma nomenclatura `engagement_notification_content` e `engagement_announcement_content` para o `WebView` elementos. Alcance está identificando-los com seu nome. 

## <a name="handle-datapush-optional"></a>Lidar com datapush (opcional)

Se quiser que o aplicativo seja capaz de receber alcance coloca de dados, você precisa implementar dois eventos da classe EngagementReach:

Em App.xaml.cs no construtor App() adicione:

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

## <a name="customize-ui-optional"></a>Personalizar a interface do usuário (opcional)

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

Em seguida, defina o conteúdo da `EngagementReach.Instance.Handler` campo com seu objeto personalizado no seu `App.xaml.cs` classe dentro a `App()` método.

**Código de exemplo:**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [AZURE.NOTE]Por padrão, o compromisso utiliza sua própria implementação dos `EngagementReachHandler`.
> Você não precisa criar seu próprio e se você fizer isso, você não precisa substituir cada método. O comportamento padrão é selecionar o objeto de base do contrato.

### <a name="web-view"></a>Modo de exibição de Web

Por padrão, o alcance usará os recursos incorporados da DLL para exibir as páginas e notificações.

Para fornecer uma total personalização possibilidades usamos somente exibição na web. Se você quiser personalizar layouts, diretamente substituir os arquivos de recursos `EngagementAnnouncement.html` e `EngagementNotification.html`. Contrato precisa todo o código em `<body></body>` seja executado corretamente. Mas você pode adicionar marca externa `engagement_webview_area`.

No entanto, você pode optar por usar seus próprios recursos.

Você pode substituir `EngagementReachHandler` métodos na sua subclasse para informar ao contrato usar layouts, mas tenha cuidado para incorporado o mecanismo de contrato:

**Código de exemplo:**
            
            // In your subclass of EngagementReachHandler
            
            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


Por padrão, é AnnouncementHTML `ms-appx-web:///Resources/EngagementAnnouncement.html`. Ele representa o arquivo html que criar o conteúdo de uma mensagem de envio (anúncio de texto, anoucement Web e anúncio de votação). AnnouncementName é `engagement_announcement_content`. É o nome do projeto da exibição da Web no seu página xaml.

NotfificationHTML é `ms-appx-web:///Resources/EngagementNotification.html`. Ele representa o arquivo html que a notificação de uma mensagem de envio de design. NotfificationName é `engagement_notification_content`. É o nome do projeto da exibição da Web no seu página xaml.

### <a name="customization"></a>Personalização

Você pode personalizar o lançamento modo web tem você deseja se você preservar o objeto de contrato e notificação. Tome cuidado essa exibição da Web objeto é descrito três vezes - a primeira vez em seu xaml, a segunda vez no seu arquivo. cs no método "setwebview()" e a hora de terceira no arquivo html.

-   Em seu xaml você descrever o componente de exibição da Web do layout gráfico atual.
-   No seu arquivo. cs, você pode definir "setwebview()" em que você definir a dimensão dos dois exibição da Web (notificação, comunicado). Ele é muito eficaz quando o aplicativo está redimensionando.
-   No arquivo html contrato podemos descrever o conteúdo de exibição da Web, design e as posições de elementos entre si.

### <a name="launch-message"></a>Iniciar mensagem

Quando um usuário clica em uma notificação do sistema (um brinde), contrato inicia o aplicativo, carregar o conteúdo das mensagens de envio e exibir a página da campanha correspondente.

Há um atraso entre o início do aplicativo e a exibição da página (dependendo da velocidade da sua rede).

Para indicar ao usuário que algo está carregando, você deve fornecer um visual informações, como uma barra de progresso ou um indicador de progresso. Contrato não pode manipular que em si, mas oferece alguns manipuladores para você.

Para implementar o retorno de chamada, em App.xaml.cs no "App() público {}" Adicione:

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

Você pode definir o retorno de chamada no seu método de "Público App() {}" da sua `App.xaml.cs` arquivo, preferência antes o `EngagementReach.Instance.Init()` chamar.

> [AZURE.TIP] Cada manipulador é chamado pelo segmento de interface do usuário. Você não precisa se preocupar ao usar uma MessageBox ou algo relacionados a interface do usuário.

##<a id="push-channel-sharing"></a>Notificações por push compartilhamento de canal

Se você estiver usando as notificações por push para outra finalidade em seu aplicativo, em seguida, você precisa usar o canal de envio recurso do SDK do contrato de compartilhamento. Isso é para evitar o envio perdido.

- Você pode fornecer seu próprio canal de envio para a inicialização contrato atinja. O SDK usará em vez de solicitar uma nova.

Atualizar a inicialização contrato atinja com seu canal de envio do `InitEngagement` método a partir do `App.xaml.cs` arquivo:
    
    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    
    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

- Como alternativa, se você quiser apenas consumir o canal de envio após a inicialização de alcance e em seguida, você pode definir um retorno de chamada no contrato contatar para obter o canal de envio de uma vez ele é criado pelo SDK.

Defina seu retorno de chamada em qualquer lugar **após** a inicialização de alcance:

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Dica de esquema personalizado

Fornecemos uso de esquema personalizado. Você pode enviar o tipo de URI diferente do contrato frontend a ser usado em seu aplicativo de contrato. Esquema padrão como `http, ftp, ...` são gerenciar pelo Windows, será uma janela prompt não se estiverem nenhum aplicativo padrão instalado no dispositivo. Você também pode criar seu próprio esquema personalizado para seu aplicativo.

De uma forma simples para definir um esquema personalizado em seu aplicativo é abrir o `Package.appxmanifest` entram em `Declarations` painel. Selecione `Protocol` nas declarações disponíveis rolar caixa e adicioná-la. Editar o `Name` nome do campo com seu novo protocolo desejado.

Agora para usar este protocolo, edite seu `App.xaml.cs` com a `OnActivated` método e não se esqueça de inicializar o contrato aqui também:

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);
            
              //TODO design action to do when app is launch
            
              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
            
                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion
 
