<properties
    pageTitle="Detalhadas remoto da área de trabalho para solução de problemas | Microsoft Azure"
    description="Examine as etapas detalhadas de solução de problemas de erros de desktop remotos onde você não pode para um máquinas virtuais do Windows no Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"
    keywords="não consegue se conectar à área de trabalho remota, solucionar problemas de área de trabalho remota, área de trabalho remota não conseguir se conectar, erros de desktop remotos, solução de problemas da área de trabalho remota, problemas de desktop remotos
"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Detalhadas etapas para solucionar problemas de conexão de área de trabalho remota para VMs do Windows no Azure

Este artigo fornece etapas detalhadas de solução de problemas para diagnosticar e corrigir erros de área de trabalho remota complexos para baseado no Windows Azure máquinas virtuais.

> [AZURE.IMPORTANT] Para eliminar os erros mais comuns de área de trabalho remota, certifique-se de ler [o artigo de solução de problemas básico para área de trabalho remota](virtual-machines-windows-troubleshoot-rdp-connection.md) antes de continuar.

Você pode encontrar uma mensagem de erro de área de trabalho remota que não se parecer com qualquer das mensagens de erro específicas abordadas no [guia de solução de problemas básico da área de trabalho remota](virtual-machines-windows-troubleshoot-rdp-connection.md). Siga estas etapas para determinar por que o cliente de área de trabalho remota (RDP) é possível conectar ao serviço RDP na máquina virtual do Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Se precisar de mais ajuda a qualquer momento neste artigo, você pode contatar os Azure especialistas no [Azure MSDN e os fóruns de estouro de pilha](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode enviar um incidente de suporte Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter suporte**. Para obter informações sobre como usar o suporte do Azure, leia as [Perguntas Frequentes de suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).


## <a name="components-of-a-remote-desktop-connection"></a>Componentes de uma conexão de área de trabalho remota

Os seguintes componentes estão envolvidos em uma conexão de RDP:

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_0.png)

Antes de prosseguir, isso poderá ajudar mentalmente revisar o que foi alterado desde a última conexão de área de trabalho remota bem-sucedida para a máquina virtual. Por exemplo:

- O endereço IP público da máquina virtual ou o serviço de nuvem que contém a máquina virtual (também chamada do endereço IP virtual [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) foi alterado. A falha RDP pode ser porque o cache de cliente DNS ainda tem o *endereço IP antigo* registrado para o nome DNS. Liberar o cache de cliente DNS e tente conectar-se a máquina virtual novamente. Ou tente conectar-se diretamente com o novo VIP.
- Você está usando um aplicativo de terceiros para gerenciar suas conexões de área de trabalho remota em vez de usar a conexão gerado pelo portal do Azure. Verifique se a configuração do aplicativo inclui a porta TCP correta para o tráfego de área de trabalho remota. Você pode verificar esta porta para uma máquina virtual clássica no [portal do Azure](https://portal.azure.com), clicando em configurações da VM > pontos de extremidade.


## <a name="preliminary-steps"></a>Etapas preliminares

Antes de prosseguir para a detalhadas de solução de problemas,

- Verificar o status da máquina virtual no portal do clássico Azure ou o portal do Azure para quaisquer problemas óbvios.
- Siga as [etapas de correção rápida para os erros comuns de RDP no Guia básico de solução de problemas](virtual-machines-windows-troubleshoot-rdp-connection.md#quick-troubleshooting-steps).


Tente reconectar-se para a máquina virtual por meio de área de trabalho remota após estas etapas.


## <a name="detailed-troubleshooting-steps"></a>Obter as etapas detalhadas de solução de problemas

O cliente de área de trabalho remota não poderá acessar o serviço de área de trabalho remota na VM Azure devido a problemas no seguintes fontes:

- [Computador cliente de Desktop remoto](#source-1-remote-desktop-client-computer)
- [Dispositivo de borda de intranet de organização](#source-2-organization-intranet-edge-device)
- [Ponto de extremidade do serviço em nuvem e controle lista de acesso (ACL)](#source-3-cloud-service-endpoint-and-acl)
- [Grupos de segurança de rede](#source-4-network-security-groups)
- [Baseado no Windows Azure VM](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Origem 1: Computador cliente de Desktop remoto

Verifique se que seu computador pode fazer conexões de área de trabalho remota para outro local, computador baseado no Windows.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_1.png)

Se não for possível, verifique as seguintes configurações no seu computador:

- Uma configuração de firewall local está bloqueando o tráfego de área de trabalho remota.
- Localmente instalado software de proxy de cliente que está impedindo conexões de área de trabalho remota.
- Localmente instalado software que está impedindo conexões de área de trabalho remota de monitoramento de rede.
- Outros tipos de software de segurança que monitoram o tráfego ou permitem/proíbem tipos específicos de tráfego que está impedindo conexões de área de trabalho remota.

Nesses casos, temporariamente desative o software e tentar se conectar a um computador local por meio de área de trabalho remota. Se você pode descobrir a causa real dessa maneira, trabalhe com seu administrador de rede para corrigir as configurações de software para permitir conexões de área de trabalho remota.

## <a name="source-2-organization-intranet-edge-device"></a>Origem 2: Dispositivo de borda de intranet de organização

Verifique se que o computador conectado diretamente à Internet pode fazer conexões de área de trabalho remota para sua máquina virtual Azure.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_2.png)

Se você não tiver um computador que esteja conectado diretamente à Internet, crie e teste com uma nova máquina virtual Azure em um serviço de grupo ou na nuvem do recurso. Para obter mais informações, consulte [criar uma máquina virtual executando o Windows no Azure](virtual-machines-windows-hero-tutorial.md). Você pode excluir a máquina virtual e o grupo de recursos ou serviço de nuvem, após o teste.

Se você pode criar uma conexão de área de trabalho remota com um computador conectado diretamente à Internet, verifique seu dispositivo de borda de intranet de organização para:

- Um firewall interno bloqueando HTTPS conexões com a Internet.
- Um servidor de proxy impedindo conexões de área de trabalho remota.
- Detecção de intrusos ou monitoramento software executado em dispositivos em sua rede de borda que está impedindo conexões de área de trabalho remota de rede.

Trabalhar com o administrador de rede para corrigir as configurações do seu dispositivo de borda de intranet de organização para permitir conexões de área de trabalho remota baseada em HTTPS com a Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Origem 3: Ponto de extremidade de serviço de nuvem e ACL

Para VMs criadas usando o modelo de implantação do clássico, verifique se que outra máquina virtual do Azure que está na mesma serviço de nuvem ou rede virtual pode fazer conexões de área de trabalho remota para sua máquina virtual do Azure.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_3.png)

> [AZURE.NOTE] Para máquinas virtuais criadas no Gerenciador de recursos, vá para [4 de origem: grupos de segurança de rede](#source-4-network-security-groups).

Se você não tiver outra máquina virtual no mesmo serviço de nuvem ou rede virtual, crie um. Siga as etapas em [criar uma máquina virtual executando o Windows no Azure](virtual-machines-windows-hero-tutorial.md). Exclua a máquina virtual de teste após o teste for concluído.

Se você pode se conectar por meio de área de trabalho remota a uma máquina virtual no mesmo serviço de nuvem ou rede virtual, verifique estas configurações:

- A configuração de ponto de extremidade para o tráfego de área de trabalho remota no destino máquina virtual: A porta TCP particular do ponto de extremidade deve corresponder a porta TCP na qual o serviço de área de trabalho remota da VM está atendendo (o padrão é 3389).
- ACL para o ponto de extremidade de tráfego de área de trabalho remota no destino máquina virtual: ACLs permitem especificar permitido ou negado tráfego da Internet com base no seu endereço de IP de origem. ACLs estão configurados incorretamente podem impedir que o tráfego de área de trabalho remota para o ponto de extremidade. Verificar seu ACLs para garantir que tráfego de entrada de seus endereços IP públicos do seu proxy ou outro servidor de borda é permitido. Para obter mais informações, consulte [o que é uma lista de controle de acesso (ACL) da rede?](../virtual-network/virtual-networks-acl.md)

Para verificar se o ponto de extremidade é a fonte do problema, remova o ponto de extremidade atual e crie um novo, escolhendo uma porta aleatória no intervalo 49152 – 65535 para o número da porta externa. Para obter mais informações, consulte [como configurar pontos de extremidade para uma máquina virtual](virtual-machines-windows-classic-setup-endpoints.md).

## <a name="source-4-network-security-groups"></a>Origem 4: Grupos de segurança de rede

Grupos de segurança de rede permitem um controle mais granular de tráfego de entrada e saído permitidos. Você pode criar regras que se estendem por sub-redes e serviços em uma rede virtual Azure em nuvem. Verifique suas regras de grupo de segurança de rede para garantir que o tráfego de área de trabalho remota da Internet é permitido:

- No portal do Azure, selecione sua máquina virtual
- Clique em **todas as configurações** | **interfaces de rede** e selecione sua interface de rede.
- Clique em **todas as configurações** | **grupo de segurança de rede** e escolha o grupo de segurança de rede.
- Clique em **todas as configurações** | **regras de segurança de entrada** e certifique-se de que você possui uma regra permitindo RDP na porta TCP 3389.
    - Se você não tiver uma regra, clique em **Adicionar** para criar uma regra. Insira **TCP** para o protocolo e **3389** para o intervalo de porta de destino.
    - Verifique se que a ação está definida como **Permitir** e clique em Okey para salvar sua nova regra de entrada.


Para obter mais informações, consulte [o que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)

## <a name="source-5-windows-based-azure-vm"></a>Fonte 5: Baseado no Windows Azure máquina virtual

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_5.png)

Use o [pacote de diagnóstico do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para ver se a falha devido a própria máquina virtual Azure. Se este pacote de diagnóstico conseguir resolver o problema de **conectividade RDP para uma máquina virtual de Azure (necessário reinicializar)** , siga as instruções [neste](virtual-machines-windows-reset-rdp.md)artigo. Este artigo redefine o serviço de área de trabalho remota na máquina virtual:

- Habilite a regra de padrão de Firewall do Windows "Área de trabalho remota" (porta TCP 3389).
- Habilite as conexões de área de trabalho remota definindo o valor de registro HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections como 0.

Tente a conexão do seu computador novamente. Se você estiver ainda não conseguir se conectar por meio de área de trabalho remota, verifique os possíveis problemas a seguintes:

- O serviço de área de trabalho remota não está em execução no máquina virtual de destino.
- O serviço de área de trabalho remota não está escutando na porta TCP 3389.
- Firewall do Windows ou outro firewall local tem uma regra de saída que está impedindo o tráfego de área de trabalho remota.
- Detecção de intrusos ou monitoramento software em execução na máquina virtual Azure de rede está impedindo conexões de área de trabalho remota.

Para VMs criadas usando o modelo clássico de implantação, você pode usar uma sessão remota do PowerShell do Azure na máquina virtual Azure. Primeiro, você precisa instalar um certificado para o serviço de nuvem hospedagem da máquina virtual. Vá para [Configurar o acesso remoto seguro PowerShell para máquinas virtuais do Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) e baixe o arquivo de script **InstallWinRMCertAzureVM.ps1** no seu computador local.

Em seguida, instale o Azure PowerShell se você ainda não fez isso. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

Em seguida, abra um prompt de comando do PowerShell do Azure e altere a pasta atual para o local do arquivo de script **InstallWinRMCertAzureVM.ps1** . Para executar um script do PowerShell do Azure, você deve definir a política de execução correta. Execute o comando **Get-ExecutionPolicy** para determinar o nível de política atual. Para obter informações sobre como definir o nível apropriado, consulte [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Em seguida, preencha seu nome de assinatura Azure, o nome do serviço de nuvem e seu nome de máquina virtual (removendo os caracteres < e >), e, em seguida, executar esses comandos.

    $subscr="<Name of your Azure subscription>"
    $serviceName="<Name of the cloud service that contains the target virtual machine>"
    $vmName="<Name of the target virtual machine>"
    .\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

Você pode obter o nome da assinatura correta da propriedade _nome de inscrição_ da exibição do comando **Get-AzureSubscription** . Você pode obter o nome do serviço de nuvem para a máquina virtual da coluna _ServiceName_ na exibição do comando **Get-AzureVM** .

Verifique se você tem o novo certificado. Abra um snap-in de certificados para o usuário atual e procure na pasta **Trusted Root certificação authorities\certificates.** . Você deve ver um certificado com o nome DNS do seu serviço de nuvem na coluna emitido para (exemplo: cloudservice4testing.cloudapp.net).

Em seguida, inicie uma sessão remota do PowerShell do Azure usando esses comandos.

    $uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
    $creds = Get-Credential
    Enter-PSSession -ConnectionUri $uri -Credential $creds

Depois de inserir as credenciais de administrador válida, você verá algo semelhante à seguinte prompt Azure PowerShell:

    [cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

A primeira parte do prompt é seu nome de serviço de nuvem que contém o destino máquina virtual, que podem ser diferentes das "cloudservice4testing.cloudapp.net". Você pode agora emitir Azure PowerShell comandos para esse serviço de nuvem investigar os problemas mencionados e corrija a configuração.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Para corrigir manualmente os serviços de área de trabalho remota listening porta TCP

Se você não consegue executar o [pacote de diagnóstico do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para o problema de **conectividade RDP para uma máquina virtual de Azure (necessário reinicializar)** , no prompt de sessão remoto do PowerShell do Azure, execute este comando.

    Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

A propriedade de número da porta mostra o número da porta atual. Se necessário, altere a área de trabalho remota porta número voltar para seu valor padrão (3389) usando este comando.

    Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Verifique se que a porta é alterada para 3389 usando este comando.

    Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Sair a sessão remota do PowerShell do Azure usando este comando.

    Exit-PSSession

Verifique se o ponto de extremidade de área de trabalho remota para a máquina virtual do Azure também está usando a porta TCP 3398 como sua porta interna. Reinicie a máquina virtual do Azure e tente novamente a conexão de área de trabalho remota.


## <a name="additional-resources"></a>Recursos adicionais

[Pacote de diagnóstico do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Como redefinir uma senha ou o serviço de área de trabalho remota para máquinas virtuais do Windows](virtual-machines-windows-reset-rdp.md)

[Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md)

[Solucionar problemas de conexões SSH (Secure Shell) a uma máquina virtual baseados em Linux Azure](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual Azure](virtual-machines-linux-troubleshoot-app-connection.md)
