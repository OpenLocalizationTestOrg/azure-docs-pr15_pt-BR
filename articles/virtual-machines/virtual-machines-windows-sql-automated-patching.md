<properties
    pageTitle="Automatizado de patches para SQL Server VMs (Gerenciador de recurso) | Microsoft Azure"
    description="Explica o recurso automatizado de patches para SQL Server máquinas virtuais em execução no Azure usando o Gerenciador de recursos."
    services="virtual-machines-windows"
    documentationCenter="na"
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
    ms.date="08/19/2016"
    ms.author="jroth" />

# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatizado de patches para SQL Server no Azure máquinas virtuais (Gerenciador de recurso)

> [AZURE.SELECTOR]
- [Gerenciador de recursos](virtual-machines-windows-sql-automated-patching.md)
- [Clássico](virtual-machines-windows-classic-sql-automated-patching.md)

Patch automatizado estabelece uma janela de manutenção para uma máquina da Azure Virtual executando o SQL Server. Atualizações automáticas só podem ser instaladas durante a janela de manutenção. Para SQL Server, esta rescriction garante que as atualizações do sistema e qualquer reinicialização associada ocorre no melhor horário possível para o banco de dados. Patch automatizado depende da [Extensão do agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico. Para exibir a versão clássica deste artigo, consulte [Automatizada patches para SQL Server no Azure máquinas virtuais clássico](virtual-machines-windows-classic-sql-automated-patching.md).

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

- [Instalar os comandos mais recentes do Azure PowerShell](../powershell-install-configure.md) se você planeja configurar a aplicação automatizada de patches com o PowerShell.

>[AZURE.NOTE] Patch automatizado depende da extensão do agente do SQL Server IaaS. Imagens de galeria de máquina virtual SQL atual adicionar essa extensão por padrão. Para obter mais informações, consulte [Extensão do agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Configurações

A tabela a seguir descreve as opções que podem ser configuradas para aplicação automatizada de patches. As etapas de configuração reais variam dependendo se você usar o portal do Azure ou comandos do PowerShell do Windows Azure.

|Configuração|Valores possíveis|Descrição|
|---|---|---|
|**Automatizada patches**|Ativar/desativar (desabilitado)|Habilita ou desabilita a aplicação automatizada de patches para uma máquina virtual Azure.|
|**Agenda de manutenção**|Todos os dias, segunda-feira, terça-feira, quarta-feira, quinta-feira, sexta-feira, sábado, domingo|A agenda para baixar e instalar atualizações do Windows, SQL Server e Microsoft para sua máquina virtual.|
|**Hora de início de manutenção**|0-24|A hora de início local para atualizar a máquina virtual.|
|**Duração da janela de manutenção**|30-180|O número de minutos permitido para concluir o download e a instalação das atualizações.|
|**Categoria de patches**|Importante|A categoria de atualizações para baixar e instalar.|

## <a name="configuration-in-the-portal"></a>Configuração no Portal
Você pode usar o portal do Azure para configurar a aplicação automatizada de patches durante a configuração ou para VMs existentes.

### <a name="new-vms"></a>Novas VMs
Use o portal do Azure para configurar a aplicação automatizada de patches quando você cria uma nova máquina de Virtual do SQL Server no modelo de implantação do Gerenciador de recursos.

Na lâmina **configurações do SQL Server** , selecione **patches automatizada**. A captura de tela portal Azure seguinte mostra a lâmina **SQL automatizada patches** .

![SQL automatizada patches no portal do Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Para o contexto, consulte o tópico concluído em [uma máquina virtual do SQL Server Azure de provisionamento](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes
Para máquinas virtuais do SQL Server existentes, selecione máquina virtual do SQL Server. Em seguida, selecione a seção de **configuração do SQL Server** da lâmina **configurações** .

![Correção automática SQL para VMs existentes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

Na lâmina **configuração do SQL Server** , clique no botão **Editar** no automatizada patches seção.

![Configurar o SQL automatizada patches para VMs existentes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Quando terminar, clique no botão **Okey** na parte inferior da lâmina **configuração do SQL Server** para salvar suas alterações.

Se você estiver ativando a aplicação automatizada de patches pela primeira vez, o Azure configura o SQL Server IaaS Agent no plano de fundo. Durante esse período, o portal do Azure pode não mostrar automatizados patch está configurado. Aguarde alguns minutos para o agente ser instalado, configurado. Depois que o portal do Azure reflete as novas configurações.

>[AZURE.NOTE] Você também pode configurar automatizada patch usando um modelo. Para obter mais informações, consulte [modelo de início rápido Azure para aplicação automatizada de patches](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).

## <a name="configuration-with-powershell"></a>Configuração com PowerShell

Após a configuração sua VM SQL, use o PowerShell para configurar a aplicação automatizada de patches.

No exemplo a seguir, o PowerShell é usado para configurar a aplicação automatizada de patches em uma VM de servidor SQL existente. O comando **AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig** configura uma nova janela de manutenção para atualizações automáticas.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

Com base neste exemplo, a tabela a seguir descreve o efeito prático no destino máquina virtual do Azure:

|Parâmetro|Efeito|
|---|---|
|**DayOfWeek**|Patches instalados toda quinta-feira.|
|**MaintenanceWindowStartingHour**|Atualizações de começar às 11:00 am.|
|**MaintenanceWindowsDuration**|Patches devem ser instalados em 120 minutos. Com base na hora de início, eles devem ser concluídas por 1:00 pm.|
|**PatchCategory**|A configuração só é possível para esse parâmetro é **importante**.|

Pode levar alguns minutos para instalar e configurar o SQL Server IaaS Agent.

Para desativar a aplicação automatizada de patches, execute o script mesmo sem o **-Habilitar** parâmetro para a **AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig**. Ausência do **-Habilitar** parâmetro sinaliza o comando para desabilitar o recurso.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre outras tarefas de automação disponíveis, consulte [Extensão do agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre a execução do SQL Server em VMs do Azure, consulte [SQL Server em máquinas virtuais do Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
