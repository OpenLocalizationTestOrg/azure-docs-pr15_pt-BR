<properties
    pageTitle="Integração do Android SDK do Azure contrato móvel"
    description="Últimas atualizações e procedimentos para Android SDK para contrato de celular do Azure"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-on-android"></a>Como integrar contrato no Android

> [AZURE.SELECTOR]
- [Universal do Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Este procedimento descreve a maneira mais simples para ativar a análise do contrato e monitoramento funções em seu aplicativo Android.

> [AZURE.IMPORTANT] Seu nível de API do Android SDK mínimo deve ser 10 ou posterior (2.3.3 não Android ou superior).

As etapas a seguir são suficientes para ativa o relatório de logs necessários para calcular todas as estatísticas sobre usuários, sessões, atividades, falhas e Technicals. O relatório logs necessários para calcular outras estatísticas como eventos, erros e trabalhos de deve ser feito manualmente usando a API do contrato (consulte [como usar o contrato de celular avançado marcação API no Android](mobile-engagement-android-use-engagement-api.md) como essas estatísticas são depende do aplicativo.

##<a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Incorporar o SDK do contrato e serviço seu projeto Android

Baixe o SDK do Android da [aqui](https://aka.ms/vq9mfn) Get `mobile-engagement-VERSION.jar` e colocá-los para o `libs` pasta do seu projeto Android (criar a pasta de bibliotecas se ele ainda não existe).

> [AZURE.IMPORTANT]
> Se você criar seu pacote de aplicativo com ProGuard, você precisa manter algumas classes. Você pode usar o trecho de configuração a seguir:
>
>
            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
            <methods>;
            }

Especifique a cadeia de caracteres de conexão do contrato ligando para o seguinte método a atividade de iniciador:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

A cadeia de conexão para o seu aplicativo é exibida no Portal do Azure.

-   Se faltando, adicione as seguintes permissões Android (antes do `<application>` marca):

            <uses-permission android:name="android.permission.INTERNET"/>
            <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   Adicionar a seguinte seção (entre os `<application>` e `</application>` marcas):

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

-   Alterar `<Your application name>` pelo nome do seu aplicativo.

> [AZURE.TIP] O `android:label` atributo permite que você escolha o nome do serviço do contrato como ele aparecerá para os usuários finais na tela "Executando services" do seu telefone. É recomendável definir esse atributo para `"<Your application name>Service"` (por exemplo, `"AcmeFunGameService"`).

Especifica o `android:process` atributo garante que o serviço de contrato será executado em seu próprio processo (executando contrato o mesmo processo como seu aplicativo fará seu thread principal/UI responde potencialmente menos).

> [AZURE.NOTE] Qualquer código que você colocar em `Application.onCreate()` e outras chamadas de retorno do aplicativo serão executadas para processos de todos os seus aplicativos, incluindo o contrato de serviço. Ele pode ter efeitos lado indesejados (como alocações de memória desnecessários e segmentos no processo do contrato, receptores de difusão duplicados ou serviços).

Se você substituir `Application.onCreate()`, recomenda-se para adicionar o trecho de código a seguir no início de seu `Application.onCreate()` função:

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

Você pode fazer a mesma coisa para `Application.onTerminate()`, `Application.onLowMemory()` e `Application.onConfigurationChanged(...)`.

Você também pode estender `EngagementApplication` em vez de estendendo `Application`: o retorno de chamada `Application.onCreate()` faz a verificação de processo e chamadas `Application.onApplicationProcessCreate()` somente se o processo atual não é aquele que hospeda o serviço de contrato, as mesmas regras de aplicação dos outros retornos de chamada.

##<a name="basic-reporting"></a>Relatórios básicos

### <a name="recommended-method-overload-your-activity-classes"></a>Recomendado método: sobrecarregar seu `Activity` classes

Para ativar o relatório de todos os logs requerido pelo contrato para calcular os usuários, sessões, atividades, falhas e estatísticas técnicas, você só precisa fazer todas as suas `*Activity` sub classes herdam correspondente `Engagement*Activity` classes (por exemplo, se estende a sua atividade herdada `ListActivity`, tornar-se estende `EngagementListActivity`).

**Sem contrato:**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**Com o contrato:**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [AZURE.IMPORTANT] Ao usar `EngagementListActivity` ou `EngagementExpandableListActivity`, verifique se qualquer uma chamada para `requestWindowFeature(...);` é feita antes da chamada para `super.onCreate(...);`, caso contrário ocorrerá uma falha.

Você pode encontrar essas classes a `src` pasta e pode copiá-los para seu projeto. As classes são também no **JavaDoc**.

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Alternativas método: chamar `startActivity()` e `endActivity()` manualmente

Se você não pode ou não deseja sobrecarregar seu `Activity` classes, em vez disso, você pode iniciar e encerrar suas atividades chamando `EngagementAgent`do métodos diretamente.

> [AZURE.IMPORTANT] O SDK do Android nunca chama o `endActivity()` método, mesmo quando o aplicativo estiver fechado (no Android, aplicativos são realmente nunca fechados). Assim, é *altamente* recomendada para chamar o `startActivity()` método na `onResume` retorno de chamada de *todas as* suas atividades e o `endActivity()` método na `onPause()` retorno de chamada de *todas as* suas atividades. Essa é a única maneira para certificar-se de que sessões não serão perdas. Se uma sessão é perdida, o serviço de contrato nunca desconectará da contrato back-end (desde que o serviço permaneça conectado como uma sessão está pendente).

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

Este exemplo muito semelhante ao `EngagementActivity` classe e suas variantes, cujo código-fonte é fornecido na `src` pasta.

##<a name="test"></a>Teste

Agora, verifique se a integração com seu executando o aplicativo móvel em um dispositivo ou emulador e verificando se ele registra uma sessão na guia Monitor.

As seções a seguir são opcionais.

##<a name="location-reporting"></a>Relatório de local

Se você quiser locais sejam informados, você precisa adicionar algumas linhas de configuração (entre os `<application>` e `</application>` marcas).

### <a name="lazy-area-location-reporting"></a>Local de área preguiça relatório

Relatórios de local de área preguiça permite relatar o país, região e localidade associado a dispositivos. Esse tipo de relatório de local usa apenas locais de rede (baseados em ID de célula ou WIFI). Área do dispositivo é relatada no máximo uma vez por sessão. O GPS nunca é usado e, portanto, nesse tipo de relatório de local tem poucas (não quer dizer que não) impacto na bateria.

Áreas relatadas são usadas para calcular estatísticas geográficas sobre usuários, sessões, eventos e erros. Eles também podem ser usados como critério em campanhas de alcance.

Para habilitar o relatório de localização de área preguiça, você poderá fazer isso usando a configuração mencionada anteriormente neste procedimento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Você também precisa adicionar a permissão a seguir se faltando:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou você pode continuar usando ``ACCESS_FINE_LOCATION`` se você já usá-lo em seu aplicativo.

### <a name="real-time-location-reporting"></a>Relatório de localização de tempo real

Relatórios de localização de tempo real permite informar a latitude e longitude associado a dispositivos. Por padrão, esse tipo de relatório de local usa apenas locais de rede (baseados em ID de célula ou WIFI) e o relatório fica ativo apenas quando o aplicativo é executado em primeiro plano (por exemplo, durante uma sessão).

Tempo real locais são *não* usado para calcular estatísticas. Sua única finalidade é permitir o uso de tempo real geográfica-instalação da cerca \<alcance-público-geofencing\> critério em campanhas de alcance.

Para habilitar o local em tempo real de relatório, você poderá fazer isso usando a configuração mencionada anteriormente neste procedimento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Você também precisa adicionar a permissão a seguir se faltando:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou você pode continuar usando ``ACCESS_FINE_LOCATION`` se você já usá-lo em seu aplicativo.

#### <a name="gps-based-reporting"></a>GPS com base em relatórios

Por padrão, a localização de tempo real relatório somente usa locais de rede com base. Para habilitar o uso de GPS com base em locais (que são muito mais precisas), use o objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Você também precisa adicionar a permissão a seguir se faltando:

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Relatório de plano de fundo

Por padrão, relatórios de localização de tempo real fica ativo apenas quando o aplicativo é executado em primeiro plano (por exemplo, durante uma sessão). Para ativar o relatório também no plano de fundo, use o objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Quando o aplicativo é executado em segundo plano, somente locais baseada em rede são relatados, mesmo se você ativou o GPS.

O relatório de local do plano de fundo será interrompido se o usuário reinicializar seu dispositivo, você pode adicionar isso para torná-la a reiniciar automaticamente no momento da inicialização:

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

Você também precisa adicionar a permissão a seguir se faltando:

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Permissões de M Android

Começando com Android M, algumas permissões são gerenciados no tempo de execução e precisa de aprovação do usuário.

As permissões de tempo de execução serão desligadas por padrão para novas instalações de aplicativo se você selecionar o nível de API Android 23. Caso contrário, ele será ativado por padrão.

O usuário pode ativar/desativar essas permissões no menu de configurações do dispositivo. Desativando permissões do menu sistema interrompe processos de plano de fundo do aplicativo, esse é um comportamento de sistema e não tem impacto sobre a capacidade de receber push no plano de fundo.

No contexto de contrato de celular, as permissões que exigem aprovação em tempo de execução são:

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`
- `WRITE_EXTERNAL_STORAGE`(somente quando o direcionamento de nível de API Android 23 para esta)

O armazenamento externo é usado somente para o recurso de panorama de alcance. Se você encontrar pedindo usuários essa permissão para ser prejudicial, você pode removê-lo se você tiver usado somente para o contrato de celular porém desabilitando o recurso de panorama.

Para os recursos de localização, você deverá solicitar permissões para usuário usando uma caixa de diálogo padrão do sistema. Se o usuário aprova, você precisará informar ``EngagementAgent`` para fazer essa alteração na conta em tempo real (caso contrário, a alteração será processada na próxima vez que o usuário inicia o aplicativo).

Aqui está um exemplo de código para usar em uma atividade do seu aplicativo para solicitar permissões e encaminhar o resultado se positivo para ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

##<a name="advanced-reporting"></a>Relatórios avançados

Opcionalmente, se você quiser eventos específicos do aplicativo, erros e trabalhos de relatório, você precisa usar a API do contrato através dos métodos das `EngagementAgent` classe. Um objeto desta classe pode ser recuperados chamando a `EngagementAgent.getInstance()` método estático.

A API do contrato permite usar todos os recursos avançados do contrato e é detalhada na como usar a API de contrato no Android (bem como na documentação técnica do `EngagementAgent` classe).

##<a name="advanced-configuration-in-androidmanifestxml"></a>Configuração avançada (em AndroidManifest.xml)

### <a name="wake-locks"></a>Bloqueios de ativação

Se você quiser Certifique-se de que as estatísticas são enviadas em tempo real ao usar Wifi ou quando a tela está desativada, adicione a seguinte permissão opcional:

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>Relatório de falha

Se desejar desativar relatórios de falha, adicionar isto (entre os `<application>` e `</application>` marcas):

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Limite de intermitente

Por padrão, os relatórios de serviço do contrato logs em tempo real. Se seu aplicativo relatórios logs muito frequentemente, é melhor para os logs de buffer e relatá-los ao mesmo tempo em uma base de tempo regular (isso se chama o "modo intermitente"). Para fazer isso, adicione esse (entre os `<application>` e `</application>` marcas):

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

O modo intermitente ligeiramente aumentar a vida de bateria, mas tem impacto sobre o Monitor de contrato: todas as sessões e trabalhos a duração será arredondado para o limite de intermitente (portanto, sessões e trabalhos menores do que o limite de intermitente pode não estar visível). É recomendável usar um limite de intermitente não está mais que 30000 (30s).

### <a name="session-timeout"></a>Tempo limite de sessão

Por padrão, uma sessão é finalizada 10s após o final da sua última atividade (que geralmente ocorre pressionando a tecla Home ou voltar, definindo o telefone ocioso ou comendo em outro aplicativo). Isso é para evitar uma divisão de sessão cada vez que a saída de usuário e retornar para o aplicativo rapidamente (que pode acontecer quando ele atende uma imagem, marque uma notificação, etc.). Talvez você queira modificar esse parâmetro. Para fazer isso, adicione esse (entre os `<application>` e `</application>` marcas):

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

##<a name="disable-log-reporting"></a>Desabilitar o relatório de log

### <a name="using-a-method-call"></a>Usando uma chamada de método

Se você quiser contrato para interromper o envio de logs, você pode chamar:

            EngagementAgent.getInstance(context).setEnabled(false);

Esta chamada é persistente: ele usa um arquivo compartilhado preferências.

Se o contrato estiver ativo quando você chamar essa função, pode levar 1 minuto para interromper o serviço. No entanto, ele não iniciar o serviço em todos os na próxima vez que você inicia o aplicativo.

Você pode habilitar o log de relatório novamente chamando a mesma função com `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integração no seu próprio`PreferenceActivity`

Em vez de chamar essa função, você pode também integrar esta configuração diretamente no seu existentes `PreferenceActivity`.

Você pode configurar contrato para usar o arquivo de preferências (com o modo desejado) a `AndroidManifest.xml` arquivo com `application meta-data`:

-   O `engagement:agent:settings:name` chave é usada para definir o nome do arquivo compartilhado preferências.
-   O `engagement:agent:settings:mode` chave é usada para definir o modo do arquivo compartilhado preferências, você deve usar o mesmo modo como em sua `PreferenceActivity`. O modo deve ser passado como um número: se você estiver usando uma combinação de sinalizadores constantes em seu código, verifique o valor total.

Contrato sempre usar o `engagement:key` chave booliana dentro do arquivo de preferências para gerenciar essa configuração.

O exemplo a seguir de `AndroidManifest.xml` mostra os valores padrão:

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

Em seguida, você pode adicionar uma `CheckBoxPreference` no layout preferência como o seguinte:

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
