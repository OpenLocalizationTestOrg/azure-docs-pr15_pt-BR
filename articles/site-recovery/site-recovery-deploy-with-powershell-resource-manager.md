<properties
    pageTitle="Proteger os servidores do Azure usando o PowerShell do Azure com o Gerenciador de recursos do Azure | Microsoft Azure"
    description="Automatize proteção de servidores no Azure com recuperação de Site do Azure usando o PowerShell e Gerenciador de recursos do Azure."
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="09/27/2016"
    ms.author="bsiva"/>

# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-by-using-powershell-and-azure-resource-manager"></a>Replicar entre máquinas de virtuais Hyper-V local e Azure usando o PowerShell e Gerenciador de recursos do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-hyper-v-site-to-azure.md)
- [PowerShell - Gerenciador de recursos](site-recovery-deploy-with-powershell-resource-manager.md)
- [Portal clássico](site-recovery-hyper-v-site-to-azure-classic.md)



## <a name="overview"></a>Visão geral

Recuperação de Site Azure contribui para sua estratégia de recuperação de desastre e continuidade de negócios por coordenação replicação, failover e recuperação de máquinas virtuais em um número de cenários de implantação. Para obter uma lista completa de cenários de implantação, consulte [Visão geral de recuperação de Site do Azure](site-recovery-overview.md).

PowerShell Azure é um módulo que fornece cmdlets para gerenciar Azure através do Windows PowerShell. Ele pode funcionar com dois tipos de módulos: módulo Azure perfil ou o módulo do Gerenciador de recursos do Azure.

Cmdlets do PowerShell de recuperação do site, disponíveis com o Azure PowerShell do Gerenciador de recursos do Azure, ajudar você a proteger e recuperar seus servidores no Azure.

Este artigo descreve como usar o Windows PowerShell, junto com o Gerenciador de recursos do Azure, para implantar recuperação de Site para configurar e coordenar proteção de servidor no Azure. O exemplo usado neste artigo mostra como proteger, falhar e recuperar máquinas virtuais em um host Hyper-V no Azure, usando o PowerShell do Azure com o Gerenciador de recursos do Azure.

> [AZURE.NOTE] Cmdlets do PowerShell de recuperação de Site atualmente permitem que você configure o seguinte: um local de Gerenciador de máquina Virtual para outro, um site do Gerenciador de máquina Virtual para o Azure e um site de Hyper-V no Azure.

Você não precisa ser um especialista o PowerShell para usar este artigo, mas você precisa compreender os conceitos básicos, como módulos, cmdlets e sessões. Para obter mais informações sobre o Windows PowerShell, consulte [Introdução ao Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Você também pode ler mais sobre [Usando o PowerShell Azure com o Gerenciador de recursos do Azure](../powershell-azure-resource-manager.md).

> [AZURE.NOTE] Parceiros da Microsoft que fazem parte do programa do provedor de solução da nuvem (CSP) podem configurar e gerenciar a proteção de servidores de seus clientes às assinaturas de CSP respectivos de seus clientes (locatário assinaturas).

## <a name="before-you-start"></a>Antes de começar

Verifique se que você tem estes pré-requisitos no lugar:

- Uma conta do [Microsoft Azure](https://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). Além disso, você pode ler sobre os [preços do gerente de recuperação de Site do Azure](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell 1.0. Para obter informações sobre essa versão e como instalá-lo, consulte [Azure PowerShell 1.0.](https://azure.microsoft.com/)
- Os módulos [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) e [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) . Você pode obter as versões mais recentes desses módulos da [Galeria do PowerShell](https://www.powershellgallery.com/)

Este artigo ilustra como usar o Powershell do Azure com o Gerenciador de recursos do Azure para configurar e gerenciar a proteção dos seus servidores. O exemplo usado neste artigo mostra como proteger uma máquina virtual, em execução em um host Hyper-V, no Azure. Os seguintes pré-requisitos são específicos para este exemplo. Para um conjunto mais abrangente de requisitos para os diversos cenários de recuperação de Site, consulte a documentação pertencentes a esse cenário.

- Um host Hyper-V executando o Windows Server 2012 R2 ou Microsoft Hyper-V Server 2012 R2 que contém uma ou mais máquinas virtuais.
- Servidores Hyper-V conectado à Internet, diretamente ou através de um proxy.
- As máquinas virtuais que você deseja proteger deve estar em conformidade com [pré-requisitos de máquina Virtual](site-recovery-best-practices.md#virtual-machines).


## <a name="step-1-sign-in-to-your-azure-account"></a>Etapa 1: Inscrever sua conta do Azure


1. Abrir um console do PowerShell e execute este comando para entrar em sua conta do Azure. O cmdlet traz uma página da web que solicitará suas credenciais de conta.

        Login-AzureRmAccount

    Como alternativa, você também pode incluir suas credenciais de conta como um parâmetro para o `Login-AzureRmAccount` cmdlet, usando o `-Credential` parâmetro.

    Se você estiver trabalhando em nome de um locatário de parceiro CSP, especifique o cliente como um locatário, usando seu nome de domínio primário tenantID ou locatário.

        Login-AzureRmAccount -Tenant "fabrikam.com"

2. Uma conta pode ter várias assinaturas, portanto você deve associar a assinatura que você deseja usar com a conta.

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  Verificar se sua assinatura está registrada para usar os Azure provedores de serviços de recuperação e recuperação de Site, usando os seguintes comandos:

    - `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
    -  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

    Na saída desses comandos, se o **RegistrationState** está definido como **registrado**, você pode continuar a etapa 2. Caso contrário, você deve registrar o provedor ausente na sua assinatura.

    Para registrar o provedor Azure para recuperação de Site e serviços de recuperação, execute os seguintes comandos:

        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

    Verificar se os provedores registrado com êxito, usando os seguintes comandos: `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` e `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.



## <a name="step-2-set-up-the-recovery-services-vault"></a>Etapa 2: Configurar o Cofre de serviços de recuperação

1. Crie um grupo de recursos do Gerenciador de recursos do Azure em que você vai criar o cofre ou usar um grupo de recursos existente. Você pode criar um novo grupo de recursos usando o seguinte comando:

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    onde a variável $ResourceGroupName contém o nome do grupo de recursos que você deseja criar e a variável de $Geo contém o Azure região no qual deseja criar o grupo de recursos (por exemplo, "Sul Brasil").

    Você pode obter uma lista de grupos de recursos em sua assinatura usando o `Get-AzureRmResourceGroup` cmdlet.

2. Crie um novo cofre de serviços de recuperação do Azure da seguinte maneira:

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Você pode recuperar uma lista de compartimentos existentes usando o `Get-AzureRmRecoveryServicesVault` cmdlet.

> [AZURE.NOTE] Se você desejar executar operações em compartimentos de recuperação de Site criados usando o portal do clássico ou o módulo do PowerShell de gerenciamento de serviço do Azure, você pode recuperar uma lista de tais compartimentos usando o `Get-AzureRmSiteRecoveryVault` cmdlet. Você deve criar um novo cofre de serviços de recuperação para todas as operações de novo. Os compartimentos de recuperação do Site que você criou anteriormente são suportados, mas não tem os recursos mais recentes.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Etapa 3: Configurar o contexto de Cofre de serviços de recuperação

1.  Defina o contexto de cofre executando o seguinte comando:

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-create-a-hyper-v-site-and-generate-a-new-vault-registration-key-for-the-site"></a>Etapa 4: Criar um site de Hyper-V e gerar uma nova chave de registro do cofre para o site.

1. Crie um novo site de Hyper-V da seguinte maneira:

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    Esse cmdlet inicia um trabalho de recuperação de Site para criar o site e retorna um objeto de trabalho de recuperação do Site. Aguarde o trabalho concluir e verificar se o trabalho que foi concluído com êxito.

    Você pode recuperar o objeto de trabalho e, portanto, verifique o status atual do trabalho, usando o cmdlet Get-AzureRmSiteRecoveryJob.
2. Gerar e baixar uma chave de registro para o site, da seguinte maneira:

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    Copie a chave baixada para o host Hyper-V. Você precisa da chave para registrar o host Hyper-V para o site.

## <a name="step-5-install-the-azure-site-recovery-provider-and-azure-recovery-services-agent-on-your-hyper-v-host"></a>Etapa 5: Instalar o provedor de recuperação de Site do Azure e agente de serviços de recuperação do Azure em seu host Hyper-V

1. Baixe o instalador para a versão mais recente do provedor da [Microsoft](https://aka.ms/downloaddra).

2. Executar o instalador do seu host Hyper-V e no final da instalação continue para a etapa de registro.

3. Quando solicitado, forneça a chave do registro de site baixado e registro completo do host Hyper-V para o site.

4. Verificar se o host Hyper-V está registrado no site usando o seguinte comando:

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy-and-associate-it-with-the-protection-container"></a>Etapa 6: Criar uma política de replicação e associá-lo com o contêiner de proteção

1. Crie uma política de replicação da seguinte maneira:

        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

    Verificar o trabalho retornado para garantir que a criação de política de replicação é bem sucedida.

    >[AZURE.IMPORTANT] A conta de armazenamento especificada deve estar na mesma região Azure como seu Cofre de serviços de recuperação e deve ter replicação geográfica habilitada.
    >
    > - Se a conta de armazenamento de recuperação especificada for do tipo de armazenamento do Azure (clássico), failover das máquinas protegidas recuperar a máquina Azure IaaS (clássico).
    > - Se a conta de armazenamento de recuperação especificada for do tipo de armazenamento do Azure (Azure Resource Manager), failover das máquinas protegidas recuperar a máquina Azure IaaS (Azure Resource Manager).

2. Obter o contêiner de proteção correspondente ao site, da seguinte maneira:

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.  Inicie a associação de contêiner de proteção com a política de replicação, da seguinte maneira:

        $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

    Aguarde o conclusão do trabalho associação e certifique-se de que ele foi concluída com êxito.

##<a name="step-7-enable-protection-for-virtual-machines"></a>Etapa 7: Habilitar proteção para máquinas virtuais

1. Obter a entidade de proteção correspondente para a máquina virtual que você deseja proteger, da seguinte maneira:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. Inicie a proteção da máquina virtual, da seguinte maneira:

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

    >[AZURE.IMPORTANT] A conta de armazenamento especificada deve estar na mesma região Azure como seu Cofre de serviços de recuperação e deve ter replicação geográfica habilitada.
    >
    > - Se a conta de armazenamento de recuperação especificada for do tipo de armazenamento do Azure (clássico), failover das máquinas protegidas recuperar a máquina Azure IaaS (clássico).
    > - Se a conta de armazenamento de recuperação especificada for do tipo de armazenamento do Azure (Azure Resource Manager), failover das máquinas protegidas recuperar a máquina Azure IaaS (Azure Resource Manager).

    > Se a máquina virtual está protegendo tem mais de um disco anexado a ele, especifique o disco de sistema operacional usando o parâmetro *OSDiskName* .

3. Aguarde até que as máquinas virtuais alcançar um estado protegido após a replicação inicial. Isso pode demorar um pouco, dependendo de fatores como a quantidade de dados sejam replicados e a largura de banda upstream disponível no Azure. O estado de trabalho e StateDescription são atualizadas da seguinte maneira, após a máquina virtual contatar um estado protegido.

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed

4. Atualize propriedades de recuperação, como o tamanho de função de máquina virtual e rede do Azure para anexar cartões de interface de rede da máquina virtual em após failover.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob


        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>Etapa 8: Executar um failover de teste

1. Execute um trabalho de failover de teste, da seguinte maneira:

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. Verificar se o teste de máquina virtual é criado no Azure. (O trabalho de failover de teste está suspenso, depois de criar o teste de máquina virtual no Azure. O trabalho completa limpando os artefatos criados para sair do trabalho, conforme ilustrado na próxima etapa.)

3. Conclua a failover de teste, da seguinte maneira:

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob


##<a name="next-steps"></a>Próximas etapas

[Leia mais](https://msdn.microsoft.com/library/azure/mt637930.aspx) sobre recuperação de Site do Azure com cmdlets do PowerShell do Gerenciador de recursos do Azure.
