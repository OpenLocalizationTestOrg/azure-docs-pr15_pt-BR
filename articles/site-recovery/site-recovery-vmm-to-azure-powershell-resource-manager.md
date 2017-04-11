<properties
    pageTitle="Replicar máquinas virtuais Hyper-V nuvens do VMM usando recuperação de Site do Azure e PowerShell (Resource Manager) | Microsoft Azure"
    description="Replicar máquinas virtuais Hyper-V nuvens do VMM usando o PowerShell e recuperação de Site do Azure"
    services="site-recovery"
    documentationCenter=""
    authors="Rajani-Janaki-Ram"
    manager="rochakm"
    editor="raynew"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="rajanaki"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>Duplicar máquinas virtuais Hyper-V nuvens do VMM para Azure usando o PowerShell e Gerenciador de recursos do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-azure.md)
- [PowerShell - Gerenciador de recursos](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portal clássico](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - clássico](site-recovery-deploy-with-powershell.md)



## <a name="overview"></a>Visão geral

Recuperação de Site Azure contribui para sua estratégia de recuperação (BCDR) de desastre e continuidade de negócios por coordenação replicação, failover e recuperação de máquinas virtuais em um número de cenários de implantação. Para obter uma lista completa de cenários de implantação, consulte [Visão geral de recuperação de Site do Azure](site-recovery-overview.md).

Este artigo mostra como usar o PowerShell para automatizar tarefas comuns, que você precisará executar ao configurar recuperação de Site do Azure para replicar máquinas virtuais Hyper-V nuvens do System Center VMM para armazenamento do Azure.

O artigo inclui os pré-requisitos para o cenário e mostra

- Como configurar um cofre de serviços de recuperação
- Instalar o provedor de recuperação de Site do Azure no servidor VMM de origem
- Registrar o servidor no cofre, adicionar uma conta de armazenamento do Azure
- Instalar o agente de serviços de recuperação do Azure em servidores de host Hyper-V
- Definir configurações de proteção para nuvens do VMM, que serão aplicadas a todas as máquinas virtuais protegidas
- Habilite proteção para essas máquinas virtuais.
- Teste a acima de falhas para garantir que tudo está funcionando conforme esperado.

Se você enfrentar problemas para configurar esse cenário, poste suas perguntas no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos e clássico](../resource-manager-deployment-model.md). Este artigo aborda usando o modelo de implantação do Gerenciador de recursos.

## <a name="before-you-start"></a>Antes de começar

Verifique se que você tem estes pré-requisitos no lugar:

### <a name="azure-prerequisites"></a>Pré-requisitos Azure

- Você precisará de uma conta [Do Microsoft Azure](https://azure.microsoft.com/) . Se você não tiver um, comece com uma [conta gratuita](https://azure.microsoft.com/free). Além disso, você pode ler sobre os [preços do gerente de recuperação de Site do Azure](https://azure.microsoft.com/pricing/details/site-recovery/).
- Você precisará de uma assinatura de CSP se você estiver experimentando a replicação a um cenário de assinatura CSP. Saiba mais sobre o programa CSP em [como se inscrever no programa CSP](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
- Você precisará de uma conta de armazenamento (Resource Manager) v2 Azure para armazenar dados duplicados no Azure. A conta deve replicação geográfica habilitada. Ele deve estar na mesma região como o serviço de recuperação de Site do Azure e ser associadas a mesma assinatura ou a assinatura de CSP. Para saber mais sobre como configurar o armazenamento do Azure, consulte [Introdução ao Microsoft Azure armazenamento](../storage/storage-introduction.md) para referência.
- Você precisará certificar-se de que máquinas virtuais que deseja proteger cumprir os [pré-requisitos do Azure máquina virtual](site-recovery-best-practices.md#azure-virtual-machine-requirements).

> [AZURE.NOTE] Atualmente, apenas operações de nível máquina virtual são possíveis através do Powershell. Suporte para operações de nível de plano de recuperação será feito em breve.  Por agora, você está limitado a executar a renovação de falhas somente em um detalhamento 'protegido máquina virtual' e não em um nível de plano de recuperação.

### <a name="vmm-prerequisites"></a>Pré-requisitos de VMM
- Você precisará servidor VMM em execução no System Center 2012 R2.
- Qualquer servidor VMM contendo máquinas virtuais que deseja proteger deve estar executando o provedor de recuperação de Site do Azure. Isso é instalado durante a implantação do Azure recuperação do Site.
- Você precisará pelo menos uma nuvem no servidor VMM que você deseja proteger. Nuvem deve conter:
    - Um ou mais VMM hospede grupos.
    - Um ou mais servidores de host Hyper-V ou clusters em cada grupo de host.
    - Máquinas virtuais uma ou mais no servidor de origem Hyper-V.
- Saiba mais sobre como configurar nuvens do VMM:
    - Leia mais sobre nuvens privadas do VMM no [que há de novo na nuvem privada com System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) e no [VMM 2012 e as nuvens](http://go.microsoft.com/fwlink/?LinkId=324956).
    - Saiba como [Configurar a estrutura de nuvem VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
    - Depois que os elementos de estrutura de nuvem estão no lugar aprender a criar nuvens privadas na [criação de uma nuvem privada no VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) e no [passo a passo: Criando nuvens privadas com System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/p/?LinkId=324954).


### <a name="hyper-v-prerequisites"></a>Pré-requisitos de Hyper-V

- Os servidores host Hyper-V devem estar executando pelo menos o **Windows Server 2012** com função Hyper-V ou **Microsoft Hyper-V Server 2012** e instalaram as últimas atualizações.
- Se você estiver executando Hyper-V em uma anotação de cluster que corretor de cluster não é criado automaticamente se você tiver um cluster de baseado no endereço IP estático. Você precisará configurar o corretor de cluster manualmente. Para
- Para obter instruções, consulte [como configurar o agente de réplica Hyper-V](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx).
- Qualquer servidor de host Hyper-V ou cluster para o qual você deseja gerenciar proteção deve ser incluído em uma nuvem VMM.

### <a name="network-mapping-prerequisites"></a>Pré-requisitos de mapeamento de rede
Quando você proteger máquinas virtuais no Azure, os mapas de mapeamento de rede na máquina virtual redes no servidor VMM de origem e destino redes Azure para habilitar o seguinte:

- Todas as máquinas que falham sobre na mesma rede podem se conectar uns aos outros, independentemente de qual plano de recuperação estiverem em.
- Se um gateway de rede está configurado no destino rede Azure, máquinas virtuais pode se conectar com outras máquinas virtuais de local.
- Se você não configurar mapeamento de rede, somente as máquinas virtuais que falham sobre no mesmo plano de recuperação será capazes de se conectar uns aos outros após falhas-acima do Azure.

Se você deseja implantar o mapeamento de rede será necessário o seguinte:

- As máquinas virtuais que você deseja proteger no servidor VMM de origem deve estar conectadas a uma rede de máquina virtual. Rede deve ser vinculada a uma rede lógica que está associada com a nuvem.
- Uma rede Azure ao qual replicadas máquinas virtuais podem se conectar após falhas sobre. Você selecionará esta rede no momento do sobre falhas. A rede deve estar na mesma região como sua assinatura de recuperação de Site do Azure.

Saiba mais sobre o mapeamento de rede na

- [Como configurar redes lógicas no VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Como configurar redes de máquina virtual e gateways no VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
- [Como configurar e monitorar redes virtuais no Azure](https://azure.microsoft.com/documentation/services/virtual-network/)


###<a name="powershell-prerequisites"></a>Pré-requisitos do PowerShell
Verifique se que você tem o Azure PowerShell pronta. Se você já estiver usando o PowerShell, você precisará atualizar para a versão 0.8.10 ou posterior. Para obter informações sobre a configuração do PowerShell, consulte o [guia para instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Depois de configurar e configurado PowerShell, você pode exibir todos os cmdlets disponíveis para o serviço [aqui](https://msdn.microsoft.com/library/dn850420.aspx).

Para saber mais sobre dicas que podem ajudar que você usa os cmdlets, como como saídas, entradas e valores de parâmetro normalmente são tratadas no Azure PowerShell, consulte o [guia de Introdução ao Azure Cmdlets](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## <a name="step-1-set-the-subscription"></a>Etapa 1: Definir a assinatura

1. Do Azure powershell, faça logon em sua conta do Azure: usando os seguintes cmdlets

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred


2. Obter uma lista das suas assinaturas. Isso também listará o subscriptionIDs para cada uma das assinaturas. Anote a subscriptionID da assinatura na qual você deseja criar o Cofre de serviços de recuperação

        Get-AzureRmSubscription

3. Definir a inscrição no qual o Cofre de serviços de recuperação será criada por mencionar a ID da assinatura

        Set-AzureRmContext –SubscriptionID <subscriptionId>


## <a name="step-2-create-a-recovery-services-vault"></a>Etapa 2: Criar um cofre de serviços de recuperação

1. Criar um grupo de recursos no Gerenciador de recursos do Azure se você não tenha um

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Criar um novo cofre de serviços de recuperação e salve o objeto de cofre ASR criado em uma variável (será usado mais tarde). Você também pode recuperar a criação de postagem do ASR cofre objeto usando o cmdlet Get-AzureRMRecoveryServicesVault:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Etapa 3: Configurar o contexto de Cofre de serviços de recuperação

1.  Definir o contexto de cofre executando o abaixo comando.

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Etapa 4: Instalar o provedor de recuperação de Site Azure

1.  Na máquina VMM, crie um diretório executando o comando a seguir:

        New-Item c:\ASR -type directory

2.  Extrair os arquivos usando o provedor baixado executando o seguinte comando

        pushd C:\ASR\
        .\AzureSiteRecoveryProvider.exe /x:. /q


3.  Instale o provedor usando os seguintes comandos:

        .\SetupDr.exe /i
        $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
        do
        {
                        $isNotInstalled = $true;
                        if(Test-Path $installationRegPath)
                        {
                                        $isNotInstalled = $false;
                        }
        }While($isNotInstalled)

    Aguarde concluir a instalação.

4.  Registre o servidor no cofre usando o seguinte comando:

        $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
        pushd $BinPath
        $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice


## <a name="step-5-create-an-azure-storage-account"></a>Etapa 5: Criar uma conta de armazenamento do Azure

1. Se você não tiver uma conta de armazenamento do Azure, crie uma conta de replicação geográfica habilitada na localização geográfica mesma como o cofre executando o seguinte comando:

        $StorageAccountName = "teststorageacc1" #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"  
        $ResourceGroupName =  “myRG”            #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Observe que a conta de armazenamento deve estar na mesma região como o serviço de recuperação de Site do Azure e ser associadas a mesma assinatura.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>Etapa 6: Instalar o agente de serviços de recuperação Azure

1. Baixe o agente de serviços de recuperação do Azure no [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) e instale-o em cada servidor de host Hyper-V localizado nas nuvens VMM que você deseja proteger.

2. Execute o seguinte comando em todos os hosts VMM:

    marsagentinstaller.exe /q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>Etapa 7: Configurar nuvem as configurações de proteção

1.  Crie uma política de replicação no Azure executando o seguinte comando:


        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

2.  Obtenha um contêiner de proteção executando os seguintes comandos:

        $PrimaryCloud = "testcloud"
        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

3.  Obter os detalhes da política a uma variável usando o trabalho que foi criado e mencionar o nome da política amigável:

        $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.  Inicie a associação de contêiner de proteção com a política de replicação:

        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  

5.  Após concluir o trabalho, execute o seguinte comando:

        $job = Get-AzureRmSiteRecoveryJob -Job $associationJob
        if($job -eq $null -or $job.StateDescription -ne "Completed")
         {
        $isJobLeftForProcessing = $true;
        }

6.  Após o trabalho de processamento, execute o seguinte comando:

        if($isJobLeftForProcessing)
        {
        Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)

Para verificar a conclusão da operação, siga as etapas em [Monitorar a atividade](#monitor).

## <a name="step-8-configure-network-mapping"></a>Etapa 8: Configurar mapeamento de rede

Antes de começar o mapeamento de rede verificar se máquinas virtuais no servidor VMM de origem estão conectadas a uma rede de máquina virtual. Além disso, crie uma ou mais redes virtuais Azure.

Saiba mais sobre como criar uma rede virtual usando o Gerenciador de recursos do Azure e PowerShell, em [criar uma rede virtual com uma conexão de VPN - to-site usando o Gerenciador de recursos do Azure e PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

Observe que várias redes de máquina Virtual podem ser mapeadas para uma única rede Azure. Se a rede de destino tem várias sub-redes e uma dessas sub-redes tem o mesmo nome de sub-rede na qual a máquina virtual de origem está localizada, em seguida, na máquina virtual de réplica será conectada para aquela sub-rede de destino após falhas sobre. Se não houver nenhuma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.

1. O primeiro comando obtém os servidores para o cofre Azure recuperação do Site atual. O comando armazena os servidores de recuperação de Site do Microsoft Azure na variável matriz $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer

2. O segundo comando obtém a rede de recuperação do site para o primeiro servidor na matriz $Servers. O comando armazena as redes na variável $Networks.


        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

3. O terceiro comando obtém Azure redes virtuais e, em seguida, esse valor na variável $AzureVmNetworks.

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork

4. O cmdlet final cria um mapeamento entre a principal e rede o Azure máquina virtual. O cmdlet especifica à rede principal como o primeiro elemento da $Networks. O cmdlet especifica uma rede de máquina virtual como o primeiro elemento de $AzureVmNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]


## <a name="step-9-enable-protection-for-virtual-machines"></a>Etapa 9: Habilitar proteção para máquinas virtuais

Depois de servidores, nuvens e redes estão configurados corretamente, você pode habilitar a proteção para máquinas virtuais na nuvem.

 Observe o seguinte:

 - Máquinas virtuais devem atender aos requisitos do Azure. Verifique estes em [pré-requisitos e suporte](site-recovery-best-practices.md) no guia de planejamento.

 - Para habilitar a proteção, o sistema operacional e o sistema operacional propriedades de disco devem ser definidas para a máquina virtual. Quando você cria uma máquina virtual no VMM usando um modelo de máquina virtual, você pode definir a propriedade. Você também pode definir essas propriedades para máquinas virtuais existentes nas guias **Geral** e **Configuração de Hardware** das propriedades de máquina virtual. Se você não definir essas propriedades no VMM, você poderá configurá-los no portal do Azure recuperação do Site.


  1. Para ativar a proteção, execute o seguinte comando para obter o contêiner de proteção:

            $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName

  2. Obter a entidade de proteção (máquina virtual) executando o seguinte comando:

            $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer

  3. Habilite o DR para a máquina virtual executando o seguinte comando:

            $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1



## <a name="test-your-deployment"></a>Testar sua implantação

Para testar sua implantação, você pode executar um teste falhas em uma única máquina virtual, ou criar um plano de recuperação consiste em várias máquinas virtuais e executar um teste failover para o plano. Teste sobre falhas simula o mecanismo de falhas e recuperação em uma rede isolada. Observe que:

- Se você deseja se conectar à máquina virtual no Azure usando a área de trabalho remota após o sobre falhas, habilite Conexão de área de trabalho remota na máquina virtual antes de executar o failover de teste.
- Após falhas sobre, você vai usar um endereço IP público para conectar-se à máquina virtual no Azure usando a área de trabalho remota. Se você quiser fazer isso, certifique-se de que você não tem quaisquer diretivas de domínio que impedem a conexão para uma máquina virtual usando um endereço público.

Para verificar a conclusão da operação, siga as etapas em [Monitorar a atividade](#monitor).


### <a name="run-a-test-failover"></a>Executar um failover de teste

1.  Inicie o failover de teste executando o seguinte comando:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>Executar um failover planejado

1. Inicie o failover planejado executando o seguinte comando:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>Executar um failover não planejado

1. Inicie o failover planejado executando o seguinte comando:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  


## <a name=monitor></a>Monitorar a atividade

Use os seguintes comandos para monitorar a atividade. Observe que você precisa esperar entre os trabalhos para o processamento concluir.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Próximas etapas

[Leia mais](https://msdn.microsoft.com/library/azure/mt637930.aspx) sobre recuperação de Site do Azure com cmdlets do PowerShell do Gerenciador de recursos do Azure.
