<properties
   pageTitle="Implantar uma máquina virtual com IP estático público usando um modelo no Gerenciador de recursos | Microsoft Azure"
   description="Saiba como implantar VMs com IP estático público usando um modelo no Gerenciador de recursos"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-a-template"></a>Implantar uma máquina virtual com IP estático público usando um modelo

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="public-ip-resources-in-a-template-file"></a>Recursos IP públicos em um arquivo de modelo

Você pode ver e baixar o [modelo de exemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json).

A seção a seguir mostra a definição do recurso IP público, com base no cenário acima.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('webVMSetting').pipName]",
        "location": "[variables('location')]",
        "properties": {
          "publicIPAllocationMethod": "Static"
        },
        "tags": {
          "displayName": "PublicIPAddress - Web"
        }
      },

Observe que a propriedade **publicIPAllocationMethod** , que é definida para *estático*. Essa propriedade pode ser *estática*ou *dinâmica* (valor padrão). Definindo-a como estático garante que o endereço IP público atribuído nunca será alterado.

A seção a seguir mostra a associação do endereço IP público com uma interface de rede.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('webVMSetting').nicName]",
        "location": "[variables('location')]",
        "tags": {
          "displayName": "NetworkInterface - Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
          "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
          "ipConfigurations": [
            {
              "name": "ipconfig1",
              "properties": {
                "privateIPAllocationMethod": "Static",
                "privateIPAddress": "[variables('webVMSetting').ipAddress]",
                "publicIPAddress": {
                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
                },
                "subnet": {
                  "id": "[variables('frontEndSubnetRef')]"
                }
              }
            }
          ]
        }
      },

Observe que a propriedade de **publicIPAddress** apontando para a **identificação** de um recurso chamado **variables('webVMSetting').pipName**. Esse é o nome do recurso IP público mostrado acima.

Por fim, a interface de rede acima está listada na propriedade **networkProfile** da máquina virtual está sendo criada.

      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Implante o modelo usando o clique para implantar

O modelo de exemplo disponível no repositório público usa um arquivo de parâmetro que contém o padrão de valores usadas para gerar o cenário descrito acima. Para implantar este modelo usando o clique para implantar, clique em **implantar no Azure** no arquivo Readme.md para o modelo de [máquina virtual com PIP estático](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP) . Substitua os valores de parâmetro padrão se desejado e insira valores para os parâmetros em branco.  Siga as instruções no portal para criar uma máquina virtual com um endereço IP público estático.

## <a name="deploy-the-template-by-using-powershell"></a>Implante o modelo usando o PowerShell

Para implantar o modelo que você baixou usando o PowerShell, siga as etapas abaixo.

1. Se você nunca usou o PowerShell do Azure, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) e siga as instruções em etapas 1 a 3.

2. Em um console do PowerShell, execute o cmdlet **New-AzureRmResourceGroup** para criar um novo grupo de recursos, se necessário. Se você já tiver criado um grupo de recursos, vá para a etapa 3.

        New-AzureRmResourceGroup -Name PIPTEST -Location westus

    Saída esperada:

        ResourceGroupName : PIPTEST
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. Em um console do PowerShell, execute o cmdlet **New-AzureRmResourceGroupDeployment** para implantar o modelo.

        New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
            -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
            -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json

    Saída esperada:

        DeploymentName    : DeployVM
        ResourceGroupName : PIPTEST
        ProvisioningState : Succeeded
        Timestamp         : <Deployment date> <Deployment time>
        Mode              : Incremental
        TemplateLink      :
                            Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
                            ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
                            ContentVersion : 1.0.0.0

        Parameters        :
                            Name                      Type                       Value     
                            ========================  =========================  ==========
                            vnetName                  String                     WTestVNet
                            vnetPrefix                String                     192.168.0.0/16
                            frontEndSubnetName        String                     FrontEnd  
                            frontEndSubnetPrefix      String                     192.168.1.0/24
                            storageAccountNamePrefix  String                     iaasestd  
                            stdStorageType            String                     Standard_LRS
                            osType                    String                     Windows   
                            adminUsername             String                     adminUser
                            adminPassword             SecureString                         

        Outputs           :

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implantar o modelo usando a CLI do Azure

Para implantar o modelo usando a CLI do Azure, siga as etapas abaixo.

1. Se você nunca usou Azure CLI, siga as etapas no artigo [instalar e configurar o CLI do Azure](../xplat-cli-install.md) e, em seguida, as etapas para conectar a CLI à sua assinatura na seção "Use azure login para autenticar interativamente" do artigo [conectar uma assinatura do Azure a Interface do Azure de linha (Azure comando)](../xplat-cli-connect.md) .
2. Execute o comando de **modo config azure** para alternar para modo de Gerenciador de recursos, conforme mostrado abaixo.

        azure config mode arm

    Aqui está a saída esperada para o comando acima:

        info:    New mode is arm

3. Abra o [arquivo de parâmetro](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json), selecione seu conteúdo e salvá-lo em um arquivo em seu computador. Neste exemplo, os parâmetros são salvos em um arquivo denominado *parameters.json*. Alterar os valores de parâmetro dentro do arquivo se desejar, mas no mínimo, é recomendável que você altere o valor para o parâmetro adminPassword para uma senha exclusiva e complexa.

4. Execute o cmdlet **criar de implantação do azure grupo** para implantar o novo VNet usando os arquivos de modelo e um parâmetro que você baixou e modificado acima. No comando abaixo, substitua <path> com o caminho que você salvou o arquivo. 

        azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e <path>\parameters.json

    Saída esperada (listas de valores de parâmetro usados):

        info:    Executing command group create
        + Getting resource group PIPTEST2
        + Creating resource group PIPTEST2
        info:    Created resource group PIPTEST2
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/<Subscription ID>/resourceGroups/PIPTEST2
        data:    Name:                PIPTEST2
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
