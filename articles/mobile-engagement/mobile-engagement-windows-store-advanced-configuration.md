<properties
    pageTitle="Configuração para contrato de aplicativos Universal do Windows SDK avançada"
    description="Configuração Opções avançadas para contrato de celular Azure com aplicativos Universal do Windows"                    
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
    ms.date="10/04/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>Configuração para contrato de aplicativos Universal do Windows SDK avançada

> [AZURE.SELECTOR]
- [Windows universal](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-configuration.md)

Este procedimento descreve como configurar diversas opções de configuração para os aplicativos do Azure Mobile contrato Android.

## <a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

##<a name="advanced-configuration"></a>Configuração avançada

### <a name="disable-automatic-crash-reporting"></a>Desabilitar o relatório de falha automática

Você pode desativar a recurso de contrato de relatório automática de falhas. Em seguida, quando ocorre uma exceção não tratada, contrato não faz nada.

> [AZURE.WARNING] Se você desabilitar esse recurso, quando ocorre uma falha não tratada no seu aplicativo, contrato não envie que a falha **e** não fecha a sessão e trabalhos.

Para desativar o relatório de falha automática, personalize sua configuração dependendo da maneira que você declarado:

#### <a name="from-engagementconfigurationxml-file"></a>De `EngagementConfiguration.xml` arquivo

Configurar falha de relatório para `false` entre `<reportCrash>` e `</reportCrash>` marcas.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>De `EngagementConfiguration` objeto em tempo de execução

Definir falha de relatório para false usando o objeto EngagementConfiguration.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="disable-real-time-reporting"></a>Desabilitar o relatório de tempo real

Por padrão, os relatórios de serviço do contrato logs em tempo real. Se seu aplicativo relatórios logs com frequência, é melhor para os logs de buffer e relatá-los ao mesmo tempo em uma base de tempo regular. Isso se chama "intermitente modo".

Para fazer isso, chame o método:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

O argumento é um valor em **milissegundos**. Sempre que você deseja reativar o log em tempo real, chame o método sem nenhum parâmetro, ou com o valor 0.

O modo intermitente ligeiramente aumenta a vida de bateria mas tem impacto sobre o Monitor de contrato: todas as sessões e trabalhos a duração são arredondados para o limite de intermitente (portanto, sessões e trabalhos menores do que o limite de intermitente pode não estar visível). É recomendável usar um limite de intermitente não está mais que 30000 (30s). Logs salvos limitam-se a 300 itens. Se enviar é muito longo, você poderá perder alguns logs.

> [AZURE.WARNING] O limite de intermitente não pode ser configurado para um período menor do que um segundo. Se você fizer isso, o SDK mostra um rastreamento com o erro e redefine automaticamente como o valor padrão, zero segundos. Isso dispara o SDK para relatar os logs em tempo real.

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
