<properties
    pageTitle="IOS de contrato Mobile Azure SDK integração | Microsoft Azure"
    description="Últimas atualizações e procedimentos para iOS SDK para contrato de celular do Azure"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-on-ios"></a>Como integrar o contrato em iOS

> [AZURE.SELECTOR]
- [Universal do Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Este procedimento descreve a maneira mais simples para ativar a análise do contrato e monitoramento funções em seu aplicativo iOS.

O SDK do contrato requer iOS6 + e Xcode 8: o destino de implantação do seu aplicativo deve ter pelo menos iOS 6.

> [AZURE.NOTE]
> Se você realmente depender XCode 7 você pode usar o [iOS v3.2.4 SDK do contrato](https://aka.ms/r6oouh). Há um bug conhecido no módulo alcance desta versão anterior enquanto execução em dispositivos iOS 10 consulte [a integração de módulo alcance](mobile-engagement-ios-integrate-engagement-reach.md) para obter mais detalhes. Se você optar por usar o v3.2.4 SDK e simplesmente ignore o `UserNotifications.framework` importar na próxima etapa.

As etapas a seguir são suficientes para ativar o relatório de logs necessários para calcular todas as estatísticas sobre usuários, sessões, atividades, falhas e Technicals. O relatório logs necessários para calcular outras estatísticas como eventos, erros e trabalhos de deve ser feito manualmente usando a API do contrato (consulte [como usar o contrato de celular avançado marcação API em seu aplicativo iOS](mobile-engagement-ios-use-engagement-api.md) como essas estatísticas são depende do aplicativo.

##<a name="embed-the-engagement-sdk-into-your-ios-project"></a>Incorporar o SDK do contrato em seu projeto do iOS

- Baixe o SDK do iOS do [aqui](http://aka.ms/qk2rnj).

- Adicionar o SDK do contrato ao projeto iOS: no Xcode, clique com o botão direito no seu projeto e selecione **"Adicionar arquivos para..."** e escolha o `EngagementSDK` pasta.

- Contrato requer estruturas adicionais para trabalhar: no Explorador de projeto, abra seu painel de projeto e selecione o destino correto. Em seguida, abra a guia **"Criar fases"** e no menu **"Link binário com bibliotecas"** , adicione essas estruturas:

    -   `UserNotifications.framework`-Defina o link como`Optional`
    -   `AdSupport.framework`-Defina o link como`Optional`
    -   `SystemConfiguration.framework`
    -   `CoreTelephony.framework`
    -   `CFNetwork.framework`
    -   `CoreLocation.framework`
    -   `libxml2.dylib`

> [AZURE.NOTE] A estrutura de AdSupport pode ser removida. Contrato necessidades essa estrutura para coletar o IDFA. No entanto, o conjunto de IDFA pode ser desabilitado \<ios-sdk-contrato-idfa\> em conformidade com a nova política de Apple sobre esta ID.

##<a name="initialize-the-engagement-sdk"></a>Inicializar o contrato SDK

Você precisa modificar seu representante de aplicativo:

-   Na parte superior do seu arquivo de implementação, importe o agente de contrato:

        [...]
        #import "EngagementAgent.h"

-   Inicializar o contrato dentro do método '**applicationDidFinishLaunching:**'ou'**aplicativo: didFinishLaunchingWithOptions:**':

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
          [...]
        }

##<a name="basic-reporting"></a>Relatórios básicos

### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>Recomendável método: sobrecarregar seu `UIViewController` classes

Para ativar o relatório de todos os logs requerido pelo contrato para calcular os usuários, sessões, atividades, falhas e estatísticas técnicas, você pode simplesmente fazer todas as suas `UIViewController` sub classes herdam a `EngagementViewController` classes (mesmo regra para `UITableViewController`  - \> `EngagementTableViewController`).

**Sem contrato:**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Com o contrato:**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>Alternativas método: chamar `startActivity()` manualmente

Se você não pode ou não deseja sobrecarregar seu `UIViewController` classes, em vez disso, você pode iniciar suas atividades chamando `EngagementAgent`do métodos diretamente.

> [AZURE.IMPORTANT] O iOS SDK automaticamente chama o `endActivity()` método quando o aplicativo é fechado. Portanto, é *altamente* recomendada para chamar o `startActivity` método sempre que a atividade do usuário alterar e como *nunca* chamar o `endActivity` método, desde que chamar esse método força a sessão atual para ser concluída.

##<a name="location-reporting"></a>Relatório de local

Termos de Apple de serviço não permitem a aplicativos para usar o rastreamento para somente a finalidade de estatísticas de local. Portanto, é recomendável habilitar relatórios de local somente se seu aplicativo também usar o rastreamento por outro motivo de local.

Começando com iOS 8, você deve fornecer uma descrição de como o seu aplicativo usa serviços de localização definindo uma cadeia de caracteres para a tecla [NSLocationWhenInUseUsageDescription] ou [NSLocationAlwaysUsageDescription] no arquivo de Info.plist do seu aplicativo. Se você quiser local de relatório no plano de fundo com contrato, adicione a chave NSLocationAlwaysUsageDescription. Em todos os outros casos, adicione a chave NSLocationWhenInUseUsageDescription.

### <a name="lazy-area-location-reporting"></a>Local de área preguiça relatório

Relatórios de local de área preguiça permite relatar o país, região e localidade associado a dispositivos. Esse tipo de relatório de local usa apenas locais de rede (baseados em ID de célula ou WIFI). Área do dispositivo é relatada no máximo uma vez por sessão. O GPS nunca é usado e, portanto, nesse tipo de relatório de local tem poucas (não quer dizer que não) impacto na bateria.

Áreas relatadas são usadas para calcular estatísticas geográficas sobre usuários, sessões, eventos e erros. Eles também podem ser usados como critério em campanhas de alcance. A última área conhecida relatada para um dispositivo pode ser recuperada graças ao [API do dispositivo].

Para habilitar o relatório de localização de área preguiça, adicione a seguinte linha após inicializar o agente de contrato:

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>Relatório de localização de tempo real

Relatórios de localização de tempo real permite informar a latitude e longitude associado a dispositivos. Por padrão, esse tipo de relatório de local usa apenas locais de rede (baseados em ID de célula ou WIFI) e o relatório fica ativo apenas quando o aplicativo é executado em primeiro plano (por exemplo, durante uma sessão).

Tempo real locais são *não* usado para calcular estatísticas. Sua única finalidade é permitir o uso de tempo real geográfica-instalação da cerca \<alcance-público-geofencing\> critério em campanhas de alcance.

Para habilitar o relatório de localização de tempo real, adicione a seguinte linha após inicializar o agente de contrato:

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>GPS com base em relatórios

Por padrão, a localização de tempo real relatório somente usa locais de rede com base. Para habilitar o uso de GPS com base em locais (que são muito mais precisas), adicione:

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>Relatório de plano de fundo

Por padrão, relatórios de localização de tempo real fica ativo apenas quando o aplicativo é executado em primeiro plano (por exemplo, durante uma sessão). Para ativar o relatório também no plano de fundo, adicione:

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] Quando o aplicativo é executado em segundo plano, somente locais baseada em rede são relatados, mesmo se você ativou o GPS.

Implementação desta função chamará [startMonitoringSignificantLocationChanges] quando seu aplicativo avança no plano de fundo. Lembre-se de que ela automaticamente reinicie seu aplicativo em segundo plano se chegar um novo evento de local.

##<a name="advanced-reporting"></a>Relatórios avançados

Opcionalmente, se você quiser eventos específicos do aplicativo, erros e trabalhos de relatório, você precisa usar a API do contrato através dos métodos das `EngagementAgent` classe. Um objeto desta classe pode ser recuperado chamando a `[EngagementAgent shared]` método estático.

A API do contrato permite usar todos os recursos avançados do contrato e é detalhada na como usar a API do contrato IOS (bem como na documentação técnica do `EngagementAgent` classe).

##<a name="disable-idfa-collection"></a>Desabilitar o conjunto de IDFA

Por padrão, o contrato usará o [IDFA] para identificar exclusivamente um usuário. Mas se você não estiver usando anunciando em outro lugar no aplicativo, você pode ser rejeitada pelo processo de revisão da App Store. Conjunto de IDFA pode ser desabilitado, adicionando a macro pré-processador `ENGAGEMENT_DISABLE_IDFA` no seu arquivo de pch (ou na `Build Settings` do seu aplicativo). Isso garante que não há nenhuma referências a `ASIdentifierManager`, `advertisingIdentifier` ou `isAdvertisingTrackingEnabled` na compilação de aplicativos.

Integração no arquivo **prefix.pch** :

    #define ENGAGEMENT_DISABLE_IDFA
    ...

Você pode confirmar que o conjunto de IDFA corretamente está desabilitado no seu aplicativo marcando os logs de teste do contrato. Consulte o teste de integração\<ios-sdk-contrato-teste-idfa\> documentação para obter mais informações.

##<a name="disable-log-reporting"></a>Desabilitar o relatório de log

### <a name="using-a-method-call"></a>Usando uma chamada de método

Se você quiser contrato para interromper o envio de logs, você pode chamar:

    [[EngagementAgent shared] setEnabled:NO];

Esta chamada é persistente: ele usa `NSUserDefaults` para armazenar as informações.

Você pode habilitar o log de relatório novamente chamando a mesma função com `YES`.

### <a name="integration-in-your-settings-bundle"></a>Integração no pacote de configurações

Em vez de chamar essa função, você pode também integrar esta configuração diretamente no seu existentes `Settings.bundle` arquivo. A cadeia de caracteres `engagement_agent_enabled` deve ser usado como um o identificador de preferência e ele devem estar associados a uma opção de alternância (`PSToggleSwitchSpecifier`).

O exemplo a seguir de `Settings.bundle` mostra como implementá-la:

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[API do dispositivo]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier
