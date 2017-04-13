<properties
   pageTitle="Abrir portas para uma máquina virtual usando o portal de Azure | Microsoft Azure"
   description="Saiba como abrir uma porta / crie um ponto de extremidade para sua máquina de virtual do Windows usando o modelo de implantação do Gerenciador de recursos no Portal do Azure"
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

# <a name="opening-ports-to-a-vm-in-azure-using-the-azure-portal"></a>Abrindo portas para uma máquina virtual no Azure usando o portal do Azure
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandos rápidos
Você também pode [executar essas etapas usando o PowerShell do Azure](virtual-machines-windows-nsg-quickstart-powershell.md).

Primeiro, crie seu grupo de segurança de rede. Selecione um grupo de recursos no portal, clique em **Adicionar**, e em seguida, pesquise e selecione o grupo de segurança de rede:

![Adicionar um grupo de segurança de rede](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

Insira um nome para seu grupo de segurança de rede, selecione ou crie um grupo de recursos e selecione um local. Clique em **criar** quando terminar:

![Criar um grupo de segurança de rede](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

Selecione o novo grupo de segurança de rede. Selecione 'regras de segurança de entrada' e, em seguida, clique no botão **Adicionar** para criar uma regra:

![Adicionar uma regra de entrada](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

Forneça um nome para a nova regra. Porta 80 já é inserida por padrão. Este blade é onde você deseja alterar a fonte, o protocolo e o destino ao adicionar regras adicionais ao seu grupo de segurança de rede. Clique em **Okey** para criar a regra:

![Criar uma regra de entrada](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

A etapa final é associar seu grupo de segurança de rede com uma sub-rede ou uma interface de rede específica. Vamos associar o grupo de segurança de rede com uma sub-rede. Selecione 'Sub-redes' e, em seguida, clique em **associar**:

![Associar um grupo de segurança de rede com uma sub-rede](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

Selecione sua rede virtual e, em seguida, selecione a sub-rede apropriada:

![Associar um grupo de segurança de rede a rede virtual](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

Agora você criou um grupo de segurança de rede, criou uma regra de entrada que permite o tráfego na porta 80 e associada a uma sub-rede. Qualquer VMs que você se conectar à sub-rede são acessíveis na porta 80.


## <a name="more-information-on-network-security-groups"></a>Obter mais informações sobre grupos de segurança de rede
Os comandos rápidos aqui permitem que você começar a trabalhar com tráfego fluindo para sua máquina virtual. Grupos de segurança de rede fornecem muitos recursos excelentes e detalhamento para controlar o acesso aos seus recursos. Você pode ler mais sobre a [criação de um grupo de segurança de rede e regras ACL aqui](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Você pode definir grupos de segurança de rede e regras ACL como parte dos modelos do Gerenciador de recursos do Azure. Leia mais sobre a [criação de grupos de segurança de rede com modelos](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se você precisar usar encaminhamento de porta para mapear uma porta externa exclusiva para uma porta interna em sua máquina virtual, use um balanceador de carga e regras de conversão de endereço de rede (NAT). Por exemplo, você talvez queira expor porta 8080 TCP externamente e ter tráfego direcionado para a porta TCP 80 em uma máquina virtual. Você pode aprender a [criar um balanceador de carga voltado para a Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Próximas etapas
Neste exemplo, você criou uma regra simples para permitir o tráfego HTTP. Você pode encontrar informações sobre como criar ambientes mais detalhadas nos seguintes artigos:

- [Visão geral do Gerenciador de recursos Azure](../azure-resource-manager/resource-group-overview.md)
- [O que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Visão geral do Gerenciador de recursos Azure para balanceadores de carga](../load-balancer/load-balancer-arm.md)
