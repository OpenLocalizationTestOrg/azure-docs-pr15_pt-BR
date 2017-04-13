<properties
    pageTitle="Criar uma máquina virtual com um modelo do Gerenciador de recursos | Microsoft Azure"
    description="Use um modelo do Gerenciador de recursos e o PowerShell para criar facilmente uma nova máquina virtual Windows."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>Criar uma máquina virtual Windows com um modelo do Gerenciador de recursos

Este artigo apresenta um modelo do Gerenciador de recursos do Azure e mostra como usar o PowerShell para implantá-lo. O modelo implanta uma única máquina virtual executando o Windows Server em uma rede virtual novo com uma única sub-rede.

Ele deve levar cerca de 20 minutos para executar as etapas neste artigo.

> [AZURE.IMPORTANT] Se quiser que sua máquina virtual seja parte de um conjunto de disponibilidade, adicioná-lo ao conjunto quando você cria a máquina virtual. Atualmente não há uma maneira de adicionar uma máquina virtual a uma disponibilidade definir depois que ela foi criada.

## <a name="step-1-create-the-template-file"></a>Etapa 1: Criar o arquivo de modelo

Você pode criar seu próprio modelo usando as informações encontradas nos [modelos de coautoria Gerenciador de recursos do Azure](../resource-group-authoring-templates.md). Você também pode implantar modelos que foram criados para você de [Modelos de guias de início rápido do Azure](https://azure.microsoft.com/documentation/templates/).

1. Abra o editor de texto favorito e adicione o elemento de esquema necessário e o elemento de contentVersion necessários:

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
        }
2. [Parâmetros](../resource-group-authoring-templates.md#parameters) nem sempre são necessárias, mas eles oferecem uma maneira de valores de entrada quando o modelo é implantado. Adicione o elemento de parâmetros e seus elementos filhos após o elemento contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

3. [Variáveis](../resource-group-authoring-templates.md#variables) podem ser usadas em um modelo para especificar valores que podem ser alteradas com frequência ou que precisam ser criado a partir de uma combinação de valores de parâmetro. Adicione o elemento de variáveis após a seção de parâmetros:

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
        
4. [Recursos](../resource-group-authoring-templates.md#resources) como a máquina virtual, a rede virtual e a conta de armazenamento são definidos Avançar no modelo. Adicione a seção de recursos após a seção de variáveis:

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
              "name": "myvn1",
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
          
    >[AZURE.NOTE] Este artigo cria uma máquina virtual executando uma versão do sistema operacional Windows Server. Para saber mais sobre como selecionar outras imagens, consulte [navegar e selecionar máquina virtual Azure imagens com o Windows PowerShell e a CLI do Azure](virtual-machines-linux-cli-ps-findimage.md).  
            
2. Salve o arquivo de modelo como *VirtualMachineTemplate.json*.

## <a name="step-2-create-the-parameters-file"></a>Etapa 2: Criar o arquivo de parâmetros

Para especificar valores para os parâmetros de recursos que foram definidos no modelo, crie um arquivo de parâmetros que contém os valores que são usados quando o modelo é implantado.

1. No editor de texto, copie esse conteúdo JSON para um novo arquivo chamado *Parameters.json*:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] Veja mais detalhes sobre [os requisitos do usuário e senha](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).

2. Salve o arquivo de parâmetros.

## <a name="step-3-install-azure-powershell"></a>Etapa 3: Instalar o PowerShell Azure

Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do PowerShell do Azure, selecionando sua assinatura e entrar sua conta.

## <a name="step-4-create-a-resource-group"></a>Etapa 4: Criar um grupo de recursos

Todos os recursos devem ser implantados em um [grupo de recursos](../azure-resource-manager/resource-group-overview.md).

1. Obter uma lista de locais disponíveis onde os recursos podem ser criados.

        Get-AzureRmLocation | sort DisplayName | Select DisplayName

2. Substitua o valor de **$locName** com um local da lista, por exemplo **Centro dos EUA**. Crie a variável.

        $locName = "location name"
        
3. Substitua o valor de **$rgName** com o nome do novo grupo de recursos. Crie a variável e o grupo de recursos.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    Você verá algo parecido com este exemplo:
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

## <a name="step-5-create-the-resources-with-the-template-and-parameters"></a>Etapa 5: Criar os recursos com o modelo e parâmetros

Substitua o valor de **$templateFile** o caminho e nome do arquivo de modelo. Substitua o valor de **$parameterFile** o caminho e o nome do arquivo parâmetros. Crie as variáveis e, em seguida, implante o modelo. 

        $templateFile = "template file"
        $parameterFile = "parameter file"
        New-AzureRmResourceGroupDeployment -ResourceGroupName $rgName -TemplateFile $templateFile -TemplateParameterFile $parameterFile

    You will see something like this:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString

        Outputs           :

>[AZURE.NOTE] Você também pode implantar modelos e parâmetros de uma conta de armazenamento do Azure. Para saber mais, consulte [Usando o PowerShell do Azure com o armazenamento do Azure](../storage/storage-powershell-guide-full.md).

## <a name="next-steps"></a>Próximas etapas

- Se houver problemas com a implantação, a próxima etapa seria examinar [implantações de grupo de recursos de solução de problemas com portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)
- Saiba como gerenciar a máquina virtual que você criou revisando [máquinas virtuais de gerenciar usando o Gerenciador de recursos do Azure e PowerShell](virtual-machines-windows-ps-manage.md).
