<properties
    pageTitle="Windows Universal relatórios avançado com o contrato de MobileApps"
    description="Como integrar Azure contrato móvel com aplicativos Universal do Windows"                  
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Relatórios avançados com o contrato de aplicativos Universal do Windows SDK

> [AZURE.SELECTOR]
- [Windows universal](mobile-engagement-windows-store-advanced-reporting.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

Este tópico descreve cenários adicionais de subordinação em seu aplicativo Universal do Windows. Esses cenários incluem opções que você pode optar por aplicar ao aplicativo criado no tutorial [Introdução](mobile-engagement-windows-store-dotnet-get-started.md) .

## <a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Antes de iniciar este tutorial, você deve primeiro concluir o tutorial de [Introdução](mobile-engagement-windows-store-dotnet-get-started.md) , que é deliberadamente simples e direto. Este tutorial apresenta opções adicionais, entre que você pode escolher.

## <a name="specifying-engagement-configuration-at-runtime"></a>Especificando configuração de contrato no tempo de execução

A configuração de contrato centralizada no `Resources\EngagementConfiguration.xml` arquivo do seu projeto, que é onde ele foi especificado no tópico [Introdução](mobile-engagement-windows-store-dotnet-get-started.md) .

Mas você também pode especificá-lo em tempo de execução: você pode chamar o seguinte método antes da inicialização do agente de contrato:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Recomendado método: sobrecarregar seu `Page` classes

Para ativar o relatório de todos os logs requeridos pelo contrato para calcular os usuários, sessões, atividades, falhas e estatísticas técnicas, faça todas as suas `Page` sub classes herdam a `EngagementPage` classes.

Aqui está um exemplo de uma página do seu aplicativo. Você pode fazer a mesma coisa para todas as páginas do seu aplicativo.

### <a name="c-source-file"></a>Arquivo de origem c#

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

> [AZURE.IMPORTANT] Se sua página substitui o `OnNavigatedTo` método, certifique-se chamar `base.OnNavigatedTo(e)`. Caso contrário, a atividade não é informado (o `EngagementPage` chamadas `StartActivity` dentro de sua `OnNavigatedTo` método).

### <a name="xaml-file"></a>Arquivo XAML

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

### <a name="override-the-default-behaviour"></a>Substituir o comportamento padrão

Por padrão, o nome da classe da página é relatado como o nome da atividade, com e sem extras. Se a classe usa o sufixo "Página", contrato removerá.

Para substituir o comportamento padrão para o nome, adicione este código:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Para relatar informações extras com a sua atividade, adicione este código:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Esses métodos são chamados de dentro do `OnNavigatedTo` método da sua página.

### <a name="alternate-method-call-startactivity-manually"></a>Alternativas método: chamar `StartActivity()` manualmente

Se você não pode ou não deseja sobrecarregar seu `Page` classes, em vez disso, você pode iniciar suas atividades chamando `EngagementAgent` métodos diretamente.

É recomendável chamada `StartActivity` dentro de sua `OnNavigatedTo` método da sua página.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [AZURE.IMPORTANT]  Certifique-se de que você encerrar sua sessão corretamente.
>
> O Windows SDK Universal automaticamente chama o `EndActivity` método quando o aplicativo é fechado. Portanto, é **altamente** recomendada para chamar o `StartActivity` método sempre que a atividade do usuário alterar e como **nunca** chamar o `EndActivity` método. Este método notifica o servidor de contrato que o usuário atual deixou o aplicativo, que afetará todos os logs de aplicativo.

## <a name="advanced-reporting"></a>Relatórios avançados

Opcionalmente, talvez você queira relatar eventos específicos do aplicativo, erros e trabalhos, para fazer isso, usam os outros métodos encontrados na `EngagementAgent` classe. A API do contrato permite o uso de recursos avançados do contrato tudo.

Para obter mais informações, consulte [como usar o contrato Mobile avançada API em seu aplicativo Windows Universal de marcação](mobile-engagement-windows-store-use-engagement-api.md).
