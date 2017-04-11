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

#<a name="how-to-integrate-engagement-reach-on-android"></a>Como integrar alcance de contrato no Android

> [AZURE.IMPORTANT] Você deve seguir o procedimento de integração descrito na como integrar o contrato no Android documento antes de seguir este guia.

##<a name="standard-integration"></a>Integração com o padrão

O SDK atinja requer a **biblioteca de suporte Android (v4)**.

A maneira mais rápida de adicionar a biblioteca ao seu projeto no **Eclipse** é `Right click on your project -> Android Tools -> Add Support Library...`.

Se você não usa Eclipse, você pode ler as instruções [aqui].

Copie arquivos de recursos de alcance do SDK do seu projeto:

-   Copie os arquivos da `res/layout` pasta entregue com o SDK para o `res/layout` pasta do seu aplicativo.
-   Copie os arquivos da `res/drawable` pasta entregue com o SDK para o `res/drawable` pasta do seu aplicativo.

Editar seu `AndroidManifest.xml` arquivo:

-   Adicionar a seguinte seção (entre os `<application>` e `</application>` marcas):

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
            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
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

-   Você precisa essa permissão para reproduzir notificações do sistema que não foram clicadas na inicialização (caso contrário, eles serão mantidos em disco, mas não será mais exibidos, você realmente precisa incluir isso).

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

-   Especificar um ícone usado para notificações (ambos do aplicativo e do sistema aquelas) copiando e editando a seguinte seção (entre os `<application>` e `</application>` marcas):

            <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [AZURE.IMPORTANT] Esta seção é **obrigatório** se você planeja usar notificações do sistema ao criar campanhas de alcance. Android impede que as notificações do sistema sem ícones sendo mostrado. Portanto se você omitir nesta seção, os usuários finais não poderão recebê-los.

-   Se você criar campanhas com notificações do sistema usando o panorama, você precisa adicionar as seguintes permissões (após o `</application>` marca) se faltando:

            <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
            <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

  -   No Android M e se o seu aplicativo direciona o nível de API Android 23 ou maior, ``WRITE_EXTERNAL_STORAGE`` permissão requer a aprovação do usuário. Leia [Esta seção](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

-   Notificações do sistema que você pode também especificar na campanha alcance se o dispositivo deve ligar para e/ou vibração. Para isso funcione, você deve certificar-se de que você declarado a permissão a seguir (após o `</application>` marca):

            <uses-permission android:name="android.permission.VIBRATE" />

    Sem essa permissão, Android impede que as notificações do sistema sejam mostradas se que você marcou anel ou a opção vibrate no Gerenciador de campanha alcançar.

-   Se você cria seu aplicativo usando **ProGuard** e tiver erros relacionados até a biblioteca de suporte Android ou jar contrato, adicione as seguintes linhas ao seu `proguard.cfg` arquivo:

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

## <a name="native-push"></a>Envio nativo

Agora que você configurou alcance módulo, você precisa configurar push nativo sejam capazes de receber as campanhas no dispositivo.

Oferecemos suporte dois serviços no Android:

  - Dispositivos do Google Play: uso de [Mensagens de nuvem do Google] seguindo o guia de [como integrar GCM com guia de contrato](mobile-engagement-android-gcm-integrate.md) .
  - Dispositivos Amazon: uso de [Mensagens de dispositivo Amazon] seguindo o guia de [como integrar ADM com guia de contrato](mobile-engagement-android-adm-integrate.md) .

Se você quiser Amazon e o Google Play dispositivos, suas possíveis ter tudo dentro de 1 AndroidManifest.xml/APK para desenvolvimento de destino. Mas ao enviar para Amazon, eles podem rejeitar seu aplicativo caso encontrem código GCM.

Você deve usar vários APKs nesse caso.

**Seu aplicativo agora está pronto para receber e Exibir campanhas de alcance!**

##<a name="how-to-handle-data-push"></a>Como lidar com o envio de dados

### <a name="integration"></a>Integração

Se quiser que o aplicativo seja capaz de receber alcance coloca de dados, você precisa criar uma classe sub de `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` e fazer referência a ela na `AndroidManifest.xml` arquivo (entre os `<application>` e/ou `</application>` marcas):

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

Em seguida, você pode substituir o `onDataPushStringReceived` e `onDataPushBase64Received` retornos de chamada. Aqui está um exemplo:

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Categoria

O parâmetro de categoria é opcional quando você criar uma campanha de envio de dados e permite que você para filtrar dados envia. Isso é útil se você tiver vários receptores difusão manipulação de diferentes tipos de dados coloca, ou se você quiser push tipos diferentes de `Base64` dados e deseja identificar seu tipo antes da análise-los.

### <a name="callbacks-return-parameter"></a>Parâmetro de retorno dos retornos de chamada

Aqui estão algumas diretrizes trate corretamente o parâmetro de retorno `onDataPushStringReceived` e `onDataPushBase64Received`:

-   Um receptor de difusão deve retornar `null` no retorno de chamada se ele não sabe como lidar com um envio de dados. Você deve usar a categoria para determinar se o seu receptor de difusão deverá manipular o envio de dados ou não.
-   Um destes receptor difusão deve retornar `true` no retorno de chamada se ele aceitar o envio de dados.
-   Um destes receptor difusão deve retornar `false` no retorno de chamada se ele reconhece o envio de dados, mas descarta por qualquer motivo. Por exemplo, retornar `false` quando os dados recebidos são inválidos.
-   Se uma difusão receptor retorna `true` enquanto outro um retorna `false` para o envio de dados mesmo, o comportamento é indefinido, você nunca deve fazer isso.

O tipo de retorno é usado apenas para as estatísticas de alcance:

-   `Replied`é incrementado se um dos receptores difusão retornado seja `true` ou `false`.
-   `Actioned`é incrementado somente se um dos receptores difusão retornado `true`.

##<a name="how-to-customize-campaigns"></a>Como personalizar campanhas

Para personalizar campanhas, você pode modificar layouts fornecidos no SDK alcançar.

Você deve manter todos os identificadores utilizados nos layouts e manter os tipos de modos de exibição que usa um identificador, especialmente para modos de exibição de texto e imagem. Alguns modos de exibição são usados apenas para ocultar ou mostrar áreas para que seus tipo pode ser alterado. Verifique o código-fonte se você pretende alterar o tipo de um modo de exibição nos layouts fornecidos.

### <a name="notifications"></a>Notificações

Há dois tipos de notificações: notificações do sistema e do aplicativo que usam arquivos de layout diferente.

#### <a name="system-notifications"></a>Notificações do sistema

Para personalizar notificações do sistema, você precisará usar as **categorias**. Você pode saltar para [as categorias](#categories).

#### <a name="in-app-notifications"></a>Notificações do aplicativo

Por padrão, uma notificação do aplicativo é um modo de exibição que é adicionado dinamicamente para a interface de usuário de atividade atual graças ao método Android `addContentView()`. Isso se chama uma sobreposição de notificação. As sobreposições de notificação são ótimas para uma integração rápida porque eles não exigem modificar qualquer layout em seu aplicativo.

Para modificar a aparência do seu sobreposições de notificação, você pode simplesmente modificar o arquivo `engagement_notification_area.xml` às suas necessidades.

> [AZURE.NOTE] O arquivo `engagement_notification_overlay.xml` é aquele que é usado para criar uma sobreposição de notificação, ele inclui o arquivo `engagement_notification_area.xml`. Você também pode personalizar para atender às suas necessidades (como posicionamento na área de notificação dentro de sobreposição).

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>Incluir o layout de notificação como parte de um layout de atividade

As sobreposições são ótimas para uma integração rápida, mas podem ser inconveniente ou têm efeitos de lado em casos especiais. O sistema de sobreposição pode ser personalizado em um nível de atividade, tornando mais fácil para impedir que os efeitos de lado para atividades especiais.

Você pode optar por incluir nosso layout de notificação no seu layout existente graças à instrução Android **incluir** . A seguir é um exemplo de um modificado `ListActivity` layout que contém apenas uma `ListView`.

**Antes de integração de contrato:**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Após a integração de contrato:**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

Neste exemplo adicionamos um contêiner pai, pois o layout original usado um modo de exibição de lista como o elemento de nível superior. Adicionamos também `android:layout_weight="1"` sejam capazes de adicionar um modo de exibição abaixo de um modo de exibição de lista configurado com `android:layout_height="fill_parent"`.

O SDK do contrato atinja detecta automaticamente que o layout de notificação é incluído nesta atividade e não adicionará uma sobreposição para esta atividade.

> [AZURE.TIP] Se você usar um ListActivity em seu aplicativo, uma sobreposição de alcance visível impedirá que você reagir ao clicado itens na exibição de lista mais. Este é um problema conhecido. Para contornar esse problema sugerimos que você para inserir o layout de notificação em seu próprio layout de atividade de lista como no exemplo anterior.

##### <a name="disabling-application-notification-per-activity"></a>Desativando a notificação de aplicativo por atividade

Se você não quiser a sobreposição a ser adicionado à sua atividade e se você não incluir o layout de notificação no seu próprio layout, você pode desabilitar a sobreposição para esta atividade na `AndroidManifest.xml` adicionando um `meta-data` seção, como no exemplo a seguir:

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a>Categorias

Quando você modifica os layouts fornecidos, você pode modificar a aparência de todas as suas notificações. Categorias permitem definir várias aparências de destino (possivelmente comportamentos) para notificações. Uma categoria pode ser especificada quando você cria uma campanha de alcance. Tenha em mente que categorias também permitem que você personalizar anúncios e pesquisas, que é descrito mais adiante neste documento.

Para registrar um manipulador de categoria para suas notificações, você precisa adicionar uma chamada quando o aplicativo for inicializado.

> [AZURE.IMPORTANT] Leia o aviso sobre o atributo android: processo \<android-sdk-contrato-process\> na como integrar o contrato no Android tópico antes de continuar.

O exemplo a seguir supõe que você confirmados aviso anterior e usa uma classe sub de `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

O `MyNotifier` objeto é a implementação do manipulador de categoria de notificação. É um de uma implementação do `EngagementNotifier` interface ou classe sub da implementação padrão: `EngagementDefaultNotifier`.

Observe que a notificação mesma pode lidar com várias categorias, você pode registrá-los como esta:

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Para substituir a implementação de categoria padrão, você pode registrar sua implementação como no exemplo a seguir:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

A categoria atual usada em um manipulador é passada como um parâmetro na maioria dos métodos que você pode substituir na `EngagementDefaultNotifier`.

Ele é passado como um `String` parâmetro ou indiretamente em um `EngagementReachContent` objeto que tem uma `getCategory()` método.

Você pode alterar a maioria do processo de criação de notificação por redefinição métodos em `EngagementDefaultNotifier`, para personalização mais avançada fique à vontade para dar uma olhada na documentação técnica e em código-fonte.

##### <a name="in-app-notifications"></a>Notificações do aplicativo

Se você quiser apenas usar layouts alternativos para uma categoria específica, você pode implementar isso como no exemplo seguinte:

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**Exemplo de `my_notification_overlay.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

Como você pode ver, o identificador de modo de exibição de sobreposição é diferente do padrão. É importante que cada layout usa um identificador exclusivo para sobreposições.

**Exemplo de `my_notification_area.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

Como você pode ver, o identificador de modo de exibição da área de notificação é diferente do padrão. É importante que cada layout usa um identificador exclusivo para as áreas de notificação.

Esse exemplo simples de categoria torna as notificações de aplicativo (ou do aplicativo) exibidas na parte superior da tela. Não alteramos os identificadores padrão usados na área de notificação em si.

Se desejar alterar isso, você precisa redefinir o `EngagementDefaultNotifier.prepareInAppArea` método. Recomenda-se para examinar a documentação técnica e código-fonte do `EngagementNotifier` e `EngagementDefaultNotifier` se desejar esse nível de personalização avançada.

##### <a name="system-notifications"></a>Notificações do sistema

Estendendo `EngagementDefaultNotifier`, você pode substituir `onNotificationPrepared` para alterar a notificação de que foi preparada pela implementação padrão.

Por exemplo:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

Este exemplo torna uma notificação do sistema para um conteúdo que está sendo exibido como um evento em andamento quando a categoria "em andamento" é usada.

Se você quiser criar o `Notification` objeto do zero, você pode retornar `false` no método e chamada `notify` por conta própria na `NotificationManager`. Nesse caso ele é importante que você mantenha uma `contentIntent`, um `deleteIntent` e o identificador de notificação usado pelo `EngagementReachReceiver`.

Aqui está um exemplo correto de tal implementação:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>Anúncios somente de notificação

O gerenciamento do clique em uma notificação comunicado só pode ser personalizado, substituindo `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` para modificar a preparado `Intent`. Usando este método permite que você ajustar os sinalizadores facilmente.

Por exemplo, para adicionar o `SINGLE_TOP` sinalizador:

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

Para usuários de contrato herdados, observe que as notificações do sistema sem ação URL agora inicia o aplicativo se tiver sido no plano de fundo, para que esse método pode ser chamado com um comunicado sem a URL da ação. Você deve considerar que ao personalizar a intenção.

Você também pode implementar `EngagementNotifier.executeNotifAnnouncementAction` do zero.

##### <a name="notification-life-cycle"></a>Ciclo de vida de notificação

Ao usar a categoria padrão, alguns métodos de ciclo de vida são chamados na `EngagementReachInteractiveContent` objeto para relatar estatísticas e atualizar o estado de campanha:

-   Quando a notificação é exibida no aplicativo ou colocar na barra de status, o `displayNotification` método é chamado (que relata estatísticas) por `EngagementReachAgent` se `handleNotification` retorna `true`.
-   Se a notificação é descartada, o `exitNotification` método é chamado, estatística é informada e campanhas próxima agora podem ser processadas.
-   Se a notificação é clicada, `actionNotification` é chamado, estatística é informada e a intenção associada é iniciada.

Se sua implementação de `EngagementNotifier` ignora o comportamento padrão, você precisa chamar esses métodos de ciclo de vida sozinho. Os exemplos a seguir ilustram alguns casos onde o comportamento padrão é ignorado:

-   Você não estender `EngagementDefaultNotifier`, por exemplo, você implementado manipulação de categoria do zero.
-   Para notificações do sistema, você substitui o `onNotificationPrepared` e modificada `contentIntent` ou `deleteIntent` no `Notification` objeto.
-   Para notificações do aplicativo, você substitui `prepareInAppArea`, certifique-se de mapear pelo menos `actionNotification` para um dos seus U.I controles.

> [AZURE.NOTE] Se `handleNotification` gera uma exceção, o conteúdo é excluída e `dropContent` é chamado. Isso é relatado em estatísticas e campanhas próxima agora podem ser processadas.

### <a name="announcements-and-polls"></a>Anúncios e votações

#### <a name="layouts"></a>Layouts

Você pode modificar a `engagement_text_announcement.xml`, `engagement_web_announcement.xml` e `engagement_poll.xml` arquivos para personalizar anúncios de texto, anúncios de web e votações.

Esses arquivos compartilham dois layouts comuns para a área de título e a área do botão. O layout para o título é `engagement_content_title.xml` e usa o arquivo drawable eponymous para o plano de fundo. O layout para os botões de ação e saída é `engagement_button_bar.xml` e usa o arquivo drawable eponymous para o plano de fundo.

Em uma sondagem, o layout de pergunta e suas escolhas são dinamicamente infladas usando várias vezes o `engagement_question.xml` arquivo de layout para as perguntas e a `engagement_choice.xml` arquivo para as escolhas.

#### <a name="categories"></a>Categorias

##### <a name="alternate-layouts"></a>Layouts alternativos

Como as notificações, categoria de campanha pode ser usada para possuem layouts alternativos para seus anúncios e votações.

Por exemplo, para criar uma categoria para um anúncio de texto, você pode estender `EngagementTextAnnouncementActivity` e fazer referência a ela o `AndroidManifest.xml` arquivo:

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

Observe que a categoria no filtro intenção é usada para fazer a diferença com a atividade de lançamento padrão.

O SDK atinja usa o sistema intenção para resolver a atividade de direita de uma categoria específica e ele volta na categoria padrão se a resolução falhou.

Em seguida, você precisa implementar `MyCustomTextAnnouncementActivity`, se você quiser apenas alterar o layout (mas manter os identificadores de exibição mesmo), você só precisa definir a classe como no exemplo a seguir:

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

Para substituir a categoria padrão de anúncios de texto, simplesmente substituir `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` pela sua implementação.

Sondagens e anúncios de web podem ser personalizadas de maneira semelhante.

Para anúncios de web, você pode estender `EngagementWebAnnouncementActivity` e declarar suas atividades a `AndroidManifest.xml` , como no exemplo a seguir:

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

Para votações você pode estender `EngagementPollActivity` e declarar sua na `AndroidManifest.xml` , como no exemplo a seguir:

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>Implementação do zero

Você pode implementar categorias para suas atividades de anúncio (e votação) sem estender um da `Engagement*Activity` classes fornecidas pelo SDK alcançar. Isso é útil por exemplo se você deseja definir um layout que não use o mesmo visualizações como os layouts padrão.

Como para personalização de notificação avançados, é recomendável examinar o código-fonte da implementação do padrão.

Eis algumas coisas a ter em mente: alcance iniciará a atividade com um propósito específico (correspondente ao filtro de intenção) plus um parâmetro extra que é o identificador de conteúdo.

Para recuperar o objeto de conteúdo que contêm os campos que você especificou ao criar a campanha no site da web, você pode fazer isso:

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

Para estatísticas, você deve reportar o conteúdo é exibido no `onResume` evento:

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

Em seguida, não se esqueça de chamar tanto `actionContent(this)` ou `exitContent(this)` no objeto de conteúdo antes da atividade avança no plano de fundo.

Se você não chamar tanto `actionContent` ou `exitContent`, estatísticas não serão enviadas (ou seja, sem análise na campanha) e mais importante, as próxima campanhas não serão notificadas até que o processo de aplicativo seja reiniciado.

Orientação ou outras alterações de configuração podem tornar o código complicado determinar se a atividade avança no plano de fundo ou não, a implementação padrão garante que o conteúdo é relatado como saído se o usuário deixar a atividade (seja pressionando `HOME` ou `BACK`), mas não se altera a orientação.

Aqui está a parte interessante da implementação:

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

Como você pode ver, se você chamou `actionContent(this)` depois de terminar a atividade, `exitContent(this)` pode ser chamado com segurança sem ter qualquer efeito.

[aqui]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Nuvem do Google de mensagens]:http://developer.android.com/guide/google/gcm/index.html
[Mensagens de dispositivo Amazon]:https://developer.amazon.com/sdk/adm.html
