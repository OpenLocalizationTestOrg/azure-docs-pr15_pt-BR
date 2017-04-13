<properties 
   pageTitle="Gerenciar o uso do PowerShell no Gerenciador de recursos de NSGs | Microsoft Azure"
   description="Saiba como gerenciar existente NSGs usando o PowerShell no Gerenciador de recursos"
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
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-powershell"></a>Gerenciar NSGs usando o PowerShell

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="retrieve-information"></a>Recuperar informações

Você pode exibir seu NSGs existentes, recuperar regras para um NSG existente e descobrir quais recursos um NSG está associado a.

### <a name="view-existing-nsgs"></a>Modo de exibição existentes NSGs
Para exibir todos os NSGs existentes em uma assinatura, execute o `Get-AzureRmNetworkSecurityGroup` cmdlet conforme mostrado abaixo.

    Get-AzureRmNetworkSecurityGroup

Resultado esperado:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                         
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
    
    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
                            
    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


Para exibir a lista de NSGs em um grupo de recursos específica, execute o `Get-AzureRmNetworkSecurityGroup` cmdlet conforme mostrado abaixo. 

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

Saída esperada:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                         
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
    
    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
         
### <a name="list-all-rules-for-an-nsg"></a>Lista todas as regras para um NSG

Para exibir as regras de um NSG chamado **NSG FrontEnd**, execute o `Get-AzureRmNetworkSecurityGroup` cmdlet conforme mostrado abaixo. 

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

Saída esperada:
    
    Name                     : rdp-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                        Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow RDP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 100
    Direction                : Inbound
    
    Name                     : web-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                        Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow HTTP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 80
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 101
    Direction                : Inbound

>[AZURE.NOTE] Você também pode usar `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` para listar as regras padrão de **FrontEnd NSG** NSG.

### <a name="view-nsgs-associations"></a>Exibir associações de NSGs

Para exibir quais recursos o NSG **NSG FrontEnd** é associar, execute o `Get-AzureRmNetworkSecurityGroup` cmdlet conforme mostrado abaixo.

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

Procure as propriedades **NetworkInterfaces** e **sub-redes** conforme mostrado abaixo:

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

No exemplo acima, o NSG não está associado a nenhuma interface de rede (NICs) e ele está associado a uma sub-rede denominada **FrontEnd**.

## <a name="manage-rules"></a>Gerenciar regras

Você pode adicionar regras para um NSG existente, editar regras existentes e remover regras.

### <a name="add-a-rule"></a>Adicionar uma regra

Para adicionar uma regra permitindo tráfego de **entrada** à porta **443** de qualquer máquina para **FrontEnd NSG** NSG, siga as etapas abaixo.

1. Executar o `Get-AzureRmNetworkSecurityGroup` cmdlet para recuperar o NSG existente e armazená-la em uma variável, conforme mostrado abaixo.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Executar o `Add-AzureRmNetworkSecurityRuleConfig` cmdlet, conforme mostrado abaixo.

        Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange * `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443

3. Para salvar as alterações feitas a NSG, execute o `Set-AzureRmNetworkSecurityGroup` cmdlet conforme mostrado abaixo.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Saída esperada mostrando apenas as regras de segurança:

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "*",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="change-a-rule"></a>Alterar uma regra

Para alterar a regra criada acima para permitir o tráfego de entrada de apenas à **Internet** , siga as etapas abaixo.

1. Executar o `Get-AzureRmNetworkSecurityGroup` cmdlet para recuperar o NSG existente e armazená-la em uma variável, conforme mostrado abaixo.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Executar o `Set-AzureRmNetworkSecurityRuleConfig` cmdlet, conforme mostrado abaixo.

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange Internet `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443

3. Para salvar as alterações feitas a NSG, execute o `Set-AzureRmNetworkSecurityGroup` cmdlet conforme mostrado abaixo.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Saída esperada mostrando apenas as regras de segurança:

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="delete-a-rule"></a>Excluir uma regra

1. Executar o `Get-AzureRmNetworkSecurityGroup` cmdlet para recuperar o NSG existente e armazená-la em uma variável, conforme mostrado abaixo.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Executar o `Remove-AzureRmNetworkSecurityRuleConfig` cmdlet, conforme mostrado abaixo.

        Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule

3. Para salvar as alterações feitas a NSG, execute o `Set-AzureRmNetworkSecurityGroup` cmdlet conforme mostrado abaixo.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Saída esperada mostrando apenas as regras de segurança, observe que a **regra de https** não está mais listado:

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 }
                               ]

## <a name="manage-associations"></a>Gerenciar associações

Você pode associar um NSG para sub-redes e placas de rede. Você também pode dissociar um NSG de qualquer recurso que ele está associado.

### <a name="associate-an-nsg-to-a-nic"></a>Associar um NSG a uma NIC

Para associar o **FrontEnd NSG** NSG para **TestNICWeb1** NIC, siga as etapas abaixo.

1. Executar o `Get-AzureRmNetworkSecurityGroup` cmdlet para recuperar o NSG existente e armazená-la em uma variável, conforme mostrado abaixo.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Executar o `Get-AzureRmNetworkInterface` cmdlet para recuperar a NIC existente e armazená-la em uma variável, conforme mostrado abaixo.

        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1

3. Defina a propriedade de **NetworkSecurityGroup** da variável **NIC** para o valor da variável **NSG** , conforme mostrado abaixo.

        $nic.NetworkSecurityGroup = $nsg

4. Para salvar as alterações feitas a NIC, execute o `Set-AzureRmNetworkInterface` cmdlet conforme mostrado abaixo.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Saída esperada mostrando apenas a propriedade **NetworkSecurityGroup** :

        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### <a name="dissociate-an-nsg-from-a-nic"></a>Dissociar um NSG de uma NIC

Para dissociar **FrontEnd NSG** NSG de **TestNICWeb1** NIC, siga as etapas abaixo.

1. Executar o `Get-AzureRmNetworkSecurityGroup` cmdlet para recuperar o NSG existente e armazená-la em uma variável, conforme mostrado abaixo.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Executar o `Get-AzureRmNetworkInterface` cmdlet para recuperar a NIC existente e armazená-la em uma variável, conforme mostrado abaixo.

        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1

3. Defina a propriedade de **NetworkSecurityGroup** da variável **NIC** como **$null**, conforme mostrado abaixo.

        $nic.NetworkSecurityGroup = $null

4. Para salvar as alterações feitas a NIC, execute o `Set-AzureRmNetworkInterface` cmdlet conforme mostrado abaixo.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Saída esperada mostrando apenas a propriedade **NetworkSecurityGroup** :

        NetworkSecurityGroup : null

### <a name="dissociate-an-nsg-from-a-subnet"></a>Dissociar um NSG de uma sub-rede

Para dissociar **FrontEnd NSG** NSG da sub-rede **FrontEnd** , siga as etapas abaixo.

1. Executar o `Get-AzureRmVirtualNetwork` cmdlet para recuperar o VNet existente e armazená-la em uma variável, conforme mostrado abaixo.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet

2. Executar o `Get-AzureRmVirtualNetworkSubnetConfig` cmdlet para recuperar a sub-rede **FrontEnd** e armazená-la em uma variável, conforme mostrado abaixo.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 

3. Defina a propriedade de **NetworkSecurityGroup** da variável **sub-rede** como **$null**, conforme mostrado abaixo.

        $subnet.NetworkSecurityGroup = $null

4. Para salvar as alterações feitas à sub-rede, execute o `Set-AzureRmVirtualNetwork` cmdlet conforme mostrado abaixo.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Saída esperada mostrando apenas as propriedades da sub-rede **FrontEnd** . Observe que há não é uma propriedade de **NetworkSecurityGroup**:

            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### <a name="associate-an-nsg-to-a-subnet"></a>Associar um NSG a uma sub-rede

Para associar o **FrontEnd NSG** NSG à sub-rede **FronEnd** novamente, siga as etapas abaixo.

1. Executar o `Get-AzureRmVirtualNetwork` cmdlet para recuperar o VNet existente e armazená-la em uma variável, conforme mostrado abaixo.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet

2. Executar o `Get-AzureRmVirtualNetworkSubnetConfig` cmdlet para recuperar a sub-rede **FrontEnd** e armazená-la em uma variável, conforme mostrado abaixo.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 

1. Executar o `Get-AzureRmNetworkSecurityGroup` cmdlet para recuperar o NSG existente e armazená-la em uma variável, conforme mostrado abaixo.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

3. Defina a propriedade de **NetworkSecurityGroup** da variável **sub-rede** como **$null**, conforme mostrado abaixo.

        $subnet.NetworkSecurityGroup = $nsg

4. Para salvar as alterações feitas à sub-rede, execute o `Set-AzureRmVirtualNetwork` cmdlet conforme mostrado abaixo.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Esperado mostrando apenas a propriedade **NetworkSecurityGroup** da sub-rede **FrontEnd** de saída:

        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## <a name="delete-an-nsg"></a>Excluir um NSG

Você só pode excluir uma NSG se ele não tiver associado a qualquer recurso. Para excluir um NSG, siga as etapas abaixo.

1. Para verificar os recursos associados a um NSG, execute o `azure network nsg show` conforme mostrado na [exibição NSGs associações](#View-NSGs-associations).
2. Se o NSG estiver associado a qualquer NICs, execute o `azure network nic set` conforme mostrado no [Dissociate um NSG de uma NIC](#Dissociate-an-NSG-from-a-NIC) para cada NIC. 
3. Se o NSG estiver associado a qualquer sub-rede, execute o `azure network vnet subnet set` conforme mostrado na [Dissociate um NSG de uma sub-rede](#Dissociate-an-NSG-from-a-subnet) para cada sub-rede.
4. Para excluir o NSG, execute o `Remove-AzureRmNetworkSecurityGroup` cmdlet conforme mostrado abaixo.

        Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force

    >[AZURE.NOTE] O **-força** parâmetro garante que você não precisa confirmar a exclusão.

## <a name="next-steps"></a>Próximas etapas

- [Habilitar o log](virtual-network-nsg-manage-log.md) para NSGs.