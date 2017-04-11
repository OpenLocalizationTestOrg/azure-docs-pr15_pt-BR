<properties
    pageTitle="Duplicar máquinas virtuais Hyper-V nuvens do VMM para um site secundário do VMM usando o PowerShell (Resource Manager) | Microsoft Azure"
    description="Descreve como implantar o Azure recuperação do Site para coordenar replicação, failover e recuperação de VMs Hyper-V em nuvens VMM para um site secundário do VMM usando o PowerShell (Gerenciador de recurso)"
    services="site-recovery"
    documentationCenter=""
    authors="sujaytalasila"
    manager="rochakm"
    editor="raynew"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="sutalasi"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager"></a>Duplicar máquinas virtuais Hyper-V nuvens do VMM para um site secundário do VMM usando o PowerShell (Gerenciador de recurso)

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-vmm.md)
- [Portal clássico](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Gerenciador de recursos](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Bem-vindo ao Site Azure recuperação! Use este artigo se você quiser replicar máquinas de virtuais Hyper-V local gerenciado no nuvens do System Center Virtual Machine Manager (VMM) para um site secundário. 

Este artigo mostra como usar o PowerShell para automatizar tarefas comuns, que você precisará executar ao configurar recuperação de Site do Azure para replicar máquinas virtuais Hyper-V nuvens do System Center VMM para nuvens System Center VMM no site secundário.

O artigo inclui os pré-requisitos para o cenário e mostra 

- Como configurar um cofre de serviços de recuperação
- Instalar o provedor de recuperação de Site do Azure em servidor VMM de origem e o servidor do VMM de destino
- Registrar o servidor (es) VMM no cofre
- Configure política de replicação para a nuvem VMM. As configurações de replicação na política serão aplicadas a todas as máquinas virtuais protegidas 
- Ative a proteção para máquinas virtuais. 
- Teste failover de VMs individualmente ou como parte de um plano de recuperação para garantir que tudo está funcionando conforme esperado.
- Execute um planejada ou um failover não planejado de VMs individualmente ou como parte de um plano de recuperação para garantir que tudo está funcionando conforme esperado.

Se você enfrentar problemas para configurar esse cenário, poste suas perguntas no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] Azure tem dois diferentes [modelos de implantação](../resource-manager-deployment-model.md) para criar e trabalhar com recursos: Gerenciador de recursos do Azure e clássico. Azure também tem dois portais – o portal de clássico Azure que suporta o modelo clássico de implantação e o portal do Azure com suporte para ambos os modelos de implantação. Este artigo discute o modelo de implantação do Gerenciador de recursos.



## <a name="on-premises-prerequisites"></a>Pré-requisitos de local

Veja aqui o que você precisará nos sites primário e secundário local implantar esse cenário:

**Pré-requisitos** | **Detalhes** 
--- | ---
**VMM** | Recomendamos que você implantar um servidor VMM no local principal e um servidor VMM no site secundário.<br/><br/> Você também pode [replicar entre nuvens em um único servidor VMM](site-recovery-single-vmm.md). Para fazer isso, você precisará pelo menos duas nuvens configurados no servidor VMM.<br/><br/> Servidores VMM devem estar executando pelo menos System Center 2012 SP1 com as últimas atualizações.<br/><br/> Cada servidor VMM deve ter em um ou mais nuvens configurados e todas as nuvens devem ter o perfil de capacidade de Hyper-V definida. <br/><br/>Nuvens devem conter um ou mais grupos de host VMM.<br/><br/>Saiba mais sobre como configurar as nuvens VMM [Configurando a estrutura de nuvem VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric), e [passo a passo: Criando nuvens privadas com System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> Servidores VMM devem ter acesso à internet. 
**Hyper-V** | Servidores Hyper-V devem estar executando pelo menos o Windows Server 2012 com a função Hyper-V e instalaram as últimas atualizações.<br/><br/> Um servidor Hyper-V deve conter um ou mais VMs.<br/><br/>  Servidores de host Hyper-V devem estar localizados em grupos de host nas nuvens VMM primárias e secundários.<br/><br/> Se você estiver executando o Hyper-V em um cluster no Windows Server 2012 R2 você deve instalar a [atualização 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Se você estiver executando Hyper-V em um cluster no Windows Server 2012 nota desse agente de cluster não é criado automaticamente se você tiver um cluster de baseado no endereço IP estático. Você precisará configurar o corretor de cluster manualmente. [Leia mais](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Provedor** | Durante a implantação de recuperação do Site que você instale o provedor de recuperação de Site do Azure em servidores VMM. O provedor se comunica com recuperação de Site pela HTTPS 443 para coordenar replicação. Replicação de dados ocorre entre os servidores de Hyper-V primárias e secundários através da LAN ou uma conexão de VPN.<br/><br/> O provedor executando no servidor VMM precisa acessar essas URLs: *. hypervrecoverymanager.windowsazure.com; *. AccessControl.Windows.NET; *. backup.windowsazure.com; *. blob.Core.Windows.NET; *. store.core.windows.net.<br/><br/> Além disso, permitir a comunicação de firewall dos servidores VMM para os [intervalos de IP do data center Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e permitir que o protocolo HTTPS (443).

### <a name="network-mapping-prerequisites"></a>Pré-requisitos de mapeamento de rede
Mapas de mapeamento de rede entre redes VMM VM nos servidores VMM primárias e secundários para:

- Coloque ideal réplica VMs em hosts Hyper-V secundários após failover.
- Conecte réplica VMs às redes de máquina virtual apropriados.
- Se você não configurar réplica de mapeamento de rede que VMs não estiver conectadas a qualquer rede após failover.
- Se você quiser configurar rede mapeamento durante a recuperação de Site implantação aqui é o que é preciso:

    - Certifique-se de que VMs no servidor de host de origem Hyper-V estão conectadas a uma rede VMM VM. Rede deve ser vinculada a uma rede lógica que está associada com a nuvem.
    - Verifique se a nuvem secundária que você usará para recuperação possui uma rede de máquina virtual correspondente configurado. Essa rede de máquina virtual deve ser vinculado a uma rede lógica associado com a nuvem secundária.


Saiba mais sobre como configurar redes VMM nos artigos abaixo

- [Como configurar redes lógicas no VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Como configurar redes de máquina virtual e gateways no VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)

[Saiba mais](site-recovery-network-mapping.md) sobre como funciona o mapeamento de rede.

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

1. Criar um grupo de recursos do Gerenciador de recursos do Azure se você não tenha um

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Criar um novo cofre de serviços de recuperação e salve o objeto de cofre ASR criado em uma variável (será usado mais tarde). Você também pode recuperar a criação de postagem do ASR cofre objeto usando o cmdlet Get-AzureRMRecoveryServicesVault:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location 

## <a name="step-3-set-the-recovery-services-vault-context"></a>Etapa 3: Configurar o contexto de Cofre de serviços de recuperação

1.  Se você tiver um cofre já criou, execute o abaixo de comando para obter o cofre.

        $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname

2.  Definir o contexto de cofre executando o abaixo comando.

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


## <a name="step-5-create-and-associate-a-replication-policy"></a>Etapa 5: Criar e associar uma política de replicação

1.  Crie uma política de replicação Hyper-V 2012 R2 executando o seguinte comando:

    
        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod 

    > [AZURE.NOTE] Nuvem VMM pode conter hosts Hyper-V executando versões diferentes do Windows Server (conforme mencionado os pré-requisitos Hyper-V), mas a política de replicação é a versão do sistema operacional específico. Se você tiver hosts diferentes em execução em versões diferentes do sistema operacional, então crie políticas de replicação separada para cada tipo de versão do sistema operacional. Para ex: se você tiver cinco hosts em execução no Windows servidores 2012 e três no Windows Server 2012 R2, criar dois políticas de replicação – um para cada tipo de versões de sistema operacional.

2.  Para obter a principal proteção contêineres (primária VMM nuvem) e recuperação proteção (recuperação VMM nuvem) executando os seguintes comandos:
    
        $PrimaryCloud = "testprimarycloud"
        $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

        $RecoveryCloud = "testrecoverycloud"
        $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
  
3.  Recuperar a política que você criou na etapa 1 usando o nome amigável da política

        $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.  Inicie a associação de contêiner de proteção (VMM nuvem) com a política de replicação:

        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer

5.  Aguarde o conclusão do trabalho de associação de política. Você pode verificar se o trabalho concluído usando o seguinte trecho do PowerShell.
   
        $job = Get-AzureRmSiteRecoveryJob -Job $associationJob
        if($job -eq $null -or $job.StateDescription -ne "Completed")
         {
            $isJobLeftForProcessing = $true;
        }

    Após o trabalho de processamento, execute o seguinte comando:

        if($isJobLeftForProcessing)
        {
        Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)


Para verificar a conclusão da operação, siga as etapas em [Monitorar a atividade](#monitor).

## <a name="step-5-configure-network-mapping"></a>Etapa 5: Configurar mapeamento de rede

1. O primeiro comando obtém os servidores para o cofre Azure recuperação do Site atual. O comando armazena os servidores de recuperação de Site do Microsoft Azure na variável matriz $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer

2. O abaixo comandos obter a rede de recuperação do site para o servidor do VMM de origem e o servidor do VMM de destino.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    
    > [AZURE.NOTE] O servidor do VMM fonte pode ser o primeiro ou o segundo na matriz servers. Marque os nomes dos servidores VMM e obter as redes adequadamente


4. O cmdlet final cria um mapeamento entre a rede principal e a rede de recuperação. O cmdlet especifica à rede principal como o primeiro elemento do $PrimaryNetworks e da rede de recuperação como o primeiro elemento de $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## <a name="step-6-configure-storage-mapping"></a>Etapa 6: Configurar mapeamento de armazenamento

1. O abaixo comando obtém a lista de classificações de armazenamento em $storageclassifications variável.

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification


2. O abaixo comandos obter a classificação de origem em classificação de variável e destino de $SourceClassificaion em $TargetClassification variável. 

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    
    > [AZURE.NOTE] As classificações de origem e destino podem ser qualquer elemento na matriz. Consulte a saída do abaixo comando para calcular o índice de classificações de origem e de destino na matriz $storageclassifications. 
    
    > Get-AzureRmSiteRecoveryStorageClassification | Select-Object - nome amigável de propriedade, Id | Formato de tabela


3. O abaixo cmdlet cria um mapeamento entre a classificação de origem e a classificação de destino. 

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

## <a name="step-7-enable-protection-for-virtual-machines"></a>Etapa 7: Habilitar proteção para máquinas virtuais

Depois de servidores, nuvens e redes estão configurados corretamente, você pode habilitar a proteção para máquinas virtuais na nuvem. 


  1. Para ativar a proteção, execute o seguinte comando para obter o contêiner de proteção:
    
            $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
    
  2. Obter a entidade de proteção (máquina virtual) executando o seguinte comando:
    
            $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
        
  3. Habilite replicação para a máquina virtual executando o seguinte comando:

            $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy


## <a name="test-your-deployment"></a>Testar sua implantação

Para testar sua implantação, você pode executar um failover de teste para uma única máquina virtual, ou criar um plano de recuperação consiste em várias máquinas virtuais e executar um failover de teste para o plano. Failover de teste simula o mecanismo de failover e recuperação em uma rede isolada. 

> [AZURE.NOTE] Você pode criar um plano de recuperação para seu aplicativo no portal do Azure.

Para verificar a conclusão da operação, siga as etapas em [Monitorar a atividade](#monitor).


### <a name="run-a-test-failover"></a>Executar um failover de teste

1.  Executar o abaixo cmdlets para acessar a rede de máquina virtual ao qual você deseja testar failover suas VMs para.

        $Servers = Get-AzureRmSiteRecoveryServer
        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

2.  Execute um failover de teste de uma máquina virtual, fazendo o seguinte:
    
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1] 

2.  Realizar um failover de teste de um plano de recuperação, fazendo o seguinte:
    
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1] 

### <a name="run-a-planned-failover"></a>Executar um failover planejado

1. Execute um failover planejado de uma máquina virtual, fazendo o seguinte:
    
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2. Realizar um failover planejado de um plano de recuperação, fazendo o seguinte:
    
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### <a name="run-an-unplanned-failover"></a>Executar um failover não planejado

1. Execute um failover não planejado de uma máquina virtual, fazendo o seguinte:
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 

2. Execute um failover não planejado de um plano de recuperação, fazendo o seguinte:
        
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 
    
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

