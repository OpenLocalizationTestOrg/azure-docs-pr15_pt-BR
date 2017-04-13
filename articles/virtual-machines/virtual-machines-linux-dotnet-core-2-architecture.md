<properties
   pageTitle="Implantando recursos com Azure modelos do Gerenciador de recursos de computação | Microsoft Azure"
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

# <a name="application-architecture-with-azure-resource-manager-templates"></a>Arquitetura de aplicativo com modelos do Gerenciador de recursos do Azure

Ao desenvolver uma implantação do Gerenciador de recursos do Azure, calcule requisitos precisam ser mapeados para serviços e recursos Azure. Se um aplicativo consistir em vários pontos de extremidade de http, um banco de dados e um serviço de cache de dados, os recursos Azure que hospedam cada desses componentes deve ser racional. Por exemplo, o aplicativo de armazenamento de música de exemplo inclui um aplicativo web que está hospedado em um máquina virtual e um banco de dados do SQL, que está hospedado no banco de dados do SQL Azure. 

Este documento detalha como os recursos de computação de armazenamento de música são configurados no modelo do Gerenciador de recursos do Azure amostra. Todas as dependências e configurações exclusivas são realçadas. Para obter a melhor experiência, previamente implante uma instância da solução à sua assinatura do Azure e trabalhar junto com o modelo do Gerenciador de recursos do Azure. O modelo completo pode ser encontrado aqui – [Implantação de repositório de música em Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="virtual-machine"></a>Máquina virtual

O aplicativo de armazenamento de música inclui um aplicativo web onde os clientes podem navegar e comprar músicas. Embora existam vários serviços Azure que podem hospedar aplicativos web, neste exemplo, uma máquina Virtual é usado. Usando o modelo de armazenamento de música de exemplo, uma máquina virtual é implantada, instalar de um servidor web e o site de repositório de música instalado e configurado. Neste artigo, somente a implantação de máquina virtual é detalhada. A configuração do servidor web e o aplicativo é detalhada em um artigo posterior.

Uma máquina virtual podem ser adicionada a um modelo usando o assistente Adicionar novo recurso do Visual Studio ou inserindo JSON válido para o modelo de implantação. Ao implantar uma máquina virtual, também são necessários vários recursos relacionados. Se usando o Visual Studio para criar o modelo, esses recursos são criados por você. Se construir manualmente o modelo, esses recursos precisam ser inserido e configurado.

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [JSON de máquina Virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L295).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "tags": {
    "displayName": "virtual-machine"
  },
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('vhdStorageName'))]",
    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]",
    "nicLoop"
  ],
  "properties": {
    "availabilitySet": {
      "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
    },
      ........<truncated>  
    }
```

Uma vez implantado, as propriedades de máquina virtual podem ser vistas no portal do Azure.

![Máquina virtual](./media/virtual-machines-linux-dotnet-core/vm.png)

## <a name="storage-account"></a>Conta de armazenamento

Contas de armazenamento tem muitas opções de armazenamento e recursos. Para o contexto de máquinas virtuais do Azure, uma conta de armazenamento mantém as unidades de disco rígido virtuais de máquina virtual e qualquer disco de dados adicionais. O exemplo de armazenamento de música inclui uma conta de armazenamento para manter a unidade de disco rígida virtual de cada máquina virtual na implantação. 

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [Conta de armazenamento](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L109).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('vhdStorageName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "storage-account"
  },
  "properties": {
    "accountType": "[variables('vhdStorageType')]"
  }
}
```

Uma conta de armazenamento é associar com uma máquina virtual dentro da declaração de modelo do Gerenciador de recursos da máquina virtual. 

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [associação de máquina Virtual e conta de armazenamento](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L341).

```none
"osDisk": {
  "name": "osdisk",
  "vhd": {
    "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/',variables('vhdStorageName')), '2015-06-15').primaryEndpoints.blob,'vhds/osdisk', copyindex(), '.vhd')]"
  },
  "caching": "ReadWrite",
  "createOption": "FromImage"
}
```

Após a implantação, a conta de armazenamento pode ser exibida no portal do Azure.

![Conta de armazenamento](./media/virtual-machines-linux-dotnet-core/storacct.png)

Clicar no contêiner de armazenamento de blob de conta, o arquivo de disco rígido virtual para cada máquina virtual implantado com o modelo pode ser visto.

![Unidades de disco rígido virtuais](./media/virtual-machines-linux-dotnet-core/vhd.png)

Para obter mais informações sobre o armazenamento do Azure, consulte a [documentação de armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/).

## <a name="virtual-network"></a>Rede virtual

Se uma máquina virtual requer uma rede interna como a capacidade de se comunicar com outros recursos Azure e máquinas virtuais, uma rede Virtual do Azure é necessária.  Uma rede virtual não faz a máquina virtual acessível pela internet. Conectividade a redes públicas requer um endereço IP público, que é detalhado posteriormente nesta série.

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [rede Virtual e sub-redes](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L136).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroup'))]"
  ],
  "tags": {
    "displayName": "virtual-network"
  },
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/24"
      ]
    },
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
    ]
  }
}
```

Azure no portal do, a rede virtual se parece com a imagem a seguir. Observe que todas as máquinas virtuais implantadas com o modelo estão conectadas à rede virtual.

![Rede virtual](./media/virtual-machines-linux-dotnet-core/vnet.png)

## <a name="network-interface"></a>Interface de rede

 Uma interface de rede se conecta a uma máquina virtual a uma rede virtual, mais especificamente a uma sub-rede definida na rede virtual. 
 
 Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [Interface de rede](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L166).
 
```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/networkInterfaces",
  "name": "[concat(variables('networkInterfaceNamePrefix'), copyindex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-interface"
  },
  "copy": {
    "name": "nicLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
    "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIpAddressName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'), '/inboundNatRules/', 'SSH-VM', copyIndex())]"
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
              "id": "[variables('lbPoolID')]"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "[concat(variables('lbID'),'/inboundNatRules/SSH-VM', copyIndex())]"
            }
          ]
        }
      }
    ]
  }
}
```

Cada recurso de máquina virtual inclui um perfil de rede. A interface de rede está associada a máquina virtual neste perfil.  

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [Perfil de rede de máquina Virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L350).


```none
"networkProfile": {
  "networkInterfaces": [
    {
      "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('networkInterfaceNamePrefix'), copyindex()))]"
    }
  ]
}
```

Azure no portal do, a interface de rede se parece com a imagem a seguir. O endereço IP interno e a associação de máquina virtual podem ser vistos no recurso de interface de rede.

![Interface de rede](./media/virtual-machines-linux-dotnet-core/nic.png)

Para obter mais informações sobre redes virtuais do Azure, consulte a [documentação de rede Virtual do Azure](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="azure-sql-database"></a>Banco de dados do SQL Azure

Além de uma máquina virtual hospedando o site de repositório de música, um banco de dados do SQL Azure é implantado para hospedar o banco de dados do repositório de música. A vantagem de usar o banco de dados do SQL Azure aqui é que um segundo conjunto de máquinas virtuais não é necessário, e a escala e disponibilidade interna do serviço.

Um banco de dados do SQL Azure pode ser adicionado usando o Visual Studio adicionar novo recurso Assistente, ou inserindo JSON válida em um modelo. Recurso do SQL Server inclui um nome de usuário e senha que é concedida direitos administrativos na instância do SQL. Além disso, um recurso de firewall do SQL é adicionado. Por padrão, aplicativos hospedados no Azure são capazes de se conectar com a instância do SQL. Para permitir que o aplicativo externo um studio de gerenciamento do SQL Server para se conectar à instância do SQL, o firewall precisa ser configurado. Para a demonstração de repositório de música, a configuração padrão está corretamente configurada. 

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [DB do SQL Azure](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L401).


```none
{
  "apiVersion": "2014-04-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "[variables('musicStoreSqlName')]",
  "location": "[resourceGroup().location]",

  "dependsOn": [],
  "tags": {
    "displayName": "sql-music-store"
  },
  "properties": {
    "administratorLogin": "[parameters('adminUsername')]",
    "administratorLoginPassword": "[parameters('sqlAdminPassword')]"
  },
  "resources": [
    {
      "apiVersion": "2014-04-01-preview",
      "type": "firewallrules",
      "name": "firewall-allow-azure",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('musicStoreSqlName'))]"
      ],
      "properties": {
        "startIpAddress": "0.0.0.0",
        "endIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Um modo de exibição do banco de dados de MusicStore conforme visto no portal do Azure e SQL server.

![SQL Server](./media/virtual-machines-linux-dotnet-core/sql.png)

Para obter mais informações sobre como implantar o banco de dados do SQL Azure, consulte [a documentação do Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).

## <a name="next-step"></a>Próxima etapa

<hr>

[Etapa 2 - segurança em modelos de Gerenciador de recursos do Azure e acesso](./virtual-machines-linux-dotnet-core-3-access-security.md)
