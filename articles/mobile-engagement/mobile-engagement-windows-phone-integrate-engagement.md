<properties 
    pageTitle="Integração do Windows Phone Silverlight contrato SDK" 
    description="Como integrar Azure contrato móvel com aplicativos do Windows Phone Silverlight"                  
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-engagement-sdk-integration"></a>Integração do Windows Phone Silverlight contrato SDK

> [AZURE.SELECTOR] 
- [Universal do Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Este procedimento descreve a maneira mais simples para ativar do Azure Mobile contrato análises e monitoramento funções em seu aplicativo do Silverlight do Windows Phone.

As etapas a seguir são suficientes para ativar o relatório de logs necessários para calcular todas as estatísticas sobre usuários, sessões, atividades, falhas e Technicals. O relatório logs necessários para calcular outras estatísticas como eventos, erros e trabalhos de deve ser feito manualmente usando a API do contrato (consulte [como usar o contrato de celular avançado marcação API em seu aplicativo do Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md) abaixo) como essas estatísticas são dependem de aplicativo.

##<a name="supported-versions"></a>Versões suportadas

O SDK de contrato Mobile para Windows Silverlight só podem ser integrado em aplicativos-alvo:

-   Windows Phone 8.0
-   Windows Phone 8.1 Silverlight

> [AZURE.NOTE] Se você está destinando Windows Phone 8.1 (não Silverlight), consulte o [procedimento de integração Universal do Windows](mobile-engagement-windows-store-integrate-engagement.md).

##<a name="install-the-mobile-engagement-silverlight-sdk"></a>Instalar o SDK do Silverlight contrato móvel

O SDK de contrato Mobile para Windows Silverlight está disponível como um pacote do Nuget chamado *MicrosoftAzure.MobileEngagement*. Você pode instalá-lo do Gerenciador de pacote do Visual Studio Nuget. 

##<a name="add-the-capabilities"></a>Adicione os recursos

O SDK do contrato necessidades alguns recursos do SDK do Windows Phone Silverlight para funcionar corretamente.

Abrir sua `WMAppManifest.xml` de arquivo e certifique-se de que os seguintes recursos são declarados a `Capabilities` painel:

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

##<a name="initialize-the-engagement-sdk"></a>Inicializar o contrato SDK

### <a name="engagement-configuration"></a>Configuração de contrato

A configuração de contrato centralizada no `Resources\EngagementConfiguration.xml` arquivo do seu projeto.

Edite esse arquivo para especificar:

-   A cadeia de caracteres de conexão de aplicativo entre marcas `<connectionString>` e `<\connectionString>`.

Se você quiser especificá-lo em tempo de execução, você pode chamar o seguinte método antes da inicialização do agente de contrato:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

A cadeia de conexão para o seu aplicativo é exibida no Portal de clássico do Azure.

### <a name="engagement-initialization"></a>Inicialização de contrato

Quando você cria um novo projeto, um `App.xaml.cs` arquivo é gerado. Esta classe herda das `Application` e contém vários métodos importantes. Ele também será usado para inicializar o SDK do contrato.

Modificar o `App.xaml.cs`:

-   Adicionar ao seu `using` instruções:

        using Microsoft.Azure.Engagement;

-   Inserir `EngagementAgent.Instance.Init` no `Application_Launching` método:

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
          EngagementAgent.Instance.Init();
        }

-   Inserir `EngagementAgent.Instance.OnActivated` no `Application_Activated` método:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
        }

> [AZURE.WARNING] Podemos altamente desencorajar para adicionar a inicialização de contrato em outro lugar do seu aplicativo. No entanto, lembre-se que a `EngagementAgent.Instance.Init` método é executado em um thread dedicado e não o segmento de interface do usuário.

##<a name="basic-reporting"></a>Relatórios básicos

### <a name="recommended-method--overload-your-phoneapplicationpage-classes"></a>Recomendado método: sobrecarregar seu `PhoneApplicationPage` classes

Para ativar o relatório de todos os logs requerido pelo contrato para calcular os usuários, sessões, atividades, falhas e estatísticas técnicas, você pode simplesmente fazer todas as suas `PhoneApplicationPage` sub classes herdam a `EngagementPage` classes.

Aqui está um exemplo de como fazê-lo para uma página do seu aplicativo. Você pode fazer a mesma coisa para todas as páginas do seu aplicativo.

#### <a name="c-source-file"></a>Arquivo de origem c#

Modificar a página `.xaml.cs` arquivo:

-   Adicionar ao seu `using` instruções:

        using Microsoft.Azure.Engagement;

-   Substituir `PhoneApplicationPage` com `EngagementPage` :

**Sem contrato:**

        namespace Example
        {
          public partial class ExamplePage : PhoneApplicationPage
          {
            [...]
          }
        }

**Com o contrato:**

        using Microsoft.Azure.Engagement;
        
        namespace Example
        {
          public partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.WARNING] Se sua página herda a `OnNavigatedTo` método, tenha cuidado para permitir que o `base.OnNavigatedTo(e)` chamar. Caso contrário, a atividade não será relatada. Na verdade, o `EngagementPage` está chamando `StartActivity` dentro do `OnNavigatedTo` método.

#### <a name="xaml-file"></a>Arquivo XAML

Modificar a página `.xaml` arquivo:

-   Adicione a suas declarações de namespaces:

        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   Substituir `phone:PhoneApplicationPage` com `engagement:EngagementPage` :

**Sem contrato:**

        <phone:PhoneApplicationPage>
            <!-- layout -->
        </phone:PhoneApplicationPage>

**Com o contrato:**

        <engagement:EngagementPage 
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
        
            <!-- layout -->
        </engagement:EngagementPage >

#### <a name="override-the-default-behavior"></a>Substituir o comportamento padrão

Por padrão, o nome da classe da página é relatado como o nome da atividade, com e sem extras. Se a classe usa o sufixo "Página", contrato também removerá.

Se você quiser substituir o comportamento padrão para o nome, basta adicione isso ao seu código:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
           /* your code */
           return "new name";
        }

Se você quiser algumas informações extras com a sua atividade de relatório, você pode adicionar isso ao seu código:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
           /* your code */
           return extra;
        }

Esses métodos são chamados de dentro do `OnNavigatedTo` método da sua página.

### <a name="alternate-method-call-startactivity-manually"></a>Alternativas método: chamar `StartActivity()` manualmente

Se você não pode ou não deseja sobrecarregar seu `PhoneApplicationPage` classes, em vez disso, você pode iniciar suas atividades chamando `EngagementAgent` métodos diretamente.

Recomendamos chamada `StartActivity` dentro de sua `OnNavigatedTo` método de seu PhoneApplicationPage.

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
           base.OnNavigatedTo(e);
           EngagementAgent.Instance.StartActivity("MyPage");
        }

> [AZURE.IMPORTANT] Certifique-se de que você encerrar sua sessão corretamente.
>
> O SDK automaticamente chama o `EndActivity` método quando o aplicativo é fechado. Portanto, é **altamente** recomendada para chamar o `StartActivity` método sempre que a atividade do usuário alterar e como **nunca** chamar o `EndActivity` método. Este método envia uma mensagem para o servidor de contrato que o usuário atual deixou o aplicativo e isso afeta todos os logs de aplicativo.

##<a name="advanced-reporting"></a>Relatórios avançados

Opcionalmente, talvez você queira eventos específicos do aplicativo de relatório, erros e trabalhos, para fazer isso, use os outros métodos encontrados na `EngagementAgent` classe. A API do contrato permite usar todos os recursos avançados do contrato.

Para obter mais informações, consulte [como usar o contrato Mobile avançada API em seu aplicativo do Windows Phone Silverlight de marcação](mobile-engagement-windows-phone-use-engagement-api.md).

##<a name="advanced-configuration"></a>Configuração avançada

### <a name="disable-automatic-crash-reporting"></a>Desabilitar o relatório de falha automática

Você pode desativar a recurso de contrato de relatório automática de falhas. Quando uma exceção não tratada ocorrerá, contrato não faça nada.

> [AZURE.WARNING] Se você planeja desabilitar esse recurso, lembre-se de que, quando uma falha não tratada ocorrerá em seu aplicativo, contrato não enviará a falha **e** não fechará a sessão e trabalhos.

Para desativar o relatório de falha automática, basta Personalize sua configuração dependendo da maneira que você declarado:

#### <a name="from-engagementconfigurationxml-file"></a>De `EngagementConfiguration.xml` arquivo

Configurar falha de relatório para `false` entre `<reportCrash>` e `</reportCrash>` marcas.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>De `EngagementConfiguration` objeto em tempo de execução

Definir falha de relatório para false usando o objeto EngagementConfiguration.

        /* Engagement configuration. */

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        /\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Modo intermitente

Por padrão, os relatórios de serviço do contrato logs em tempo real. Se seu aplicativo relatórios logs muito frequentemente, é melhor para os logs de buffer e relatá-los ao mesmo tempo em uma base de tempo regular (isso se chama o "modo intermitente").

Para fazer isso, chame o método:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

O argumento é um valor em **milissegundos**. A qualquer momento, se você quiser reativar o registro em log em tempo real, basta chame o método sem nenhum parâmetro, ou com o valor 0.

O modo intermitente ligeiramente aumentar a vida de bateria, mas tem impacto sobre o Monitor de contrato: todas as sessões e trabalhos a duração será arredondado para o limite de intermitente (portanto, sessões e trabalhos menores do que o limite de intermitente pode não estar visível). É recomendável usar um limite de intermitente não está mais que 30000 (30s). Você precisa estar ciente de que salvou logs são limitadas a 300 itens. Se enviar for muito longo, você pode perder alguns logs.

> [AZURE.WARNING] O limite de intermitente não pode ser configurado para um período menor que um segundo. Se você tentar fazer isso, o SDK mostrará um rastreamento com o erro e será automaticamente redefinida para o valor padrão, ou seja, zero segundos. Isso causará o SDK para relatar os logs em tempo real.
 
