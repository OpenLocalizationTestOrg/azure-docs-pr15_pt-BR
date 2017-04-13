<properties
    pageTitle="Instalar proteção de ponto de extremidade do Symantec em uma máquina virtual | Microsoft Azure"
    description="Saiba como instalar e configurar a extensão de segurança de proteção de ponto de extremidade do Symantec em uma nova ou existente do Azure máquina virtual criada com o modelo de implantação clássico."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="iainfou"/>

# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Como instalar e configurar a proteção de ponto de extremidade do Symantec em uma máquina virtual Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Este artigo mostra como instalar e configurar o cliente de proteção de ponto de extremidade do Symantec em uma máquina virtual existente (máquina virtual) executando o Windows Server. Este é o cliente completo, que inclui serviços como vírus e proteção contra spyware, firewall e prevenção de intrusos. O cliente é instalado como uma extensão de segurança usando o agente de máquina virtual.

Se você tiver uma assinatura existente da Symantec para uma solução local, você pode usá-lo para proteger suas máquinas virtuais Azure. Se você não ainda é um cliente, você pode inscrever-se para uma assinatura de avaliação. Para obter mais informações sobre essa solução, consulte [Proteção de ponto de extremidade de Symantec na plataforma Windows Azure da Microsoft][Symantec]. Esta página também tem links para informações sobre o licenciamento e as instruções para instalar o cliente se você já for um cliente Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Instalar proteção de ponto de extremidade do Symantec em uma máquina virtual existente

Antes de começar, você precisa do seguinte:

- O módulo do PowerShell do Azure, versão 0.8.2 ou posterior, no seu computador de trabalho. Você pode verificar a versão do PowerShell do Azure que você tenha instalado com o **azure Get-módulo | versão Formatar tabela** comando. Para obter instruções e um link para a versão mais recente, consulte [como instalar e configurar o Azure PowerShell][PS]. Faça logon em sua assinatura Azure usando `Add-AzureAccount`.

- O agente de máquina virtual em execução na máquina Virtual do Azure.

Primeiro, verifique se que o agente de máquina virtual já está instalado na máquina virtual. Preencha o nome de serviço de nuvem e máquina virtual e, em seguida, execute os seguintes comandos em um prompt de comando do PowerShell do Azure de nível de administrador. Substituir tudo entre aspas, incluindo o < e > caracteres.

> [AZURE.TIP] Se você não souber o serviço de nuvem e nomes de máquina virtual, execute **Get-AzureVM** para listar os nomes de todas as máquinas virtuais em sua assinatura atual.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Se o comando **write-host** exibe **True**, o agente de máquina virtual está instalado. Se ele exibe **False**, consulte as instruções e um link para o download no blog Azure postar [agente de máquina virtual e extensões - parte 2][Agent].

Se o agente de máquina virtual estiver instalado, execute esses comandos para instalar o agente de proteção de ponto de extremidade do Symantec.

    $Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

    Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Para verificar se a extensão de segurança Symantec foi instalada e está atualizada:

1.  Faça logon na máquina virtual. Para obter instruções, consulte [como faça logon no Máquina Virtual executando o Windows Server][Logon].
2.  Para o Windows Server 2008 R2, clique em **Iniciar > proteção de ponto de extremidade do Symantec**. Para o Windows Server 2012 ou Windows Server 2012 R2, na tela Iniciar, digite **Symantec**e, em seguida, clique em **Proteção de ponto de extremidade de Symantec**.
3.  Na guia **Status** da janela de **Proteção de ponto de extremidade de Status-Symantec** , aplicar atualizações ou reiniciar se necessário.

## <a name="additional-resources"></a>Recursos adicionais

[Como fazer logon em uma máquina Virtual executando o Windows Server][Logon]

[Recursos e extensões de máquina virtual azure][Ext]


<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-classic-tutorial.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-windows-classic-connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493