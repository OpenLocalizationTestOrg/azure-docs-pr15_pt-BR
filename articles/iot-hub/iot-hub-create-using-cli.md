<properties
    pageTitle="Criar um Hub de IoT usando Azure CLI | Microsoft Azure"
    description="Siga este artigo para criar um Hub de IoT usando a Interface de linha do Azure."
    services="iot-hub"
    documentationCenter=".net"
    authors="BeatriceOltean"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/21/2016"
     ms.author="boltean"/>

# <a name="create-an-iot-hub-using-azure-cli"></a>Criar um Hub de IoT usando CLI do Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

Você pode usar a Interface de linha de comando do Azure para criar e gerenciar hubs Azure IoT programaticamente. Este artigo mostra como usar a CLI do Azure para criar um IoT Hub.

Para concluir este tutorial, você precisa do seguinte:

- Uma conta do Azure active. Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.
- [CLI Azure 0.10.4] [ lnk-CLI-install] ou posterior. Se você já tiver CLI Azure você pode validar a versão atual no prompt de comando com o seguinte comando:
```
    azure --version
```

> [AZURE.NOTE] Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos do Azure e clássico](../resource-manager-deployment-model.md). A CLI do Azure deve estar no modo do Gerenciador de recursos do Azure:
```
    azure config mode arm
```

## <a name="set-your-azure-account-and-subscription"></a>Configurar sua conta do Azure e assinatura 

1. No prompt de comando login digitando o seguinte comando
```
    azure login
```
Use o navegador da web sugeridos e o código para autenticar.

2. Se você tiver várias assinaturas Azure, conectando ao Azure concede acesso a todas as assinaturas Azure associado a suas credenciais. Você pode exibir as assinaturas Azure, bem como qual é o padrão, usando o comando
```
    azure account list 
```

Para definir o contexto de assinatura em que você deseja executar o restante do uso de comandos

```
    azure account set <subscription name>
```

3. Se você não tiver um grupo de recursos que você pode criar um nomeado **exampleResourceGroup** 
```
    azure group create -n exampleResourceGroup -l westus
```

> [AZURE.TIP] O artigo [usar a CLI do Azure para gerenciar grupos de recursos e recursos Azure] [ lnk-CLI-arm] fornece mais informações sobre como usar o Azure CLI para gerenciar recursos do Azure. 


## <a name="create-an-iot-hub"></a>Criar um Hub IoT

Parâmetros necessários:

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
Para ver todos os parâmetros disponíveis para a criação de você pode usar o comando help no prompt de comando
```
    azure iothub create -h 
```
Exemplo rápido:

 Para criar um IoT Hub chamado **exampleIoTHubName** no grupo de recursos **exampleResourceGroup** simplesmente execute o seguinte comando
```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [AZURE.NOTE] Este comando do Azure CLI cria um Hub de IoT padrão de S1 para o qual você é cobrado. Você pode excluir o hub de IoT **exampleIoTHubName** usando o seguinte comando 
```
    azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
```


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como desenvolver para IoT Hub, consulte o seguinte:
- [Hub de IoT SDKs][lnk-sdks]

Para explorar os recursos do IoT Hub, consulte:

- [Usando o Portal do Azure para gerenciar IoT Hub][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-CLI-arm]: ../xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
