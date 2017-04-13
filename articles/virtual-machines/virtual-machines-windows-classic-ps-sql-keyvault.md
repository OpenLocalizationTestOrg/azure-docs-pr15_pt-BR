<properties
    pageTitle="Configurar a integração do Azure cofre chave para o SQL Server em VMs Azure (clássico)"
    description="Saiba como automatizar a configuração de criptografia do SQL Server para uso com o Azure chave cofre. Este tópico explica como usar o Azure chave cofre integração com o SQL Server máquinas virtuais criadas no modelo de implantação clássico."
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
    ms.date="09/26/2016"
    ms.author="jroth"/>

# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-classic"></a>Configurar a integração do Azure cofre chave para o SQL Server em VMs Azure (clássico)

> [AZURE.SELECTOR]
- [Gerenciador de recursos](virtual-machines-windows-ps-sql-keyvault.md)
- [Clássico](virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Visão geral
Há vários recursos de criptografia do SQL Server, como [criptografia de dados transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [criptografia de nível de coluna (LIM)](https://msdn.microsoft.com/library/ms173744.aspx)e [criptografia de backup](https://msdn.microsoft.com/library/dn449489.aspx). Esses formulários de criptografia exigem que você gerenciar e armazenar as chaves de criptografia usado para criptografia. O serviço do Azure chave cofre (AKV) foi projetado para melhorar a segurança e o gerenciamento destas teclas em um local seguro e altamente disponível. O [SQL Server Connector](http://www.microsoft.com/download/details.aspx?id=45344) permite que o SQL Server para utilizar estas teclas do Azure chave cofre.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Se você estiver executando o SQL Server com máquinas locais, há [etapas que você pode seguir para acessar Azure chave cofre da máquina local do SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Mas, para SQL Server no Azure VMs, você poderá economizar tempo usando o recurso de *Integração de Cofre de chave do Azure* . Com alguns cmdlets do PowerShell do Azure para ativar esse recurso, você pode automatizar a configuração necessária para uma VM SQL para acessar seu Cofre de chave.

Quando este recurso está habilitado, ele automaticamente instala o SQL Server Connector, configura o provedor EKM para acessar Cofre de chave do Azure e cria a credencial para que você possa acessar seu cofre. Se você procurou as etapas na documentação do local mencionado anteriormente, você pode ver que esse recurso automatiza as etapas 2 e 3. A única coisa que você ainda precisa fazer manualmente é criar as teclas e chave cofre. A partir daí, toda a configuração do seu SQL VM é automatizada. Depois que esse recurso concluiu essa configuração, você pode executar instruções T-SQL para começar a criptografar seus bancos de dados ou backups como você faria normalmente.

[AZURE.INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Configurar a integração de AKV
Usar o PowerShell para configurar a integração do Azure chave cofre. As seções a seguir fornecem uma visão geral dos parâmetros necessários e, em seguida, um exemplo de script do PowerShell.

### <a name="install-the-sql-server-iaas-extension"></a>Instale a extensão de IaaS do SQL Server

Primeiro, [instale a extensão de IaaS do SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Compreender os parâmetros de entrada
A tabela a seguir lista os parâmetros necessários para executar o script do PowerShell na próxima seção.

|Parâmetro|Descrição|Exemplo|
|---|---|---|
|**$akvURL**|**A URL do cofre chave**|"https://contosokeyvault.vault.azure.net/"|
|**$spName**|**Nome Principal do serviço**|"fde2b411 - 33d 5-4e11-af04eb07b669ccf2"|
|**$spSecret**|**Secreta de capital de serviço**|"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM ="|
|**$credName**|**Nome de credencial**: integração de AKV cria uma credencial dentro do SQL Server, permitindo que a máquina virtual tenha acesso a chave cofre. Escolha um nome para esta credencial.|"mycred1"|
|**$vmName**|**Nome de máquina virtual**: O nome de uma VM SQL criado anteriormente.|"myvmname"|
|**$serviceName**|**Nome do serviço**: nome do serviço de nuvem que está associado com a VM de SQL.|"mycloudservicename"|

### <a name="enable-akv-integration-with-powershell"></a>Habilitar a integração de AKV com o PowerShell
O cmdlet **New-AzureVMSqlServerKeyVaultCredentialConfig** cria um objeto de configuração para o recurso de integração de Cofre de chave do Azure. O **Conjunto AzureVMSqlServerExtension** configura essa integração com o parâmetro **KeyVaultCredentialSettings** . As etapas a seguir mostram como usar esses comandos.

1. PowerShell do Azure, primeiro configure os parâmetros de entrada com valores específicos conforme descrito nas seções anteriores deste tópico. O script a seguir é um exemplo.

        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2.  Use o seguinte script para configurar e habilitar a integração de AKV.

        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

A extensão do agente do SQL IaaS atualizará a VM SQL com essa nova configuração.

[AZURE.INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]
