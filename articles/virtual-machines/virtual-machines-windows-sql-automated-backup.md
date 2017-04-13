<properties
    pageTitle="Backup automático para máquinas virtuais do servidor SQL (Gerenciador de recurso) | Microsoft Azure"
    description="Explica o recurso de Backup automatizado para SQL Server em execução em máquinas virtuais do Azure usando o Gerenciador de recursos. "
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
    ms.date="07/14/2016"
    ms.author="jroth" />

# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Backup automatizado para SQL Server no Azure máquinas virtuais (Gerenciador de recurso)

> [AZURE.SELECTOR]
- [Gerenciador de recursos](virtual-machines-windows-sql-automated-backup.md)
- [Clássico](virtual-machines-windows-classic-sql-automated-backup.md)

Backup automatizado configura automaticamente o [Backup gerenciado ao Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os novos e existentes bancos de dados em uma VM Azure executando o SQL Server 2014 Standard ou Enterprise. Isso permite que você configure backups regulares do banco de dados que utilizam o armazenamento de blob do Microsoft Azure durável. Backup automatizado depende da [Extensão do agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico. Para exibir a versão clássica deste artigo, consulte [Automatizada Backup para SQL Server no Azure máquinas virtuais clássico](virtual-machines-windows-classic-sql-automated-backup.md).

## <a name="prerequisites"></a>Pré-requisitos

Para usar o Backup automatizado, considere os seguintes pré-requisitos:

**Sistema operacional**:

- Windows Server 2012
- Windows Server 2012 R2

**Versão/edição do SQL Server**:

- SQL Server 2014 padrão
- SQL Server 2014 Enterprise

**Configuração de banco de dados**:

- Bancos de dados de destino devem usar o modelo de recuperação total

**Azure PowerShell**:

- [Instalar os comandos mais recentes do Azure PowerShell](../powershell-install-configure.md) se você planeja configurar Backup automatizado com o PowerShell.

>[AZURE.NOTE] Backup automatizado depende da extensão do agente do SQL Server IaaS. Imagens de galeria de máquina virtual SQL atual adicionar essa extensão por padrão. Para obter mais informações, consulte [Extensão do agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Configurações

A tabela a seguir descreve as opções que podem ser configuradas para Backup automatizado. As etapas de configuração reais variam dependendo se você usar o portal do Azure ou comandos do PowerShell do Windows Azure.

|Configuração|Intervalo (padrão)|Descrição|
|---|---|---|
|**Backup automatizado**|Ativar/desativar (desabilitado)|Habilita ou desabilita Backup automatizado para uma máquina virtual de Azure executando o SQL Server 2014 Standard ou Enterprise.|
|**Período de retenção**|1-30 dias (30 dias)|O número de dias para manter um backup.|
|**Conta de armazenamento**|Conta de armazenamento do Azure (a conta de armazenamento criada para a máquina virtual especificada)|Uma conta de armazenamento do Azure para usar para armazenar arquivos de Backup automático no armazenamento de blob. Um contêiner é criado neste local para armazenar todos os arquivos de backup. A convenção de nomenclatura de arquivo de backup inclui a data, hora e nome da máquina.|
|**Criptografia**|Ativar/desativar (desabilitado)|Habilita ou desabilita a criptografia. Quando a criptografia estiver habilitada, os certificados usados para restaurar o backup estão localizados na conta de armazenamento especificado no mesmo contêiner automaticbackup usando a mesma convenção de nomenclatura. Se a senha for alterada, um novo certificado é gerado com essa senha, mas o certificado antigo permanece restaurar backups anteriores.|
|**Senha**|Texto de senha (nenhum)|Uma senha para chaves de criptografia. Isso só é necessário se a criptografia estiver ativada. Para restaurar um backup criptografado, você deve ter a senha correta e o certificado relacionado que foi usado no momento em que o backup foi feito.|

## <a name="configuration-in-the-portal"></a>Configuração no Portal
Você pode usar o Portal do Azure para configurar Backup automatizado durante provisionamento ou para VMs existentes.

### <a name="new-vms"></a>Novas VMs
Use o Portal do Azure para configurar Backup automatizado quando você cria uma nova máquina SQL Server 2014 Virtual no modelo de implantação do Gerenciador de recursos.

Na lâmina **configurações do SQL Server** , selecione **backup automático**. A captura de tela portal Azure seguinte mostra a lâmina de **Backup automatizado de SQL** .

![Configuração de Backup automatizado do SQL no portal do Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Para o contexto, consulte o tópico concluído em [uma máquina virtual do SQL Server Azure de provisionamento](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes
Para máquinas virtuais do SQL Server existentes, selecione máquina virtual do SQL Server. Em seguida, selecione a seção de **configuração do SQL Server** da lâmina **configurações** .

![Backup automatizado de SQL para VMs existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Na lâmina **configuração do SQL Server** , clique no botão **Editar** na seção de backup automatizada.

![Configurar o Backup de automatizado de SQL para VMs existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Quando terminar, clique no botão **Okey** na parte inferior da lâmina **configuração do SQL Server** para salvar suas alterações.

Se você estiver ativando Backup automatizado pela primeira vez, o Azure configura o SQL Server IaaS Agent no plano de fundo. Durante esse período, o portal do Azure pode não mostrar que o Backup automatizada está configurado. Aguarde alguns minutos para o agente ser instalado, configurado. Depois disso, o portal do Azure refletirão as novas configurações.

>[AZURE.NOTE] Você também pode configurar Backup automatizado usando um modelo. Para obter mais informações, consulte o [modelo de início rápido Azure para Backup automatizado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configuration-with-powershell"></a>Configuração com PowerShell

Após a configuração sua VM SQL, use o PowerShell para configurar Backup automatizado.

No exemplo a seguir do PowerShell, Backup automatizado está configurado para uma 2014 VM existente do SQL Server. O comando **AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig** configura as configurações de Backup automatizado para armazenar backups na conta de armazenamento do Azure associada a máquina virtual. Esses backups serão mantidos por 10 dias. O comando **Set-AzureRmVMSqlServerExtension** atualiza VM Azure especificada com essas configurações.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriodInDays 10 -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Pode levar alguns minutos para instalar e configurar o SQL Server IaaS Agent.

Para ativar a criptografia, modifique o script anterior para passar o parâmetro **EnableEncryption** juntamente com uma senha (cadeia segura) para o parâmetro **CertificatePassword** . O script a seguir permite que as configurações de Backup automático no exemplo anterior e adiciona criptografia.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Para desativar o backup automático, execute o script mesmo sem o **-Habilitar** parâmetro com o comando **AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig** . Ausência do **-Habilitar** parâmetro sinaliza o comando para desabilitar o recurso. Como com a instalação, pode levar alguns minutos para desabilitar o Backup automatizado.

>[AZURE.NOTE] Removendo o SQL Server IaaS Agent não remove as configurações de Backup automatizado anteriormente definidas. Você deve desativar o Backup automatizado antes de desabilitar ou desinstalar o SQL Server IaaS Agent.

## <a name="next-steps"></a>Próximas etapas

Backup automatizado configura o Backup gerenciado no Azure VMs. Portanto é importante revisar [a documentação de Backup gerenciado](https://msdn.microsoft.com/library/dn449496.aspx) entender o comportamento e implicações.

Você pode localizar adicional backup e restaurar a orientação para o SQL Server em VMs Azure no tópico a seguir: [Backup e restauração para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

Para obter informações sobre outras tarefas de automação disponíveis, consulte [Extensão do agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre a execução do SQL Server em VMs do Azure, consulte [SQL Server em máquinas virtuais do Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
