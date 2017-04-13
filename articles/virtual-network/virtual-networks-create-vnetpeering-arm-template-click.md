<properties
   pageTitle="Criar VNet correspondência usando o recurso Gerenciador de modelos | Microsoft Azure"
   description="Aprenda a criar uma rede virtual correspondência usando os modelos no Gerenciador de recursos."
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai;annahar"/>

# <a name="create-vnet-peering-using-resource-manager-templates"></a>Criar VNet correspondência usando modelos do Gerenciador de recursos

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para criar um VNet correspondência usando modelos do Gerenciador de recursos, siga as etapas abaixo:

1. Se você nunca usou o PowerShell do Azure, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) e siga as instruções totalmente ao final para entrar no Azure e selecione sua assinatura.

    > [AZURE.NOTE] O cmdlet do PowerShell para gerenciar a correspondência de VNet é fornecido com o [Azure PowerShell 1,6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. O texto abaixo mostra a definição de um link de correspondência VNet para VNet1 para VNet2, baseado no cenário acima. Copiar o conteúdo abaixo e salve-o em um arquivo denominado VNetPeeringVNet1.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }

3. A seção a seguir mostra a definição de um link de correspondência VNet para VNet2 para VNet1, baseado no cenário acima.  Copiar o conteúdo abaixo e salve-o em um arquivo denominado VNetPeeringVNet2.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }

    Como visto no modelo acima, há algumas propriedades configuráveis para correspondência de VNet:

  	|Opção|Descrição|Padrão|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Se é ou não o espaço de endereço de um ponto VNet incluído como parte da marca virtual_network.|Sim|
  	|AllowForwardedTraffic|Se o tráfego não provenientes um VNet peered for aceito ou descartado.|Não|
  	|AllowGatewayTransit|Permite que o ponto VNet para usar seu gateway VNet.|Não|
  	|UseRemoteGateways|Use o gateway de VNet do seu correspondente. O ponto VNet deve ter um gateway configurado e AllowGatewayTransit selecionado. Você não pode usar esta opção se você tiver um gateway configurado.|Não|

    Cada link no VNet correspondência tem o conjunto de propriedades acima. Por exemplo, você pode definir AllowVirtualNetworkAccess como True para VNet aos link VNet1 para VNet2 e defina-a False para o link de correspondência VNet na outra direção.


4. Para implantar o arquivo de modelo, você pode executar o cmdlet New-AzureRmResourceGroupDeployment para criar ou atualizar a implantação. Para obter mais informações sobre como usar o recurso Gerenciador de modelos, consulte este [artigo](../resource-group-template-deploy.md).

        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all

    > [AZURE.NOTE] Substitua o arquivo de modelo e nome da grupo de recursos conforme apropriado.

    Abaixo está um exemplo com base no cenário acima:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all

    Mostra a saída:

        DeploymentName      : VNetPeeringVNet1
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:05:03 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all

    Mostra a saída:

        DeploymentName      : VNetPeeringVNet2
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:07:22 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

5. Após a implantação, você pode executar o cmdlet abaixo para exibir o estado de correspondência:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2

    Mostra a saída:

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : VNet101
        VirtualNetworkName  : VNet1
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Depois de correspondência for estabelecida neste cenário, você deve ser capaz de iniciar as conexões de qualquer máquina virtual para qualquer máquina virtual em ambos os VNets. Por padrão, AllowVirtualNetworkAccess for verdadeiro e VNet correspondência será provisionar os ACLs adequadas para permitir a comunicação entre VNets. Você ainda pode aplicar regras de grupo (NSG) de segurança de rede para bloquear a conectividade entre sub-redes específicas ou máquinas virtuais para obter controle fino do access entre duas redes virtuais.  Para obter mais informações de criação de regras NSG, consulte este [artigo](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para criar um VNet correspondência em assinaturas, siga as etapas abaixo:

1. Entrar no Azure com privilégios usuário-A da conta na assinatura-A e execute o seguinte cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

    Isso não é um requisito, correspondência pode ser estabelecida mesmo se usuários individualmente geram solicitações de correspondência para seus respectivos Vnets desde que as solicitações correspondem. Adicionar um usuário com privilégios da outro VNet como usuários no VNet local facilita fazer a instalação.

2. Entrar no Azure com a conta com privilégios-do usuário B para assinatura-B e execute o seguinte cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. No usuário-A é sessão de login, executar este cmdlet:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all

    Veja aqui como o arquivo JSON é definido.  

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }

4. Na sessão de logon do usuário-B, execute o seguinte cmdlet:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all

    Veja aqui como o arquivo JSON é definido:

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }

    Depois de correspondência for estabelecida neste cenário, você deve ser capaz de iniciar as conexões de qualquer máquina virtual para qualquer máquina virtual do ambas as VNets em diferentes assinaturas.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Neste cenário, você pode implantar o modelo de exemplo abaixo para estabelecer a correspondência de VNet.  Você precisará definir a propriedade AllowForwardedTraffic como True, que permite que o equipamento de rede virtual a VNet peered para enviar e receber tráfego.

    Aqui está o modelo para criar um VNet correspondência de HubVNet para VNet1. Observe que AllowForwardedTraffic é definida como false.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
            }
        ]
        }

2. Aqui está o modelo para criar um VNet correspondência de VNet1 para HubVnet. Observe que AllowForwardedTraffic está definido como true.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }


3. Depois de correspondência for estabelecida, você pode consultar a deste [artigo](virtual-network-create-udr-arm-ps.md) definir routes(UDR) definidas pelo usuário para redirecionar o tráfego de VNet1 por meio de um dispositivo virtual para usar seus recursos. Quando você especifica o endereço do próximo salto no roteiro, você pode defini-lo para o endereço IP do dispositivo virtual no ponto VNet HubVNet.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Para criar uma correspondência entre redes virtuais de diferentes modelos de implantação, siga as etapas abaixo:
1. O texto abaixo mostra a definição de um link de correspondência VNet para VNET1 para VNET2 neste cenário. Somente um link é necessária para uma rede virtual clássica a uma rede virtual do Azure recurso Gerenciador de mesmo nível.

    Certifique-se de colocar em sua identificação de assinatura para onde a rede virtual clássica ou VNET2 está localizado e altere MyResouceGroup para o nome do grupo de recursos apropriado.

    {"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", "contentVersion": "1.0.0.0", "parâmetros": {}, "variáveis": {}, "recursos": [{"apiVersion": "2016-06-01", "tipo": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings", "nome": "VNET1/LinkToVNET2", "local": "[resourceGroup () .location]", "propriedades": {"allowVirtualNetworkAccess": verdadeiro, "allowForwardedTraffic": falso, "allowGatewayTransit": "useRemoteGateways" false,: falso, "remoteVirtualNetwork": {"id": "[identificação de recurso (' Microsoft.ClassicNetwork/virtualNetworks', 'VNET2')]"}}}]}

2. Para implantar o arquivo de modelo, execute o cmdlet a seguir para criar ou atualizar a implantação.

        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all

        Output shows:

        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

3. Após a implantação estiver concluído, você pode executar o cmdlet a seguir para exibir o estado de correspondência:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2

        Output shows:

        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

Depois de correspondência é estabelecida entre um VNet clássico e um gerente de recursos VNet, você deve ser capaz de iniciar conexões de qualquer máquina virtual no VNET1 para qualquer máquina virtual no VNET2 e vice-versa.
