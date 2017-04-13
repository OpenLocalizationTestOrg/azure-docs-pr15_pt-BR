<properties
    pageTitle="Backup automático para máquinas virtuais do servidor SQL (clássico) | Microsoft Azure"
    description="Explica o recurso de Backup automatizado para SQL Server em execução em máquinas virtuais do Azure usando o Gerenciador de recursos. "
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

# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Backup automatizado para SQL Server no Azure máquinas virtuais (clássico)

> [AZURE.SELECTOR]
- [Gerenciador de recursos](virtual-machines-windows-sql-automated-backup.md)
- [Clássico](virtual-machines-windows-classic-sql-automated-backup.md)

Backup automatizado configura automaticamente o [Backup gerenciado ao Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os novos e existentes bancos de dados em uma VM Azure executando o SQL Server 2014 Standard ou Enterprise. Isso permite que você configure backups regulares do banco de dados que utilizam o armazenamento de blob do Microsoft Azure durável. Backup automatizado depende da [Extensão do agente do SQL Server IaaS](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para exibir a versão do Gerenciador de recursos deste artigo, consulte [Automatizada Backup para SQL Server no Gerenciador de recursos de máquinas virtuais do Azure](virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Pré-requisitos

Para usar o Backup automatizado, considere os seguintes pré-requisitos:

**Sistema operacional**:

- Windows Server 2012
- Windows Server 2012 R2

**Versão/edição do SQL Server**:

- SQL Server 2014 padrão
- SQL Server 2014 Enterprise

>[AZURE.NOTE] SQL Server 2016 ainda não é suportado para Backup automatizado.

**Configuração de banco de dados**:

- Bancos de dados de destino devem usar o modelo de recuperação total.

**Azure PowerShell**:

- [Instalar os comandos do PowerShell do Azure mais recentes](../powershell-install-configure.md).

**Extensão de IaaS do SQL Server**:

- [Instale a extensão de IaaS do SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="settings"></a>Configurações

A tabela a seguir descreve as opções que podem ser configuradas para Backup automatizado. Para VMs clássicas, você deve usar o PowerShell para definir essas configurações.

|Configuração|Intervalo (padrão)|Descrição|
|---|---|---|
|**Backup automatizado**|Ativar/desativar (desabilitado)|Habilita ou desabilita Backup automatizado para uma máquina virtual de Azure executando o SQL Server 2014 Standard ou Enterprise.|
|**Período de retenção**|1-30 dias (30 dias)|O número de dias para manter um backup.|
|**Conta de armazenamento**|Conta de armazenamento do Azure (a conta de armazenamento criada para a máquina virtual especificada)|Uma conta de armazenamento do Azure para usar para armazenar arquivos de Backup automático no armazenamento de blob. Um contêiner é criado neste local para armazenar todos os arquivos de backup. A convenção de nomenclatura de arquivo de backup inclui a data, hora e nome da máquina.|
|**Criptografia**|Ativar/desativar (desabilitado)|Habilita ou desabilita a criptografia. Quando a criptografia estiver habilitada, os certificados usados para restaurar o backup estão localizados na conta de armazenamento especificado no mesmo contêiner automaticbackup usando a mesma convenção de nomenclatura. Se a senha for alterada, um novo certificado é gerado com essa senha, mas o certificado antigo permanece restaurar backups anteriores.|
|**Senha**|Texto de senha (nenhum)|Uma senha para chaves de criptografia. Isso só é necessário se a criptografia estiver ativada. Para restaurar um backup criptografado, você deve ter a senha correta e o certificado relacionado que foi usado no momento em que o backup foi feito.|

## <a name="configuration-with-powershell"></a>Configuração com PowerShell

No exemplo a seguir do PowerShell, Backup automatizado está configurado para uma 2014 VM existente do SQL Server. O comando **New-AzureVMSqlServerAutoBackupConfig** configura as configurações de Backup automático para armazenar backups na conta de armazenamento do Azure especificada pela variável $storageaccount. Esses backups serão mantidos por 10 dias. O comando **Set-AzureVMSqlServerExtension** atualiza VM Azure especificada com essas configurações.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Pode levar alguns minutos para instalar e configurar o SQL Server IaaS Agent.

Para ativar a criptografia, modifique o script anterior para passar o parâmetro EnableEncryption juntamente com uma senha (cadeia segura) para o parâmetro CertificatePassword. O script a seguir permite que as configurações de Backup automático no exemplo anterior e adiciona criptografia.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Para desativar o backup automático, execute o script mesmo sem o **-Habilitar** parâmetro para o **Novo AzureVMSqlServerAutoBackupConfig**. Como com a instalação, pode levar alguns minutos para desabilitar o Backup automatizado.

>[AZURE.NOTE] Desabilitando e desinstalando o SQL Server IaaS Agent não remove as configurações de Backup gerenciado anteriormente definidas. Você deve desativar o Backup automatizado antes de desabilitar ou desinstalar o SQL Server IaaS Agent.

## <a name="next-steps"></a>Próximas etapas

Backup automatizado configura o Backup gerenciado no Azure VMs. Portanto é importante revisar [a documentação de Backup gerenciado](https://msdn.microsoft.com/library/dn449496.aspx) entender o comportamento e implicações.

Você pode localizar adicional backup e restaurar a orientação para o SQL Server em VMs Azure no tópico a seguir: [Backup e restauração para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

Para obter informações sobre outras tarefas de automação disponíveis, consulte [Extensão do agente do SQL Server IaaS](virtual-machines-windows-classic-sql-server-agent-extension.md).

Para obter mais informações sobre a execução do SQL Server em VMs do Azure, consulte [SQL Server em máquinas virtuais do Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).
