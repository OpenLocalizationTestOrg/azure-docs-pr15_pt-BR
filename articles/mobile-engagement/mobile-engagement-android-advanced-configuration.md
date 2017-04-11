<properties
    pageTitle="Configuração avançada de SDK do Azure Mobile contrato Android"
    description="Descreve as opções de configuração avançada, incluindo o manifesto Android com o SDK do Azure Mobile contrato Android"
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
    ms.date="10/04/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Configuração avançada de SDK do Azure Mobile contrato Android

> [AZURE.SELECTOR]
- [Windows universal](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-configuration.md)

Este procedimento descreve como configurar diversas opções de configuração para os aplicativos do Azure Mobile contrato Android.

## <a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>Requisitos de permissão
Algumas opções exigem permissões específicas, todas elas são listadas aqui para referência e em linha no recurso específico. Adicionar essas permissões para a AndroidManifest.xml do seu projeto imediatamente antes ou depois do `<application>` marca.

O código de permissão deve parecer com o seguinte, onde você preenche a permissão apropriada da tabela a seguir.

    <uses-permission android:name="android.permission.[specific permission]"/>


| Permissão | Quando usada |
| ---------- | --------- |
| INTERNET | Necessário. Para relatórios básicos |
| ACCESS_NETWORK_STATE | Necessário. Para relatórios básicos |
| RECEIVE_BOOT_COMPLETED | Necessário. Para mostrar o Centro de notificações após a reinicialização do dispositivo |
| WAKE_LOCK | Recomendado. Habilita a coleta de dados ao usar WiFi ou tela está desativada |
| VIBRAÇÃO | Opcional. Habilita vibração quando forem recebidas notificações |
| DOWNLOAD_WITHOUT_NOTIFICATION | Opcional. Habilita a notificação de panorama Android |
| WRITE_EXTERNAL_STORAGE | Opcional. Habilita a notificação de panorama Android |
| ACCESS_COARSE_LOCATION | Opcional. Habilita o relatório de local em tempo real |
| ACCESS_FINE_LOCATION | Opcional. Habilita o relatório de local baseado em GPS |

Começando com Android M, [algumas permissões são gerenciados em tempo de execução](mobile-engagement-android-location-reporting.md#Android-M-Permissions).

Se você já estiver usando ``ACCESS_FINE_LOCATION``, então você não precisa usar também ``ACCESS_COARSE_LOCATION``.

## <a name="android-manifest-configuration-options"></a>Opções de configuração de manifesto Android

### <a name="crash-report"></a>Relatório de falha

Para desativar relatórios de falha, adicione este código entre o `<application>` e `</application>` marcas:

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Limite de intermitente

Por padrão, os relatórios de serviço do contrato logs em tempo real. Se os logs de relatório do aplicativo variam com frequência, é melhor para os logs de buffer e relatá-los ao mesmo tempo em uma base de tempo regular (chamado de "intermitente modo"). Para fazer isso, adicione este código entre o `<application>` e `</application>` marcas:

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

O modo intermitente ligeiramente aumenta a vida de bateria mas tem impacto sobre o Monitor de contrato: todas as sessões e trabalhos a duração são arredondados para o limite de intermitente (portanto, sessões e trabalhos menores do que o limite de intermitente pode não estar visível). O limite de intermitente deve ser não mais do que 30000 (30s).

### <a name="session-timeout"></a>Tempo limite de sessão

 Você pode encerrar uma atividade pressionando a tecla **Home** ou para **trás** , definindo o telefone ocioso ou comendo em outro aplicativo. Por padrão, uma sessão é encerrada dez segundos após o final da sua última atividade. Isso evita uma divisão de sessão sempre que o usuário sair e retorna para o aplicativo rapidamente, que pode acontecer quando o usuário seleciona uma imagem, verifica uma notificação, etc. Talvez você queira modificar esse parâmetro. Para fazer isso, adicione este código entre o `<application>` e `</application>` marcas:

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Desabilitar o relatório de log

### <a name="using-a-method-call"></a>Usando uma chamada de método

Se você quiser contrato para interromper o envio de logs, você pode chamar:

    EngagementAgent.getInstance(context).setEnabled(false);

Esta chamada é persistente: ele usa um arquivo compartilhado preferências.

Se o contrato estiver ativo quando você chamar essa função, pode levar um minuto para o serviço parar. No entanto, ele não iniciar o serviço em todos os na próxima vez que você inicia o aplicativo.

Você pode habilitar o log de relatório novamente chamando a mesma função com `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integração no seu próprio`PreferenceActivity`

Em vez de chamar essa função, você pode também integrar esta configuração diretamente no seu existentes `PreferenceActivity`.

Você pode configurar contrato para usar o arquivo de preferências (com o modo desejado) a `AndroidManifest.xml` arquivo com `application meta-data`:

-   O `engagement:agent:settings:name` chave é usada para definir o nome do arquivo compartilhado preferências.
-   O `engagement:agent:settings:mode` chave é usada para definir o modo do arquivo compartilhado preferências. Usar o mesmo modo como em sua `PreferenceActivity`. O modo deve ser passado como um número: se você estiver usando uma combinação de sinalizadores constantes em seu código, verifique o valor total.

Contrato sempre usa o `engagement:key` chave booliana dentro do arquivo de preferências para gerenciar essa configuração.

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
