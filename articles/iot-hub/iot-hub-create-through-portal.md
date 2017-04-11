<properties
     pageTitle="Usar o portal do Azure para criar um IoT Hub | Microsoft Azure"
     description="Uma visão geral de como criar e gerenciar hubs IoT Azure por meio do portal do Azure"
     services="iot-hub"
     documentationCenter=""
     authors="dominicbetts"
     manager="timlt"
     editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/30/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-the-azure-portal"></a>Criar um hub de IoT usando o portal do Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]


## <a name="introduction"></a>Introdução

Este artigo descreve como encontrar o serviço de IoT Hub no portal do Azure e como criar e gerenciar hubs IoT.

## <a name="where-to-find-iot-hubs"></a>Onde encontrar IoT hubs

Há vários lugares onde você pode encontrar IoT hubs.

1. **+ Nova**: **Azure IoT Hub** é um serviço de IoT e podem ser encontradas na categoria **Internet das coisas**, em **+ nova**, semelhante a outros serviços.

2. Hubs IoT também podem ser acessadas por meio de mercado como o serviço de banner em **Internet das coisas**.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Você pode criar um hub de IoT usando os seguintes métodos:

- Criar um hub IoT através da opção **+ nova** leva lâmina mostrado na próxima captura de tela. As etapas para criar o hub de IoT através este método e o marketplace são idênticas.

- Criando um hub IoT através do Marketplace: clicando em **criar** abre uma lâmina é idêntica lâmina anterior para a experiência de **+ novo** . As seções a seguir listam as diversas etapas envolvidas na criação de um hub IoT.

### <a name="choose-the-name-of-the-iot-hub"></a>Escolha o nome do hub IoT

Para criar um hub IoT, nomeie o hub. Esse nome deve ser exclusivo entre os hubs. Sem duplicação dos hubs é permitida no back-end, portanto é recomendável que este hub é chamado exclusivamente possível.

### <a name="choose-the-pricing-tier"></a>Escolha o nível de preço

Você pode escolher entre quatro níveis: **livre**, **padrão de 1** e **2 padrão**e **S3 padrão**. A camada gratuita permite apenas 500 dispositivos conectados ao hub IoT e até 8.000 mensagens por dia.

**S1 padrão**: IoT Hubs S1 edition foi projetado para soluções de IoT que têm um grande número de dispositivos gerando relativamente pequenas quantidades de dados por dispositivo. Cada unidade da edição S1 permite até 400.000 mensagens por dia em todos os dispositivos conectados.

**S2 padrão**: IoT Hub S2 edition foi projetado para soluções IoT nas quais dispositivos geram grandes quantidades de dados. Cada unidade da edição S2 permite até 6 milhões de mensagens por dia entre todos os dispositivos conectados.

**S3 padrão**: IoT Hub S3 edition foi projetado para soluções IoT que geram grandes quantidades de dados. Cada unidade da edição S3 permite até 300 milhões de mensagens por dia entre todos os dispositivos conectados.

![][4]

> [AZURE.NOTE] Hub de IoT permite apenas um hub gratuito por assinatura Azure.

### <a name="iot-hub-units"></a>Unidades de hub IoT

Uma unidade de hub IoT inclui um determinado número de mensagens por dia. O número total de mensagens com suporte para este hub é o número de unidades multiplicado pelo número de mensagens por dia para essa camada. Por exemplo, se desejar que o hub de IoT para dar suporte a entrada de 700.000 mensagens, escolha duas unidades de nível de S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Dispositivo para partições de nuvem e grupo de recursos

Você pode alterar o número de partições para um hub IoT. Partições padrão são definidas como 4; No entanto, você pode escolher um número diferente de partições de uma lista suspensa.

Grupos de recursos, você não precisa criar explicitamente um grupo de recursos vazia. Ao criar um recurso, você pode optar por criar um novo grupo de recursos ou usar um grupo de recursos existente.

![][5]

### <a name="choose-subscriptions"></a>Escolha assinaturas

Hub de IoT Azure automaticamente mostra a lista de assinaturas Azure ao qual a conta de usuário está vinculada. Você pode escolher uma das opções aqui para associar o hub de IoT essa assinatura do Azure.

### <a name="choose-the-location"></a>Escolha o local

A opção local fornece uma lista das regiões na qual IoT Hub é oferecida. Hub de IoT está disponível para implantar nos seguintes locais: Austrália Oriental, Austrália Sudeste, Ásia Oriental, Sudeste da Ásia, Norte da Europa, Europa Ocidental, Japão Leste, oeste Japão conosco Leste, oeste EUA.

### <a name="create-the-iot-hub"></a>Criar o hub de IoT

Quando todas as etapas anteriores forem concluídas, o hub de IoT está pronto para ser criado. Clique em **criar** para iniciar o processo de back-end da criação este hub IoT com as opções específicas e implantá-lo para o local especificado.

Pode levar alguns minutos para que o hub de IoT seja criado como leva tempo para a implantação de back-end ocorrer nos servidores local apropriado.

## <a name="change-the-settings-of-the-iot-hub"></a>Alterar as configurações do hub IoT

Você pode alterar as configurações de um hub IoT existente após sua criação da lâmina IoT Hub.

![][8]

**Políticas de acesso compartilhado**: essas políticas definem as permissões para dispositivos e serviços para se conectar ao IoT Hub. Você pode acessar essas políticas clicando em **Políticas de acesso compartilhados** em **Geral**. Neste lâmina, você pode modificar diretivas existentes ou adicionar uma nova política.

### <a name="create-a-policy"></a>Criar uma política

- Clique em **Adicionar** para abrir um blade. Aqui você pode inserir o novo nome de política e as permissões que você deseja associar a esta política, conforme mostrado na figura a seguir.

    Há várias permissões que podem ser associadas essas políticas compartilhadas. As duas primeiras políticas, **registro de leitura** e **gravação de registro**, conceder ler e direitos de acesso de gravação para armazenamento de identidade do dispositivo ou o registro de identidade. Escolhendo a opção de gravação automaticamente escolhe a opção de leitura também.

    A política de **serviço se conectar** concede permissão para acessar os pontos de extremidade do lado do nuvem como o grupo de consumidor para serviços conectando ao hub IoT. A política de **dispositivo conectar** concede permissões para enviar e receber mensagens nos pontos de extremidade do lado do dispositivo do hub IoT.

- Clique em **criar** para adicionar esse recém-criado política à lista existente.

![][10]

## <a name="messaging"></a>Sistema de mensagens

Clique em **sistema de mensagens** para exibir uma lista das propriedades para o hub de IoT que está sendo modificado de mensagens. Há dois tipos principais de propriedades que você pode modificar ou copiar: **nuvem para o dispositivo** e o **dispositivo na nuvem**.

- Configurações de **nuvem para o dispositivo** : essa configuração tem dois subsettings: **nuvem dispositivo TTL** (time-to-live) e o **tempo de retenção** para as mensagens. Quando o hub de IoT é criado pela primeira vez, essas duas configurações são criadas com um valor padrão de uma hora. Para ajustar esses valores, use os controles deslizantes ou digite os valores.

- Configurações do **dispositivo nuvem** : essa configuração tem várias subsettings, alguns dos quais denominada/atribuídos quando o hub de IoT é criado e só pode ser copiado para outras subsettings personalizáveis. Essas configurações estão listadas na próxima seção.

**Partições**: esse valor é definido quando o hub de IoT é criado e pode ser alterado através dessa configuração.

**Ponto de extremidade e compatível com o evento Hub nome**: quando IoT o hub é criada, um Hub de evento é criado internamente que você pode precisar acessar em determinadas circunstâncias. Esse nome de compatível com o evento Hub e o ponto de extremidade não pode ser personalizada, mas está disponíveis para uso através do botão **Copiar** .

**Tempo de retenção**: definido como um dia por padrão, mas pode ser personalizado para outros valores usando a lista suspensa. Esse valor é em dias do dispositivo na nuvem e não em horas, como a configuração semelhante de nuvem para o dispositivo.

**Grupos de consumidor**: consumidor grupos são uma configuração semelhante a outros sistemas de mensagens que podem ser usados para extrair dados de formas específicas para se conectar a outros aplicativos ou serviços IoT hub. Cada hub IoT é criado com um grupo de consumidor do padrão. No entanto, você pode adicionar ou excluir grupos de consumidor para seu hubs IoT.

> [AZURE.NOTE] Grupo de consumidor padrão não pode ser editado ou excluído.

![][11]

## <a name="pricing-and-scale"></a>Preços e escala

O preço de um hub IoT existente pode ser alterado por meio das configurações de **preços** , com as seguintes exceções:

- Na implementação atual, um hub IoT com uma SKU livre não pode alterar níveis para uma das SKUs do pagas, ou vice-versa.
- Pode haver apenas um hub de IoT nível gratuito na assinatura do Azure.

![][12]

Mover de um nível superior (S2 ou S3) para diminuir nível (S1 ou S2) é permitida somente quando o número de mensagens enviadas para esse dia não está em conflito. Por exemplo, se o número de mensagens por dia exceder 400.000, em seguida, a camada para o hub de IoT pode ser alterada. No entanto, se você mudar para o nível de S1 hub é acelerado para esse dia.

## <a name="delete-the-iot-hub"></a>Excluir o hub de IoT

Você pode navegar para o hub de IoT que você deseja excluir clicando em **Procurar**e, em seguida, escolhendo o hub apropriado para excluir. Clique no botão **Excluir** abaixo do nome de hub para excluir o hub.

## <a name="next-steps"></a>Próximas etapas

Siga estes links para saber mais sobre como gerenciar Azure IoT Hub:

- [Em massa gerenciar IoT dispositivos][lnk-bulk]
- [Métricas de uso][lnk-metrics]
- [Operações de monitoramento][lnk-monitor]

Para explorar os recursos do IoT Hub, consulte:

- [Guia Desenvolvedor][lnk-devguide]
- [Simular um dispositivo com o SDK do Gateway IoT][lnk-gateway]
- [Proteger sua solução IoT desde o início para cima][lnk-securing]


  [4]: ./media/iot-hub-create-through-portal/create-iothub.png
  [5]: ./media/iot-hub-create-through-portal/location1.png
  [8]: ./media/iot-hub-create-through-portal/portal-settings.png
  [10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
  [11]: ./media/iot-hub-create-through-portal/messaging-settings.png
  [12]: ./media/iot-hub-create-through-portal/pricing-error.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md