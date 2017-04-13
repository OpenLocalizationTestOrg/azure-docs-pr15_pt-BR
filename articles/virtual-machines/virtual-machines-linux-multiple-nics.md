<properties
   pageTitle="Criar uma VM Linux com várias placas de rede | Microsoft Azure"
   description="Saiba como criar uma VM Linux com várias placas de rede anexadas usando os modelos de CLI do Azure ou Gerenciador de recursos."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="creating-a-linux-vm-with-multiple-nics"></a>Criando uma VM Linux com várias placas de rede
Você pode criar uma máquina virtual (VM) no Azure que tem várias interfaces de rede virtual (NICs) anexadas a ele. Um cenário comum seria ter sub-redes diferentes para conectividade de front-end e back-end ou uma rede dedicada a uma solução de monitoramento ou de backup. Este artigo fornece comandos rápidos para criar uma máquina virtual com várias placas de rede anexadas. Para obter informações detalhadas, incluindo como criar várias NICs dentro de seus próprios scripts Bash, leia mais sobre como [implantar VMs multi-NIC](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Diferentes [tamanhos de máquina virtual](virtual-machines-linux-sizes.md) suporte um número variável de NICs, portanto dimensionar sua máquina virtual adequadamente.

>[AZURE.WARNING] Você deve anexar várias placas de rede quando você cria uma máquina virtual - você não pode adicionar NICs para uma máquina virtual existente. Você pode [criar uma máquina virtual com base na s virtual original](virtual-machines-linux-copy-vm.md) e criar várias placas de rede quando você implantar a máquina virtual.

## <a name="quick-commands"></a>Comandos rápidos
Certifique-se de que você tenha o [Azure CLI](../xplat-cli-install.md) conectado e usando o modo do Gerenciador de recursos:

```bash
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Nomes de parâmetro de exemplo incluídos `myResourceGroup`, `mystorageaccount`, e `myVM`.

Primeiro, crie um grupo de recursos. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no `WestUS` local:

```bash
azure group create myResourceGroup -l WestUS
```

Crie uma conta de armazenamento para armazenar suas VMs. O exemplo a seguir cria uma conta de armazenamento denominada `mystorageaccount`:

```bash
azure storage account create mystorageaccount -g myResourceGroup \
    -l WestUS --kind Storage --sku-name PLRS
```

Crie uma rede virtual para conectar seu VMs a. O exemplo a seguir cria uma rede virtual chamada `myVnet` com um prefixo de endereço `192.168.0.0/16`:

```bash
azure network vnet create -g myResourceGroup -l WestUS \
    -n myVnet -a 192.168.0.0/16
```

Criar duas sub-redes de rede virtual - um para o tráfego de front-end e outro para o tráfego de back-end. O exemplo a seguir cria duas sub-redes, chamados `mySubnetFrontEnd` e `mySubnetBackEnd`:

```bash
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetFrontEnd -a 192.168.1.0/24
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetBackEnd -a 192.168.2.0/24
```

Crie duas NICs, anexando uma NIC à sub-rede front-end e uma NIC à sub-rede back-end. O exemplo a seguir cria duas NICs, chamadas `myNic1` e `myNic2`e anexa-las a sua sub-redes:

```bash
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic1 -m myVnet -k mySubnetFrontEnd
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic2 -m myVnet -k mySubnetBackEnd
```

Finalmente, crie sua máquina virtual, anexando as duas NICs que você criou anteriormente. O exemplo a seguir cria uma máquina virtual chamada `myVM`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-azure-cli"></a>Criando várias placas de rede usando CLI do Azure
Se você tiver criado anteriormente uma máquina virtual usando a CLI do Azure, os comandos rápidos devem ser familiares. O processo é o mesmo para criar uma NIC ou várias placas de rede. Você pode ler mais detalhes sobre [Implantando várias placas de rede usando a CLI do Azure](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), incluindo o script do processo de loop através de criar todas as NICs.

O exemplo a seguir cria duas NICs, chamadas `myNic1` e `myNic2`, com uma NIC conectando a cada sub-rede:

```bash
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic1 --subnet-vnet-name myVnet --subnet-name mySubnetFrontEnd
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic2 --subnet-vnet-name myVnet --subnet-name mySubnetBackEnd
```

Normalmente você também cria um [Grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) ou [balanceador de carga](../load-balancer/load-balancer-overview.md) de para ajudar a gerenciar e distribuir o tráfego em suas VMs. Novamente, os comandos são a mesma ao trabalhar com várias placas de rede. O exemplo a seguir cria um grupo de segurança de rede chamado `myNetworkSecurityGroup`:

```bash
azure network nsg create --resource-group myResourceGroup --location WestUS \
    --name myNetworkSecurityGroup
```

Vincular suas NICs para o grupo de segurança de rede usando `azure network nic set`. O exemplo a seguir vincula `myNic1` e `myNic2` com `myNetworkSecurityGroup`:

```bashazure 
azure network nic set --resource-group myResourceGroup --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set --resource-group myResourceGroup --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

Ao criar a máquina virtual, agora você especificar várias placas de rede. Em vez disso usando `--nic-name` para fornecer uma única NIC, em vez disso você utilize `--nic-names` e forneça uma lista separada por vírgulas de NICs. Você também precisa cuidam quando você seleciona o tamanho de máquina virtual. Há limites para o número total de NICs que você pode adicionar a uma máquina virtual. Leia mais sobre [os tamanhos de máquina virtual Linux](virtual-machines-linux-sizes.md). O exemplo a seguir mostra como especificar várias placas de rede e, em seguida, um tamanho de máquina virtual que suporta usando várias placas de rede (`Standard_DS2_v2`):

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Criando várias placas de rede usando o recurso Gerenciador de modelos
Modelos de Gerenciador de recursos Azure usam arquivos JSON declarativos para definir seu ambiente. Você pode ler uma [Visão geral do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md). Modelos do Gerenciador de recursos fornecem uma maneira de criar várias instâncias de um recurso durante a implantação, como criar várias placas de rede. Usar a *cópia* para especificar o número de instâncias para criar:

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Leia mais sobre como [criar várias instâncias usando *Copiar*](../resource-group-create-multiple.md). 

Você também pode usar um `copyIndex()` , em seguida, acrescentar um número para um nome de recurso, que permite que você crie `myNic1`, `myNic2`, etc. A seguir mostra um exemplo de acrescentar o valor de índice:

```bash
"name": "[concat('myNic', copyIndex())]", 
```

Você pode ler um exemplo completo de [criação de várias placas de rede usando o recurso Gerenciador de modelos](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Próximas etapas
Verifique se revisar [tamanhos de máquina virtual Linux](virtual-machines-linux-sizes.md) ao tentar criar uma máquina virtual com várias placas de rede. Observe o número máximo de NICs suporta o tamanho de cada máquina virtual. 

Lembre-se de que você não pode adicionar NICs adicionais para uma máquina virtual existente, você deve criar todas as NICs quando você implanta a máquina virtual. Tome cuidado ao planejar as implantações para certificar-se de que você tem todos os a conectividade de rede necessária desde o início.