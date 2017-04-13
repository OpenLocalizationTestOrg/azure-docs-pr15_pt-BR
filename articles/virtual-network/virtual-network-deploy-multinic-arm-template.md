<properties
   pageTitle="Implantar vários NIC VMs usando um modelo no Gerenciador de recursos | Microsoft Azure"
   description="Saiba como implantar vários NIC VMs usando um modelo no Gerenciador de recursos"
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
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="deploy-multi-nic-vms-using-a-template"></a>Implantar vários NIC VMs usando um modelo

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implantação clássico](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Desde que a neste momento você não pode ter VMs com uma única NIC e VMs com várias placas de rede no mesmo grupo de recursos, você irá implementar os servidores de back-end em um grupo de recursos e todos os outros componentes em outro grupo de segurança. As etapas abaixo usam um grupo de recursos denominado *IaaSStory* para o grupo de recursos principal e *IaaSStory-back-end* para os servidores de back-end.

## <a name="prerequisites"></a>Pré-requisitos

Antes de implantar os servidores de back-end, você precisa implantar o grupo de recursos principal com todos os recursos necessários para esse cenário. Para implantar esses recursos, siga as etapas abaixo.

1. Navegue até [a página de modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Na página de modelo, à direita do **grupo de recursos do pai**, clique em **implantar no Azure**.
3. Se necessário, altere os valores de parâmetro, siga as etapas no portal do Azure preview para implantar o grupo de recursos.

> [AZURE.IMPORTANT] Verifique se que os nomes de conta de armazenamento são exclusivos. Você não pode ter nomes de conta de armazenamento duplicado no Azure.

## <a name="understand-the-deployment-template"></a>Entender o modelo de implantação

Antes de implantar o modelo fornecido com esta documentação, certifique-se de que você entende o que ela faz. As etapas a seguir fornecem uma boa visão geral do modelo em questão.

1. Navegue até [a página de modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Clique em **azuredeploy.json** para abrir o arquivo de modelo.
3. Observe que o parâmetro *osType* listado abaixo. Este parâmetro é usado para selecionar quais imagem de máquina virtual para usar para o servidor de banco de dados, juntamente com o sistema operacional do várias configurações relacionadas.

        "osType": {
          "type": "string",
          "defaultValue": "Windows",
          "allowedValues": [
            "Windows",
            "Ubuntu"
          ],
          "metadata": {
            "description": "Type of OS to use for VMs: Windows or Ubuntu."
          }
        },

4. Role para baixo até a lista de variáveis e verificar a definição para as variáveis de **dbVMSetting** , listados abaixo. Ele recebe um dos elementos da matriz contidos na variável **dbVMSettings** . Se você estiver familiarizado com a terminologia de desenvolvimento de software, você pode exibir a variável **dbVMSettings** como uma tabela de hash ou uma dictionay.

        "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"

5. Suponha que você decidiu implantar VMs do Windows que executam o SQL no back-end. Em seguida, o valor para **osType** seria *Windows*e a variável **dbVMSetting** conterá o elemento listado abaixo, que representa o primeiro valor na variável **dbVMSettings** .

          "Windows": {
            "vmSize": "Standard_DS3",
            "publisher": "MicrosoftSQLServer",
            "offer": "SQL2014SP1-WS2012R2",
            "sku": "Standard",
            "version": "latest",
            "vmName": "DB",
            "osdisk": "osdiskdb",
            "datadisk": "datadiskdb",
            "nicName": "NICDB",
            "ipAddress": "192.168.2.",
            "extensionDeployment": "",
            "avsetName": "ASDB",
            "remotePort": 3389,
            "dbPort": 1433
          },

6. Observe que o **vmSize** contém o valor *Standard_DS3*. Permitir que apenas determinados tamanhos de máquina virtual para o uso de várias placas de rede. Você pode verificar quais tamanhos de máquina virtual são multi NIC ativados visitando a [Visão geral NIC multi](virtual-networks-multiple-nics.md).
7. Role para baixo até **recursos** e observe o primeiro elemento. Ele descreve uma conta de armazenamento. Esta conta de armazenamento será usada para manter os discos de dados usados por cada máquina virtual do banco de dados. Nesse cenário, cada máquina virtual do banco de dados tem um disco de sistema operacional armazenados em armazenamento regular e duas discos de dados armazenados em armazenamento de SSD (premium).

        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('prmStorageName')]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "Storage Account - Premium"
          },
          "properties": {
            "accountType": "[parameters('prmStorageType')]"
          }
        },

8. Role para baixo até o próximo recurso, conforme listado abaixo. Esse recurso representa a NIC usada para acesso de banco de dados em cada máquina virtual do banco de dados. Observe o uso da função **cópia** deste recurso. O modelo permite que você implantar VMs quantas como você deseja, com base no parâmetro **dbCount** . Portanto, você precisa criar a mesma quantidade de NICs para acesso de banco de dados, uma para cada máquina virtual.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "NetworkInterfaces - DB DA"
          },
          "copy": {
            "name": "dbniccount",
            "count": "[parameters('dbCount')]"
          },
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Static",
                  "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
                  "subnet": {
                    "id": "[variables('backEndSubnetRef')]"
                  }
                }
              }
            ]
          }
        },

9. Role para baixo até o próximo recurso, conforme listado abaixo. Esse recurso representa a NIC usada para gerenciamento em cada máquina virtual do banco de dados. Novamente, você precisa de um destas NICs para cada máquina virtual do banco de dados. Observe que o elemento **networkSecurityGroup** , um NSG que permita acessar RDP/SSH a este NIC somente de vinculação.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "NetworkInterfaces - DB RA"
          },
          "copy": {
            "name": "dbniccount",
            "count": "[parameters('dbCount')]"
          },
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "networkSecurityGroup": {
                    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
                  },
                  "privateIPAllocationMethod": "Static",
                  "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
                  "subnet": {
                    "id": "[variables('backEndSubnetRef')]"
                  }
                }
              }
            ]
          }
        },

10. Role para baixo até o próximo recurso, conforme listado abaixo. Esse recurso representa uma disponibilidade definida para ser compartilhado por todas as VMs de banco de dados. Dessa maneira, você garante que sempre haverá uma VM no conjunto executando durante a manutenção.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/availabilitySets",
          "name": "[variables('dbVMSetting').avsetName]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "AvailabilitySet - DB"
          }
        },

11. Role para baixo até o próximo recurso. Esse recurso representa o banco de dados VMs, conforme visto no primeiro algumas linhas listadas abaixo. Observe o uso da função **Copiar** novamente, garantindo que várias VMs são criadas com base no parâmetro **dbCount** . Observe também a coleção de **dependsOn** . Ela lista duas NICs é necessário ser criada antes que a máquina virtual for implantada, juntamente com o conjunto de disponibilidade e a conta de armazenamento.

          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
          "location": "[variables('location')]",
          "dependsOn": [
            "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
            "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
            "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
            "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
          ],
          "tags": {
            "displayName": "VMs - DB"
          },
          "copy": {
            "name": "dbvmcount",
            "count": "[parameters('dbCount')]"
          },

12. Rolar para baixo o recurso de máquina virtual para o elemento **networkProfile** , conforme listado abaixo. Observe que há dois NICs sendo referência para cada máquina virtual. Quando você cria várias placas de rede para uma máquina virtual, você deve definir a propriedade **primary** de uma das NICs como *true*e o restante como *false*.

        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
              "properties": { "primary": true }
            },
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
              "properties": { "primary": false }
            }
          ]
        }
      }

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Implantar o modelo ARM usando o clique para implantar

> [AZURE.IMPORTANT] Certifique-se de que você siga as etapas de [pré-requisitos](#Pre-requisites) antes de seguir as instruções abaixo.

O modelo de exemplo disponível no repositório público usa um arquivo de parâmetro que contém o padrão de valores usadas para gerar o cenário descrito acima. Para implantar este modelo usando o clique para distribuir, siga [Este link](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), à direita do **grupo de recursos de back-end (consulte a documentação)** clique **implantar para Azure**, substitua os valores de parâmetro padrão, se necessário e siga as instruções no portal.

A figura a seguir mostra o conteúdo do grupo de recursos novos, após a implantação.

![Grupo de recursos de back-end](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>Implante o modelo usando o PowerShell

Para implantar o modelo que você baixou usando o PowerShell, siga as etapas abaixo.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. Executar o **`New-AzureRmResourceGroup`** cmdlet para criar um grupo de recursos usando o modelo.

        New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
            -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'

    Saída esperada:

        ResourceGroupName : IaaSStory-Backend
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  

        Resources         :
                            Name                 Type                                 Location
                            ===================  ===================================  ========
                            ASDB                 Microsoft.Compute/availabilitySets   westus  
                            DB1                  Microsoft.Compute/virtualMachines    westus  
                            DB2                  Microsoft.Compute/virtualMachines    westus  
                            NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                            NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                            NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                            NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                            wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  

        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implantar o modelo usando a CLI do Azure

Para implantar o modelo usando a CLI do Azure, siga as etapas abaixo.

1. Se você nunca usou CLI do Azure, consulte [instalar e configurar o CLI do Azure](../xplat-cli-install.md) e siga as instruções para cima até o ponto onde você selecionar sua conta do Azure e assinatura.
2. Executar o **`azure config mode`** comando para alternar para modo de Gerenciador de recursos, conforme mostrado abaixo.

        azure config mode arm

    Aqui está a saída esperada para o comando acima:

        info:    New mode is arm

3. Abra o [arquivo de parâmetro](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), selecione seu conteúdo e salvá-lo em um arquivo em seu computador. Neste exemplo, podemos salvou o arquivo de parâmetros *parameters.json*.

4. Executar o **`azure group deployment create`** cmdlet para implantar o novo VNet usando o modelo e o parâmetro arquivos que você baixou e modificado acima. A lista mostrada após a saída explica os parâmetros usados.

        azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json

    Saída esperada:

        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
