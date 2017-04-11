<properties 
    pageTitle="Procedimentos de atualização do Windows SDK Universal de aplicativos" 
    description="Procedimentos de atualização de SDK Universal de aplicativos do Windows Azure contrato móvel"                     
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

#<a name="windows-universal-apps-sdk-upgrade-procedures"></a>Procedimentos de atualização do Windows SDK Universal de aplicativos

Se você já tiver integrado uma versão antiga do contrato em seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Você pode precisar acompanhar vários procedimentos se você perdeu várias versões do SDK. Por exemplo, se você migrar do 0.10.1 para 0.11.0, que você precisa primeiro, siga o procedimento "de 0.9.0 para 0.10.1", em seguida, o procedimento "de 0.10.1 para 0.11.0".

##<a name="from-330-to-340"></a>De 3.3.0 para 3.4.0

### <a name="test-logs"></a>Logs de teste

Logs de console produzidos pelo SDK agora podem ser habilitado/desabilitado/filtrada. Para personalizar isso, atualizar a propriedade `EngagementAgent.Instance.TestLogEnabled` para uma do valor disponível a `EngagementTestLogLevel` enumeração, por exemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>Recursos

Sobreposição de alcance foi aprimorada. Ele é parte dos recursos de pacote SDK NuGet.

Durante a atualização para a nova versão do SDK, você pode escolher se deseja manter os seus arquivos da pasta de sobreposição de seus recursos ou não:

* Se a sobreposição anterior está trabalhando para você ou é integrar o `WebView` elementos manualmente, em seguida, você pode decidir manter seus arquivos existentes, ele ainda funcionarão. 
* Se desejar atualizar para a nova sobreposição, basta substituir todo `overlay` pasta de seus recursos com o novo nome do pacote SDK (aplicativos UWP: após a atualização, você pode obter a nova pasta de sobreposição da % USERPROFILE %\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [AZURE.WARNING] Usando a nova sobreposição substituirá todas as personalizações feitas na versão anterior.

##<a name="from-320-to-330"></a>De 3.2.0 para 3.3.0

### <a name="resources"></a>Recursos
Esta etapa se refere somente recursos personalizados. Se você personalizou os recursos oferecidos pelo SDK (html, imagens, sobreposição), você deve fazer backup-las antes da atualização e reaplicar sua personalização recursos atualizado.

##<a name="from-310-to-320"></a>De 3.1.0 para 3.2.0

### <a name="resources"></a>Recursos
Esta etapa se refere somente recursos personalizados. Se você personalizou os recursos oferecidos pelo SDK (html, imagens, sobreposição), você deve fazer backup-las antes da atualização e reaplicar sua personalização recursos atualizado.

### <a name="webview-integration"></a>Integração de exibição da Web
Alguns aprimoramentos para corresponder fatores forma de dispositivo diferentes foram introduzidos nesta versão. Certifique-se de que a integração da exibição da Web coincidir com o seguinte:

Na sua página XAML ():

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

E no seu arquivo. cs associados:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
            
              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }
              
              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif
            
              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }
            
              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;
            
                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

##<a name="from-200-to-300"></a>De 2.0.0 para 3.0.0

### <a name="resources"></a>Recursos
Esta etapa se refere somente recursos personalizados. Se você personalizou os recursos oferecidos pelo SDK (html, imagens, sobreposição), você deve fazer backup-las antes da atualização e reaplicar sua personalização recursos atualizado.

##<a name="from-111-to-200"></a>De 1.1.1 para 2.0.0

A seguir descreve como migrar uma integração SDK do serviço Capptain oferecido pelo Capptain SAS para um aplicativo do Azure Mobile contrato da plataforma. 

> [Azure.IMPORTANT] Capptain e Mobile contrato não são os mesmos serviços e o procedimento abaixo realça apenas como migrar o aplicativo cliente. Migrar o SDK no aplicativo será não migrar seus dados de servidores Capptain aos servidores do contrato de celular

Se você estiver migrando de uma versão anterior, consulte o site do Capptain para migrar para o 1.1.1 primeiro e em seguida, aplique o procedimento a seguir

### <a name="nuget-package"></a>Pacote do NuGet

Substitua **Capptain.WindowsPhone** por **MicrosoftAzure.MobileEngagement** Nuget pacote.

### <a name="applying-mobile-engagement"></a>Aplicando contrato móvel

O SDK usa o termo `Engagement`. Você precisa atualizar seu projeto para corresponder a essa alteração.

Você precisa desinstalar o pacote de nuget Capptain atual. Considere a possibilidade de que todas as suas alterações na pasta de recursos de Capptain serão removidas. Se você quiser manter esses arquivos, em seguida, faça uma cópia delas.

Depois disso, instale o novo pacote do Microsoft Azure Engagement nuget em seu projeto. Você pode encontrá-lo diretamente no [site nuget]. ou índice aqui. Esta ação substitui todos os arquivos de recursos usados pelo contrato e adiciona a nova DLL de contrato para suas referências do projeto.

Você precisa limpar suas referências de projeto excluindo Capptain DLL referências. Se você não fizer isso, a versão do Capptain entrarão em conflito e erros acontecerá.

Se você personalizou Capptain recursos, copie o conteúdo de arquivos antigo e colá-las nos novos arquivos de contrato. Observe que os arquivos xaml e cs devem ser atualizados.

Ao concluir essas etapas, você só precisa substituir as referências de Capptain antigas pelas novas referências de contrato.

1. Todos os namespaces Capptain devem ser atualizados.

    Antes da migração:
    
        using Capptain.Agent;
        using Capptain.Reach;
    
    Após a migração:
    
        using Microsoft.Azure.Engagement;

2. Todas as classes Capptain que contêm "Capptain" devem conter "Contrato".

    Antes da migração:
    
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
    
    Após a migração:
    
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }

3. Para arquivos xaml atributos e Capptain namespace também alterar.

    Antes da migração:
    
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
    
    Após a migração:
    
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>

4. Alterações de página de sobreposição
    > [AZURE.IMPORTANT] Sobreposição também altera. Seu novo namespace é `Microsoft.Azure.Engagement.Overlay`. Ele deve ser usado nos arquivos xaml e cs. Além disso `CapptainGrid` deve ser nomeado `EngagementGrid`, `capptain_notification_content` e `capptain_announcement_content` são nomeados `engagement_notification_content` e `engagement_announcement_content`.
    
    Sobreposição:
    
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
    
    Torna-se:
    
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>

5. Para os outros recursos como Capptain imagens e arquivos HTML, observe que eles também foram renomeados para usar "Contrato".

### <a name="project-declaration"></a>Declaração de projeto

Em Package.appxmanifest `File Type Associations` foi atualizado em:

 -   capptain\_atinja\_conteúdo contrato\_atinja\_conteúdo
 -   capptain\_log\_arquivo para contrato\_log\_arquivo

### <a name="application-id--sdk-key"></a>ID do aplicativo / chave SDK

Compromisso utiliza uma cadeia de conexão. Você não precisa especificar uma ID de aplicativo e uma tecla de SDK com contrato de celular, você só precisa especificar uma cadeia de conexão. Você pode configurá-lo no seu arquivo de EngagementConfiguration.

A configuração de contrato pode ser definida no seu `Resources\EngagementConfiguration.xml` arquivo do seu projeto.

Edite esse arquivo para especificar:

-   A cadeia de caracteres de conexão de aplicativo entre marcas `<connectionString>` e `<\connectionString>`.

Se você quiser especificá-lo em tempo de execução, você pode chamar o seguinte método antes da inicialização do agente de contrato:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

A cadeia de conexão para o seu aplicativo é exibida no Portal de clássico do Azure.

### <a name="items-name-change"></a>Alteração de nome de itens

Todos os itens nomeados *capptain* tem sido chamados *contrato*. Da mesma forma para *Capptain* *contratos*.

Exemplos de itens de Capptain comumente usados:

-   CapptainConfiguration agora denominado EngagementConfiguration
-   CapptainAgent agora denominado EngagementAgent
-   CapptainReach agora denominado EngagementReach
-   CapptainHttpConfig agora denominado EngagementHttpConfig
-   GetCapptainPageName agora denominado GetEngagementPageName

Observe que renomear também afeta métodos substituídos.

 
