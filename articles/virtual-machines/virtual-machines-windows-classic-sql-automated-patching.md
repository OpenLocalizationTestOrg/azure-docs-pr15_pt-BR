<properties
    pageTitle="Automatizado de patches para SQL Server VMs (clássico) | Microsoft Azure"
    description="Explica o recurso automatizado de patches para SQL Server máquinas virtuais em execução no Azure usando o modo clássico de implantação."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="jroth" />

# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatizado de patches para SQL Server no Azure máquinas virtuais (clássico)

> [AZURE.SELECTOR]
- [Gerenciador de recursos](virtual-machines-windows-sql-automated-patching.md)
- [Clássico](virtual-machines-windows-classic-sql-automated-patching.md)

Patch automatizado estabelece uma janela de manutenção para uma máquina da Azure Virtual executando o SQL Server. Atualizações automáticas só podem ser instaladas durante a janela de manutenção. Para SQL Server, isso garante que as atualizações do sistema e qualquer reinicialização associada ocorre no melhor horário possível para o banco de dados. Patch automatizado depende da [Extensão do agente do SQL Server IaaS](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para exibir a versão do Gerenciador de recursos deste artigo, consulte [Automatizada patches para o SQL Server no Gerenciador de recursos de máquinas virtuais do Azure](virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Pré-requisitos

Para usar a aplicação automatizada de patches, considere os seguintes pré-requisitos:

**Sistema operacional**:

- Windows Server 2012
- Windows Server 2012 R2

**Versão do SQL Server**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [Instalar os comandos do PowerShell do Azure mais recentes](../powershell-install-configure.md).

**Extensão de IaaS do SQL Server**:

- [Instale a extensão de IaaS do SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="settings"></a>Configurações

A tabela a seguir descreve as opções que podem ser configuradas para aplicação automatizada de patches. Para VMs clássicas, você deve usar o PowerShell para definir essas configurações.

|Configuração|Valores possíveis|Descrição|
|---|---|---|
|**Automatizada patches**|Ativar/desativar (desabilitado)|Habilita ou desabilita a aplicação automatizada de patches para uma máquina virtual Azure.|
|**Agenda de manutenção**|Todos os dias, segunda-feira, terça-feira, quarta-feira, quinta-feira, sexta-feira, sábado, domingo|A agenda para baixar e instalar atualizações do Windows, SQL Server e Microsoft para sua máquina virtual.|
|**Hora de início de manutenção**|0-24|A hora de início local para atualizar a máquina virtual.|
|**Duração da janela de manutenção**|30-180|O número de minutos permitido para concluir o download e a instalação das atualizações.|
|**Categoria de patches**|Importante|A categoria de atualizações para baixar e instalar.|

## <a name="configuration-with-powershell"></a>Configuração com PowerShell

No exemplo a seguir, o PowerShell é usado para configurar a aplicação automatizada de patches em uma VM de servidor SQL existente. O comando **New-AzureVMSqlServerAutoPatchingConfig** configura uma nova janela de manutenção para atualizações automáticas.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Com base neste exemplo, a tabela a seguir descreve o efeito prático no destino máquina virtual do Azure:

|Parâmetro|Efeito|
|---|---|
|**DayOfWeek**|Patches instalados toda quinta-feira.|
|**MaintenanceWindowStartingHour**|Atualizações de começar às 11:00 am.|
|**MaintenanceWindowsDuration**|Patches devem ser instalados em 120 minutos. Com base na hora de início, eles devem ser concluídas por 1:00 pm.|
|**PatchCategory**|A configuração só é possível para este parâmetro é "Importante".|

Pode levar alguns minutos para instalar e configurar o SQL Server IaaS Agent.

Para desabilitar a aplicação automatizada de patches, execute o mesmo script sem-habilitar o parâmetro para o novo-AzureVMSqlServerAutoPatchingConfig. Como com a instalação, pode levar alguns minutos para desativar automatizada patches.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre outras tarefas de automação disponíveis, consulte [Extensão do agente do SQL Server IaaS](virtual-machines-windows-classic-sql-server-agent-extension.md).

Para obter mais informações sobre a execução do SQL Server em VMs do Azure, consulte [SQL Server em máquinas virtuais do Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
