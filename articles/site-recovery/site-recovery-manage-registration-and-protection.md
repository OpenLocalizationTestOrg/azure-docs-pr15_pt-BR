<properties
    pageTitle="Remover servidores e desativar a proteção | Microsoft Azure" 
    description="Este artigo descreve como cancelar o registro servidores do cofre recuperação do Site e desativar a proteção para máquinas virtuais e servidores físicos." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016" 
    ms.author="raynew"/>

# <a name="remove-servers-and-disable-protection"></a>Remover servidores e desativar a proteção

O serviço de recuperação de Site do Azure contribui para sua estratégia de recuperação (BCDR) de desastre e continuidade de negócios por coordenação replicação, failover e recuperação de máquinas virtuais e servidores físicos. Máquinas podem ser replicadas para Azure ou uma central de dados local secundário. Para obter uma visão geral rápida leia [o que é recuperação de Site do Azure?](site-recovery-overview.md)

## <a name="overview"></a>Visão geral

Este artigo descreve como cancelar o registro de servidores do cofre a recuperação do Site e como desabilitar a proteção para máquinas virtuais protegidos por recuperação do Site. 

Poste quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="unregister-a-vmm-server"></a>Cancelar o registro de um servidor VMM

Cancelar o registro de um servidor VMM de um cofre excluindo o servidor na guia **servidores** no portal do Azure recuperação do Site. Observe que:

-  **Servidor VMM conectado**: Recomendamos cancelar o registro do servidor VMM quando ele está conectado ao Azure. Isso garante que as configurações no servidor VMM no local e os servidores VMM associados a ela (VMM os servidores que contêm nuvens que são mapeados para nuvens no servidor que você deseja excluir) são eliminadas corretamente. Recomendamos que você só remover um servidor desconectado se há um problema permanente com conectividade.
- **Servidor VMM desconectados**: se o servidor do VMM não está conectado quando você exclui-lo você precisará executar um script manualmente para executar a limpeza. O script está disponível na [Galeria de Microsoft](http://aka.ms/asr-cleanup-script-vmm). Observe a identificação de VMM do servidor para concluir o processo de limpeza manual.
- **Servidor VMM cluster**: se você precisar cancelar o registro de um servidor VMM que é implantado em um cluster, faça o seguinte:

    - Se o servidor está conectado, exclua o servidor VMM conectado na guia **servidores** . Para desinstalar o provedor no servidor, faça login em cada nó de cluster e desinstalá-lo no painel de controle. Execute o script de limpeza referenciado na seção anterior em todos os nós passivos no cluster para excluir entradas de registro.
    - Se o servidor não estiver conectado, você precisará executar o script de limpeza em todos os nós de cluster.

### <a name="unregister-an-unconnected-vmm-server"></a>Cancelar o registro de um servidor VMM desconectado

Você deseja remover do servidor VMM:

1. Cancelar o registro do servidor VMM do portal do Azure.
2. No servidor VMM, baixe o script de limpeza.
3. Abra o PowerShell com executar como opção de administrador para alterar a política de execução para o escopo de padrão (LocalMachine).
4. Siga as instruções no script. 

Nos servidores VMM que possuem nuvens que estão emparelhados com nuvens no servidor que você está removendo:

1. Execute o script de limpeza e siga as etapas 2 a 4.
2. Especifica a identificação de VMM para o servidor VMM que foi cancelado. 
3. Esse script removerá as informações de registro para servidor VMM e a nuvem as informações de junção.


## <a name="unregister-a-hyper-v-server-in-a-hyper-v-site"></a>Cancelar o registro de um servidor Hyper-V em um site de Hyper-V

Quando a recuperação de Site Azure é implantada para proteger máquinas virtuais localizadas em um servidor Hyper-V em um site de Hyper-V (com nenhum servidor VMM) você pode cancelar o registro de um servidor Hyper-V de um cofre da seguinte maneira:

1. Desative a proteção para máquinas virtuais localizado no servidor Hyper-V.
2. Na guia **servidores** no portal do Azure recuperação do Site, selecione o servidor > Excluir. O servidor não precisa estar conectado ao Azure quando fizer isso.
3. Execute o seguinte script para limpar as configurações do servidor e cancelar o registro do cofre. 

        pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }
    
            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host
        
            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }
        
            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }
        
            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
        
            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."  
                    Remove-Item -Recurse -Path $registrationPath
                }
    
                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }
    
                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }
    
            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {   
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0] 
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd


## <a name="stop-protecting-a-hyper-v-virtual-machine"></a>Parar a proteção de uma máquina virtual Hyper-V

Se você quiser parar a proteção de uma máquina virtual Hyper-V você precisará remover a proteção por ele. Dependendo da maneira como você remover proteção talvez seja necessário limpar as configurações de proteção manualmente na máquina. 

### <a name="remove-protection"></a>Remover proteção

1. Na guia **máquinas virtuais** das propriedades de nuvem, selecione a máquina virtual > **Remover**.
2. Na página **Confirmar a remoção da máquina Virtual** , você tem algumas opções:

    - **Desativar a proteção**— se você habilitar e salva esta opção na máquina virtual já não será protegida por recuperação do Site. As configurações de proteção para a máquina virtual serão limpo automaticamente.
    - **Remover do cofre**— se você selecionar essa opção na máquina virtual será removida apenas do Cofre de recuperação do Site. Configurações de proteção do local para a máquina virtual não serão afetadas. Você precisará limpar configurações manualmente para remover as configurações de proteção e remover a máquina virtual da assinatura Azure e remover as configurações de proteção que você precisará limpá-los manualmente usando as instruções abaixo.

Se você selecionar Excluir a máquina virtual e seus discos rígido que eles serão removidos do local de destino.

### <a name="clean-up-protection-settings-manually-between-vmm-sites"></a>Limpar as configurações de proteção manualmente (entre sites VMM)

Se você selecionou **Parar de gerenciar a máquina virtual**, limpe manualmente as configurações:

1. No servidor primário executar este script do console do VMM para limpar as configurações de máquina virtual primária. No console do VMM clique no botão do PowerShell para abrir o console do PowerShell do VMM. Substitua o nome da sua máquina virtual SQLVM1.

         $vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. No servidor VMM secundário executar este script para limpar as configurações de máquina virtual secundária:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force

3. No servidor VMM secundário, depois de executar o script, atualize as máquinas virtuais no servidor host Hyper-V para que a máquina virtual secundária obtém detectada novamente no console do VMM.
4. As etapas acima limpará o servidor do VMM somente replicação configurações. Se você quiser remover a replicação de máquina virtual para a máquina virtual. Você precisará fazer as etapas em ambos os primário e secundário abaixo máquinas virtuais. Executar o abaixo de script para remover a replicação e substituir SQLVM1 com o nome de sua máquina virtual.

        Remove-VMReplication –VMName “SQLVM1”


### <a name="clean-up-protection-settings-manually-between-on-premises-vmm-sites-and-azure"></a>Limpar as configurações de proteção manualmente (entre locais VMM e sites do Azure)

1. No servidor VMM de origem executar este script para limpar as configurações de máquina virtual primária:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. As etapas acima limpará o servidor do VMM somente replicação configurações. Depois que você removeu a replicação de servidor VMM Certifique-se para remover a replicação da máquina virtual em execução no servidor host Hyper-V com este script. Substitua o nome da sua máquina virtual e host01.contoso.com com o nome do servidor host Hyper-V SQLVM1.

        $vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

### <a name="clean-up-protection-settings-manually-between-hyper-v-sites-and-azure"></a>Limpar as configurações de proteção manualmente (entre sites Hyper-V e Azure)

1. No servidor de host de origem Hyper-V, para remover replicação da máquina virtual use este script. Substitua o nome da sua máquina virtual SQLVM1.

        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

## <a name="stop-protecting-a-vmware-virtual-machine-or-a-physical-server"></a>Parar a proteção de uma máquina virtual VMware ou um servidor físico

Se você quiser parar a proteção de uma máquina virtual VMware ou um servidor físico você precisará remover a proteção por ele. Dependendo da maneira como você remover proteção talvez seja necessário limpar as configurações de proteção manualmente na máquina. 

### <a name="remove-protection"></a>Remover proteção

1. Na guia **máquinas virtuais** das propriedades de nuvem, selecione a máquina virtual > **Remover**.
2. Na **Remover Máquina Virtual** , selecione uma das opções:

    - **Desativar a proteção (use para redimensionamento de busca detalhada e o volume de recuperação)**— só vai ver e habilitar essa opção se você:
        - **O volume de máquina virtual resized**— ao redimensionar um volume na máquina virtual entra em um estado crítico. Se isso acontecer, selecione essa opção. Ele desabilita a proteção mantendo pontos de recuperação no Azure. Quando você reativar proteção da máquina os dados para o volume redimensionado serão transferidos para o Azure.
        - **Executar um failover**— após você testou seu ambiente executando um failover de máquinas virtuais VMware a locais ou servidores físicos no Azure, selecione essa opção para começar a proteger suas máquinas virtuais de local novamente. Essa opção desativa a cada máquina virtual e, em seguida, você precisará Reativar proteção para eles. Observe que:
            - Desativar a máquina virtual com essa configuração não afeta a máquina virtual de réplica no Azure.
            - Você não deve desinstalar o serviço de mobilidade na máquina virtual.
    
    - **Desativar a proteção**— se você habilitar e salva esta opção máquina já não será protegida por recuperação do Site. As configurações de proteção para a máquina serão limpo automaticamente.
    - **Remover do cofre**— se você selecionar essa opção máquina será removida apenas do Cofre de recuperação do Site. As configurações de proteção do local da máquina não serão afetadas. Para remover as configurações na máquina e remover máquina virtual do Azure assinatura e você precisará limpar as configurações Desinstalando o serviço de mobilidade.
    
        ![Remover opções](./media/site-recovery-manage-registration-and-protection/remove-vm.png)

















