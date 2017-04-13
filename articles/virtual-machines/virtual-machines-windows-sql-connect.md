<properties
    pageTitle="Conectar a uma máquina de Virtual do SQL Server (Gerenciador de recurso) | Microsoft Azure"
    description="Saiba como conectar ao SQL Server em execução em uma máquina Virtual no Azure. Este tópico usa o modelo de implantação clássico. Os cenários diferem dependendo da configuração da rede e o local do cliente."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"    
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />

# <a name="connect-to-a-sql-server-virtual-machine-on-azure-resource-manager"></a>Conectar a uma máquina de Virtual do SQL Server no Azure (Gerenciador de recurso)

> [AZURE.SELECTOR]
- [Gerenciador de recursos](virtual-machines-windows-sql-connect.md)
- [Clássico](virtual-machines-windows-classic-sql-connect.md)

## <a name="overview"></a>Visão geral

Este tópico descreve como se conectar à sua instância do SQL Server em execução em uma máquina virtual Azure. Ele aborda alguns [cenários de conectividade geral](#connection-scenarios) e, em seguida, fornece [etapas detalhadas para configurar a conectividade do SQL Server em uma máquina virtual do Azure](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico. Para exibir a versão clássica deste artigo, consulte [conectar a uma SQL Server Máquina Virtual em clássico do Azure](virtual-machines-windows-classic-sql-connect.md).

Se você preferiria ter um detalhado completo de provisionamento e conectividade, consulte [provisionamento uma máquina de Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Cenários de Conexão

A maneira como um cliente se conecta ao SQL Server em execução em uma máquina Virtual é diferente dependendo da localização do cliente e a configuração de máquina/rede. Esses cenários incluem:

- [Conectar ao SQL Server pela internet](#connect-to-sql-server-over-the-internet)
- [Conectar ao SQL Server na mesma rede virtual](#connect-to-sql-server-in-the-same-virtual-network)

### <a name="connect-to-sql-server-over-the-internet"></a>Conectar ao SQL Server pela Internet

Se você quiser se conectar ao seu mecanismo de banco de dados do SQL Server da Internet, há várias etapas necessárias, como configurar o firewall, habilitar a autenticação do SQL e configurar seu grupo de segurança de rede que você deve ter uma regra de grupo de segurança de rede para permitir o tráfego TCP na porta 1433.

Se você usar o portal para provisionar uma imagem de máquina virtual do SQL Server com o Gerenciador de recursos, estas etapas sejam executadas para você quando você seleciona **público** para a opção de conectividade do SQL:

![Opção de conectividade SQL pública durante a configuração](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Se não foi uma durante a provisionamento, em seguida, você pode configurar manualmente SQL Server e suas máquinas virtuais seguindo as [etapas neste artigo para configurar manualmente a conectividade](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

>[AZURE.NOTE] A imagem de máquina virtual do SQL Server Express edition não habilitar automaticamente o protocolo de TCP/IP. Para edição Express, você deve usar o Gerenciador de configuração do SQL Server para [ativar manualmente o protocolo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) depois de criar a máquina virtual.

Quando isso é feito, qualquer cliente com acesso à internet pode se conectar à instância do SQL Server, especificando o endereço IP público da máquina virtual ou o rótulo DNS atribuído a esse endereço IP. Se a porta do SQL Server é 1433, você não precisa especificar isso na cadeia de conexão.

    "Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Embora isso permite a conectividade para clientes pela internet, isso não significa que qualquer pessoa pode se conectar ao SQL Server. Fora de clientes precisam o nome correto e senha. Para ter mais segurança, você pode evitar a porta conhecida 1433. Por exemplo, se você configura o SQL Server para escutar na porta 1500 e estabelecida firewall adequado e regras de grupo de segurança de rede, você pode conectar anexando o número da porta ao nome do servidor, como no exemplo seguinte:

    "Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] É importante observar que ao usar essa técnica para se comunicar com o SQL Server, todos os dados de saída do data center Azure está sujeito normal [preços nas transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Conectar ao SQL Server na mesma rede virtual

[Rede virtual](../virtual-network/virtual-networks-overview.md) permite cenários adicionais. Você pode conectar VMs na mesma rede virtual, mesmo se essas VMs existem em grupos de recursos diferentes. E com uma [VPN to-site](../vpn-gateway/vpn-gateway-site-to-site-create.md), você pode criar uma arquitetura de híbrido que conecta VMs com máquinas e redes de local.

Redes virtuais também permite que você ingressar suas VMs do Azure em um domínio. Esta é a única maneira de usar a autenticação do Windows para o SQL Server. Os outros cenários de conexão requerem autenticação SQL com nomes de usuário e senhas.

Se você usar o portal para provisionar uma imagem de máquina virtual do SQL Server com o Gerenciador de recursos, as regras de firewall adequado para comunicação em uma rede virtual são configuração quando você seleciona **privada** para a opção de conectividade do SQL. Se não foi uma durante a provisionamento, em seguida, você pode configurar manualmente SQL Server e suas máquinas virtuais seguindo as [etapas neste artigo para configurar manualmente a conectividade](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm). Mas se você estiver planejando configurar um ambiente de domínio e a autenticação do Windows, você não precisa usar as etapas neste artigo para configurar a autenticação do SQL e logon. Você também não precisa configurar regras de grupo de segurança de rede para acesso pela internet.

>[AZURE.NOTE] A imagem de máquina virtual do SQL Server Express edition não habilitar automaticamente o protocolo de TCP/IP. Para edição Express, você deve usar o Gerenciador de configuração do SQL Server para [ativar manualmente o protocolo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) depois de criar a máquina virtual.

Presumindo que você configurou DNS da sua rede virtual, você pode se conectar à sua instância do SQL Server, especificando o nome do computador de máquina virtual do SQL Server na cadeia de conexão. O exemplo a seguir também pressupõe que a autenticação do Windows também foi configurada e que o usuário recebeu acesso à instância do SQL Server.

    "Server=mysqlvm;Integrated Security=true"

Observe que neste cenário, você pode também especificar o endereço IP da máquina virtual.

## <a name="steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm"></a>Etapas para configurar manualmente a conectividade do SQL Server em uma máquina virtual do Azure

As etapas a seguir demonstram como configurar manualmente a conectividade com a instância do SQL Server e, opcionalmente, conecte-se pela internet usando o SQL Server Management Studio (SSMS). Observe que muitas dessas etapas são feitas para você quando você selecionar as opções apropriadas de conectividade do SQL Server no portal.

Antes de poder conectar à instância do SQL Server na máquina virtual outro ou na internet, você deve concluir as seguintes tarefas, conforme descrito nas seções a seguir:

- [Portas TCP abertas no firewall do Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurar o SQL Server para escutar o protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurar o SQL Server para autenticação de modo misto](#configure-sql-server-for-mixed-mode-authentication)
- [Criar logon de autenticação do SQL Server](#create-sql-server-authentication-logins)
- [Configurar uma regra de entrada do grupo de segurança de rede](#configure-a-network-security-group-inbound-rule-for-the-vm)
- [Configurar um rótulo de DNS para o endereço IP público](#configure-a-dns-label-for-the-public-ip-address)
- [Conectar-se ao mecanismo de banco de dados do outro computador](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Próximas etapas

Para ver instruções juntamente com estas etapas de conectividade de provisionamento, consulte [provisionamento uma máquina de Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

[Explorar o caminho de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em Azure máquinas virtuais.

Para outros tópicos relacionados a execução do SQL Server no Azure VMs, consulte [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
