<properties
    pageTitle="Implantar uma máquina virtual usando c# e um modelo do Gerenciador de recursos | Microsoft Azure"
    description="Saiba como usar c# e um modelo do Gerenciador de recursos para implantar uma máquina virtual do Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="davidmu"/>

# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Implantar uma máquina Virtual do Azure usando c# e um modelo do Gerenciador de recursos

Usando modelos e grupos de recursos, conseguir gerenciar todos os recursos juntos que seu aplicativo de suporte. Este artigo mostra como usar o Visual Studio e c# para configurar a autenticação, crie um modelo e implantar o Azure recursos usando o modelo que você criou.

Você precisa primeiro para certificar-se de que você já fez estas etapas de configuração:

- Instale o [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- Verificar a instalação do [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- Obter um [token de autenticação](../resource-group-authenticate-service-principal.md)
- Crie um grupo de recursos usando o [PowerShell do Azure](../resource-group-template-deploy.md), [Azure CLI](../resource-group-template-deploy-cli.md)ou [portal Azure](../resource-group-template-deploy-portal.md).

Ele leva cerca de 30 minutos para siga estas etapas.
    
## <a name="step-1-create-the-visual-studio-project-the-template-file-and-the-parameters-file"></a>Etapa 1: Criar o projeto do Visual Studio, o arquivo de modelo e o arquivo de parâmetros

### <a name="create-the-template-file"></a>Criar o arquivo de modelo

Um modelo do Gerenciador de recursos do Azure possibilita para implantar e gerenciar recursos Azure juntos. O modelo é uma descrição de JSON dos recursos e parâmetros de implantação associado.

No Visual Studio, siga estas etapas:

1. Clique em **arquivo** > **nova** > **projeto**.

2. Em **modelos** > **Visual c#**, selecione **Aplicativo de Console**, insira o nome e local do projeto e clique em **Okey**.

3. Clique com botão direito no nome do projeto no Solution Explorer, clique em **Adicionar** > **Novo Item**.

4. Clique em Web, selecione arquivo de JSON, insira *VirtualMachineTemplate.json* para o nome e clique em **Adicionar**.

5. Nos colchetes de abertura e fechamento do arquivo VirtualMachineTemplate.json, adicione o elemento de esquema necessário e o elemento de contentVersion necessários:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }

6. [Parâmetros](../resource-group-authoring-templates.md#parameters) nem sempre são necessárias, mas eles oferecem uma maneira de valores de entrada quando o modelo é implantado. Adicione o elemento de parâmetros e seus elementos filhos após o elemento contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

7. [Variáveis](../resource-group-authoring-templates.md#variables) podem ser usadas em um modelo para especificar valores que podem ser alteradas com frequência ou que precisam ser criado a partir de uma combinação de valores de parâmetro. Adicione o elemento de variáveis após a seção de parâmetros:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }

8. [Recursos](../resource-group-authoring-templates.md#resources) como a máquina virtual, a rede virtual e a conta de armazenamento são definidos Avançar no modelo. Adicione a seção de recursos após a seção de variáveis:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
      
9. Salve o arquivo de modelo que você criou.

### <a name="create-the-parameters-file"></a>Criar o arquivo de parâmetros

Para especificar valores para os parâmetros de recursos que foram definidos no modelo, você cria um arquivo de parâmetros que contém os valores que são usados quando o modelo é implantado. No Visual Studio, siga estas etapas:

1. Clique com botão direito no nome do projeto no Solution Explorer, clique em **Adicionar** > **Novo Item**.

2. Clique em Web, selecione arquivo de JSON, insira *Parameters.json* para o nome e clique em **Adicionar**.

3. Abra o arquivo parameters.json e adicione esse conteúdo JSON:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] Este artigo cria uma máquina virtual executando uma versão do sistema operacional Windows Server. Para saber mais sobre como selecionar outras imagens, consulte [navegar e selecionar máquina virtual Azure imagens com o Windows PowerShell e a CLI do Azure](virtual-machines-linux-cli-ps-findimage.md).

4. Salve o arquivo de parâmetros que você criou.

## <a name="step-2-install-the-libraries"></a>Etapa 2: Instalar as bibliotecas

Pacotes do NuGet são a maneira mais fácil de instalar as bibliotecas que você precisa concluir este tutorial. Você precisará a biblioteca de gerenciamento de recursos do Azure e a biblioteca de autenticação do Azure Active Directory para criar os recursos. Para obter essas bibliotecas no Visual Studio, siga estas etapas:

1. Clique com botão direito no nome do projeto no Solution Explorer, clique em **Gerenciar pacotes NuGet**e, em seguida, clique em Procurar.

2. Tipo *Do Active Directory* na caixa de pesquisa, clique em **instalar** para o pacote de biblioteca de autenticação do Active Directory e siga as instruções para instalar o pacote.

4. Na parte superior da página, selecione **Incluir pré-lançamento**. Digite *Microsoft.Azure.Management.ResourceManager* na caixa de pesquisa, clique em **instalar** para as bibliotecas de gerenciamento de recursos do Microsoft Azure e siga as instruções para instalar o pacote.

Agora você está pronto para começar a usar as bibliotecas para criar seu aplicativo.

## <a name="step-3-create-the-credentials-that-are-used-to-authenticate-requests"></a>Etapa 3: Criar as credenciais que são usadas para autenticar solicitações

O aplicativo do Azure Active Directory é criado e a biblioteca de autenticação estiver instalada. Agora você pode formatar as informações do aplicativo em credenciais que são usados para autenticar solicitações para o Gerenciador de recursos do Azure.

1. Abra o arquivo Program.cs para o projeto que você criou e, em seguida, adicioná-los usando instruções para a parte superior do arquivo:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Rest;
        using System.IO;

2.  Adicione esse método para a classe de programa para obter o token necessárias para criar as credenciais:

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token.");
          }
          return token;
        }

    Substitua {id do cliente} o identificador do Azure Active Directory do aplicativo, {segredo de cliente} com a chave de acesso do aplicativo do AD e {locatário-id} com o identificador de locatário para sua assinatura. Você pode encontrar a identificação de locatário executando Get-AzureRmSubscription. Você pode encontrar a chave de acesso, usando o portal do Azure.

3. Para criar as credenciais, adicione este código para o método principal no arquivo Program.cs:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Salve o arquivo Program.cs.

## <a name="step-4-deploy-the-template"></a>Etapa 4: Implantar o modelo

Nesta etapa, você usa o grupo de recursos que você criou anteriormente, mas você também pode criar um grupo de recursos usando o [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) e as classes [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) .

1. Adicione variáveis para o método principal da classe programa para especificar os nomes dos recursos que você criou anteriormente, o nome de implantação e o identificador de assinatura:

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var deploymentName = "deployment name";

    Substitua o valor de nome de grupo com o nome do seu grupo de recursos. Substitua o valor de deploymentName com o nome que você deseja usar para a implantação. Você pode encontrar o identificador de assinatura executando Get-AzureRmSubscription.

2. Adicione esse método para a classe de programa para implantar os recursos para o grupo de recursos usando o modelo que você definiu:

        public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
          TokenCredentials credential,
          string groupName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            Template = File.ReadAllText("..\\..\\VirtualMachineTemplate.json"),
            Parameters = File.ReadAllText("..\\..\\Parameters.json")
          };
          var resourceManagementClient = new ResourceManagementClient(credential) 
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
            groupName,
            deploymentName,
            deployment);
        }

    Se você quiser implantar o modelo de uma conta de armazenamento, você pode substituir a propriedade de modelo com a propriedade TemplateLink.

3. Para chamar o método que você acabou de adicionar, adicione este código para o método principal:

        var dpResult = CreateTemplateDeploymentAsync(
          credential,
          groupName,
          deploymentName,
          subscriptionId);
        Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

## <a name="step-5-delete-the-resources"></a>Etapa 5: Excluir os recursos

Porque cobrado para recursos usados no Azure, sempre é uma boa prática excluir recursos que não são mais necessárias. Você não precisa excluir cada recurso separadamente de um grupo de recursos. Excluir o grupo de recursos e todos os seus recursos são excluídos automaticamente.

1.  Para excluir o grupo de recursos, adicione esse método para a classe de programa:

        public static async void DeleteResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
        }

2.  Para chamar o método que você acabou de adicionar, adicione este código para o método principal:

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

##<a name="step-6-run-the-console-application"></a>Etapa 6: Executar o aplicativo de console

1.  Para executar o aplicativo de console, clique em **Iniciar** no Visual Studio e, em seguida, entrar no Azure AD usando as mesmas credenciais que você usa com sua assinatura.

2.  Depois que o status de aceitos for exibida, pressione **Enter** .

    Demorará cerca de cinco minutos para este aplicativo de console seja executado completamente do início ao fim. Antes de pressionar Enter para iniciar a exclusão de recursos, você pode levar alguns minutos para verificar a criação dos recursos no portal do Azure antes de excluí-los.

3. Para ver o status dos recursos, navegue até os Logs de auditoria no portal do Azure:

    ![Procurar logs de auditoria no portal do Azure](./media/virtual-machines-windows-csharp-template/crpportal.png)

## <a name="next-steps"></a>Próximas etapas

- Se houver problemas com a implantação, a próxima etapa seria examinar [implantações de grupo de recursos de solução de problemas com portal Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Saiba como gerenciar a máquina virtual que você criou revisando [máquinas virtuais de gerenciar usando o Gerenciador de recursos do Azure e PowerShell](virtual-machines-windows-csharp-manage.md).
