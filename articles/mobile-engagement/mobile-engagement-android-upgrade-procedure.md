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


#<a name="upgrade-procedures"></a>Procedimentos de atualização

Se você já tiver integrado uma versão mais antiga do nosso SDK em seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Você pode precisar acompanhar vários procedimentos se você perdeu várias versões do SDK. Por exemplo, se você migrar do 1.4.0 para 1.6.0 que é necessário primeiro, siga o procedimento "de 1.4.0 para 1.5.0", em seguida, o procedimento "de 1.5.0 para 1.6.0".

Qualquer versão você atualiza a partir do, você precisa substituir o `mobile-engagement-VERSION.jar` com o novo nome.

##<a name="from-420-to-421"></a>De 4.2.0 para 4.2.1

Nesta etapa, na verdade, pode ser feita em qualquer versão do SDK, é uma melhoria de segurança quando você integra alcance atividades.

Agora você deve adicionar `exported="false"` a todas as atividades de alcance.

Atividades de alcance agora devem ter esta aparência seu `AndroidManifest.xml`:

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

##<a name="from-400-to-410"></a>De 4.0.0 à versão 4.1.0

O SDK agora alça nova permissão modelo do Android M.

Se você usar os recursos de localização ou notificações de panorama, leia [Esta seção](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

Além do novo modelo de permissão, agora oferecemos suporte Configurando recursos de localização em tempo de execução.
Estamos ainda compatíveis com os parâmetros de manifesto para local, mas ele agora está obsoleta. Para usar a configuração de tempo de execução, remova as seguintes seções de seu ``AndroidManifest.xml``:

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

e leia [este procedimento atualizado](mobile-engagement-android-integrate-engagement.md#location-reporting) para usar a configuração de tempo de execução em vez disso.

##<a name="from-300-to-400"></a>De 3.0.0 para 4.0.0

### <a name="native-push"></a>Envio nativo

Envio nativo (GCM/ADM) agora também é usado para em notificações do aplicativo para que você deve configurar as credenciais de envio nativo para qualquer tipo de campanha de envio.

Se ainda não siga [este procedimento](mobile-engagement-android-integrate-engagement-reach.md#native-push).

### <a name="androidmanifestxml"></a>AndroidManifest.xml

Integração de alcance foi modificada em ``AndroidManifest.xml``.

Substitua isso:

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

Por

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

Há possivelmente uma tela de carregamento agora quando você clica em um comunicado (com texto/conteúdo da web) ou uma votação.
Você precisa adicionar isto para essas campanhas trabalhar no 4.0.0:

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>Recursos

Inserir o novo `res/layout/engagement_loading.xml` arquivo para seu projeto.

##<a name="from-240-to-300"></a>De 2.4.0 a 3.0.0

A seguir descreve como migrar uma integração SDK do serviço Capptain oferecido pelo Capptain SAS para um aplicativo do Azure Mobile contrato da plataforma. Se você estiver migrando de uma versão anterior, consulte o site Capptain para migrar para 2.4.0 primeiro e aplique o procedimento a seguir.

>[AZURE.IMPORTANT] Capptain e Mobile contrato não são os mesmos serviços, e o procedimento abaixo realça apenas como migrar o aplicativo cliente. Migrar o SDK no aplicativo será não migrar seus dados de servidores Capptain aos servidores do contrato de celular.

### <a name="jar-file"></a>Arquivo JAR

Substituir `capptain.jar` por `mobile-engagement-VERSION.jar` no seu `libs` pasta.

### <a name="resource-files"></a>Arquivos de recurso

Cada arquivo de recurso que fornecemos (precedidos por `capptain_`) deve ser substituído aos novos (com o prefixo `engagement_`).

Se você personalizou esses arquivos, você precisa aplicar sua personalização nos novos arquivos, **todos os identificadores nos arquivos de recurso também foram renomeados**novamente.

### <a name="application-id"></a>ID do aplicativo

Agora o contrato usa uma cadeia de conexão para configurar os identificadores SDK como o identificador de aplicativo.

Você precisa usar `EngagementAgent.init` método em sua atividade de iniciador assim:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

A cadeia de conexão para o seu aplicativo é exibida no Portal do Azure.

Remova qualquer chamada para `CapptainAgent.configure` como `EngagementAgent.init` substitui esse método.

O `appId` já não podem ser configuradas usando `AndroidManifest.xml`.

Remova essa seção do seu `AndroidManifest.xml` se você tivê-la:

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>API Java

Todas as chamadas para qualquer classe Java do nosso SDK tem a ser renomeada; Por exemplo, `CapptainAgent.getInstance(this)` devem ser renomeados `EngagementAgent.getInstance(this)`, `extends CapptainActivity` devem ser renomeados `extends EngagementActivity` etc...

Se você estava integrado com arquivos de preferência de agente do padrão, o nome de arquivo padrão agora é `engagement.agent` e a chave é `engagement:agent`.

Ao criar anúncios de web, o fichário Javascript agora é `engagementReachContent`.

### <a name="androidmanifestxml"></a>AndroidManifest.xml

Muitas alterações aconteceu lá, o serviço não é mais compartilhado e muitas receptores não são exportáveis mais.

A declaração de serviço agora é mais simples; Remova o filtro intenção e todos os metadados dentro dele e adicione `exportable=false`.

Além disso, tudo é renomeado para usar o contrato.

Agora, ele se parece com:

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

Quando você quiser ativar logs de teste, os metadados agora foi movido para a marca de aplicativo e foi renomeado:

            <application>
            
              <meta-data android:name="engagement:log:test" android:value="true" />
            
              <service/>
            
            </application>

Todos os outros metadados apenas foram renomeados, aqui está a lista completa (de renomear curso somente aquelas que você usa):

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>
            
            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

Google Play e SmartAd rastreamento foi removido do SDK apenas tiver removê-la sem substituição:

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

As atividades de alcance agora são declaradas como esta:

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>
            
Se você tiver atividades personalizadas de alcance, você só precisa alterar as ações intenção para corresponder tanto `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` ou `com.microsoft.azure.engagement.reach.intent.action.POLL`.

Os receptores transmissão foram renomeados, além de agora adicionamos `exported=false`. Aqui está a lista completa dos receptores com a especificação de novo, (de renomear curso somente aquelas que você usa):

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>
            
            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

Receptor de rastreamento foi removido, para que você precise remover esta seção:

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

Observe que a declaração da implementação do receptor difusão **EngagementMessageReceiver** mudou na `AndroidManifest.xml`. Isso ocorre porque a API para enviar e remover mensagens XMPP aleatório de entidades XMPP aleatório e a API para enviar e receber mensagens entre dispositivos foram removidos. Portanto, você deve também excluir os retornos de chamada de procedimentos de sua implementação de **EngagementMessageReceiver** :

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

e

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

Exclua qualquer chamada em **EngagementAgent** para:

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

e

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>ProGuard

Configuração ProGuard pode ser afetada por rebranding, as regras agora procuram como:

            -dontwarn android.**
            -keep class android.support.v4.** { *; }
            
            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }
 
