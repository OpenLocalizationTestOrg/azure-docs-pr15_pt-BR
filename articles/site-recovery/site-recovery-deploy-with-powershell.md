<properties
    pageTitle="Replicar máquinas virtuais Hyper-V nuvens do VMM usando recuperação de Site do Azure e PowerShell | Microsoft Azure"
    description="Saiba como automatizar a replicação de máquinas virtuais Hyper-V nuvens do VMM usando o PowerShell e recuperação de Site."
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="bsiva"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell---classic"></a>Duplicar máquinas virtuais Hyper-V nuvens do VMM para Azure usando o Powershell - clássico

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-azure.md)
- [PowerShell - Gerenciador de recursos](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portal clássico](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - clássico](site-recovery-deploy-with-powershell.md)


## <a name="overview"></a>Visão geral

Recuperação de Site Azure contribui para sua estratégia de recuperação (BCDR) de desastre e continuidade de negócios por coordenação replicação, failover e recuperação de máquinas virtuais em um número de cenários de implantação. Para obter uma lista completa de cenários de implantação, consulte [Visão geral de recuperação de Site do Azure](site-recovery-overview.md).

Este artigo mostra como usar o PowerShell para automatizar tarefas comuns, que você precisará executar ao configurar recuperação de Site do Azure para replicar máquinas virtuais Hyper-V nuvens do System Center VMM para armazenamento do Azure.

O artigo inclui os pré-requisitos para o cenário e mostra como configurar um cofre de recuperação do Site, instalar o provedor de recuperação de Site do Azure no servidor VMM de origem, registrar o servidor no cofre, adicionar uma conta de armazenamento do Azure, instalar o agente de serviços de recuperação do Azure em servidores de host Hyper-V, definir configurações de proteção para nuvens VMM que serão aplicadas a todas as máquinas virtuais protegidas e em seguida, Habilitar proteção para essas máquinas virtuais. Concluir a configuração testando o failover para garantir que tudo está funcionando conforme esperado.

Se você enfrentar problemas para configurar esse cenário, poste suas perguntas no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


> [AZURE.NOTE] Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos e clássico](../resource-manager-deployment-model.md). Este artigo aborda usando o modelo de implantação do clássico.



## <a name="before-you-start"></a>Antes de começar

Verifique se que você tem estes pré-requisitos no lugar:

### <a name="azure-prerequisites"></a>Pré-requisitos Azure

- Você precisará de uma conta [Do Microsoft Azure](https://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Você precisará de uma conta de armazenamento do Azure para armazenar dados duplicados. A conta deve replicação geográfica habilitada. Ele deve ser na mesma região como o Cofre de recuperação de Site do Azure e ser associadas a mesma assinatura. [Saiba mais sobre o armazenamento do Azure](../storage/storage-introduction.md).
- Você precisará certificar-se de que máquinas virtuais que deseja proteger cumprir os [pré-requisitos do Azure máquina virtual](site-recovery-best-practices.md#virtual-machines).

### <a name="vmm-prerequisites"></a>Pré-requisitos de VMM
- Você precisará servidor VMM em execução no System Center 2012 R2.
- Você precisará pelo menos uma nuvem no servidor VMM que você deseja proteger. Nuvem deve conter:
    - Um ou mais VMM hospede grupos.
    - Um ou mais servidores de host Hyper-V ou clusters em cada grupo de host.
    - Máquinas virtuais uma ou mais no servidor de origem Hyper-V.

### <a name="hyper-v-prerequisites"></a>Pré-requisitos de Hyper-V

- Os servidores host Hyper-V devem estar executando pelo menos o **Windows Server 2012** com função Hyper-V ou **Microsoft Hyper-V Server 2012** e instalaram as últimas atualizações.
- Se você estiver executando Hyper-V em uma anotação de cluster que corretor de cluster não é criado automaticamente se você tiver um cluster de baseado no endereço IP estático. Você precisará configurar o corretor de cluster manualmente. Para fazer isso, no Gerenciador de servidores > Gerenciador de Cluster de Failover, conectar-se ao cluster, clique **Configurar função** e selecione **Agente de réplica do Hyper-V** na tela **Selecione função** do Assistente de alta disponibilidade.
- Qualquer servidor de host Hyper-V ou cluster para o qual você deseja gerenciar proteção deve ser incluído em uma nuvem VMM.

### <a name="network-mapping-prerequisites"></a>Pré-requisitos de mapeamento de rede
Quando você proteger máquinas virtuais em mapas de mapeamento de rede Azure entre redes de máquina virtual no servidor VMM de origem e destino redes Azure para habilitar o seguinte:

- Todas as máquinas que falham sobre na mesma rede podem se conectar uns aos outros, independentemente de qual plano de recuperação estiverem em.
- Se um gateway de rede está configurado no destino rede Azure, máquinas virtuais pode se conectar com outras máquinas virtuais de local.
- Se você não configurar mapeamento somente máquinas virtuais de rede que falham sobre no mesmo plano de recuperação será capaz de se conectar uns aos outros após failover no Azure.

Se você deseja implantar o mapeamento de rede será necessário o seguinte:

- As máquinas virtuais que você deseja proteger no servidor VMM de origem deve estar conectadas a uma rede de máquina virtual. Rede deve ser vinculada a uma rede lógica que está associada com a nuvem.
- Uma rede Azure ao qual replicadas máquinas virtuais podem se conectar após failover. Você selecionará esta rede no momento de falha. A rede deve estar na mesma região como sua assinatura de recuperação de Site do Azure.
- [Saiba mais](site-recovery-network-mapping.md) sobre o mapeamento de rede:

###<a name="powershell-prerequisites"></a>Pré-requisitos do PowerShell
Verifique se que você tem o Azure PowerShell pronta. Se você já estiver usando o PowerShell, você precisará atualizar para a versão 0.8.10 ou posterior. Para obter informações sobre a configuração do PowerShell, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Depois de configurar e configurado PowerShell, você pode exibir todos os cmdlets disponíveis para o serviço [aqui](https://msdn.microsoft.com/library/dn850420.aspx).

Para saber mais sobre dicas que podem ajudar que você usa os cmdlets, como como saídas, entradas e valores de parâmetro normalmente são tratadas no Azure PowerShell, consulte [Introdução ao Azure Cmdlets](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## <a name="step-1-set-the-subscription"></a>Etapa 1: Definir a assinatura

No PowerShell, execute estas cmdlets:

```
$UserName = "<user@live.com>"
$Password = "<password>"
$AzureSubscriptionName = "prod_sub1"

$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
Add-AzureAccount -Credential $Cred;
$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

```

Substitua os elementos dentro do "< >" suas informações específicas.

## <a name="step-2-create-a-site-recovery-vault"></a>Etapa 2: Criar um cofre de recuperação do Site

No PowerShell, substitua os elementos dentro do "< >" por suas informações específicas e executar esses comandos:

```

$VaultName = "<testvault123>"
$VaultGeo  = "<Southeast Asia>"
$OutputPathForSettingsFile = "<c:\>"

```


```
New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## <a name="step-3-generate-a-vault-registration-key"></a>Etapa 3: Gerar uma chave de registro do cofre

Gere uma chave de registro no cofre. Depois de baixar o provedor de recuperação de Site do Azure e instalá-lo no servidor VMM, você usará esta tecla para registrar o servidor VMM no cofre.

1.  Obter o arquivo de configuração do cofre e definir o contexto:

    ```

    $VaultName = "<testvault123>"
    $VaultGeo  = "<Southeast Asia>"
    $OutputPathForSettingsFile = "<c:\>"

    $VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;

    ```

2.  Defina o contexto de cofre executando os seguintes comandos:

    ```

    $VaultSettingFilePath = $vaultSetingsFile.FilePath
    $VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop

    ```

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Etapa 4: Instalar o provedor de recuperação de Site Azure

1.  Na máquina VMM, crie um diretório executando o comando a seguir:

    ```

    pushd C:\ASR\

    ```

2.  Extrair os arquivos usando o provedor baixado executando o seguinte comando

    ```

    AzureSiteRecoveryProvider.exe /x:. /q

    ```

3.  Instale o provedor usando os seguintes comandos:

    ```

    .\SetupDr.exe /i

    ```

    ```

    $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
    do
    {
        $isNotInstalled = $true;
        if(Test-Path $installationRegPath)
        {
            $isNotInstalled = $false;
        }
    }While($isNotInstalled)

    ```

    Aguarde concluir a instalação.

4.  Registre o servidor no cofre usando o seguinte comando:

    ```

    $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
    pushd $BinPath
    $encryptionFilePath = "C:\temp\"
    .\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

    ```

## <a name="step-5-create-an-azure-storage-account"></a>Etapa 5: Criar uma conta de armazenamento do Azure

Se você não tiver uma conta de armazenamento do Azure, crie uma conta de replicação geográfica habilitada executando o seguinte comando:

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

Observe que a conta de armazenamento deve estar na mesma região como o serviço de recuperação de Site do Azure e ser associadas a mesma assinatura.


## <a name="step-6-install-the-azure-recovery-services-agent"></a>Etapa 6: Instalar o agente de serviços de recuperação Azure

Azure no portal do, instale o agente de serviços de recuperação do Azure em cada servidor de host Hyper-V localizado nas nuvens VMM que você deseja proteger.

Execute o seguinte comando em todos os hosts VMM:

```

marsagentinstaller.exe /q /nu

```


## <a name="step-7-configure-cloud-protection-settings"></a>Etapa 7: Configurar nuvem as configurações de proteção

1.  Crie um perfil de proteção de nuvem para Azure executando o seguinte comando:

    ```

    $ReplicationFrequencyInSeconds = "300";
    $ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName -RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds  $ReplicationFrequencyInSeconds;

    ```

2.  Obtenha um contêiner de proteção executando os seguintes comandos:

    ```

    $PrimaryCloud = "testcloud"
    $protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;

    ```

3.  Inicie a associação de contêiner de proteção com a nuvem:

    ```

    $associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;        

    ```

4.  Após concluir o trabalho, execute o seguinte comando:


        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
        $isJobLeftForProcessing = $true;
        }


5.  Após o trabalho de processamento, execute o seguinte comando:


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



Para verificar a conclusão da operação, siga as etapas em [Monitorar a atividade](#monitor).

## <a name="step-8-configure-network-mapping"></a>Etapa 8: Configurar mapeamento de rede
Antes de começar o mapeamento de rede verificar se máquinas virtuais no servidor VMM de origem estão conectadas a uma rede de máquina virtual. Além disso, crie uma ou mais redes virtuais Azure. Observe que várias redes de máquina virtual podem ser mapeadas para uma única rede Azure.

Observe que, se a rede de destino tem várias sub-redes e uma dessas sub-redes tem o mesmo nome de sub-rede na qual a máquina virtual de origem está localizada, em seguida, na máquina virtual de réplica será conectada ao sub-rede destino após failover. Se não houver uma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.

O primeiro comando obtém os servidores para o cofre Azure recuperação do Site atual. O comando armazena os servidores de recuperação de Site do Microsoft Azure na variável matriz $Servers.

    $Servers = Get-AzureSiteRecoveryServer


O segundo comando obtém a rede de recuperação do site para o primeiro servidor na matriz $Servers. O comando armazena as redes na variável $Networks.


    $Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

O terceiro comando obtém suas assinaturas Azure usando o cmdlet Get-AzureSubscription e, em seguida, armazena esse valor na variável $Subscriptions.

    $Subscriptions = Get-AzureSubscription



O quarto comando obtém redes virtuais Azure usando o cmdlet Get-AzureVNetSite e, em seguida, esse valor na variável $AzureVmNetworks.


    $AzureVmNetworks = Get-AzureVNetSite



O cmdlet final cria um mapeamento entre a principal e rede o Azure máquina virtual. O cmdlet especifica à rede principal como o primeiro elemento da $Networks. O cmdlet especifica uma rede de máquina virtual como o primeiro elemento de $AzureVmNetworks usando sua ID. O comando inclui sua ID do Azure assinatura.


    New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id


## <a name="step-9-enable-protection-for-virtual-machines"></a>Etapa 9: Habilitar proteção para máquinas virtuais

Depois de servidores, nuvens e redes estão configurados corretamente, você pode habilitar a proteção para máquinas virtuais na nuvem. Observe o seguinte:

Máquinas virtuais deve atender aos [pré-requisitos do Azure máquina virtual](site-recovery-best-practices.md#virtual-machines).

Para ativar a proteção do sistema operacional e o sistema operacional propriedades de disco devem ser definidas para a máquina virtual. Quando você cria uma máquina virtual no VMM usando um modelo de máquina virtual, você pode definir a propriedade. Você também pode definir essas propriedades para máquinas virtuais existentes nas guias **Geral** e **Configuração de Hardware** das propriedades de máquina virtual. Se você não definir essas propriedades no VMM, você poderá configurá-los no portal do Azure recuperação do Site.



1.  Para ativar a proteção, execute o seguinte comando para obter o contêiner de proteção:

        $ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName



2. Obter a entidade de proteção (máquina virtual) executando o seguinte comando:


        $protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer



3. Habilite o DR para a máquina virtual executando o seguinte comando:



        $jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity  -Protection Enable -Force



## <a name="test-your-deployment"></a>Testar sua implantação

Para testar sua implantação, você pode executar um failover de teste para uma única máquina virtual, ou criar um plano de recuperação consiste em várias máquinas virtuais e executar um failover de teste para o plano. Failover de teste simula o mecanismo de failover e recuperação em uma rede isolada. Observe que:

- Se você deseja se conectar à máquina virtual no Azure usando a área de trabalho remota após o failover, habilite Conexão de área de trabalho remota na máquina virtual antes de executar o failover de teste.
- Após o failover, você vai usar um endereço IP público para conectar-se à máquina virtual no Azure usando a área de trabalho remota. Se você quiser fazer isso, certifique-se de que você não tem quaisquer diretivas de domínio que impedem a conexão para uma máquina virtual usando um endereço público.

Para verificar a conclusão da operação, siga as etapas em [Monitorar a atividade](#monitor).

### <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. Crie um arquivo. XML como um modelo para seu plano de recuperação usando os dados abaixo e, em seguida, salve-o como "C:\RPTemplatePath.xml".
2. Altere o nó RecoveryPlan Id, nome, PrimaryServerId e SecondaryServerId.
3. Altere o nó ProtectionEntity PrimaryProtectionEntityId (vmid do VMM).
4. Você pode adicionar mais VMs adicionando mais nós ProtectionEntity.



        <#
        <?xml version="1.0" encoding="utf-16"?>
        <RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test"  PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5"  SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description=""     Version="V2014_07">
          <Actions />
          <ActionGroups>
            <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </ShutdownAllActionGroup>
            <FailoverAllActionGroup Id="FailoverAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </FailoverAllActionGroup>
            <BootActionGroup Id="DefaultActionGroup">
              <PreActionSequence />
              <PostActionSequence />
              <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03- cf163cc36ef8" />
            </BootActionGroup>
          </ActionGroups>
          <ActionGroupSequence>
            <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup"  Before="FailoverAllActionGroup" />
            <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup"  After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
            <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
          </ActionGroupSequence>
        </RecoveryPlan>
        #>



4. Preencha os dados no modelo:


        $TemplatePath = "C:\RPTemplatePath.xml";



5. Crie o RecoveryPlan:

        $RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;



### <a name="run-a-test-failover"></a>Executar um failover de teste

1.  Obter o objeto RecoveryPlan executando o seguinte comando:

        $RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;


2.  Inicie o failover de teste executando o seguinte comando:


        $jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;


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

[Leia mais](https://msdn.microsoft.com/library/dn850420.aspx) sobre cmdlets do PowerShell de recuperação de Site do Azure. </a>.
