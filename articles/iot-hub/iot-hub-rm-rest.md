<properties
    pageTitle="Criar um Hub de IoT usando a API REST | Microsoft Azure"
    description="Siga este tutorial para começar a usar a API REST para criar um IoT Hub."
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

# <a name="tutorial-create-an-iot-hub-using-a-c-program-and-the-rest-api"></a>Tutorial: Criar um hub de IoT usando um programa c# e a API REST

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

Você pode usar o [provedor de recursos do IoT Hub API REST] [ lnk-rest-api] para criar e gerenciar hubs Azure IoT programaticamente. Este tutorial mostra como usar o provedor de recursos do IoT Hub API REST para criar um hub IoT a partir de um programa c#.

> [AZURE.NOTE] Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos do Azure e clássico](../resource-manager-deployment-model.md).  Este artigo aborda usando o modelo de implantação do Gerenciador de recursos do Azure.

Para concluir este tutorial, você precisa do seguinte:

- Microsoft Visual Studio 2015.
- Uma conta do Azure active. <br/>Se você não tiver uma conta, você pode criar uma [conta gratuita] [ lnk-free-trial] em apenas alguns minutos.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] ou posterior.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparar seu projeto do Visual Studio

1. No Visual Studio, crie um projeto do Visual c# Windows usando o modelo de projeto de **Aplicativo de Console** . Nome do projeto **CreateIoTHubREST**.

2. No Solution Explorer, clique com botão direito no seu projeto e clique em **Gerenciar pacotes do NuGet**.

3. No Gerenciador de pacote do Nuget, marque **incluir pré-lançamento**e pesquise por **Microsoft.Azure.Management.ResourceManager**. Clique em **instalar**, em **Revisar alterações** **Okey**, clique **para aceitar** para aceitar as licenças.

4. No Gerenciador de pacote do Nuget, procure por **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Clique em **instalar**, em **Revisar alterações** **Okey**, clique **para aceitar** para aceitar a licença.

6. No Program.cs, substitua as declarações existente **usando** o seguinte:

    ```
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```
    
7. No Program.cs, adicione as seguintes variáveis estáticas substituindo os valores de espaço reservado. Você fez anote **ApplicationId**, **SubscriptionId**, **TenantId**e **senha** anteriormente neste tutorial. **Nome do grupo de recursos** é o nome do grupo de recursos que você usa quando você cria o hub de IoT, ele pode ser um grupo de recursos já existente ou um novo. **Hub de IoT nome** é o nome do IoT Hub criar, como **MyIoTHub** (esse nome deve ser exclusivo, para que ele deve incluir seu nome ou iniciais). **Nome de implantação** é um nome para a implantação, como **Deployment_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-rest-api-to-create-an-iot-hub"></a>Usar a API REST para criar um hub IoT

Usar a [API do IoT Hub REST] [ lnk-rest-api] criar um hub IoT no seu grupo de recursos. Você também pode usar a API REST para fazer alterações em um hub IoT existente.

1. Adicione o seguinte método para Program.cs:
    
    ```
    static void CreateIoTHub(string token)
    {
        
    }
    ```

2. Adicione o seguinte código para o método **CreateIoTHub** para criar um objeto **HttpClient** com o símbolo de autenticação nos cabeçalhos:

    ```
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Adicione o seguinte código para o método **CreateIoTHub** para descrever o hub de IoT para criar e gerar uma representação JSON (para a lista atual de locais que suportam IoT Hub ver o [Status do Azure][lnk-status]):

    ```
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };
    
    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Adicione o seguinte código para o método **CreateIoTHub** para enviar a solicitação REST para Azure, verifique a resposta e recuperar a URL que você pode usar para monitorar o estado da tarefa implantação:

    ```
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;
      
    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }
    
    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Adicione o seguinte código para o final do método **CreateIoTHub** para usar o endereço de **asyncStatusUri** recuperado na etapa anterior para aguardar a implantação concluir:

    ```
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Adicione o seguinte código para o final do método **CreateIoTHub** para recuperar as chaves do hub IoT você criou e imprimi-los no console:

    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2015-08-15-preview", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
    
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```
    
## <a name="complete-and-run-the-application"></a>Concluir e executar o aplicativo

Agora você pode concluir o aplicativo chamando o método de **CreateIoTHub** antes de criar e executá-lo.

1. Adicione o seguinte código para o final do método **principal** :

    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
    
2. Clique em **criar** e, em seguida, **criar solução**. Corrija os erros.

3. Clique em **Depurar** e **Iniciar depuração** para executar o aplicativo. Ele pode levar alguns minutos para que a implantação seja executado.

4. Você pode verificar se o seu aplicativo adicionados novos hub IoT visitando o [portal do Azure] [ lnk-azure-portal] e exibir sua lista de recursos, ou usando o cmdlet **Get-AzureRmResource** do PowerShell.

> [AZURE.NOTE] Este aplicativo de exemplo adiciona um Hub de IoT padrão de S1 para o qual você é cobrado. Quando tiver terminado, você pode excluir o hub de IoT por meio do [portal do Azure] [ lnk-azure-portal] ou usando o cmdlet do PowerShell **Remover AzureRmResource** quando tiver terminado.

## <a name="next-steps"></a>Próximas etapas

Agora você implantou um hub de IoT usando a API REST, talvez você queira explorar ainda mais:

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

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
