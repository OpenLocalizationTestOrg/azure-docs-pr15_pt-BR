<properties
    pageTitle="Relatórios opções avançadas para Android SDK do Azure Mobile contrato"
    description="Descreve como fazer relatórios avançados capturar analytics para Android SDK do Azure Mobile contrato"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-reporting-with-engagement-on-android"></a>Relatórios avançados com contrato no Android

> [AZURE.SELECTOR]
- [Windows universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

Este tópico descreve cenários adicionais de subordinação em seu aplicativo Android. Você pode aplicar essas opções para o aplicativo que criou o tutorial de [Introdução](mobile-engagement-android-get-started.md) .

## <a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

O tutorial você concluiu foi deliberadamente simples e direto, mas existem opções que você pode escolher avançados.

## <a name="modifying-your-activity-classes"></a>Modificando seu `Activity` classes

No [tutorial de Introdução](mobile-engagement-android-get-started.md), tudo o que você tinha de fazer era tornar seu `*Activity` subclasses herdam correspondente `Engagement*Activity` classes. Por exemplo, se sua atividade herdada estendido `ListActivity`, você tornaria estender `EngagementListActivity`.

> [AZURE.IMPORTANT] Ao usar `EngagementListActivity` ou `EngagementExpandableListActivity`, verifique se qualquer uma chamada para `requestWindowFeature(...);` é feita antes da chamada para `super.onCreate(...);`, caso contrário, uma falha ocorre.

Você pode encontrar essas classes a `src` pasta e pode copiá-los para seu projeto. As classes são também no **JavaDoc**.

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Alternativas método: chamar `startActivity()` e `endActivity()` manualmente

Se você não pode ou não deseja sobrecarregar seu `Activity` classes, em vez disso, você pode iniciar e encerrar suas atividades chamando a `EngagementAgent`do métodos diretamente.

> [AZURE.IMPORTANT] O SDK do Android nunca chama o `endActivity()` método, mesmo quando o aplicativo estiver fechado (no Android, aplicativos são nunca fechados). Assim, é *altamente* recomendada para chamar o `startActivity()` método na `onResume` retorno de chamada de *todas as* suas atividades e o `endActivity()` método na `onPause()` retorno de chamada de *todas as* suas atividades. Essa é a única maneira para certificar-se de que sessões não seja perdas. Se uma sessão é perdida, o serviço de contrato nunca desconecta de back-end contrato (desde que o serviço permaneça conectado como uma sessão está pendente).

Aqui está um exemplo:

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

Este exemplo é semelhante ao `EngagementActivity` classe e suas variantes, cujo código-fonte é fornecido na `src` pasta.

## <a name="using-applicationoncreate"></a>Usando Application.onCreate()

Qualquer código que você colocar em `Application.onCreate()` e em outro aplicativo retornos de chamada é executado para processos de todos os seus aplicativos, incluindo o contrato de serviço. Ele pode ter efeitos indesejados de lado, como alocações de memória desnecessários e segmentos no processo do contrato, ou duplicados receptores difusão ou serviços.

Se você substituir `Application.onCreate()`, recomendamos adicionando o trecho de código a seguir no início de seu `Application.onCreate()` função:

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

Você pode fazer a mesma coisa para `Application.onTerminate()`, `Application.onLowMemory()`, e `Application.onConfigurationChanged(...)`.

Você também pode estender `EngagementApplication` em vez de estendendo `Application`: o retorno de chamada `Application.onCreate()` faz a verificação de processo e chamadas `Application.onApplicationProcessCreate()` somente se o processo atual não é aquele que hospeda o serviço de contrato, as mesmas regras de aplicação dos outros retornos de chamada.

## <a name="tags-in-the-androidmanifestxml-file"></a>Marcas no arquivo AndroidManifest.xml

Na marca de serviço no arquivo AndroidManifest.xml, o `android:label` atributo permite que você escolha o nome do serviço do contrato como aparece para os usuários finais na tela "Executando services" do seu telefone. É recomendável a configuração desse atributo como `"<Your application name>Service"` (por exemplo, `"AcmeFunGameService"`).

Especifica o `android:process` atributo garante que o serviço de contrato é executado em seu próprio processo (executando contrato o mesmo processo como seu aplicativo torna seu thread principal/UI responde potencialmente menos).

## <a name="building-with-proguard"></a>Criando com ProGuard

Se você criar seu pacote de aplicativo com ProGuard, você precisa manter algumas classes. Você pode usar o trecho de configuração a seguir:

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
    }
