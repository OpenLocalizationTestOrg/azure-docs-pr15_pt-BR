<properties
    pageTitle="Conectar a uma máquina de Virtual do SQL Server (clássico) | Microsoft Azure"
    description="Saiba como conectar ao SQL Server em execução em uma máquina Virtual no Azure. Este tópico usa o modelo de implantação clássico. Os cenários diferem dependendo da configuração da rede e o local do cliente."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="jroth" />

# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Conectar a uma máquina de Virtual do SQL Server no Azure (implantação clássico)

> [AZURE.SELECTOR]
- [Gerenciador de recursos](virtual-machines-windows-sql-connect.md)
- [Clássico](virtual-machines-windows-classic-sql-connect.md)

## <a name="overview"></a>Visão geral

Este tópico descreve como se conectar à sua instância do SQL Server em execução em uma máquina virtual Azure. Ele aborda alguns [cenários de conectividade geral](#connection-scenarios) e, em seguida, fornece [etapas detalhadas para configurar a conectividade do SQL Server em uma máquina virtual do Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Se você estiver usando o Gerenciador de recursos VMs, consulte [conectar a um SQL Máquina Virtual do servidor no Azure usando o Gerenciador de recursos](virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Cenários de Conexão

A maneira como um cliente se conecta ao SQL Server em execução em uma máquina Virtual é diferente dependendo da localização do cliente e a configuração de máquina/rede. Esses cenários incluem:

- [Conectar ao SQL Server no serviço de nuvem mesmo](#connect-to-sql-server-in-the-same-cloud-service)
- [Conectar ao SQL Server pela internet](#connect-to-sql-server-over-the-internet)
- [Conectar ao SQL Server na mesma rede virtual](#connect-to-sql-server-in-the-same-virtual-network)

>[AZURE.NOTE] Antes de se conectar com qualquer um desses métodos, você deve seguir as [etapas neste artigo para configurar a conectividade](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Conectar ao SQL Server no serviço de nuvem mesmo

Várias máquinas virtuais podem ser criadas no serviço de nuvem do mesmo. Para compreender esse cenário de máquinas virtuais, consulte [como conectar máquinas virtuais com um serviço de rede ou nuvem virtual](virtual-machines-windows-classic-connect-vms.md#connect-vms-in-a-standalone-cloud-service). Este cenário é quando um cliente em uma máquina virtual tenta se conectar ao SQL Server em execução em outra máquina virtual no serviço de nuvem do mesmo.

Neste cenário, você pode se conectar usando o **nome** de máquina virtual (também mostrado como **Nome do computador** ou **hostname** no portal do). Este é o nome fornecido para a máquina virtual durante a criação. Por exemplo, se você chamar seu SQL VM **mysqlvm**, um cliente de máquina virtual no serviço de nuvem mesmo poderia usar a seguinte cadeia de conexão para se conectar:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Conectar ao SQL Server pela Internet

Se você quiser se conectar ao seu mecanismo de banco de dados do SQL Server da Internet, você deve criar um ponto de extremidade de máquina virtual para comunicação TCP recebida. Essa etapa de configuração Azure, direciona o tráfego de portas TCP porta TCP acessível na máquina virtual.

Para se conectar pela internet, você deve usar o nome DNS da VM e o número de porta de ponto de extremidade de máquina virtual (configurado neste artigo). Para localizar o nome de DNS, navegue até o Portal do Azure e selecione **máquinas virtuais (clássico)**. Selecione sua máquina virtual. O **nome DNS** é mostrado na seção **Visão geral** .

Por exemplo, considere uma máquina virtual clássica denominada **mysqlvm** com um nome de DNS de **mysqlvm7777.cloudapp.net** e um ponto de extremidade de máquina virtual do **57500**. Considerando que conectividade configurada corretamente, a cadeia de conexão a seguir pode ser usada para acessar a máquina virtual em qualquer lugar na internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Embora isso permite a conectividade para clientes pela internet, isso não significa que qualquer pessoa pode se conectar ao SQL Server. Fora de clientes precisam o nome correto e senha. Para ter mais segurança, não use a porta conhecida 1433 para o ponto de extremidade do público máquina virtual. E se possível, considere a adição de uma ACL em seu ponto de extremidade para restringir o tráfego somente para os clientes que você permitir. Para obter instruções sobre como usar ACLs com pontos de extremidade, consulte [Gerenciar a ACL em um ponto de extremidade](virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint).

>[AZURE.NOTE] É importante observar que ao usar essa técnica para se comunicar com o SQL Server, todos os dados de saída do data center Azure está sujeito normal [preços nas transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Conectar ao SQL Server na mesma rede virtual

[Rede virtual](../virtual-network/virtual-networks-overview.md) permite cenários adicionais. Você pode conectar VMs na mesma rede virtual, mesmo se essas VMs existem nos serviços de nuvem diferentes. E com uma [VPN to-site](../vpn-gateway/vpn-gateway-site-to-site-create.md), você pode criar uma arquitetura de híbrido que conecta VMs com máquinas e redes de local.

Redes virtuais também permite que você ingressar suas VMs do Azure em um domínio. Esta é a única maneira de usar a autenticação do Windows para o SQL Server. Os outros cenários de conexão requerem autenticação SQL com nomes de usuário e senhas.

Se você fizer configurar um ambiente de domínio e a autenticação do Windows, você não precisa usar as etapas neste artigo para configurar o ponto de extremidade público ou a autenticação do SQL e logon. Neste cenário, você pode se conectar à sua instância do SQL Server, especificando o nome de máquina virtual do SQL Server na cadeia de conexão. O exemplo a seguir pressupõe que a autenticação do Windows também foi configurada e que o usuário recebeu acesso à instância do SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Etapas para configurar a conectividade do SQL Server em uma máquina virtual do Azure

As etapas a seguir demonstram como conectar-se a instância do SQL Server pela internet usando o SQL Server Management Studio (SSMS). Entretanto, aplicam as mesmas etapas para tornar sua máquina do SQL Server virtual acessíveis para seus aplicativos, executando ambos os locais e no Azure.

Antes de poder conectar à instância do SQL Server na máquina virtual outro ou na internet, você deve concluir as seguintes tarefas, conforme descrito nas seções a seguir:

- [Criar um ponto de extremidade TCP da máquina virtual](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Portas TCP abertas no firewall do Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurar o SQL Server para escutar o protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurar o SQL Server para autenticação de modo misto](#configure-sql-server-for-mixed-mode-authentication)
- [Criar logon de autenticação do SQL Server](#create-sql-server-authentication-logins)
- [Determinar o nome DNS da máquina virtual](#determine-the-dns-name-of-the-virtual-machine)
- [Conectar-se ao mecanismo de banco de dados do outro computador](#connect-to-the-database-engine-from-another-computer)

O caminho de conexão é resumido, o diagrama a seguir:

![Conectando a um computador de virtual do SQL Server](../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[AZURE.INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Classic Steps](../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Próximas etapas

Se você também estiver planejando usar grupos de disponibilidade sempre ativado para alta disponibilidade e recuperação de dados, você deve considerar implementar um ouvinte. Clientes de banco de dados se conectar ao ouvinte em vez de diretamente a uma das instâncias do SQL Server. O ouvinte direciona clientes para a réplica principal no grupo de disponibilidade. Para obter mais informações, consulte [Configurar um ouvinte ILB para grupos de disponibilidade sempre ativado no Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

É importante revise todas as práticas recomendadas de segurança para o SQL Server em execução em uma máquina virtual Azure. Para obter mais informações, consulte [Considerações de segurança para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-security.md).

[Explorar o caminho de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em Azure máquinas virtuais. 

Para outros tópicos relacionados a execução do SQL Server no Azure VMs, consulte [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
