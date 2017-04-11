<properties
 pageTitle="Criar seu hub IoT e registrar seu framboesa Pi 3 | Microsoft Azure"
 description="Criar um grupo de recursos, crie um hub IoT do Azure e registrar seu Pi no hub do Azure IoT utilizando a CLI do Azure."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="22-create-your-iot-hub-and-register-your-raspberry-pi-3"></a>2.2 criar seu hub IoT e registrar seu framboesa Pi 3

## <a name="221-what-you-will-do"></a>2.2.1 o que você fará

- Crie um grupo de recursos.
- Crie seu hub Azure IoT no grupo de recursos.
- Adicione seu framboesa Pi 3 ao hub do Azure IoT utilizando a CLI do Azure.

Quando você usa o CLI do Azure para adicionar seu Pi ao seu hub IoT, o serviço gera uma chave para seu Pi autenticar com o serviço. Se você atender a todos os problemas, busca soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="222-what-you-will-learn"></a>2.2.2 o que você aprenderá

- Como usar a CLI do Azure para criar um Hub de IoT Azure.
- Como criar uma identidade de dispositivo para seu Pi no seu Hub de IoT Azure.

## <a name="223-what-you-need"></a>2.2.3 o que é necessário

- Uma conta do Azure
- Um Mac ou um computador com Windows com a CLI Azure instalado

## <a name="224-create-your-azure-iot-hub"></a>2.2.4 criar seu hub IoT do Azure

Hub de IoT Azure ajuda você a conectar, monitorar e gerenciar milhões de IoT ativos. Para criar o seu hub IoT Azure, siga estas etapas:

1. Faça logon em sua conta do Azure executando o seguinte comando:

    ```bash
    az login
    ```

    Todas as suas assinaturas Azure disponíveis são listadas após um login bem-sucedido.

2. Defina o padrão de assinatura do Azure que você deseja usar, execute o seguinte comando:

    ```bash
    az account set -n {subscription id or name}
    ```

    O nome ou a ID da assinatura pode ser encontrada na saída do `az login`.

3. Registre o provedor, execute o seguinte comando:

    ```bash
    az resource provider register -n "Microsoft.Devices"
    ```

    Você deve registrar o provedor antes de implantar o recurso Azure que fornece o provedor.

    > [AZURE.NOTE] A maioria dos provedores são registrados automaticamente pelo portal do Azure ou CLI Azure você está usando, mas não todos. Para obter mais informações sobre o provedor, consulte [Solucionar problemas erros comuns da implantação Azure com o Gerenciador de recursos do Azure](../resource-manager-common-deployment-errors.md)

4. Crie um grupo de recursos denominado iot amostra na região Oeste EUA executando o seguinte comando:

    ```bash
    az resource group create --name iot-sample --location westus
    ```

5. Crie um hub IoT no grupo de recursos de amostra iot executando o seguinte comando:

    ```bash
    az iot hub create --name {my hub name} --resource-group iot-sample
    ```

    A edição padrão do hub IoT que criar é **F1**, que é **gratuito**. Para obter mais informações, consulte [preços do Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

> [AZURE.NOTE] O nome do seu hub IoT deve ser exclusivo.
>
> Você pode criar apenas uma edição de **F1** do Azure IoT Hub em sua assinatura do Azure.

## <a name="225-register-your-pi-in-your-iot-hub"></a>2.2.5 registrar seu Pi no seu hub IoT

Cada dispositivo que envia/recebe mensagens de/para o seu hub Azure IoT deve ser registrado com uma ID exclusiva.

Registre seu Pi no seu hub Azure IoT por comando a seguir em execução:

```bash
az iot device create --device-id myraspberrypi --hub {my hub name} --resource-group iot-sample
```

## <a name="226-summary"></a>2.2.6 resumo de

Você criou um hub IoT do Azure e registrado seu Pi com uma identidade de dispositivo no seu hub IoT do Azure. Você está pronto para prosseguir para a próxima lição para saber como enviar mensagens de seu Pi para seu hub IoT.

## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para começar a [criar um aplicativo de função Azure e uma conta de armazenamento do Azure para processar e armazenar mensagens de hub IoT](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)lição 3 que começa.