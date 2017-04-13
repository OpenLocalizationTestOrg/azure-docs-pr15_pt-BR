<properties
    pageTitle="Extensão do agente do SQL Server para SQL Server VMs (clássico) | Microsoft Azure"
    description="Este tópico descreve como gerenciar a extensão do agente do SQL Server, automatizar tarefas de administração do SQL Server específicas. Eles incluem Backup automatizado, automatizada patches e integração de Cofre de chave do Azure. Este tópico usa o modo clássico de implantação."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/27/2016"
    ms.author="jroth"/>

# <a name="sql-server-agent-extension-for-sql-server-vms-classic"></a>Extensão do agente do SQL Server para SQL Server VMs (clássico)

> [AZURE.SELECTOR]
- [Gerenciador de recursos](virtual-machines-windows-sql-server-agent-extension.md)
- [Clássico](virtual-machines-windows-classic-sql-server-agent-extension.md)

A extensão do SQL Server IaaS agente (SQLIaaSAgent) é executado no Azure máquinas virtuais para automatizar tarefas de administração. Este tópico fornece uma visão geral dos serviços de suporte a extensão, além de instruções de instalação, status e remoção.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para exibir a versão do Gerenciador de recursos deste artigo, consulte [Extensão do agente do SQL Server para VMs recurso Gerenciador do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Serviços com suporte

A extensão do agente do SQL Server IaaS suporta as seguintes tarefas de administração:

| Recurso de administração | Descrição |
|---------------------|-------------------------------|
| **Backup automatizado de SQL** | Automatiza o agendamento de backups para todos os bancos de dados para a instância padrão do SQL Server na máquina virtual. Para obter mais informações, consulte [backup automático para SQL Server em máquinas virtuais do Azure (clássico)](virtual-machines-windows-classic-sql-automated-backup.md).|
| **SQL automatizado de patches** | Configura uma janela de manutenção durante os quais atualizações para sua máquina virtual podem ocorrer, para que você possa evitar atualizações horários de pico para sua carga de trabalho. Para obter mais informações, consulte [automatizado de patches para o SQL Server em máquinas virtuais do Azure (clássico)](virtual-machines-windows-classic-sql-automated-patching.md).|
| **Integração do Azure cofre chave** | Permite automaticamente instalar e configurar o Azure chave cofre em sua máquina de virtual do SQL Server. Para obter mais informações, consulte [Configurar integração de cofre do Azure chave para o SQL Server em Azure VMs (clássico)](virtual-machines-windows-classic-ps-sql-keyvault.md).|

## <a name="prerequisites"></a>Pré-requisitos

Requisitos para usar a extensão do agente do SQL Server IaaS em sua máquina virtual:

### <a name="operating-system"></a>Sistema Operacional:

- Windows Server 2012
- Windows Server 2012 R2

### <a name="sql-server-versions"></a>Versões do SQL Server:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:

[Baixe e configure os comandos do PowerShell do Azure mais recentes](../powershell-install-configure.md).

Iniciar o Windows PowerShell e conectá-lo à sua assinatura do Azure com o comando **Add-AzureAccount** .

    Add-AzureAccount

Se você tiver várias assinaturas, use **Selecione AzureSubscription** para selecionar a assinatura que contém seu destino máquina virtual clássico.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Neste ponto, você pode obter uma lista de máquinas virtuais clássicas e seus nomes de serviço associado com o comando **Get-AzureVM** .

    Get-AzureVM

## <a name="installation"></a>Instalação

Para VMs clássicas, você deve usar o PowerShell para instalar a extensão do agente do SQL Server IaaS e configurar seus serviços associados. Use o cmdlet do PowerShell **Set-AzureVMSqlServerExtension** para instalar a extensão. Por exemplo, o comando a seguir instala a extensão em um Windows Server máquina virtual (clássico) e o nomeará "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Se você atualizar para a versão mais recente da extensão do agente do SQL IaaS, reinicie sua máquina virtual depois de atualizar a extensão.

>[AZURE.NOTE] Clássicas máquinas virtuais não tem uma opção para instalar e configurar a extensão do agente SQL IaaS por meio do portal.

## <a name="status"></a>Status

É uma maneira de verificar se a extensão está instalada exibir o status do agente no Portal do Azure. Selecione **todas as configurações** na máquina virtual lâmina e clique em **extensões**. Você deve ver a extensão de **SQLIaaSAgent** listada.

![Extensão do agente de IaaS do SQL Server no Portal do Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Você também pode usar o cmdlet do Powershell do Azure **Get-AzureVMSqlServerExtension** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Remoção   

No Portal do Azure, você pode desinstalar a extensão clicando nas reticências na lâmina **extensões** de suas propriedades de máquina virtual. Clique em **Excluir**.

![Desinstalar a extensão de IaaS agente do SQL Server no Portal do Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Você também pode usar o cmdlet do Powershell **AzureVMSqlServerExtension remover** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Próximas etapas

Começar a usar um dos serviços de suporte a extensão. Para obter mais detalhes, consulte os tópicos mencionados na seção de [Serviços de suporte](#supported-services) deste artigo.

Para obter mais informações sobre a execução do SQL Server em máquinas virtuais do Azure, consulte [SQL Server em máquinas virtuais do Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
