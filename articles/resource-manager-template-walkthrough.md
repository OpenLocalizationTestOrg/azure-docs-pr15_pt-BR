<properties
   pageTitle="Passo a passo do Gerenciador de recursos de modelo | Microsoft Azure"
   description="Instruções passo a passo a passo de um modelo de Gerenciador de recurso uma arquitetura de Azure IaaS básica de provisionamento."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="navale;tomfitz"/>
   
# <a name="resource-manager-template-walkthrough"></a>Explicação passo a passo de modelo do Gerenciador de recursos

Uma das primeiras perguntas ao criar um modelo é "como iniciar?". Um pode iniciar de um modelo em branco, seguindo a estrutura básica descrita no [artigo de modelo de criação](resource-group-authoring-templates.md#template-format)e adicionar os recursos e parâmetros apropriados e variáveis. Uma boa alternativa seria comece acessando por meio da [Galeria de início rápido](https://github.com/Azure/azure-quickstart-templates) e procure cenários semelhantes ao que você está tentando criar. Você pode mesclar vários modelos ou edite uma existente para atender às sua próprias cenário específico. 

Vamos dar uma olhada em uma infraestrutura comuns:

* Duas máquinas virtuais que usam a mesma conta de armazenamento, são o mesmo conjunto de disponibilidade e, na mesma sub-rede de uma rede virtual.
* Um único NIC e VM o endereço IP para cada máquina virtual.
* Um balanceador de carga com uma regra na porta 80 balanceamento de carga

![arquitetura](./media/resource-group-overview/arm_arch.png)

Este tópico apresenta as etapas da criação de um modelo do Gerenciador de recursos para essa infraestrutura. O modelo final que criar baseia-se em um modelo de início rápido chamado [2 VMs em um balanceador de carga e as regras de balanceamento de carga](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

Mas, que é muito criar ao mesmo tempo, então vamos primeiro criar uma conta de armazenamento e implantá-lo. Depois de você domínio criando a conta de armazenamento, você adicionará os outros recursos e implantar o modelo para concluir a infraestrutura novamente.

>[AZURE.NOTE] Você pode usar qualquer tipo de editor ao criar o modelo. Visual Studio fornece ferramentas que simplificam o desenvolvimento de modelo, mas você não precisa Visual Studio para concluir este tutorial. Para um tutorial sobre como usar o Visual Studio para criar uma implantação Web App e o banco de dados SQL, consulte [Criando e implantando grupos de recursos Azure por meio do Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). 

## <a name="create-the-resource-manager-template"></a>Criar o modelo do Gerenciador de recursos

O modelo é um arquivo JSON que define todos os recursos que você implantará. Também permite que você defina parâmetros especificados durante a implantação, variáveis que construído a partir de outros valores e expressões e saídas da implantação. 

Vamos começar com o modelo mais simples:

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }
 ```

Salvar este arquivo como **azuredeploy.json** (Observe que o modelo pode ter qualquer nome desejado, basta que ele deve ser um arquivo de json).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Dentro da seção de **recursos** , adicione um objeto que define a conta de armazenamento, conforme mostrado abaixo. 

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

Você pode estar se perguntando onde essas propriedades e valores vêm. As propriedades **tipo**, **nome**, **apiVersion**e **local** são elementos padrão que estão disponíveis para todos os tipos de recursos. Você pode aprender sobre os elementos comuns em [recursos](resource-group-authoring-templates.md#resources). **nome** está definido como um valor de parâmetro que você passa em durante a implantação e **localização** como o local usado pelo grupo de recursos. Vamos examinar como você determinar o **tipo** e **apiVersion** nas seções a seguir.

A seção **Propriedades** contém todas as propriedades que são exclusivas para um tipo de recurso específico. Os valores que você especificar nesta seção exatamente corresponder a operação de colocar na API REST para criar esse tipo de recurso. Ao criar uma conta de armazenamento, você deve fornecer um **accountType**. Observe na [API REST para criar uma conta de armazenamento](https://msdn.microsoft.com/library/azure/mt163564.aspx) que a seção de propriedades da operação de REST também contém uma propriedade **accountType** e os valores permitidos são documentados. Neste exemplo, o tipo de conta está definido como **Standard_LRS**, mas você pode especificar algum outro valor ou permitir que os usuários passar o tipo de conta como um parâmetro.

Agora vamos voltar para a seção **parâmetros** e ver como você definir o nome da conta de armazenamento. Você pode saber mais sobre o uso de parâmetros em [parâmetros](resource-group-authoring-templates.md#parameters). 

```json
"parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
}
```
Aqui você definiu um parâmetro de tipo de cadeia de caracteres que conterá o nome da conta de armazenamento. O valor para esse parâmetro será fornecido durante a implantação do modelo.

## <a name="deploying-the-template"></a>Implantando o modelo
Temos um modelo completo para criar uma nova conta de armazenamento. Como você se lembrar, o modelo foi salvo no arquivo **azuredeploy.json** :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
  },  
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('storageAccountName')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    }
  ]
}
```

Há algumas maneiras de implantar um modelo, como você pode ver o [artigo de implantação do recurso](resource-group-template-deploy.md). Para implantar o modelo usando o PowerShell do Azure, use:

```powershell
# create a new resource group
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West Europe"

# deploy the template to the resource group
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile azuredeploy.json
```

Ou, para implantar o modelo usando Azure CLI, use:

```
azure group create -n ExampleResourceGroup -l "West Europe"

azure group deployment create -f azuredeploy.json -g ExampleResourceGroup -n ExampleDeployment
```

Você agora é o proprietário orgulhoso de uma conta de armazenamento!

As próximas etapas será adicionar todos os recursos necessários para implantar a arquitetura descrita no início deste tutorial. Você adicionará esses recursos no mesmo modelo que você estava trabalhando.

## <a name="availability-set"></a>Conjunto de disponibilidade
Após a definição da conta de armazenamento, adicione um disponível definido para as máquinas virtuais. Nesse caso, não há nenhuma propriedades adicionais necessárias, portanto sua definição é bastante simple. Consulte a [API REST para a criação de um conjunto de disponibilidade](https://msdn.microsoft.com/library/azure/mt163607.aspx) para a seção inteira de propriedades, caso você queira definir as atualização domínio contagem e falhas domínio contar valores.

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

Observe que o **nome** está definido para o valor de uma variável. Para este modelo, o nome do conjunto de disponibilidade é necessário em alguns lugares diferentes. Com mais facilidade, você pode manter seu modelo definindo esse valor uma vez e usá-lo em vários locais.

O valor especificado para **tipo** contém o provedor de recursos e o tipo de recurso. Para conjuntos de disponibilidade, o provedor de recursos é **Microsoft.Compute** e o tipo de recurso é **availabilitySets**. Você pode obter a lista de provedores de recursos disponíveis, execute o seguinte comando do PowerShell:

```powershell
    Get-AzureRmResourceProvider -ListAvailable
```

Ou, se você estiver usando o Azure CLI, você pode executar o seguinte comando:
```
    azure provider list
```
Considerando que neste tópico que você está criando com contas de armazenamento, máquinas virtuais e rede virtual, você irá trabalhar com:

- Microsoft.Storage
- Microsoft.Compute
- Microsoft.Network

Para ver os tipos de recursos para um determinado provedor, execute o seguinte comando do PowerShell:

```powershell
    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

Ou, para CLI do Azure, o seguinte comando irá retornar os tipos disponíveis no formato JSON e salvá-lo em um arquivo.

```
    azure provider show Microsoft.Compute --json > c:\temp.json
```

Você deve ver **availabilitySets** como um dos tipos de dentro de **Microsoft.Compute**. O nome completo do tipo é **Microsoft.Compute/availabilitySets**. Você pode determinar o nome do tipo de recurso para qualquer um dos recursos no modelo de você.

## <a name="public-ip"></a>IP público
Defina um endereço IP público. Novamente, examine a [API REST para endereços IP públicos](https://msdn.microsoft.com/library/azure/mt163590.aspx) para as propriedades definir.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

O método de alocação está definido como **dinâmico** , mas você pode configurá-lo para o valor que você precisa ou configurá-lo para aceitar um valor de parâmetro. Você habilitou os usuários do modelo para passar um valor para o rótulo de nome de domínio.

Agora, vamos observar como determinar a **apiVersion**. O valor especificado simplesmente corresponde à versão da API REST que você deseja usar ao criar o recurso. Portanto, você pode examinar a documentação da API REST para esse tipo de recurso. Ou, você pode executar o seguinte comando do PowerShell para um tipo específico.

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions
```
Que retorna os seguintes valores:

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Para ver as versões de API com CLI do Azure, execute o mesmo comando **Mostrar provedor azure** mostrado anteriormente.

Ao criar um novo modelo, escolha a versão mais recente da API.

## <a name="virtual-network-and-subnet"></a>Sub-rede e uma rede virtual
Crie uma rede virtual com uma sub-rede. Examine a [API REST para redes virtuais](https://msdn.microsoft.com/library/azure/mt163661.aspx) para todas as propriedades definir.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## <a name="load-balancer"></a>Balanceador de carga
Agora, você criará um balanceador de carga opostas externos. Porque este balanceador de carga usa o endereço IP público, você deve declarar uma dependência no endereço IP público na seção **dependsOn** . Isso significa que o balanceador de carga não é implantado até que o endereço IP público tenha terminado de implantação. Sem definir essa dependência, você receberá um erro porque Resource Manager tentará implantar os recursos em paralelo e tentará definir balanceador de carga para endereço IP público que ainda não existe. 

Você também criará um pool de endereços de back-end, algumas regras de NAT de entrada para RDP em VMs e uma regra de balanceamento de carga com um teste de tcp na porta 80 nessa definição de recurso. Check-out da [API REST para balanceador de carga](https://msdn.microsoft.com/library/azure/mt163574.aspx) para todas as propriedades.

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## <a name="network-interface"></a>Interface de rede
Você criará 2 interfaces de rede, uma para cada máquina virtual. Em vez de ter que inclui entradas duplicadas para as interfaces de rede, você pode usar a [função copyIndex()](resource-group-create-multiple.md) iteramos sobre o loop de cópia (referido como nicLoop) e criar as interfaces de rede números conforme definido na `numberOfInstances` variáveis. A interface de rede depende de criação de rede virtual e balanceador de carga. Ele usa a sub-rede definida na criação de uma rede virtual e a id de Balanceador de carga para configurar o pool de endereços de Balanceador de carga e as regras NAT de entrada.
Examine a [API REST para interfaces de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx) para todas as propriedades.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## <a name="virtual-machine"></a>Máquina virtual
Você irá criar máquinas virtuais 2, usando a função de copyIndex(), que você fez na criação de [interfaces de rede](#network-interface).
A criação de máquina virtual depende da conta de armazenamento, conjunto de interface e a disponibilidade de rede. Esta máquina virtual será criado de uma imagem de mercado, conforme definido na `storageProfile` propriedade - `imageReference` é usado para definir o publisher de imagem, a oferta, o sku e a versão. Finalmente, um perfil de diagnóstico está configurado para habilitar o diagnóstico para a máquina virtual. 

Para localizar as propriedades relevantes para uma imagem de marketplace, siga os artigos [Selecione imagens da máquina virtual Linux](./virtual-machines/virtual-machines-linux-cli-ps-findimage.md) ou [imagens de máquina virtual do Windows](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md) .

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

>[AZURE.NOTE] Para imagens publicadas por **fornecedores 3º**, você precisará especificar outra propriedade denominada `plan`. Um exemplo pode ser encontrado em [Este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) da Galeria de início rápido. 

Terminar de definir os recursos para o seu modelo.

## <a name="parameters"></a>Parâmetros

Na seção parâmetros, defina os valores que podem ser especificados ao implantar o modelo. Só Defina parâmetros para os valores que você acha que deve ser variados durante a implantação. Você pode fornecer um valor padrão para um parâmetro que é usado se não for fornecido durante a implantação. Você também pode definir os valores permitidos conforme mostrado para o parâmetro **imageSKU** .

```json
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## <a name="variables"></a>Variáveis

Na seção variáveis, você pode definir valores que são usados em mais de um local no seu modelo ou valores que são criados de outras expressões ou variáveis. Variáveis são usados para simplificar a sintaxe do modelo.

```json
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```

Você concluiu o modelo! Você pode comparar o seu modelo em relação ao modelo completo na [Galeria de início rápido](https://github.com/Azure/azure-quickstart-templates) em [2 VMs com balanceador de carga e carregar o modelo de regras de Balanceador](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules). Seu modelo pode ser ligeiramente diferentes com base em usando números de versão diferentes. 

Novamente, você pode implantar o modelo, usando os mesmos comandos que você usou quando implantando a conta de armazenamento. Você não precisa excluir a conta de armazenamento antes de implantar novamente porque o Gerenciador de recursos ignorará recriação recursos que já existem e não foram alterados.

## <a name="next-steps"></a>Próximas etapas

- [Visualizador de modelo de Gerenciador de recursos do Azure (ARMViz)](http://armviz.io/#/) é uma ótima ferramenta para visualizar modelos ARM, conforme eles poderão ficar muito grandes para entender apenas de leitura do arquivo de json.
- Para saber mais sobre a estrutura de um modelo, consulte [modelos de coautoria Gerenciador de recursos do Azure](resource-group-authoring-templates.md).
- Para saber sobre como implantar um modelo, consulte [implantar um grupo de recursos com o modelo do Gerenciador de recursos do Azure](resource-group-template-deploy.md)
