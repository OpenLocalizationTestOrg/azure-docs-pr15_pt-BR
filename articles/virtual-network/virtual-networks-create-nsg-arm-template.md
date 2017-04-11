<properties
   pageTitle="Como criar NSGs no modo ARM usando um modelo | Microsoft Azure"
   description="Aprenda a criar e implantar NSGs no BRAÇO usando um modelo"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="how-to-create-nsgs-using-a-template"></a>Como criar NSGs usando um modelo

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo discute o modelo de implantação do Gerenciador de recursos. Você também pode [criar NSGs no modelo clássico de implantação](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## <a name="nsg-resources-in-a-template-file"></a>Recursos NSG em um arquivo de modelo

Você pode ver e baixar o [modelo de exemplo](https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/NSGs.json).

A seção a seguir mostra a definição do front-end NSG, baseado no cenário acima.

      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('frontEndNSGName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "NSG - Front End"
      },
      "properties": {
        "securityRules": [
          {
            "name": "rdp-rule",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "Internet",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 100,
              "direction": "Inbound"
            }
          },
          {
            "name": "web-rule",
            "properties": {
              "description": "Allow WEB",
              "protocol": "Tcp",
              "sourcePortRange": "*",
              "destinationPortRange": "80",
              "sourceAddressPrefix": "Internet",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 101,
              "direction": "Inbound"
            }
          }
        ]
      }

Para associar o NSG à sub-rede front-end, você precisa alterar a definição de sub-rede no modelo e usar a id de referência para o NSG.

        "subnets": [
          {
            "name": "[parameters('frontEndSubnetName')]",
            "properties": {
              "addressPrefix": "[parameters('frontEndSubnetPrefix')]",
              "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('frontEndNSGName'))]"
              }
            }
          }, ...

Observe o mesmo sendo feita para NSG back-end e a sub-rede de back-end no modelo.

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Implantar o modelo ARM usando o clique para implantar

O modelo de exemplo disponível no repositório público usa um arquivo de parâmetro que contém o padrão de valores usadas para gerar o cenário descrito acima. Para implantar este modelo usando o clique para distribuir, siga [Este link](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG), clique em **implantar no Azure**, substitua os valores de parâmetro padrão, se necessário e siga as instruções no portal.

## <a name="deploy-the-arm-template-by-using-powershell"></a>Implantar o modelo ARM usando o PowerShell

Para implantar o modelo ARM que você baixou usando o PowerShell, siga as etapas abaixo.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Se você nunca usou o PowerShell do Azure, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) e siga as instruções totalmente ao final para entrar no Azure e selecione sua assinatura.

3. Executar o **`New-AzureRmResourceGroup`** cmdlet para criar um grupo de recursos usando o modelo.

        New-AzureRmResourceGroup -Name TestRG -Location uswest `
            -TemplateFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.json' `
            -TemplateParameterFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.parameters.json'

    Saída esperada:

        ResourceGroupName : TestRG
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  

        Resources         :
                            Name                Type                                     Location
                            ==================  =======================================  ========
                            sqlAvSet            Microsoft.Compute/availabilitySets       westus  
                            webAvSet            Microsoft.Compute/availabilitySets       westus  
                            SQL1                Microsoft.Compute/virtualMachines        westus  
                            SQL2                Microsoft.Compute/virtualMachines        westus  
                            Web1                Microsoft.Compute/virtualMachines        westus  
                            Web2                Microsoft.Compute/virtualMachines        westus  
                            TestNICSQL1         Microsoft.Network/networkInterfaces      westus  
                            TestNICSQL2         Microsoft.Network/networkInterfaces      westus  
                            TestNICWeb1         Microsoft.Network/networkInterfaces      westus  
                            TestNICWeb2         Microsoft.Network/networkInterfaces      westus  
                            NSG-BackEnd         Microsoft.Network/networkSecurityGroups  westus  
                            NSG-FrontEnd        Microsoft.Network/networkSecurityGroups  westus  
                            TestPIPSQL1         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPSQL2         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPWeb1         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPWeb2         Microsoft.Network/publicIPAddresses      westus  
                            TestVNet            Microsoft.Network/virtualNetworks        westus  
                            testvnetstorageprm  Microsoft.Storage/storageAccounts        westus  
                            testvnetstoragestd  Microsoft.Storage/storageAccounts        westus  

        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Implantar o modelo ARM usando a CLI do Azure

Para implantar o modelo ARM usando a CLI do Azure, siga as etapas abaixo.

1. Se você nunca usou CLI do Azure, consulte [instalar e configurar o CLI do Azure](../xplat-cli-install.md) e siga as instruções para cima até o ponto onde você selecionar sua conta do Azure e assinatura.
2. Executar o **`azure config mode`** comando para alternar para modo de Gerenciador de recursos, conforme mostrado abaixo.

        azure config mode arm

    Aqui está a saída esperada para o comando acima:

        info:    New mode is arm

4. Executar o **`azure group deployment create`** cmdlet para implantar o novo VNet usando o modelo e o parâmetro arquivos que você baixou e modificado acima. A lista mostrada após a saída explica os parâmetros usados.

        azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.json' -e 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.parameters.json'

    Saída esperada:

        info:    Executing command group create
        info:    Getting resource group TestRG
        info:    Creating resource group TestRG
        info:    Created resource group TestRG
        info:    Initializing template configurations and parameters
        info:    Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:    
        info:    group create command OK

    - **-n (ou - nome)**. Nome do grupo de recursos a ser criado.
    - **-l (ou - local)**. Azure região onde o grupo de recursos será criado.
    - **-f (ou --arquivo de modelo)**. Caminho para o arquivo de modelo ARM.
    - **-e (ou - arquivo de parâmetros)**. Caminho para o seu arquivo de parâmetros ARM.
