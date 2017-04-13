<properties
    pageTitle="Instalar a segurança de profunda Micro tendência em uma máquina virtual | Microsoft Azure"
    description="Este artigo descreve como instalar e configurar a segurança de antivírus em uma máquina virtual criada com o modelo de implantação clássico no Azure."
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


# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Como instalar e configurar tendência Micro profunda Security como um serviço em uma máquina virtual Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Este artigo mostra como instalar e configurar tendência Micro profunda Security como um serviço em uma nova ou existente máquina virtual (VM) executando o Windows Server. Segurança profunda como um serviço inclui proteção antimalware, um firewall, um sistema de prevenção de intrusos e monitoramento de integridade.

O cliente é instalado como uma extensão de segurança através do agente de máquina virtual. Em uma nova máquina virtual, você instala o agente de máquina virtual juntamente com o agente de segurança profunda. Em uma máquina virtual existente que não tem o agente de máquina virtual, você precisa baixar e instalá-lo primeiro. Este artigo aborda ambas as situações.

Se você tiver assinatura existente do antivírus para uma solução local, você pode usá-lo para ajudar a proteger suas máquinas virtuais Azure. Se você não ainda é um cliente, você pode inscrever-se para uma assinatura de avaliação. Para obter mais informações sobre essa solução, consulte a postagem [Microsoft Azure máquina virtual agente de extensão para profunda segurança](http://go.microsoft.com/fwlink/p/?LinkId=403945)do blog do antivírus.

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Instalar o agente de segurança detalhada em uma nova VM

O [portal de clássico Azure](http://manage.windowsazure.com) permite que você instale o agente de máquina virtual e a extensão de segurança de antivírus quando você usa a opção **De galeria** para criar a máquina virtual. Se você estiver criando uma única máquina virtual, usando o portal é uma maneira fácil de adicionar proteção de antivírus.

Essa opção **De galeria** abre um assistente que ajuda você a configurar a máquina virtual. Você pode usar a última página do Assistente para instalar o agente de máquina virtual e a extensão de segurança de antivírus. Para obter instruções gerais, consulte [criar uma máquina virtual executando o Windows no portal de clássico do Azure](virtual-machines-windows-classic-tutorial.md). Quando chegar à última página do assistente, faça o seguinte:

1.  Em **Agente de máquina virtual**, marque **Instalar agente de máquina virtual**.

2.  Em **Extensões de segurança**, marque **Agente de segurança profunda Micro tendência**.

    ![Instale o agente de máquina virtual e o agente de segurança profunda](./media/virtual-machines-windows-classic-install-trend/InstallVMAgentandTrend.png)

3.  Clique na marca de seleção para criar a máquina virtual.

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Instalar o agente de segurança detalhada em uma máquina virtual existente

Para instalar o agente em uma máquina virtual existente, você precisa do seguinte:

- O módulo do PowerShell do Azure, versão 0.8.2 ou mais recente, instalado no computador local. Você pode verificar a versão do PowerShell do Azure que você instalou usando o **azure Get-módulo | versão Formatar tabela** comando. Para obter instruções e um link para a versão mais recente, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Faça logon em sua assinatura Azure usando `Add-AzureAccount`.

- O agente de máquina virtual instalado na máquina virtual de destino.

Primeiro, verifique se o agente de máquina virtual já está instalado. Preencha o nome de serviço de nuvem e máquina virtual e, em seguida, execute os seguintes comandos em um prompt de comando do PowerShell do Azure de nível de administrador. Substituir tudo entre aspas, incluindo o < e > caracteres.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Se você não souber o serviço de nuvem e o nome da máquina virtual, execute **Get-AzureVM** para exibir essas informações para todas as máquinas virtuais em sua assinatura atual.

Se o comando **write-host** retorna **True**, o agente de máquina virtual está instalado. Se ela retorna **False**, consulte as instruções e um link para o download no blog Azure postar [agente de máquina virtual e extensões - parte 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Se o agente de máquina virtual estiver instalado, execute esses comandos.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Próximas etapas

Leva alguns minutos para que o agente iniciar a execução quando ele é instalado. Depois disso, você precisa ativar segurança profunda na máquina virtual, de forma que ele pode ser gerenciado por um gerente de segurança profunda. Veja a seguir para obter instruções adicionais:

- Artigo de tendência sobre essa solução, [Instant-On segurança de nuvem do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
- Um [exemplo de script do Windows PowerShell](http://go.microsoft.com/fwlink/?LinkId=404100) para configurar a máquina virtual
- [Instruções](http://go.microsoft.com/fwlink/?LinkId=404099) para a amostra

## <a name="additional-resources"></a>Recursos adicionais

[Como fazer logon em uma máquina virtual executando o Windows Server]

[Extensões de máquina virtual Azure e recursos]


<!--Link references-->
[Como fazer logon em uma máquina virtual executando o Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Extensões de máquina virtual Azure e recursos]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
