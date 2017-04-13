<properties
   pageTitle="Acesso e segurança em modelos de Gerenciador de recursos Azure | Microsoft Azure" 
   description="Tutorial do Azure Máquina Virtual DotNet Core"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# <a name="access-and-security-in-azure-resource-manager-templates"></a>Acesso e segurança em modelos do Gerenciador de recursos do Azure

Aplicativos hospedados no Azure provavelmente precisam estar acesso pela internet ou por uma VPN / conexão via expressa com o Azure. Com a amostra de aplicativo do repositório de música, o site é disponibilizado na internet com um endereço IP público. Com acesso estabelecido, conexões com o aplicativo e acessar os recursos de máquina virtual próprios devem ser protegidos. Essa segurança do access é fornecida com um grupo de segurança de rede. 

Este documento detalha como o aplicativo do repositório de música é protegido no modelo do Gerenciador de recursos do Azure amostra. Todas as dependências e configurações exclusivas são realçadas. Para obter a melhor experiência, previamente implante uma instância da solução à sua assinatura do Azure e trabalhar junto com o modelo do Gerenciador de recursos do Azure. O modelo completo pode ser encontrado aqui – [Implantação de repositório de música em Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


## <a name="public-ip-address"></a>Endereço IP público

Para fornecer acesso público a um recurso Azure, um recurso de endereço IP público pode ser usado. Endereço IP público pode ser configurado com um endereço IP estático ou dinâmico. Se um endereço dinâmico é usado e a máquina virtual for interrompida e desalocada, os endereços será removido. Quando o computador for iniciado novamente, ele pode ser atribuído um endereço IP público diferente. Para impedir a alteração de um endereço IP, um endereço IP reservado pode ser usado. 

Um endereço IP público podem ser adicionado a um modelo do Gerenciador de recursos do Azure usando o Visual Studio assistente Adicionar novo recurso ou inserindo JSON válida em um modelo. 

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [Endereço IP público](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L121).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicipaddressName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "public-ip-front"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

Um endereço IP público podem ser associado um adaptador de rede Virtual ou um balanceador de carga. Neste exemplo, porque o site de repositório de música é carga equilibrada várias máquinas virtuais, o endereço IP público está conectado ao balanceador de carga.

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [associação de endereço IP público com balanceador de carga](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208).

```none
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicipaddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

O endereço IP público como visto do portal do Azure. Observe que o endereço IP público está associado a um balanceador de carga e não uma máquina virtual. Balanceadores de carga de rede estão detalhados no próximo documento desta série.

![Endereço IP público](./media/virtual-machines-linux-dotnet-core/pubip.png)

Para obter mais informações sobre endereços de IP públicos do Azure, consulte [endereços IP no Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).

## <a name="network-security-group"></a>Grupo de segurança de rede

Quando tiver sido estabelecido acesso aos recursos Azure, esse acesso deve ser limitado. Para máquinas virtuais Azure, acesso seguro é realizado usando um grupo de segurança de rede. Com a amostra de aplicativo do repositório de música, todo o acesso à máquina virtual é restrito, exceto para porta 80 para acesso http e porta 22 para acesso SSH sobre. Um grupo de segurança de rede podem ser adicionado a um modelo do Gerenciador de recursos do Azure usando o Visual Studio assistente Adicionar novo recurso ou inserindo JSON válida em um modelo.

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [Grupo de segurança de rede](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L68).

```none
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('nsgfront')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "nsg-front"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
}
```

Neste exemplo, o grupo de segurança de rede está associado com o objeto de sub-rede declarado no recurso de rede Virtual. 

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [associação de grupo de segurança de rede com uma rede Virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L158).


```none
"subnets": [
  {
    "name": "[variables('subnetName')]",
    "properties": {
      "addressPrefix": "10.0.0.0/24",
      "networkSecurityGroup": {
        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
      }
    }
  }
```

Veja como fica o grupo de segurança de rede do portal do Azure. Observe que uma NSG pode associar uma interface sub-rede e / ou rede. Nesse caso, o NSG está associado a uma sub-rede. Nessa configuração, as regras de entrada aplicam a todas as máquinas virtuais conectadas à sub-rede.

![Grupo de segurança de rede](./media/virtual-machines-linux-dotnet-core/nsg.png)

Para obter informações detalhadas sobre os grupos de segurança de rede, consulte [o que é um grupo de segurança de rede]( https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/).

## <a name="next-step"></a>Próxima etapa

<hr>

[Etapa 3 - disponibilidade e escala no Azure modelos do Gerenciador de recursos](./virtual-machines-linux-dotnet-core-4-availability-scale.md)
