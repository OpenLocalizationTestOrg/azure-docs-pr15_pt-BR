<properties
   pageTitle="Azure suporte do Gerenciador de recursos para balanceador de carga | Microsoft Azure "
   description="Usando o powershell para balanceador de carga com o Gerenciador de recursos do Azure. Usando modelos para balanceador de carga"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Usando o Azure Gerenciador de recursos de suporte com balanceador de carga Azure

Gerenciador de recursos de Azure é a estrutura de gerenciamento preferida para serviços no Azure. Azure balanceador de carga pode ser gerenciado usando ferramentas e APIs baseadas em Gerenciador de recursos do Azure.

## <a name="concepts"></a>Conceitos

Com o Gerenciador de recursos, balanceador de carga do Azure contém os seguintes recursos de filho:

- Configuração de IP front-end – um balanceador de carga pode incluir um ou mais front-end endereços IP, também conhecidos como um IPs virtual (VIPs). Esses endereços IP servem como entrada para o tráfego.

- Pool de endereços de back-end – esses são endereços IP associados com a máquina virtual placa de rede (NIC) aos quais carga será distribuída.

- Regras de balanceamento de carga – uma propriedade regra mapeia um determinado front-end IP e combinação de porta para um conjunto de endereços IP de back-end e combinação de porta. Um balanceador de carga único pode ter várias regras de balanceamento de carga. Cada regra é uma combinação de um front-end IP e porta e back-end IP e porta associada VMs.

- Testes – testes permitem que você controlar a integridade de instâncias de máquina virtual. Se um teste de integridade falhar, a instância de máquina virtual será levada fora rotação automaticamente.

- Regras de entrada NAT – regras NAT que definem o tráfego de entrada fluindo pela frente encerrar IP e distribuídos para o IP de back-end.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Modelos de início rápido

Gerenciador de recursos de Azure permite provisionar seus aplicativos usando um modelo declarativo. Em um único modelo, você pode implantar vários serviços juntamente com suas dependências. Use o mesmo modelo para implantar repetidamente seu aplicativo durante cada estágio do ciclo de vida do aplicativo.

Os modelos podem incluir definições para máquinas virtuais, redes virtuais, conjuntos de disponibilidade, Interfaces de rede (NICs), contas de armazenamento, balanceadores de carga, grupos de segurança de rede e endereços IP do público. Com modelos, você pode criar tudo o que precisa para um aplicativo complexo. O arquivo de modelo pode ser verificado no sistema de gerenciamento de conteúdo para controle de versão e colaboração.

[Saiba mais sobre modelos](http://go.microsoft.com/fwlink/?LinkId=544798)

[Saiba mais sobre os recursos de rede](../virtual-network/resource-groups-networking.md)

Modelos de início rápido usando balanceador de carga do Azure podem ser encontrados em um [repositório de GitHub](https://github.com/Azure/azure-quickstart-templates) um conjunto de modelos de comunidade gerado de hospedagem.

Exemplos de modelos:

- [2 VMs em um balanceador de carga e as regras de balanceamento de carga](http://go.microsoft.com/fwlink/?LinkId=544799)
- [2 VMs em um VNET com um regras interno balanceador de carga e balanceador de carga](http://go.microsoft.com/fwlink/?LinkId=544800)
- [2 VMs em um balanceador de carga e configurar regras NAT na LB](http://go.microsoft.com/fwlink/?LinkId=544801)


## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Configuração de Balanceador de carga do Azure com uma PowerShell ou CLI

Começar a usar o Gerenciador de recursos do Azure cmdlets, ferramentas de linha de comando e APIs REST

- [Cmdlets de rede do Azure](https://msdn.microsoft.com/library/azure/mt163510.aspx) pode ser usado para criar um balanceador de carga.
- [Como criar um balanceador de carga usando o Gerenciador de recursos do Azure](load-balancer-get-started-ilb-arm-ps.md)
- [Usando a CLI Azure com gerenciamento de recursos do Azure](../xplat-cli-azure-resource-manager.md)
- [APIs REST de Balanceador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## <a name="next-steps"></a>Próximas etapas

Você também pode [começar a criar um opostas balanceador de carga de Internet](load-balancer-get-started-internet-arm-ps.md) e configurar o tipo de [modo de distribuição](load-balancer-distribution-mode.md) para um comportamento de tráfego de rede de Balanceador de carga específico.

Saiba como gerenciar [as configurações de tempo limite ociosas TCP para um balanceador de carga](load-balancer-tcp-idle-timeout.md). Isso é importante quando seu aplicativo precisa manter conexões ativa para servidores atrás de um balanceador de carga.
