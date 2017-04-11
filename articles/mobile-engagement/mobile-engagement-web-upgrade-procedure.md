<properties
    pageTitle="Procedimentos de atualização de Mobile contrato Web SDK Azure | Microsoft Azure"
    description="As últimas atualizações e procedimentos para o SDK do Web para contrato de celular do Azure"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="piyushjo" />


# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Procedimentos de atualização de Mobile contrato Web SDK Azure

Se você já tiver integrado uma versão anterior do Azure Mobile contrato Web SDK em seu aplicativo web, você precisa considerar os seguintes pontos quando você atualiza o SDK.

Se você tiver ignorado várias versões do SDK Mobile contrato da Web, talvez seja necessário concluir vários procedimentos durante o processo de atualização. Por exemplo, se você migrar do 1.4.0 para 1.6.0, primeiro siga os procedimentos para atualizar 1.4.0 para 1.5.0. Em seguida, siga os procedimentos para atualizar 1.5.0 para 1.6.0.

Qualquer versão que você atualiza a partir do, substituir qualquer versão anterior do azure-engagement.js arquivo com a versão mais recente do arquivo.

## <a name="upgrade-from-121-to-200"></a>Atualizar do 1.2.1 para 2.0.0

Esta seção descreve como migrar uma integração Web SDK do Mobile contrato de serviço Capptain, oferecido pelo Capptain SAS, um aplicativo do Azure Mobile contrato. Se você estiver migrando de uma versão anterior, consulte o site Capptain para migrar primeiro para 1.2.1 e aplique os procedimentos a seguir.

Esta versão do SDK Mobile contrato Web não dá suporte a Samsung Smart TV, Opera TV, webOS ou o recurso de alcance.

>[AZURE.IMPORTANT] Capptain e Azure Mobile contrato não são o mesmo serviço. O procedimento a seguir destaca apenas como migrar o aplicativo cliente. Migrar o SDK do Mobile contrato da Web no aplicativo será não migrar seus dados de um servidor de Capptain em um servidor de contrato de celular.

### <a name="javascript-files"></a>Arquivos JavaScript

Substitua o arquivo capptain-sdk.js o arquivo azure-engagement.js e atualize seu importações de script adequadamente.

### <a name="remove-capptain-reach"></a>Remover Capptain alcance

Esta versão do SDK Mobile contrato da Web não suporta o recurso de alcance. Se você integrado Capptain alcance ao seu aplicativo, você precisa removê-lo.

Remover a importação de CSS atinja da sua página e exclua o arquivo. CSS relacionados (capptain-reach.css, por padrão).

Excluir os seguintes recursos de alcance: a imagem fechar (capptain-close.png, por padrão) e o ícone de marca (capptain--ícone de notificação, por padrão).

Remova a interface do usuário alcançar para notificações do aplicativo. O layout padrão tem esta aparência:

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

Remova a interface do usuário alcançar para texto e anúncios da web e votações. O layout padrão tem esta aparência:

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

Remover o `reach` objeto da sua configuração, se ele existir. Ele fica assim:

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

Remova qualquer outra personalização de alcance, como categorias.

### <a name="remove-deprecated-apis"></a>Remover APIs obsoletas

Algumas APIs do Capptain são preteridos no SDK Mobile contrato da Web.

Remova quaisquer chamadas para as seguintes APIs: `agent.connect`, `agent.disconnect`, `agent.pause`, e `agent.sendMessageToDevice`.

Remover todas as instâncias dos retornos de chamada de procedimentos de sua configuração de Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, e `onPushMessageReceived`.

### <a name="configuration"></a>Configuração

Contrato de celular usa uma cadeia de conexão para configurar identificadores SDK, por exemplo, o identificador de aplicativo.

Substitua o ID de aplicativo com sua cadeia de conexão. Observe que o objeto global para a configuração de SDK muda de `capptain` para `azureEngagement`.

Antes da migração:

    window.capptain = {
      appId: ...,
      [...]
    };

Após a migração:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

A cadeia de conexão para o seu aplicativo é exibida no Portal do Azure.

### <a name="javascript-apis"></a>APIs JavaScript

O objeto JavaScript global `window.capptain` foi renomeado `window.azureEngagement` , mas você pode usar o `window.engagement` alias para chamadas de API. Você não pode usar o alias para definir a configuração de SDK.

Por exemplo, `capptain.deviceId` fica `engagement.deviceId`, `capptain.agent.startActivity` fica `engagement.agent.startActivity`e assim por diante.
