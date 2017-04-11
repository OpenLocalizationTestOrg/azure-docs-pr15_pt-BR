## <a name="scenario"></a>Cenário

Este documento orientará durante uma implantação que usa várias placas de rede em VMs em um cenário específico. Neste cenário, você tem uma carga de IaaS duas camadas hospedada no Azure. Cada nível é implantado em sua própria sub-rede em uma rede virtual (VNet). A camada de front-end é composta de vários servidores web, agrupados em um balanceador de carga definido para alta disponibilidade. A camada de back-end é composta de vários servidores de banco de dados. Esses servidores de banco de dados serão implantados com dois NICs cada, uma para acesso de banco de dados, o outro para o gerenciamento. O cenário também inclui grupos de segurança de rede (NSGs) para controlar o tráfego que é permitido para cada sub-rede e NIC na implantação. A figura a seguir mostra a arquitetura básica desse cenário.  

![Cenário de MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

