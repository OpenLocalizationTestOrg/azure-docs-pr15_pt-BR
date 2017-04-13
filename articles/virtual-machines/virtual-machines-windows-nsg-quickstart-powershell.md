<properties
   pageTitle="Abrir portas para uma máquina virtual usando o PowerShell | Microsoft Azure"
   description="Saiba como abrir uma porta / crie um ponto de extremidade para sua máquina de virtual do Windows usando o modo de implantação do Gerenciador de recursos do Azure e o Azure PowerShell"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Abrindo portas e pontos de extremidade para uma máquina virtual no Azure usando o PowerShell
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandos rápidos
Para criar um grupo de segurança de rede e ACL regras é necessário [a versão mais recente do PowerShell do Azure instalado](../powershell-install-configure.md). Você também pode [executar essas etapas usando o portal do Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Faça logon em sua conta do Azure:

```powershell
Login-AzureRmAccount
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Nomes de parâmetro de exemplo incluídos `myResourceGroup`, `myNetworkSecurityGroup`, e `myVnet`.

Crie uma regra. O exemplo a seguir cria uma regra chamada `myNetworkSecurityGroupRule` para permitir o tráfego TCP na porta 80:

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" -Access "Allow" -Protocol "Tcp" -Direction "Inbound" `
    -Priority "100" -SourceAddressPrefix "Internet" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

Em seguida, criar o grupo de segurança de rede e atribuir a regra HTTP recém-criado da seguinte maneira. O exemplo a seguir cria um grupo de segurança de rede chamado `myNetworkSecurityGroup`:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNetworkSecurityGroup" -SecurityRules $httprule
```

Agora vamos atribuir seu grupo de segurança de rede para uma sub-rede. O exemplo a seguir atribui uma rede virtual existente chamada `myVnet` à variável `$vnet`:

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associe seu grupo de segurança de rede com sua sub-rede. O exemplo a seguir associa a sub-rede denominada `mySubnet` com seu grupo de segurança de rede:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Finalmente, atualize sua rede virtual na ordem para que as alterações tenham efeito:

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Obter mais informações sobre grupos de segurança de rede
Os comandos rápidos aqui permitem que você começar a trabalhar com tráfego fluindo para sua máquina virtual. Grupos de segurança de rede fornecem muitos recursos excelentes e detalhamento para controlar o acesso aos seus recursos. Você pode ler mais sobre a [criação de um grupo de segurança de rede e regras ACL aqui](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Você pode definir grupos de segurança de rede e regras ACL como parte dos modelos do Gerenciador de recursos do Azure. Leia mais sobre a [criação de grupos de segurança de rede com modelos](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se você precisar usar encaminhamento de porta para mapear uma porta externa exclusiva para uma porta interna em sua máquina virtual, use um balanceador de carga e regras de conversão de endereço de rede (NAT). Por exemplo, você talvez queira expor porta 8080 TCP externamente e ter tráfego direcionado para a porta TCP 80 em uma máquina virtual. Você pode aprender a [criar um balanceador de carga voltado para a Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Próximas etapas
Neste exemplo, você criou uma regra simples para permitir o tráfego HTTP. Você pode encontrar informações sobre como criar ambientes mais detalhadas nos seguintes artigos:

- [Visão geral do Gerenciador de recursos Azure](../azure-resource-manager/resource-group-overview.md)
- [O que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Visão geral do Gerenciador de recursos Azure para balanceadores de carga](../load-balancer/load-balancer-arm.md)