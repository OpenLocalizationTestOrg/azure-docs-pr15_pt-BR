<properties
    pageTitle="Não é possível RDP para uma máquina virtual Azure | Microsoft Azure"
    description="Solucionar problemas quando você não consegue se conectar à sua máquina virtual Windows Azure usando a área de trabalho remota"
    keywords="Erro de desktop remoto, erro de conexão de área de trabalho remota, não consigo conectar à máquina virtual, solução de problemas da área de trabalho remota"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="10/26/2016"
    ms.author="iainfou"/>

# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Solucionar problemas de conexões de área de trabalho remota a uma máquina virtual Azure

A conexão de protocolo de área de trabalho remota (RDP) para sua baseado no Windows Azure máquina virtual (VM) pode falhar por vários motivos, deixando não é possível acessar sua máquina virtual. O problema pode ser com o serviço de área de trabalho remota na máquina virtual, a conexão de rede ou o cliente de área de trabalho remota no computador host. Este artigo orienta você a alguns dos métodos mais comuns para resolver problemas de conexão de RDP. 

Se precisar de mais ajuda a qualquer momento neste artigo, você pode contatar os especialistas Azure no [MSDN Azure e fóruns de estouro de pilha](https://azure.microsoft.com/support/forums/). Como alternativa, você pode enviar um incidente de suporte Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**.

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Etapas de solução de problemas rápidas
Após cada etapa de solução de problemas, reconectar-se para a máquina virtual:

1. Redefina a configuração de área de trabalho remota.
2. Grupo de segurança de rede verificar regras / nuvem pontos de extremidade de serviços.
3. Examine os logs de console de máquina virtual.
4. Verificar a integridade do recurso de máquina virtual.
5. Redefina sua senha de máquina virtual.
6. Reinicie sua máquina virtual.
7. Reimplante sua máquina virtual.

Se você precisar de etapas mais detalhadas e explicações, continue lendo.

> [AZURE.TIP] Se o botão **Conectar** para sua máquina virtual estiver esmaecido no portal e você não estiver conectado ao Azure por meio de uma conexão [Via expressa](../expressroute/expressroute-introduction.md) ou [- to-Site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) , você precisa criar e atribuir um endereço IP público de sua máquina virtual antes de poder usar RDP. Você pode ler mais sobre [os endereços IP públicos no Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Maneiras de solucionar problemas RDP
Você pode solucionar VMs criadas usando o modelo de implantação do Gerenciador de recursos usando um dos seguintes métodos:

- [Portal do azure](#using-the-azure-portal) - excelente se você precisar redefinir rapidamente as credenciais de usuário ou configurações de RDP e você não tem as ferramentas Azure instaladas.
- [Azure PowerShell](#using-azure-powershell) - se você estiver familiarizado com o prompt do PowerShell redefinir rapidamente as credenciais de usuário ou configurações de RDP usando os cmdlets do PowerShell do Azure.

Você também pode encontrar as etapas em VMs criadas usando o [modelo de implantação do clássico](#troubleshoot-vms-created-using-the-classic-deployment-model)de solução de problemas.


<a id="fix-common-remote-desktop-errors"></a>
## <a name="troubleshoot-using-the-azure-portal"></a>Solucionar problemas usando o portal do Azure
Após cada etapa de solução de problemas, tente conectar à sua máquina virtual novamente. Se você ainda não consegue se conectar, tente a próxima etapa.

1. **Redefinir sua conexão de RDP**. Esta etapa de solução de problemas redefine a configuração de RDP quando conexões remotas estão desabilitadas ou regras de Firewall do Windows estão bloqueando RDP, por exemplo.

    Selecione sua máquina virtual no portal do Azure. Role para baixo o painel de configurações para a seção **suporte + solução de problemas** , parte inferior da lista. Clique no botão **Redefinir a senha** . Defina o **modo** para **Redefinir somente configuração** e clique no botão de **atualização** :

    ![Redefinir a configuração de RDP no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-rdp.png)

2. **Verifique se o grupo de segurança de rede regras**. Esta etapa de solução de problemas verifica se você tem uma regra no seu grupo de segurança de rede para permitir o tráfego RDP. A porta padrão para RDP é a porta TCP 3389. Uma regra para permitir o tráfego RDP pode não ser criada automaticamente quando você cria sua máquina virtual.

    Selecione sua máquina virtual no portal do Azure. Clique nas **interfaces de rede** do painel de configurações.

    ![Interfaces de rede de modo de exibição para uma máquina virtual no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-network-interfaces.png)

    Selecione a interface de rede na lista (há normalmente apenas um):

    ![Selecione a interface de rede no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-interface.png)

    Selecione o **grupo de segurança de rede** para exibir o grupo de segurança de rede associada a sua interface de rede:

    ![Selecione o grupo de segurança de rede no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-nsg.png)

    Verificar se uma regra de entrada existe que permite o tráfego RDP na porta TCP 3389. O exemplo a seguir mostra uma regra de segurança válido que permite o tráfego RDP. Você pode ver `Service` e `Action` estão configurados corretamente:

    ![Verifique se a regra de RDP NSG no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/verify-nsg-rules.png)

    Se você não tiver uma regra que permite que o tráfego RDP, [Crie uma regra de grupo de segurança de rede](virtual-machines-windows-nsg-quickstart-portal.md). Permitir que a porta TCP 3389.

3. **Diagnóstico de inicialização de máquina virtual de revisão**. Esta etapa de solução de problemas analisa os logs de console de máquina virtual para determinar se a máquina virtual está relatando um problema. Nem todas as VMs tem diagnóstico de inicialização habilitado, portanto essa etapa de solução de problemas pode ser opcional.
    
    Etapas de solução de problemas específicas estão além do escopo deste artigo, mas podem indicar um problema mais largo que está afetando a conectividade RDP. Para obter mais informações sobre como revisar os logs de console e a captura de tela de máquina virtual, consulte [Diagnóstico de inicialização para VMs](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

4. **Verificar a integridade do recurso de máquina virtual**. Esta etapa de solução de problemas verifica não existem problemas conhecidos com a plataforma Windows Azure que podem afetar a conectividade com a máquina virtual.

    Selecione sua máquina virtual no portal do Azure. Role para baixo o painel de configurações para a seção **suporte + solução de problemas** , parte inferior da lista. Clique no botão de **integridade do recurso** . Uma máquina virtual eficaz, relatórios como sendo **disponível**:

    ![Verificar a integridade do recurso de máquina virtual no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/check-resource-health.png)

5. **Redefinir credenciais de usuário**. Esta etapa de solução de problemas redefine a senha em uma conta de administrador local quando você não tiver certeza ou tiver esquecido as credenciais.

    Selecione sua máquina virtual no portal do Azure. Role para baixo o painel de configurações para a seção **suporte + solução de problemas** , parte inferior da lista. Clique no botão **Redefinir a senha** . Verifique se que o **modo** é definido como **Redefinir a senha** e insira seu nome de usuário e uma nova senha. Por fim, clique no botão de **atualização** :

    ![Redefinir as credenciais de usuário no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-password.png)

6. **Reinicie sua máquina virtual**. Esta etapa de solução de problemas pode corrigir qualquer problema subjacente que a máquina virtual em si está tendo.

    Selecione sua máquina virtual no portal do Azure e clique na guia **Visão geral** . Clique no botão **Reiniciar** :

    ![Reiniciar a máquina virtual no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/restart-vm.png)

7. **Reimplantar sua máquina virtual**. Esta etapa de solução de problemas reimplanta sua máquina virtual para outro host dentro do Azure para corrigir problemas de rede ou qualquer plataforma subjacente.

    Selecione sua máquina virtual no portal do Azure. Role para baixo o painel de configurações para a seção **suporte + solução de problemas** , parte inferior da lista. Clique no botão **reimplantar** e clique em **reimplantar**:

    ![Reimplantar a máquina virtual no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/redeploy-vm.png)

    Após concluir esta operação, dados de disco efêmeras são perdidos e endereços IP dinâmicos que estão associados a máquina virtual são atualizados.

Se você ainda estiver encontrando problemas RDP, você pode [Abrir uma solicitação de suporte](https://azure.microsoft.com/support/options/) ou leia os [conceitos de solução de problemas de RDP mais detalhados e etapas](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-using-azure-powershell"></a>Solucionar problemas usando o PowerShell do Azure
Se você ainda não fez isso, [Instale e configure o PowerShell mais recente do Azure](../powershell-install-configure.md).

Os exemplos a seguir usam variáveis como `myResourceGroup`, `myVM`, e `myVMAccessExtension`. Substitua esses locais e nomes de variável com seus próprios valores.

> [AZURE.NOTE] Você redefinir as credenciais de usuário e a configuração de RDP usando o cmdlet do PowerShell [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) . Nos exemplos a seguir, `myVMAccessExtension` é um nome que você especificar como parte do processo. Se você anteriormente tiver trabalhado com o VMAccessAgent, você pode obter o nome da extensão existente usando `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` para verificar as propriedades da máquina virtual. Para exibir o nome, procure na seção 'Extensões' da saída.

Após cada etapa de solução de problemas, tente conectar à sua máquina virtual novamente. Se você ainda não consegue se conectar, tente a próxima etapa.

1. **Redefinir sua conexão de RDP**. Esta etapa de solução de problemas redefine a configuração de RDP quando conexões remotas estão desabilitadas ou regras de Firewall do Windows estão bloqueando RDP, por exemplo.

    O exemplo a seguir redefine a conexão RDP em uma máquina virtual chamada `myVM` no `WestUS` local e, no grupo de recursos denominado `myResourceGroup`:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```

2. **Verifique se o grupo de segurança de rede regras**. Esta etapa de solução de problemas verifica se você tem uma regra no seu grupo de segurança de rede para permitir o tráfego RDP. A porta padrão para RDP é a porta TCP 3389. Uma regra para permitir o tráfego RDP pode não ser criada automaticamente quando você cria sua máquina virtual.

    Primeiro, atribua todos os dados de configuração para seu grupo de segurança de rede para o `$rules` variável. O exemplo a seguir obtém informações sobre o grupo de segurança de rede chamado `myNetworkSecurityGroup` no grupo de recursos denominado `myResourceGroup`:

    ```powershell
    $rules = Get-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```

    Agora, exiba as regras que estão configuradas para esse grupo de segurança de rede. Verifique se existe uma regra para permitir a porta TCP 3389 para conexões de entrada da seguinte maneira:

    ```powershell
    $rules.SecurityRules
    ```

    O exemplo a seguir mostra uma regra de segurança válido que permite o tráfego RDP. Você pode ver `Protocol`, `DestinationPortRange`, `Access`, e `Direction` estão configurados corretamente:

    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```

    Se você não tiver uma regra que permite que o tráfego RDP, [Crie uma regra de grupo de segurança de rede](virtual-machines-windows-nsg-quickstart-powershell.md). Permitir que a porta TCP 3389.

3. **Redefinir credenciais de usuário**. Esta etapa de solução de problemas redefine a senha da conta administrador local que você especifica quando você não tiver certeza do ou tiver esquecido, as credenciais.

    Primeiro, especifique o nome de usuário e uma nova senha atribuindo credenciais para o `$cred` variável da seguinte forma:

    ```powershell
    $cred=Get-Credential
    ```

    Agora, atualize as credenciais em sua máquina virtual. O exemplo a seguir atualiza as credenciais em uma máquina virtual chamado `myVM` no `WestUS` local e, no grupo de recursos denominado `myResourceGroup`:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```

4. **Reinicie sua máquina virtual**. Esta etapa de solução de problemas pode corrigir qualquer problema subjacente que a máquina virtual em si está tendo.

    O exemplo a seguir reinicia a máquina virtual chamada `myVM` no grupo de recursos denominado `myResourceGroup`:

    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

5. **Reimplantar sua máquina virtual**. Esta etapa de solução de problemas reimplanta sua máquina virtual para outro host dentro do Azure para corrigir problemas de rede ou qualquer plataforma subjacente.

    O exemplo a seguir reimplanta a máquina virtual chamada `myVM` no `WestUS` local e, no grupo de recursos denominado `myResourceGroup`:

    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

Se você ainda estiver encontrando problemas RDP, você pode [Abrir uma solicitação de suporte](https://azure.microsoft.com/support/options/) ou leia os [conceitos de solução de problemas de RDP mais detalhados e etapas](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Solucionar problemas de VMs criadas usando o modelo de implantação do clássico

Após cada etapa de solução de problemas, tente reconectar para a máquina virtual.

1. **Redefinir sua conexão de RDP**. Esta etapa de solução de problemas redefine a configuração de RDP quando conexões remotas estão desabilitadas ou regras de Firewall do Windows estão bloqueando RDP, por exemplo.

    Selecione sua máquina virtual no portal do Azure. Clique na **... Mais** e clique em **Redefinir acesso remoto**:

    ![Redefinir a configuração de RDP no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-rdp.png)

2.  **Pontos de extremidade de verificar os serviços de nuvem**. Esta etapa de solução de problemas verifica se você tem pontos de extremidade em seus serviços de nuvem para permitir o tráfego RDP. A porta padrão para RDP é a porta TCP 3389. Uma regra para permitir o tráfego RDP pode não ser criada automaticamente quando você cria sua máquina virtual.

    Selecione sua máquina virtual no portal do Azure. Clique no botão de **pontos de extremidade** para exibir os pontos de extremidade atualmente configurados para sua máquina virtual. Verificar se os pontos de extremidade existem permitir o tráfego RDP na porta TCP 3389.
    
    O exemplo a seguir mostra os pontos de extremidade válidos que permitem o tráfego RDP:

    ![Verifique se os pontos de extremidade de serviços de nuvem no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)

    Se você não tiver um ponto de extremidade que permite que o tráfego RDP, [Crie um ponto de extremidade de serviços de nuvem](virtual-machines-windows-classic-setup-endpoints.md). Permitir TCP particular porta 3389.

3. **Diagnóstico de inicialização de máquina virtual de revisão**. Esta etapa de solução de problemas analisa os logs de console de máquina virtual para determinar se a máquina virtual está relatando um problema. Nem todas as VMs tem diagnóstico de inicialização habilitado, portanto essa etapa de solução de problemas pode ser opcional.
    
    Etapas de solução de problemas específicas estão além do escopo deste artigo, mas podem indicar um problema mais largo que está afetando a conectividade RDP. Para obter mais informações sobre como revisar os logs de console e a captura de tela de máquina virtual, consulte [Diagnóstico de inicialização para VMs](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

4. **Verificar a integridade do recurso de máquina virtual**. Esta etapa de solução de problemas verifica não existem problemas conhecidos com a plataforma Windows Azure que podem afetar a conectividade com a máquina virtual.

    Selecione sua máquina virtual no portal do Azure. Role para baixo o painel de configurações para a seção **suporte + solução de problemas** , parte inferior da lista. Clique no botão de **Integridade do recurso** . Uma máquina virtual eficaz, relatórios como sendo **disponível**:

    ![Verificar a integridade do recurso de máquina virtual no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-check-resource-health.png)

5. **Redefinir credenciais de usuário**. Esta etapa de solução de problemas redefine a senha da conta administrador local que você especificar quando você não tiver certeza ou tiver esquecido as credenciais.

    Selecione sua máquina virtual no portal do Azure. Role para baixo o painel de configurações para a seção **suporte + solução de problemas** , parte inferior da lista. Clique no botão **Redefinir a senha** . Insira seu nome de usuário e uma nova senha. Por fim, clique no botão **Salvar** :

    ![Redefinir as credenciais de usuário no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-password.png)

6. **Reinicie sua máquina virtual**. Esta etapa de solução de problemas pode corrigir qualquer problema subjacente que a máquina virtual em si está tendo.

    Selecione sua máquina virtual no portal do Azure e clique na guia **Visão geral** . Clique no botão **Reiniciar** :

    ![Reiniciar a máquina virtual no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-restart-vm.png)
    
Se você ainda estiver encontrando problemas RDP, você pode [Abrir uma solicitação de suporte](https://azure.microsoft.com/support/options/) ou leia os [conceitos de solução de problemas de RDP mais detalhados e etapas](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-specific-rdp-errors"></a>Solucionar problemas de erros específicos de RDP
Você pode encontrar uma mensagem de erro específica ao tentar se conectar à sua máquina virtual via RDP. A seguir estão as mensagens de erro mais comuns:

- [A sessão remota foi desconectada porque não há nenhum servidores de licença de área de trabalho remota disponíveis para fornecer uma licença](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdplicense).
- [Área de trabalho remota não consegue encontrar o computador "nome"](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpname).
- Erro de [uma autenticação. Autoridade de segurança Local não pode ser contatada](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpauth).
- [Erro de segurança do Windows: suas credenciais não funcionou](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#wincred).
- [Este computador não pode se conectar ao computador remoto](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpconnect).


## <a name="additional-resources"></a>Recursos adicionais
Se nenhuma desses erros ocorreu e você ainda não consegue se conectar para a máquina virtual por meio de área de trabalho remota, leia detalhadas de [solução de problemas de guia para área de trabalho remota](virtual-machines-windows-detailed-troubleshoot-rdp.md).

- [Pacote de diagnóstico do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
- Para etapas ao acessar aplicativos em execução em uma máquina virtual de solução de problemas, consulte [Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure](virtual-machines-linux-troubleshoot-app-connection.md).
- Se você estiver tendo problemas usando Secure Shell (SSH) para se conectar a uma VM Linux no Azure, consulte [Solucionar problemas de SSH conexões para uma máquina virtual de Linux no Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).
