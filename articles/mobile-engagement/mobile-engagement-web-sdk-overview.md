<properties
    pageTitle="Visão geral do Azure contrato móvel Web SDK | Microsoft Azure"
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
    ms.date="10/18/2016"
    ms.author="piyushjo" />


# <a name="azure-mobile-engagement-web-sdk"></a>Contrato de móvel Azure Web SDK

Inicie aqui para todos os detalhes sobre como integrar o contrato do Azure Mobile em um aplicativo web. Se você quiser experimentar antes de começar com seu próprio aplicativo web, consulte nosso [tutorial de 15 minutos](mobile-engagement-web-app-get-started.md).

## <a name="integration-procedures"></a>Procedimentos de integração
1. Aprenda [a integrar o contrato de Mobile em seu aplicativo web](mobile-engagement-web-integrate-engagement.md).

2. Para implementação do plano de marca, Aprenda [a usar o contrato de celular avançado marcação API em seu aplicativo web](mobile-engagement-web-use-engagement-api.md).

## <a name="release-notes"></a>Notas de versão

### <a name="202-10182016"></a>2.0.2 (18/10/2016)

-   Falha fixa na navegação privada (Safari).
-   Falha fixa em navegadores com cookies desabilitados.

Para todas as versões, consulte as [notas de versão completa](mobile-engagement-web-release-notes.md).

## <a name="upgrade-procedures"></a>Procedimentos de atualização

### <a name="upgrade-from-121-to-200"></a>Atualizar do 1.2.1 para 2.0.0

As seções a seguir descrevem como migrar uma integração Web SDK do Mobile contrato de serviço Capptain, oferecido pelo Capptain SAS, um aplicativo do Azure Mobile contrato. Se você estiver migrando de uma versão anterior ao 1.2.1, consulte o site Capptain para migrar para 1.2.1 primeiro e aplique os procedimentos a seguir.

Esta versão do SDK Mobile contrato Web não dá suporte a Samsung Smart TV, Opera TV, webOS ou o recurso de alcance.

>[AZURE.IMPORTANT] Capptain e Azure Mobile contrato não o mesmo serviço e os procedimentos a seguir realçar somente como migrar o aplicativo cliente. Migrar o SDK do Mobile contrato da Web no aplicativo será não migrar seus dados de um servidor de Capptain em um servidor de contrato de celular.

#### <a name="javascript-files"></a>Arquivos JavaScript

Substitua o arquivo capptain-sdk.js o arquivo azure-engagement.js e atualize seu importações de script adequadamente.

#### <a name="remove-capptain-reach"></a>Remover Capptain alcance

Esta versão do SDK Mobile contrato da Web não suporta o recurso de alcance. Se você tiver integrado Capptain alcance em seu aplicativo, você precisa removê-lo.

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

#### <a name="remove-deprecated-apis"></a>Remover APIs obsoletas

Algumas APIs do Capptain são preteridos no SDK Mobile contrato da Web.

Remova quaisquer chamadas para as seguintes APIs: `agent.connect`, `agent.disconnect`, `agent.pause`, e `agent.sendMessageToDevice`.

Remover qualquer uma dos retornos de chamada de procedimentos da configuração do seu Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, e `onPushMessageReceived`.

#### <a name="configuration"></a>Configuração

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

A cadeia de conexão para o seu aplicativo é exibida no portal do Azure.

#### <a name="javascript-apis"></a>APIs JavaScript

O objeto JavaScript global `window.capptain` foi renomeado `window.azureEngagement`, mas você pode usar o `window.engagement` alias para chamadas de API. Você não pode usar este alias para definir a configuração de SDK.

Por exemplo, `capptain.deviceId` fica `engagement.deviceId`, `capptain.agent.startActivity` fica `engagement.agent.startActivity`e assim por diante.

Se você já tiver integrado uma versão anterior do Azure Mobile contrato Web SDK ao seu aplicativo, leia sobre como [atualizar procedimentos](mobile-engagement-web-upgrade-procedure.md).
