<properties
    pageTitle="Redefinir a senha ou a configuração de área de trabalho remota em uma máquina virtual Windows | Microsoft Azure"
    description="Aprenda a redefinir uma senha de conta ou de serviços de área de trabalho remota em uma máquina de virtual do Windows usando o portal do Azure ou Azure PowerShell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Como redefinir sua senha de logon em uma máquina virtual Windows ou o serviço de área de trabalho remota

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Se você não consegue se conectar a uma máquina virtual do Windows (máquina virtual), você pode redefinir a senha de administrador local ou redefinir a configuração do serviço de área de trabalho remota. Você pode usar o portal do Azure ou a extensão de acesso de máquina virtual do PowerShell do Azure para redefinir a senha. Se você estiver usando o PowerShell, verifique se você tiver o módulo do PowerShell mais recente instalado no seu computador de trabalho e estiver conectado à sua assinatura do Azure. Para obter etapas detalhadas, leia [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

> [AZURE.TIP] Você pode verificar a versão do PowerShell que você instalou usando`Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`

## <a name="windows-vms-in-resource-manager-deployment-model"></a>Windows VMs no modelo de implantação do Gerenciador de recursos

### <a name="azure-portal"></a>Portal do Azure
Selecione sua máquina virtual clicando em **Procurar** > **máquinas virtuais** > *máquina virtual Windows* > **todas as configurações** > **Redefinir a senha**. A lâmina de redefinição de senha é exibida:

![Página de redefinição de senha](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

Insira o nome de usuário e uma nova senha, clique em **Salvar**. Tente se conectar à sua máquina virtual novamente.

### <a name="vmaccess-extension-and-powershell"></a>Extensão de VMAccess e PowerShell

Verifique se você tiver Azure PowerShell 1.0 ou posterior instalado e você tiver conectado a sua conta usando o `Login-AzureRmAccount` cmdlet.

#### <a name="reset-the-local-administrator-account-password"></a>**Redefinir a senha da conta administrador local**

Você pode redefinir o nome de usuário ou senha do administrador usando o comando do PowerShell [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) .

Crie credenciais de conta de administrador local usando o seguinte comando:

    $cred=Get-Credential

Se você digitar um nome diferente que a conta atual, o seguinte comando de extensão VMAccess renomeia a conta de administrador local, atribui a senha para essa conta e emite um evento de logoff de área de trabalho remota. Se a conta administrador local estiver desabilitada, a extensão de VMAccess permite que ele.

Use a extensão de acesso de máquina virtual para configurar as novas credenciais da seguinte maneira:

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" `
        -Location WestUS -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"


Substituir `myRG`, `myVM`, `myVMAccess`e local com valores relevantes para a configuração.


#### <a name="reset-the-remote-desktop-service-configuration"></a>**Redefinir a configuração do serviço de área de trabalho remota**

Você pode redefinir o acesso remoto para sua máquina virtual usando [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) ou [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx), da seguinte maneira. (Substituir o `myRG`, `myVM`, `myVMAccess` e local com seus próprios valores.)

    Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
        -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"

Ou:<br>

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0


> [AZURE.TIP] Ambos os comandos adicionam um novo agente de acesso de máquina virtual nomeado na máquina virtual. A qualquer momento, uma máquina virtual pode ter apenas um único agente de acesso de máquina virtual. Para definir o acesso de máquina virtual agente propriedades com êxito, remover o agente de acesso definido anteriormente usando `Remove-AzureRmVMAccessExtension` ou `Remove-AzureRmVMExtension`. A partir do Azure PowerShell versão 1.2.2, você pode evitar essa etapa ao usar `Set-AzureRmVMExtension` com um `-ForceRerun` opção. Ao usar `-ForceRerun`, certifique-se de usar o mesmo nome para o agente de acesso de máquina virtual conforme definido pelo comando anterior.

Se você ainda não consegue se conectar remotamente a sua máquina virtual, consulte mais etapas para tentar em [conexões de solucionar problemas de área de trabalho remota para uma máquina virtual baseado no Windows Azure](virtual-machines-windows-troubleshoot-rdp-connection.md).


## <a name="windows-vms-in-the-classic-deployment-model"></a>Windows VMs no modelo clássico de implantação

### <a name="azure-portal"></a>Portal do Azure

Para máquinas virtuais criadas usando o modelo clássico de implantação, você pode usar o [portal do Azure](https://portal.azure.com) para redefinir o serviço de área de trabalho remota. Click: **Procurar** > **máquinas virtuais (clássico)** > *máquina virtual Windows* > **Redefinir remoto …**. A seguinte página é exibida.

![Redefinir página de configuração de RDP](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

Você também pode tentar redefinir o nome e senha da conta administrador local. Click: **Procurar** > **máquinas virtuais (clássico)** > *máquina virtual Windows* > **todas as configurações** > **Redefinir a senha**. A seguinte página é exibida.

![Página de redefinição de senha](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Depois de inserir o novo nome de usuário e senha, clique em **Salvar**.

### <a name="vmaccess-extension-and-powershell"></a>Extensão de VMAccess e PowerShell

Verifique se que o agente de máquina virtual está instalado na máquina virtual. A extensão de VMAccess não precisa estar instalado antes que você possa usá-lo, desde que o agente de máquina virtual está disponível. Verifique se que o agente de máquina virtual já está instalado usando o comando a seguir. (Substitua "myCloudService" e "myVM" pelos nomes de seu serviço de nuvem e sua máquina virtual, respectivamente. Você pode aprender esses nomes executando `Get-AzureVM` sem parâmetros.)

    $vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
    write-host $vm.VM.ProvisionGuestAgent

Se o comando **write-host** exibe **True**, o agente de máquina virtual está instalado. Se ele exibe **False**, consulte as instruções e um link para o download na postagem do blog Azure [agente de máquina virtual e extensões - parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) .

Se você criou a máquina virtual usando o portal, verifique se `$vm.GetInstance().ProvisionGuestAgent` retorna **True**. Caso contrário, você poderá defini-la usando este comando:

    $vm.GetInstance().ProvisionGuestAgent = $true

Este comando impede que o seguinte erro quando você estiver executando o comando **Set-AzureVMExtension** nas próximas etapas: "Provisionar convidado agente deve estar habilitado no objeto máquina virtual antes de configurar a extensão de acesso de VM IaaS."

#### <a name="reset-the-local-administrator-account-password"></a>**Redefinir a senha da conta administrador local**

Criar uma credencial entrar com o nome da conta administrador local atual e uma nova senha e, em seguida, execute o `Set-AzureVMAccessExtension` da seguinte maneira.

    $cred=Get-Credential
    Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

Se você digitar um nome diferente que a conta atual, a extensão de VMAccess renomeia a conta de administrador local, atribui a senha para essa conta e emite uma área de trabalho remota saída. Se a conta administrador local estiver desabilitada, a extensão de VMAccess permite que ele.

Esses comandos também redefinir a configuração do serviço de área de trabalho remota.

#### <a name="reset-the-remote-desktop-service-configuration"></a>**Redefinir a configuração do serviço de área de trabalho remota**

Para redefinir a configuração do serviço de área de trabalho remota, execute o seguinte comando:

    Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

A extensão de VMAccess executa dois comandos na máquina virtual:

- `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

Este comando permite que o grupo interno do Firewall do Windows que permite que o tráfego de área de trabalho remota entrada, que usa a porta TCP 3389.

- `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

Este comando define o fDenyTSConnections valor do registro como 0, habilitando conexões de área de trabalho remota.


## <a name="next-steps"></a>Próximas etapas

Se a extensão de acesso de máquina virtual do Azure não responde e não for possível redefinir a senha, você pode [Redefinir a senha do Windows local offline](virtual-machines-windows-reset-local-password-without-agent.md). Este método é um processo mais avançado e requer que você conectar o disco rígido virtual da máquina virtual problemático para máquina virtual outra. Siga as etapas documentadas neste artigo primeiro e tente apenas o método de redefinição de senha offline como último recurso.

[Recursos e extensões de máquina virtual azure](virtual-machines-windows-extensions-features.md)

[Conectar a uma máquina virtual Azure com RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Solucionar problemas de conexões de área de trabalho remota para um computador baseado no Windows Azure virtual](virtual-machines-windows-troubleshoot-rdp-connection.md)
