<properties
   pageTitle="Configurar balanceador de carga para SQL sempre em | Microsoft Azure"
   description="Configurar balanceador de carga para trabalhar com SQL sempre em e como aproveitar o powershell para criar balanceador de carga para a implementação de SQL"
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

# <a name="configure-load-balancer-for-sql-always-on"></a>Configurar balanceador de carga para SQL sempre em

Grupos de disponibilidade do SQL Server AlwaysOn agora pode ser executados com ILB. Grupo de disponibilidade é solução de principal do SQL Server para alta disponibilidade e recuperação de desastres. O ouvinte de grupo de disponibilidade permite que aplicativos cliente conectar-se diretamente para a réplica primária, independentemente o número de réplicas na configuração.

O nome do ouvinte (DNS) é mapeado para um endereço IP balanceamento de carga e balanceador de carga do Azure direciona o tráfego de entrada para apenas o servidor principal do conjunto de réplica.

Você pode usar o suporte ILB para pontos de extremidade do SQL Server AlwaysOn (ouvinte). Agora, você tem controle sobre a acessibilidade do ouvinte e pode escolher o endereço IP balanceamento de carga de uma sub-rede específica na sua rede Virtual (VNet).

Usando ILB no ouvinte, o ponto de extremidade do SQL server (por exemplo, servidor = tcp:ListenerName, 1433; banco de dados = DatabaseName) é acessível apenas por:

- Serviços e VMs na mesma rede Virtual
- Serviços e VMs da rede local conectada
- Serviços e VMs de VNets interconectados

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.png)

Figura 1 - SQL AlwaysOn configurado com balanceador de carga de voltado para a Internet

## <a name="add-internal-load-balancer-to-the-service"></a>Adicionar interno balanceador de carga ao serviço

1. No exemplo a seguir, podemos configurará uma rede Virtual que contenha uma sub-rede chamada 'Sub-rede-1':

        Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc

2. Adicionar pontos de extremidade de balanceamento de carga para ILB em cada máquina virtual

        Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –
        DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

        Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    No exemplo acima, você tem da máquina 2 virtual chamado "sqlsvc1" e "sqlsvc2" execução na nuvem do serviço "Sqlsvc". Depois de criar a ILB com opção de "DirectServerReturn", você pode adicionar pontos de extremidade de balanceamento de carga para o ILB para permitir que o SQL configurar os ouvintes para os grupos de disponibilidade.

Para obter mais informações sobre SQL AlwaysOn, consulte [Configurar um balanceador de carga interna para um grupo de disponibilidade sempre ativado no Azure](../virtual-machines/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Consulte também

[Começar a configurar um opostas balanceador de carga de Internet](load-balancer-get-started-internet-arm-ps.md)

[Começar a configurar um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição de Balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite TCP ociosas para seu balanceador de carga](load-balancer-tcp-idle-timeout.md)
