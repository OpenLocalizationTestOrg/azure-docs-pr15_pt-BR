<properties
    pageTitle="Local de relatórios para o SDK do Azure contrato móvel Android"
    description="Descreve como configurar relatórios for Android SDK do Azure Mobile contrato de local"
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
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Local de relatórios para o SDK do Azure contrato móvel Android

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-integrate-engagement.md)

Este tópico descreve como fazer a localização de relatório para o seu aplicativo Android.

## <a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>Relatório de local

Se você quiser locais sejam informados, você precisa adicionar algumas linhas de configuração (entre os `<application>` e `</application>` marcas).

### <a name="lazy-area-location-reporting"></a>Local de área preguiça relatório

Localização da área preguiça permite relatar o país, região e localidade associados dispositivos de relatório. Esse tipo de relatório de local usa apenas locais de rede (baseados em ID de célula ou WIFI). Área do dispositivo é relatada no máximo uma vez por sessão. O GPS nunca é usado e, portanto, esse tipo de relatório de local tem baixo impacto na bateria.

Áreas relatadas são usadas para calcular estatísticas geográficas sobre usuários, sessões, eventos e erros. Eles também podem ser usados como critério em campanhas de alcance.

Habilitar o local da área preguiça relatórios usando a configuração mencionada anteriormente neste procedimento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Você também precisa especificar uma permissão de local. Este código usa ``COARSE`` permissão:

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Se seu aplicativo exigir, você pode usar ``ACCESS_FINE_LOCATION`` em vez disso.

### <a name="real-time-location-reporting"></a>Relatório de local em tempo real

Relatório de local em tempo real habilita o relatório a latitude e longitude associados dispositivos. Por padrão, esse tipo de relatório de local usa apenas locais de rede, com base em ID de célula ou WIFI. O relatório está ativo somente quando o aplicativo é executado no primeiro plano (por exemplo, durante uma sessão).

Em tempo real locais são *não* usado para calcular estatísticas. Sua única finalidade é permitir o uso de instalação da cerca-geográfica em tempo real \<alcance-público-geofencing\> critério em campanhas de alcance.

Para habilitar o local em tempo real de relatório, adicione uma linha de código para onde você definir a cadeia de conexão do contrato na atividade inicializador. O resultado tem a seguinte aparência:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>GPS com base em relatórios

Por padrão, local em tempo real de relatório usa somente locais baseados em rede. Para habilitar o uso de locais baseado em GPS, que são muito mais precisas, use o objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Você também precisa adicionar a permissão a seguir se faltando:

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Relatório de plano de fundo

Por padrão, relatórios de local em tempo real fica ativo apenas quando o aplicativo é executado em primeiro plano (por exemplo, durante uma sessão). Para ativar o relatório também no plano de fundo, use esse objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Quando o aplicativo é executado em segundo plano, somente os locais baseados em rede são relatados, mesmo se você ativou o GPS.

Se o usuário reinicializar seus dispositivos, o relatório de local do plano de fundo é interrompido. Para tornar reiniciar automaticamente no momento da inicialização, adicione este código.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

Você também precisa adicionar a permissão a seguir se faltando:

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Permissões de M Android

Começando com Android M, algumas permissões são gerenciados no tempo de execução e precisam de aprovação do usuário.

Se você selecionar o nível de API Android 23, as permissões de tempo de execução estão desativadas por padrão para novas instalações do aplicativo. Caso contrário, elas estão ativadas por padrão.

Você pode ativar/desativar essas permissões no menu de configurações do dispositivo. Desativar as permissões no menu de sistema elimina os processos de plano de fundo do aplicativo, que é um comportamento do sistema e não tem impacto sobre a capacidade de receber push no plano de fundo.

No contexto de localização Mobile contrato de relatório, as permissões que exigem aprovação em tempo de execução são:

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`

Solicite permissões do usuário usando uma caixa de diálogo padrão do sistema. Se o usuário aprovar, informe ``EngagementAgent`` para fazer essa alteração em conta em tempo real. Caso contrário, a alteração é processada na próxima vez que o usuário inicia o aplicativo.

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
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
