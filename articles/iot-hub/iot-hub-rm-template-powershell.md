<properties
    pageTitle="Criar um Hub de IoT usando um modelo do Gerenciador de recursos do Azure e PowerShell | Microsoft Azure"
    description="Siga este tutorial para começar a usar o Gerenciador de recursos do Azure modelos para criar um IoT Hub com o PowerShell."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/07/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-powershell"></a>Criar um hub de IoT usando o PowerShell

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

Você pode usar o Gerenciador de recursos do Azure para criar e gerenciar hubs Azure IoT programaticamente. Este tutorial mostra como usar um modelo do Gerenciador de recursos do Azure para criar um hub IoT com o PowerShell.

> [AZURE.NOTE] Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos do Azure e clássico](../resource-manager-deployment-model.md).  Este artigo aborda usando o modelo de implantação do Gerenciador de recursos do Azure.

Para concluir este tutorial, você precisa do seguinte:

- Uma conta do Azure active. <br/>Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] ou posterior.

> [AZURE.TIP] O artigo [Usando o PowerShell Azure com o Gerenciador de recursos do Azure] [ lnk-powershell-arm] fornece mais informações sobre como usar modelos de Gerenciador de recursos do Azure e scripts do PowerShell para criar recursos Azure. 

## <a name="connect-to-your-azure-subscription"></a>Conectar-se a sua assinatura do Azure

Em um prompt de comando do PowerShell, digite o seguinte comando para entrar em sua assinatura do Azure:

```
Login-AzureRmAccount
```

Você pode usar os seguintes comandos para descobrir onde você pode implantar um hub IoT e as versões de API atualmente suportadas:

```
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Crie um grupo de recursos para conter seu hub de IoT usando o comando a seguir em um dos locais com suporte para IoT Hub. Este exemplo cria um grupo de recursos chamado **MyIoTRG1**:

```
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Enviar um modelo do Gerenciador de recursos do Azure para criar um hub IoT

Use um modelo JSON para criar um hub IoT no seu grupo de recursos. Você também pode usar um modelo do Gerenciador de recursos do Azure para fazer alterações em um hub IoT existente.

1. Use um editor de texto para criar um modelo do Gerenciador de recursos do Azure chamado **template.json** com a seguinte definição de recurso para criar um novo hub de IoT padrão. Este exemplo adiciona o Hub de IoT na região **Leste EUA** , cria dois grupos de consumidor (**cg1** e **cg2**) no ponto de extremidade compatível com o evento Hub e usa a versão de **2016-02-03** API. Este modelo também espera que você passar o nome de hub IoT como um parâmetro chamado **hubName**. Para a lista atual de locais que suportam IoT Hub ver o [Status do Azure][lnk-status].

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. Salve o arquivo de modelo do Gerenciador de recursos do Azure em sua máquina local. Este exemplo pressupõe que você salvá-lo em uma pasta chamada **c:\templates**.

3. Execute o seguinte comando para implantar o seu hub IoT novo, passando o nome do seu hub IoT como um parâmetro. Neste exemplo, o nome do hub IoT é **abcmyiothub** (Observe que esse nome deve ser exclusivo para que ele deve incluir seu nome ou iniciais):

    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```

4. A saída exibe as chaves para o hub de IoT que você criou.

5. Você pode verificar se o seu aplicativo adicionados novos hub IoT visitando o [portal do Azure] [ lnk-azure-portal] e exibir sua lista de recursos, ou usando o cmdlet **Get-AzureRmResource** do PowerShell.

> [AZURE.NOTE] Este aplicativo de exemplo adiciona um Hub de IoT padrão de S1 para o qual você é cobrado. Você pode excluir o hub de IoT por meio do [portal do Azure] [ lnk-azure-portal] ou usando o cmdlet do PowerShell **Remover AzureRmResource** quando tiver terminado.

## <a name="next-steps"></a>Próximas etapas

Agora você implantou um hub de IoT usando um modelo do Gerenciador de recursos do Azure com o PowerShell, talvez você queira explorar ainda mais:

- Leia sobre os recursos do [provedor de recursos do IoT Hub API REST][lnk-rest-api].
- Leia [Visão geral do Gerenciador de recursos do Azure] [ lnk-azure-rm-overview] para saber mais sobre os recursos do Gerenciador de recursos do Azure.

Para saber mais sobre como desenvolver para IoT Hub, consulte o seguinte:

- [Introdução ao SDK C][lnk-c-sdk]
- [Hub de IoT SDKs][lnk-sdks]

Para explorar os recursos do IoT Hub, consulte:

- [Simular um dispositivo com o SDK do Gateway IoT][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
