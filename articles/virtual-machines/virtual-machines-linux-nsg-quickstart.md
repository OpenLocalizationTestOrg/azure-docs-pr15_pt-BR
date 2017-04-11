<properties
   pageTitle="Abrir portas e pontos de extremidade para uma VM Linux | Microsoft Azure"
   description="Saiba como abrir uma porta / crie um ponto de extremidade para sua VM Linux usando o modelo de implantação do Gerenciador de recursos do Azure e a CLI do Azure"
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
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure"></a>Abrindo portas e pontos de extremidade para uma VM Linux no Azure
Você abre uma porta ou cria um ponto de extremidade, a uma máquina virtual (VM) no Azure, criando um filtro de rede em uma sub-rede ou a interface de rede de máquina virtual. Coloque esses filtros, que controlam o tráfego de entrada e de saída, um grupo de segurança de rede anexados ao recurso que recebe o tráfego. Vamos usar um exemplo comum de tráfego da web na porta 80.

## <a name="quick-commands"></a>Comandos rápidos
Para criar um grupo de segurança de rede e as regras que você precisa [CLI Azure](../xplat-cli-install.md) instalado e usando o modo do Gerenciador de recursos:

```bash
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Nomes de parâmetro de exemplo incluídos `myResourceGroup`, `myNetworkSecurityGroup`, e `myVnet`.

Crie seu grupo de segurança de rede, inserindo seus próprios nomes e local adequadamente. O exemplo a seguir cria um grupo de segurança de rede chamado `myNetworkSecurityGroup` no `WestUS` local:

```
azure network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

Adicione uma regra para permitir o tráfego HTTP para o seu servidor Web (ou ajustar para seu cenário, como SSH access ou banco de dados corporativos de conectividade). O exemplo a seguir cria uma regra chamada `myNetworkSecurityGroupRule` para permitir o tráfego TCP na porta 80:

```
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow
```

O grupo de segurança de rede associe da interface de rede do sua máquina virtual (NIC). O exemplo a seguir associa uma NIC existente denominada `myNic` com o grupo de segurança de rede chamado `myNetworkSecurityGroup`:

```
azure network nic set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup --name myNic
```

Como alternativa, você pode associar seu grupo de segurança de rede com uma sub-rede de uma rede virtual em vez de apenas para a interface de rede em uma única VM. O exemplo a seguir associa uma sub-rede existente denominada `mySubnet` no `myVnet` rede virtual com o grupo de segurança de rede chamado `myNetworkSecurityGroup`:

```
azure network vnet subnet set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>Obter mais informações sobre grupos de segurança de rede
Os comandos rápidos aqui permitem que você começar a trabalhar com tráfego fluindo para sua máquina virtual. Grupos de segurança de rede fornecem muitos recursos excelentes e detalhamento para controlar o acesso aos seus recursos. Você pode ler mais sobre a [criação de um grupo de segurança de rede e regras ACL aqui](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

Você pode definir grupos de segurança de rede e regras ACL como parte dos modelos do Gerenciador de recursos do Azure. Leia mais sobre a [criação de grupos de segurança de rede com modelos](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se você precisar usar encaminhamento de porta para mapear uma porta externa exclusiva para uma porta interna em sua máquina virtual, use um balanceador de carga e regras de conversão de endereço de rede (NAT). Por exemplo, você talvez queira expor porta 8080 TCP externamente e ter tráfego direcionado para a porta TCP 80 em uma máquina virtual. Você pode aprender a [criar um balanceador de carga voltado para a Internet](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Próximas etapas
Neste exemplo, você criou uma regra simples para permitir o tráfego HTTP. Você pode encontrar informações sobre como criar ambientes mais detalhadas nos seguintes artigos:

- [Visão geral do Gerenciador de recursos Azure](../azure-resource-manager/resource-group-overview.md)
- [O que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Visão geral do Gerenciador de recursos Azure para balanceadores de carga](../load-balancer2    /load-balancer-arm.md)