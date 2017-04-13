<properties
   pageTitle="Disponibilidade e escala em modelos de Gerenciador de recursos Azure | Microsoft Azure"
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

# <a name="availability-and-scale-in-azure-resource-manager-templates"></a>Disponibilidade e escala em modelos do Gerenciador de recursos do Azure

Disponibilidade e escala se referir a atividade e a capacidade de atender à demanda. Se um aplicativo deve ser 99,9% das vezes, ela precisa ter uma arquitetura que permite para vários recursos de computação simultâneas. Por exemplo, em vez de ter um único site, uma configuração com um nível maior de disponibilidade inclui várias instâncias do mesmo site, com balanceamento de tecnologia na frente-los. Nessa configuração, uma instância do aplicativo pode ser desligada para manutenção, enquanto o restante continuar a funcionar. Escala por outro lado se refere a uma capacidade de aplicativos para atender a demanda. Com uma carga equilibrado aplicativo, adicionando ou removendo instâncias do pool permite que um aplicativo atender às demanda.

Este documento detalha como a implantação de amostra do repositório de música é configurada para disponibilidade e escala. Todas as dependências e configurações exclusivas são realçadas. Para obter a melhor experiência, previamente implante uma instância da solução à sua assinatura do Azure e trabalhar junto com o modelo do Gerenciador de recursos do Azure. O modelo completo pode ser encontrado aqui – [Implantação de repositório de música em Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="availability-set"></a>Conjunto de disponibilidade

Um conjunto de disponibilidade logicamente abranger máquinas virtuais do Azure hosts físicos e outros componentes base como fontes de alimentação e hardware de rede física. Conjuntos de disponibilidade Certifique-se de que, durante a manutenção, falha de dispositivo ou outro tempo de inatividade, não todas as máquinas virtuais são afetadas. Um conjunto de disponibilidade podem ser adicionado a um modelo do Gerenciador de recursos do Azure usando o Visual Studio assistente Adicionar novo recurso ou inserir JSON válida em um modelo.

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [Disponibilidade definida](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L387).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/availabilitySets",
  "name": "[variables('availabilitySetName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "avalibility-set"
  },
  "properties": {
    "platformUpdateDomainCount": 5,
    "platformFaultDomainCount": 3
  }
}
```

Um conjunto de disponibilidade é declarado como uma propriedade de um recurso de máquina Virtual. 

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [associação disponibilidade definida com Máquina Virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L313).


```none
"properties": {
  "availabilitySet": {
    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
  }
```
A disponibilidade definida como visto a partir do portal do Azure. Cada máquina virtual e detalhes sobre a configuração são detalhados aqui.

![Conjunto de disponibilidade](./media/virtual-machines-linux-dotnet-core/aset.png)

Para obter informações detalhadas sobre conjuntos de disponibilidade, consulte [Gerenciar disponibilidade de máquinas virtuais](./virtual-machines-linux-manage-availability.md). 

## <a name="network-load-balancer"></a>Balanceador de carga de rede

Enquanto um conjunto de disponibilidade oferece tolerância de aplicativo, um balanceador de carga disponibiliza várias instâncias do aplicativo em um único endereço de rede. Várias instâncias de um aplicativo podem ser hospedadas em muitas máquinas virtuais, cada um deles conectado a um balanceador de carga. Quando o aplicativo é acessado balanceador de carga roteia a solicitação de entrada pelos membros anexados. Um balanceador de carga podem ser adicionado usando o Visual Studio assistente Adicionar novo recurso ou inserindo corretamente formatados recurso JSON para o modelo do Gerenciador de recursos do Azure.

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [Balanceador de carga de rede](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers",
  "name": "[variables('loadBalancerName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer-front"
  },
  ........<truncated>
}
```

Porque o aplicativo de exemplo é exposto à internet com um endereço IP público, este endereço está associado com o balanceador de carga. 

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [associação de Balanceador de carga de rede com o endereço IP público](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L221).

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

Azure no portal do, a visão geral balanceador de carga de rede mostra a associação com o endereço IP público.

![Balanceador de carga de rede](./media/virtual-machines-linux-dotnet-core/nlb.png)

## <a name="load-balancer-rule"></a>Regra de Balanceador de carga

Ao usar um balanceador de carga, regras são configuradas que controlam como o tráfego é equilibrado os recursos pretendidos. Com o aplicativo de armazenamento de música de exemplo, o tráfego chega à porta 80 do endereço IP público e é distribuído a porta 80 de todas as máquinas virtuais. 

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [Regra de Balanceador de carga](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L270).


```none
"loadBalancingRules": [
  {
    "name": "[variables('loadBalencerRule')]",
    "properties": {
      "frontendIPConfiguration": {
        "id": "[concat(resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName')), '/frontendIPConfigurations/LoadBalancerFrontend')]"
      },
      "backendAddressPool": {
        "id": "[variables('lbPoolID')]"
      },
      "protocol": "Tcp",
      "frontendPort": 80,
      "backendPort": 80,
      "enableFloatingIP": false,
      "idleTimeoutInMinutes": 5,
      "probe": {
        "id": "[variables('lbProbeID')]"
      }
    }
  }
]
```

Um modo de exibição da regra de Balanceador de carga de rede no portal do.

![Regra de Balanceador de carga de rede](./media/virtual-machines-linux-dotnet-core/lbrule.png)

## <a name="load-balancer-probe"></a>Teste de Balanceador de carga

O balanceador de carga também precisa monitorar cada máquina virtual para que solicitações são fornecidas somente a execução de sistemas. Este monitoramento ocorrerá ao fazer uma sondagem constante de uma porta predefinida. A implantação do repositório de música está configurada para teste porta 80 em todas as máquinas virtuais incluídas. 

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [Teste de Balanceador de carga](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L257).


```none
"probes": [
  {
    "properties": {
      "protocol": "Tcp",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 2
    },
    "name": "lbprobe"
  }
]
```

O teste de Balanceador de carga visto a partir do portal do Azure.

![Teste de Balanceador de carga de rede](./media/virtual-machines-linux-dotnet-core/lbprobe.png)

## <a name="inbound-nat-rules"></a>Regras NAT de entrada

Ao usar um balanceador de carga, regras precisam ser colocados em prática que fornecem não-carga equilibrado acesso a cada máquina Virtual. Por exemplo, ao criar uma conexão SSH com cada máquina virtual, esse tráfego não deve ser balanceamento de carga, em vez disso, um caminho predeterminado deve ser configurado. caminhos predeterminados estão configurados usando um recurso de regra de NAT de entrada. Usando esse recurso, comunicação de entrada pode ser mapeada para máquinas virtuais individuais. 

Com o aplicativo de armazenamento de música, uma porta começando pelo 5000 é mapeada para porta 22 em cada máquina Virtual para acesso SSH. O `copyindex()` função é usada para incrementar a porta de entrada, de forma que a segunda máquina Virtual recebe uma porta de entrada de 5001, a terceira 5002 e assim por diante.

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [Regras de NAT de entrada](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L270). 

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers/inboundNatRules",
  "name": "[concat(variables('loadBalancerName'), '/', 'SSH-VM', copyIndex())]",
  "tags": {
    "displayName": "load-balancer-nat-rule"
  },
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "lbNatLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
  ],
  "properties": {
    "frontendIPConfiguration": {
      "id": "[variables('ipConfigID')]"
    },
    "protocol": "tcp",
    "frontendPort": "[copyIndex(5000)]",
    "backendPort": 22,
    "enableFloatingIP": false
  }
}
```

Exemplo de uma regra NAT de entrada como visto no portal do Azure. Uma regra de SSH NAT é criada para cada máquina virtual na implantação.

![Regra NAT de entrada](./media/virtual-machines-linux-dotnet-core/natrule.png)

Para obter informações detalhadas sobre o balanceador de carga de rede do Azure, consulte [balanceamento de carga para serviços de infraestrutura Azure](./virtual-machines-linux-load-balance.md).

## <a name="deploy-multiple-vms"></a>Implantar várias VMs

Finalmente, para um conjunto de disponibilidade ou balanceador de carga funcionar de forma eficaz, várias máquinas virtuais são necessárias. Várias VMs podem ser implantadas usando a função de copiar de modelo do Gerenciador de recursos do Azure. Usando a função copiar, não é necessário definir um número finito de máquinas virtuais, em vez disso, esse valor pode ser fornecido dinamicamente no momento da implantação. A função Copiar consome o número de instâncias criadas e as alças Implantando o número correto de máquinas virtuais e recursos associados.

O modelo de exemplo de armazenamento de música, um parâmetro é definido que recebe uma contagem de instância. Esse número é usado em todo o modelo ao criar máquinas virtuais e recursos relacionados.

```none
"numberOfInstances": {
  "type": "int",
  "minValue": 1,
  "defaultValue": 1,
  "metadata": {
    "description": "Number of VM instances to be created behind load balancer."
  }
}
```

Sobre o recurso de máquina Virtual, o loop de cópia é dado um nome e o número de instâncias parâmetro usado para controlar o número de cópias resultantes.

Siga este link para ver a amostra JSON dentro do modelo de Gerenciador de recursos – a [Função de cópia de máquina Virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L300). 


```none
"apiVersion": "2015-06-15",
"type": "Microsoft.Compute/virtualMachines",
"name": "[concat(variables('vmName'),copyindex())]",
"location": "[resourceGroup().location]",
"copy": {
  "name": "virtualMachineLoop",
  "count": "[parameters('numberOfInstances')]"
}
```

A iteração atual da função cópia pode ser acessada com o `copyIndex()` função. O valor da função índice cópia pode ser usado para nomear máquinas virtuais e outros recursos. Por exemplo, se duas instâncias de uma máquina virtual são implantadas, eles precisam nomes diferentes. O `copyIndex()` função pode ser usada como parte do nome da máquina virtual para criar um nome exclusivo. Um exemplo do `copyindex()` função usada para fins de nomenclatura pode ser vista no recurso de máquina Virtual. Aqui, o nome do computador é uma concatenação do `vmName` parâmetro e o `copyIndex()` função. 

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [Função índice de cópia](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L319). 


```none
"osProfile": {
  "computerName": "[concat(parameters('vmName'),copyindex())]",
  "adminUsername": "[parameters('adminUsername')]",
  "linuxConfiguration": {
    "disablePasswordAuthentication": "true",
    "ssh": {
      "publicKeys": [
        {
          "path": "[variables('sshKeyPath')]",
          "keyData": "[parameters('sshKeyData')]"
        }
      ]
    }
  }
}
```

O `copyIndex` função é usada várias vezes no modelo de exemplo do repositório de música. Recursos e funções utilizando `copyIndex` incluir qualquer coisa específicos para uma única instância da máquina virtual como interface de rede, regras de Balanceador de carga, e qualquer depende de funções. 

Para obter mais informações sobre a função copiar, consulte [criar várias instâncias de recursos no Gerenciador de recursos do Azure](../resource-group-create-multiple.md).

## <a name="next-step"></a>Próxima etapa

<hr>

[Etapa 4 - implantação do aplicativo com modelos de Gerenciador de recursos do Azure](./virtual-machines-linux-dotnet-core-5-app-deployment.md)