<properties
    pageTitle="Integração do Android SDK do Azure contrato móvel"
    description="Últimas atualizações e procedimentos para Android SDK para contrato de celular do Azure"
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
    ms.date="10/10/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-gcm-with-mobile-engagement"></a>Como integrar GCM com contrato móvel

> [AZURE.IMPORTANT] Você deve seguir o procedimento de integração descrito na como integrar o contrato no Android documento antes de seguir este guia.
>
> Este documento é útil apenas se você já integrado o módulo de alcance e o plano para dispositivos do Google Play por push. Para integrar campanhas de alcance em seu aplicativo, leia primeiro como integrar contrato alcançar no Android.

##<a name="introduction"></a>Introdução

Integração GCM permite que o seu aplicativo para ser enviado.

Cargas GCM enviadas para o SDK sempre contêm o `azme` chave no objeto de dados. Portanto, se você usar GCM para outra finalidade em seu aplicativo, você pode filtrar coloca com base na chave.

> [AZURE.IMPORTANT] Somente os dispositivos que executam o Android 2.2 ou acima, tendo Google Play instalado e tendo Google habilitado de conexão de plano de fundo pode ser enviado usando GCM; No entanto, você pode integrar este código com segurança em dispositivos sem suporte (apenas utiliza propósitos).

##<a name="create-a-google-cloud-messaging-project-with-api-key"></a>Criar um projeto de mensagens de nuvem do Google com chave API

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

##<a name="sdk-integration"></a>Integração do SDK

### <a name="managing-device-registrations"></a>Gerenciamento de registros de dispositivo

Cada dispositivo deve enviar um comando de registro aos servidores do Google, caso contrário, eles não podem ser acessados.

Um dispositivo também pode cancelar o registro de notificações de GCM (o dispositivo será cancelado automaticamente se o aplicativo é desinstalado).

Se você não usa o [SDK do Google Play] ou se você não já envie a intenção de registro por conta própria, você pode fazer com que o contrato registrar o dispositivo automaticamente para você.

Para permitir isso, adicione o seguinte ao seu `AndroidManifest.xml` arquivo, dentro do `<application/>` marca:

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Comunicar identificação de registro para o serviço de contrato Push e receber notificações

Para comunicar a identificação de registro do dispositivo para o serviço de contrato Push e receber notificações, adicione o seguinte ao seu `AndroidManifest.xml` arquivo, dentro do `<application/>` marca (mesmo se você mesmo gerencia registros de dispositivo):

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

Verifique se você tem as seguintes permissões seu `AndroidManifest.xml` (após o `</application>` marca).

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##<a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Conceder acesso de contrato móvel a sua chave de API GCM

Seguir [Este guia](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) para conceder acesso de contrato Mobile a sua chave de API do GCM.

[Google Play SDK]:https://developers.google.com/cloud-messaging/android/start
