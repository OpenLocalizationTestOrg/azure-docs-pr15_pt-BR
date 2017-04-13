<properties
    pageTitle="Extensão do agente do SQL Server para SQL Server VMs (Gerenciador de recurso) | Microsoft Azure"
    description="Este tópico descreve como gerenciar a extensão do agente do SQL Server, automatizar tarefas de administração do SQL Server específicas. Eles incluem Backup automatizado, automatizada patches e integração de Cofre de chave do Azure. Este tópico usa o modo de implantação do Gerenciador de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/27/2016"
    ms.author="jroth"/>

# <a name="sql-server-agent-extension-for-sql-server-vms-resource-manager"></a>Extensão do agente do SQL Server para SQL Server VMs (Gerenciador de recurso)

> [AZURE.SELECTOR]
- [Gerenciador de recursos](virtual-machines-windows-sql-server-agent-extension.md)
- [Clássico](virtual-machines-windows-classic-sql-server-agent-extension.md)

A extensão do SQL Server IaaS agente (SQLIaaSExtension) é executado no Azure máquinas virtuais para automatizar tarefas de administração. Este tópico fornece uma visão geral dos serviços de suporte a extensão, além de instruções de instalação, status e remoção.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico. Para exibir a versão clássica deste artigo, consulte [Extensão do agente do SQL Server para VMs clássico do SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Serviços com suporte

A extensão do agente do SQL Server IaaS suporta as seguintes tarefas de administração:

| Recurso de administração | Descrição |
|---------------------|-------------------------------|
| **Backup automatizado de SQL** | Automatiza o agendamento de backups para todos os bancos de dados para a instância padrão do SQL Server na máquina virtual. Para obter mais informações, consulte [backup de automatizada para o SQL Server em máquinas virtuais do Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md).|
| **SQL automatizado de patches** | Configura uma janela de manutenção durante os quais atualizações para sua máquina virtual podem ocorrer, para que você possa evitar atualizações horários de pico para sua carga de trabalho. Para obter mais informações, consulte [automatizada patches para o SQL Server em máquinas virtuais do Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md).|
| **Integração do Azure cofre chave** | Permite automaticamente instalar e configurar o Azure chave cofre em sua máquina de virtual do SQL Server. Para obter mais informações, consulte [Configurar integração de cofre do Azure chave para o SQL Server em Azure VMs (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md).|

## <a name="prerequisites"></a>Pré-requisitos

Requisitos para usar a extensão do agente do SQL Server IaaS em sua máquina virtual:

**Sistema operacional**:

- Windows Server 2012
- Windows Server 2012 R2

**Versões do SQL Server**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [Baixe e configure os comandos mais recentes do PowerShell do Azure](../powershell-install-configure.md)

## <a name="installation"></a>Instalação

A extensão do agente do SQL Server IaaS é instalada automaticamente quando você provisiona uma das imagens de galeria de máquina virtual do SQL Server.

Se você criar uma máquina virtual de somente sistema operacional Windows Server, você pode instalar a extensão manualmente usando o cmdlet do PowerShell **Set-AzureVMSqlServerExtension** . Por exemplo, o comando a seguir instala a extensão em um VM de servidor somente sistema operacional do Windows e o nomeará "SQLIaaSExtension".

    Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2"

Se você atualizar para a versão mais recente da extensão do agente do SQL IaaS, reinicie sua máquina virtual depois de atualizar a extensão.

>[AZURE.NOTE] Se você instalar a extensão do agente do SQL Server IaaS manualmente em uma máquina de virtual do Windows Server, você deve usar e gerenciar seus recursos usando comandos do PowerShell. Interface do portal está disponível somente para imagens da Galeria do SQL Server.

## <a name="status"></a>Status

É uma maneira de verificar se a extensão está instalada exibir o status do agente no Portal do Azure. Selecione **todas as configurações** na máquina virtual lâmina e clique em **extensões**. Você deve ver a extensão de **SQLIaaSExtension** listada.

![Extensão do agente de IaaS do SQL Server no Portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Você também pode usar o cmdlet do Powershell do Azure **Get-AzureVMSqlServerExtension** .

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

O comando anterior confirma o agente está instalado e fornece informações gerais de status. Você também pode obter informações de status específicas sobre Backup automatizado e patch com os seguintes comandos.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Remoção   

No Portal do Azure, você pode desinstalar a extensão clicando nas reticências na lâmina **extensões** de suas propriedades de máquina virtual. Clique em **Excluir**.

![Desinstalar a extensão de IaaS agente do SQL Server no Portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Você também pode usar o cmdlet do Powershell **AzureRmVMSqlServerExtension remover** .

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Próximas etapas

Começar a usar um dos serviços de suporte a extensão. Para obter mais detalhes, consulte os tópicos mencionados na seção de [Serviços de suporte](#supported-services) deste artigo.

Para obter mais informações sobre a execução do SQL Server em máquinas virtuais do Azure, consulte [SQL Server em máquinas virtuais do Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
