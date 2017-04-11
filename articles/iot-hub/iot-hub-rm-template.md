<properties
    pageTitle="Criar um Hub de IoT usando um modelo ARM e c# | Microsoft Azure"
    description="Siga este tutorial para começar a usar o Gerenciador de recursos do Azure modelos para criar um IoT Hub com um programa c#."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-a-c-program-with-an-azure-resource-manager-template"></a>Criar um hub de IoT usando um programa c# com um modelo do Gerenciador de recursos do Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

Você pode usar o Gerenciador de recursos do Azure para criar e gerenciar hubs Azure IoT programaticamente. Este tutorial mostra como usar um modelo do Gerenciador de recursos do Azure para criar um hub IoT a partir de um programa c#.

> [AZURE.NOTE] Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos do Azure e clássico](../resource-manager-deployment-model.md).  Este artigo aborda usando o modelo de implantação do Gerenciador de recursos do Azure.

Para concluir este tutorial, você precisa do seguinte:

- Microsoft Visual Studio 2015.
- Uma conta do Azure active. <br/>Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.
- Uma [conta de armazenamento do Azure] [ lnk-storage-account] onde você pode armazenar seus arquivos de modelo do Gerenciador de recursos do Azure.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] ou posterior.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparar seu projeto do Visual Studio

1. No Visual Studio, crie um projeto do Visual c# Windows usando o modelo de projeto de **Aplicativo de Console** . Nome do projeto **CreateIoTHub**.

2. No Solution Explorer, clique com botão direito no seu projeto e clique em **Gerenciar pacotes do NuGet**.

3. No Gerenciador de pacote do Nuget, marque **incluir pré-lançamento**e pesquise por **Microsoft.Azure.Management.ResourceManager**. Clique em **instalar**, em **Revisar alterações** **Okey**, clique **para aceitar** para aceitar as licenças.

4. No Gerenciador de pacote do Nuget, procure por **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Clique em **instalar**, em **Revisar alterações** **Okey**, clique **para aceitar** para aceitar a licença.

5. No Program.cs, substitua as declarações existente **usando** o seguinte:

    ```
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
    
6. No Program.cs, adicione as seguintes variáveis estáticas substituindo os valores de espaço reservado. Você fez anote **ApplicationId**, **SubscriptionId**, **TenantId**e **senha** anteriormente neste tutorial. **Nome da conta de seu armazenamento do Azure** é o nome da conta de armazenamento do Azure onde você pode armazenar seus arquivos de modelo do Gerenciador de recursos do Azure. **Nome do grupo de recursos** é o nome do grupo de recursos que você usa quando você cria o Hub de IoT, ele pode ser um grupo de recursos já existente ou um novo. **Nome de implantação** é um nome para a implantação, como **Deployment_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Enviar um modelo do Gerenciador de recursos do Azure para criar um hub IoT

Use um arquivo de modelo e um parâmetro JSON para criar um hub IoT no seu grupo de recursos. Você também pode usar um modelo do Gerenciador de recursos do Azure para fazer alterações em um hub IoT existente.

1. No Solution Explorer, clique com botão direito no seu projeto, clique em **Adicionar**e, em seguida, clique em **Novo Item**. Adicione um arquivo JSON chamado **template.json** ao seu projeto.

2. Substituir o conteúdo de **template.json** com a seguinte definição de recurso para adicionar um hub IoT padrão para a região **Leste EUA** (para a lista atual de regiões que suportam IoT Hub ver o [Status do Azure][lnk-status]):

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

3. No Solution Explorer, clique com botão direito no seu projeto, clique em **Adicionar**e, em seguida, clique em **Novo Item**. Adicione um arquivo JSON chamado **parameters.json** ao seu projeto.

4. Substituir o conteúdo de **parameters.json** com as seguintes informações de parâmetro que define um nome para o novo hub IoT como **{suas iniciais} mynewiothub**. Observe que o nome do hub IoT deve ser exclusivo para que ele deve incluir seu nome ou iniciais):

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```

5. No **Server Explorer**, conectar-se a sua assinatura do Azure e em sua conta de armazenamento do Azure criar um contêiner chamado **modelos**. No painel **Propriedades** , defina as permissões de **acesso de leitura público** para o contêiner de **modelos** ao **Blob**.

6. No **Server Explorer**, clique com botão direito no contêiner de **modelos** e clique em **Contêiner de Blob do modo de exibição**. Clique no botão **Carregar Blob** , selecione os dois arquivos, **parameters.json** e **templates.json**e clique em **Abrir** para carregar os arquivos JSON ao contêiner de **modelos** . As URLs dos blobs contendo os dados JSON são:

    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```

7. Adicione o seguinte método para Program.cs:
    
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. Adicione o seguinte código para o método **CreateIoTHub** para enviar os arquivos de modelo e um parâmetro para o Gerenciador de recursos do Azure:

    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

6. Adicione o seguinte código para o método de **CreateIoTHub** que exibe o status e as chaves para o novo hub IoT:

    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Concluir e executar o aplicativo

Agora você pode concluir o aplicativo chamando o método de **CreateIoTHub** antes de criar e executá-lo.

1. Adicione o seguinte código para o final do método **principal** :

    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
    
2. Clique em **criar** e, em seguida, **criar solução**. Corrija os erros.

3. Clique em **Depurar** e **Iniciar depuração** para executar o aplicativo. Ele pode levar alguns minutos para que a implantação seja executado.

4. Você pode verificar se o seu aplicativo adicionados novos hub IoT visitando o [portal do Azure] [ lnk-azure-portal] e exibir sua lista de recursos, ou usando o cmdlet **Get-AzureRmResource** do PowerShell.

> [AZURE.NOTE] Este aplicativo de exemplo adiciona um Hub de IoT padrão de S1 para o qual você é cobrado. Você pode excluir o hub de IoT por meio do [portal do Azure] [ lnk-azure-portal] ou usando o cmdlet do PowerShell **Remover AzureRmResource** quando tiver terminado.

## <a name="next-steps"></a>Próximas etapas

Agora você implantou um hub de IoT usando um modelo do Gerenciador de recursos do Azure com um programa c#, talvez você queira explorar ainda mais:

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
[lnk-storage-account]: ../storage/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
