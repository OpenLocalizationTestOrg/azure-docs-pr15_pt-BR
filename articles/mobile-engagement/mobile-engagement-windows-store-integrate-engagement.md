<properties 
    pageTitle="Integração de SDK do contrato de Universal de aplicativos do Windows" 
    description="Como integrar Azure contrato móvel com aplicativos Universal do Windows"                  
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

# <a name="windows-universal-apps-engagement-sdk-integration"></a>Integração de SDK do contrato de Universal de aplicativos do Windows

> [AZURE.SELECTOR] 
- [Windows universal](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

Este procedimento descreve a maneira mais simples para ativar a análise do contrato e monitoramento funções em seu aplicativo Universal do Windows.

As etapas a seguir são suficientes para ativar o relatório de logs necessários para calcular todas as estatísticas sobre usuários, sessões, atividades, falhas e Technicals. O relatório logs necessários para calcular outras estatísticas como eventos, erros e trabalhos de deve ser feito manualmente usando a API do contrato (consulte [como usar o contrato Mobile avançada API em seu aplicativo Windows Universal de marcação](mobile-engagement-windows-store-use-engagement-api.md) como essas estatísticas são depende do aplicativo.

## <a name="supported-versions"></a>Versões suportadas

O contrato SDK para Windows Universal aplicativos Mobile só pode ser integrados em tempo de execução do Windows e aplicativos de plataforma do Windows Universal direcionamento:

-   Windows 8
-   Windows 8.1
-   Windows Phone 8.1
-   Windows 10 (famílias de desktop e móveis)

> [AZURE.NOTE] Se você está destinando Silverlight do Windows Phone, consulte o [procedimento de integração do Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md).

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>Instalar o SDK do contrato móvel Universal aplicativos

### <a name="all-platforms"></a>Todas as plataformas

O contrato SDK para Windows Universal aplicativo Mobile está disponível como um pacote do Nuget chamado *MicrosoftAzure.MobileEngagement*. Você pode instalá-lo do Gerenciador de pacote do Visual Studio Nuget.

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8. x e Windows Phone 8.1

NuGet implanta automaticamente os recursos SDK no `Resources` pasta na raiz do seu projeto de aplicativo.

### <a name="windows-10-universal-windows-platform-applications"></a>Aplicativos da plataforma Windows para Universal do Windows 10

NuGet não implantar automaticamente os recursos SDK em seu aplicativo de UWP ainda. Você deve fazê-lo manualmente até implantação de recursos é reintroduzida no NuGet:

1.  Abra o Explorador de arquivos.
2.  Navegue até o local seguinte (**x.x.x** é a versão do contrato está instalando): *% USERPROFILE %\\.nuget\packages\MicrosoftAzure.MobileEngagement\\**x.x.x**\\content\win81*
3.  Arraste e solte na pasta **recursos** do Explorador de arquivos na raiz do seu projeto no Visual Studio.
4.  No Visual Studio selecionar seu projeto e ativar o ícone **Mostrar todos os arquivos** na parte superior do **Solution Explorer**.
5.  Alguns arquivos não são incluídos no projeto. Para importá-los ao mesmo tempo clique com botão direito na pasta de **recursos** , **Excluir do projeto** e em seguida, outra clique direito do mouse na pasta **recursos** , **incluir no projeto** para incluir novamente a pasta inteira. Agora, todos os arquivos da pasta de **recursos** estão incluídos no seu projeto.

## <a name="add-the-capabilities"></a>Adicione os recursos

O SDK do contrato necessidades alguns recursos do SDK do Windows para funcionar corretamente.

Abrir sua `Package.appxmanifest` de arquivo e certifique-se de que os seguintes recursos são declarados:

-   `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>Inicializar o contrato SDK

### <a name="engagement-configuration"></a>Configuração de contrato

A configuração de contrato centralizada no `Resources\EngagementConfiguration.xml` arquivo do seu projeto.

Edite esse arquivo para especificar:

-   A cadeia de caracteres de conexão de aplicativo entre marcas `<connectionString>` e `<\connectionString>`.

Se você quiser especificá-lo em tempo de execução, você pode chamar o seguinte método antes da inicialização do agente de contrato:
          
          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

A cadeia de conexão para o seu aplicativo é exibida no Portal de clássico do Azure.

### <a name="engagement-initialization"></a>Inicialização de contrato

Quando você cria um novo projeto, um `App.xaml.cs` arquivo é gerado. Esta classe herda das `Application` e contém vários métodos importantes. Ele também será usado para inicializar o SDK do contrato.

Modificar o `App.xaml.cs`:

-   Adicionar ao seu `using` instruções:

        using Microsoft.Azure.Engagement;

-   Defina um método para compartilhar a inicialização de contrato uma vez para todas as chamadas:

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
        
          // or
        
          EngagementAgent.Instance.Init(e, engagementConfiguration);
        }
        
-   Chamar `InitEngagement` no `OnLaunched` método:

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
          InitEngagement(e);
        }

-   Quando seu aplicativo é iniciado usando um esquema personalizado, outro aplicativo ou da linha de comando e em seguida, o `OnActivated` método é chamado. Você também precisa iniciar o SDK do contrato quando seu aplicativo está ativado. Para fazer isso, substituir `OnActivated` método:

        protected override void OnActivated(IActivatedEventArgs args)
        {
          InitEngagement(args);
        }

> [AZURE.IMPORTANT] Podemos altamente desencorajar para adicionar a inicialização de contrato em outro lugar do seu aplicativo.

## <a name="basic-reporting"></a>Relatórios básicos

### <a name="recommended-method-overload-your-page-classes"></a>Recomendado método: sobrecarregar seu `Page` classes

Para ativar o relatório de todos os logs requerido pelo contrato para calcular os usuários, sessões, atividades, falhas e estatísticas técnicas, você pode simplesmente fazer todas as suas `Page` sub classes herdam a `EngagementPage` classes.

Aqui está um exemplo de como fazê-lo para uma página do seu aplicativo. Você pode fazer a mesma coisa para todas as páginas do seu aplicativo.

#### <a name="c-source-file"></a>Arquivo de origem c#

Modificar a página `.xaml.cs` arquivo:

-   Adicionar ao seu `using` instruções:

        using Microsoft.Azure.Engagement;

-   Substituir `Page` com `EngagementPage`:

**Sem contrato:**
    
        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Com o contrato:**

        using Microsoft.Azure.Engagement;
        
        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.IMPORTANT] Se sua página substitui o `OnNavigatedTo` método, certifique-se chamar `base.OnNavigatedTo(e)`. Caso contrário, a atividade não será relatada (o `EngagementPage` chamadas `StartActivity` dentro de sua `OnNavigatedTo` método).

#### <a name="xaml-file"></a>Arquivo XAML

Modificar a página `.xaml` arquivo:

-   Adicione a suas declarações de namespaces:

        xmlns:engagement="using:Microsoft.Azure.Engagement"

-   Substituir `Page` com `engagement:EngagementPage`:

**Sem contrato:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Com o contrato:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

#### <a name="override-the-default-behaviour"></a>Substituir o comportamento padrão

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

Se você não pode ou não deseja sobrecarregar seu `Page` classes, em vez disso, você pode iniciar suas atividades chamando `EngagementAgent` métodos diretamente.

É recomendável chamar `StartActivity` dentro de sua `OnNavigatedTo` método da sua página.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [AZURE.IMPORTANT]  Certifique-se de que você encerrar sua sessão corretamente.
> 
> O Windows SDK Universal automaticamente chama o `EndActivity` método quando o aplicativo é fechado. Portanto, é **altamente** recomendada para chamar o `StartActivity` método sempre que a atividade do usuário alterar e como **nunca** chamar o `EndActivity` método, este método envia ao servidor de contrato que o usuário atual tem deixar o aplicativo, esta será afeta todos os logs de aplicativo.

## <a name="advanced-reporting"></a>Relatórios avançados

Opcionalmente, talvez você queira eventos específicos do aplicativo de relatório, erros e trabalhos, para fazer isso, use os outros métodos encontrados na `EngagementAgent` classe. A API do contrato permite usar todos os recursos avançados do contrato.

Para obter mais informações, consulte [como usar o contrato Mobile avançada API em seu aplicativo Windows Universal de marcação](mobile-engagement-windows-store-use-engagement-api.md).

##<a name="advanced-configuration"></a>Configuração avançada

### <a name="disable-automatic-crash-reporting"></a>Desabilitar o relatório de falha automática

Você pode desativar a recurso de contrato de relatório automática de falhas. Quando uma exceção não tratada ocorrerá, contrato não faça nada.

> [AZURE.WARNING] Se você planeja desabilitar esse recurso, lembre-se de que quando uma falha não tratada ocorrerá em seu aplicativo, contrato não enviará que a falha **e** não fechará a sessão e trabalhos.

Para desativar o relatório de falha automática, basta Personalize sua configuração dependendo da maneira que você declarado:

#### <a name="from-engagementconfigurationxml-file"></a>De `EngagementConfiguration.xml` arquivo

Configurar falha de relatório para `false` entre `<reportCrash>` e `</reportCrash>` marcas.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>De `EngagementConfiguration` objeto em tempo de execução

Definir falha de relatório para false usando o objeto EngagementConfiguration.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Modo intermitente

Por padrão, os relatórios de serviço do contrato logs em tempo real. Se seu aplicativo relatórios logs muito frequentemente, é melhor para os logs de buffer e relatá-los ao mesmo tempo em uma base de tempo regular (isso se chama o "modo intermitente").

Para fazer isso, chame o método:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

O argumento é um valor em **milissegundos**. A qualquer momento, se você quiser reativar o registro em log em tempo real, basta chame o método sem nenhum parâmetro, ou com o valor 0.

O modo intermitente ligeiramente aumentar a vida de bateria, mas tem impacto sobre o Monitor de contrato: todas as sessões e trabalhos a duração será arredondado para o limite de intermitente (portanto, sessões e trabalhos menores do que o limite de intermitente pode não estar visível). É recomendável usar um limite de intermitente não está mais que 30000 (30s). Você precisa estar ciente de que salvou logs são limitadas a 300 itens. Se enviar for muito longo, você pode perder alguns logs.

> [AZURE.WARNING] O limite de intermitente não pode ser configurado para um período menor que 1s. Se você tentar fazer isso, o SDK mostrará um rastreamento com o erro e será automaticamente Redefinir para o valor padrão, ou seja, 0s. Isso causará o SDK para relatar os logs em tempo real.

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
 
