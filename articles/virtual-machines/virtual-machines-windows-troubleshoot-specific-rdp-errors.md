<properties
    pageTitle="Mensagens de erro RDP específicas para VMs Azure | Microsoft Azure"
    description="Entender as mensagens de erro específicas que você pode receber ao tentar usam a conexão de área de trabalho remota para um computador virtual Windows no Azure"
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
    ms.date="10/14/2016"
    ms.author="iainfou"/>

# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Solução de problemas de mensagens de erro RDP específicas para uma máquina de virtual do Windows no Azure
Você pode receber uma mensagem de erro específica ao usar conexão de área de trabalho remota a uma máquina virtual do Windows (máquina virtual) no Azure. Este artigo detalha algumas das mensagens de erro mais comuns encontradas, juntamente com as etapas para resolvê-los de solução de problemas. Se você estiver tendo problemas de conexão com sua máquina virtual usando RDP, mas não encontrar uma mensagem de erro específica, consulte o [guia de área de trabalho remota de solução de problemas](virtual-machines-windows-troubleshoot-rdp-connection.md).

Para obter informações sobre mensagens de erro específicas, consulte o seguinte:

- [A sessão remota foi desconectada porque não há nenhum servidores de licença de área de trabalho remota disponíveis para fornecer uma licença](#rdplicense).
- [Área de trabalho remota não consegue encontrar o computador "nome"](#rdpname).
- Erro de [uma autenticação. Autoridade de segurança Local não pode ser contatada](#rdpauth).
- [Erro de segurança do Windows: suas credenciais não funcionou](#wincred).
- [Este computador não pode se conectar ao computador remoto](#rdpconnect).

<a id="rdplicense"></a>
## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>A sessão remota foi desconectada porque não há nenhum servidores de licença de área de trabalho remota disponíveis para fornecer uma licença.

Causa: O período de cortesia de licenciamento de 120 dias para a função de servidor de área de trabalho remota expirou e você precisa instalar licenças.

Como alternativa, salve uma cópia local do arquivo RDP a partir do portal e executar este comando no prompt de comando do PowerShell para se conectar. Esta etapa desativa licenciamento para apenas essa conexão:

        mstsc <File name>.RDP /admin

Se realmente não precisar mais de duas conexões de área de trabalho remota simultâneas para a máquina virtual, você pode usar o Gerenciador de servidor para remover a função de servidor de área de trabalho remota.

Para obter mais informações, consulte o blog postar [máquina virtual do Azure falha "Sem servidores de licenças de área de trabalho remota disponível"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>
## <a name="remote-desktop-cant-find-the-computer-name"></a>Área de trabalho remota não consegue encontrar o computador "nome".

Causa: O cliente de Desktop remoto em seu computador não consegue resolver o nome do computador nas configurações do arquivo RDP.

Soluções possíveis:

- Se você estiver na intranet de uma organização, verifique se seu computador tem acesso ao servidor proxy e pode enviar tráfego HTTPS para ela.

- Se você estiver usando um arquivo RDP armazenado localmente, tente usar aquele que é gerado pelo portal. Esta etapa garante que você tenha o nome correto de DNS para a máquina virtual, ou o serviço de nuvem e a porta de ponto de extremidade da máquina virtual. Aqui está um exemplo de arquivo RDP gerado pelo portal:

        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

A parte de endereço deste arquivo RDP tem:
- O nome de domínio totalmente qualificado do serviço de nuvem que contém a máquina virtual ("tailspin-azdatatier.cloudapp.net" neste exemplo).

- Porta TCP externa do ponto de extremidade para o tráfego de área de trabalho remota (55919).

<a id="rdpauth"></a>
## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Ocorreu um erro de autenticação. Autoridade de segurança Local não pode ser contatada.

Causa: O destino máquina virtual não consegue localizar a autoridade de segurança na parte do nome de usuário das suas credenciais.

Quando o seu nome de usuário for no formulário *SecurityAuthority*\\*UserName* (exemplo: CORP\User1), a parte *SecurityAuthority* é o nome do computador da VM (para a autoridade de segurança local) ou um nome de domínio do Active Directory.

Soluções possíveis:

- Se a conta for local para a máquina virtual, certifique-se de que o nome de máquina virtual é escrito corretamente.

- Se a conta estiver em um domínio Active Directory, verifique a ortografia do nome de domínio.

- Se for uma conta de domínio do Active Directory e o nome de domínio escrito corretamente, verifique se um controlador de domínio está disponível nesse domínio. É um problema comum em redes virtuais Azure que contêm controladores de domínio que um controlador de domínio não está disponível porque ele ainda não foi iniciado. Como alternativa, você pode usar uma conta de administrador local em vez de uma conta de domínio.

<a id="wincred"></a>
## <a name="windows-security-error-your-credentials-did-not-work"></a>Erro de segurança do Windows: suas credenciais não funcionam.

Causa: O destino máquina virtual não pode validar seu nome de conta e senha.

Um computador baseado no Windows pode validar as credenciais de uma conta local ou uma conta de domínio.

- Para contas locais, use o *NomeDoComputador*\\sintaxe de*nome de usuário* (exemplo: SQL1\Admin4798).
- Para contas de domínio, use *nome_do_domínio*\\sintaxe de*nome de usuário* (exemplo: CONTOSO\peterodman).

Se você tiver promovido sua máquina virtual para um controlador de domínio em uma nova floresta do Active Directory, a conta de administrador local que você assinou com é convertida em uma conta equivalente com a mesma senha na nova floresta e domínio. A conta local é excluída.

Por exemplo, se você conectado com a conta local DC1\DCAdmin e, em seguida, promovidos na máquina virtual como um controlador de domínio em uma nova floresta para o domínio corp.contoso.com, a conta local DC1\DCAdmin é excluída e uma nova conta de domínio (CORP\DCAdmin) é criada com a mesma senha.

Certifique-se de que o nome da conta é um nome que a máquina virtual pode verificar como uma conta válida e que a senha está correta.

Se você precisar alterar a senha da conta administrador local, consulte [como redefinir uma senha ou o serviço de área de trabalho remota para máquinas virtuais do Windows](virtual-machines-windows-reset-rdp.md).

<a id="rdpconnect"></a>
## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Este computador não pode se conectar ao computador remoto.

Causa: A conta que é usada para conectar não tem direitos de entrada da área de trabalho remota.

Cada computador Windows tem um área de trabalho remota usuários grupo local, que contém as contas e grupos que podem entrar remotamente. Membros do grupo de administradores locais também têm acesso, embora as contas não estão listadas no grupo local de usuários de área de trabalho remota. Para máquinas de domínio, o grupo de administradores locais também contém os administradores de domínio para o domínio.

Certifique-se de que a conta que você está usando para se conectar com tem direitos de entrada da área de trabalho remota. Como alternativa, use uma conta de administrador local ou domínio para se conectar pela área de trabalho remota. Para adicionar a conta desejada para o grupo local de usuários de área de trabalho remota, use o snap-in do Console de gerenciamento da Microsoft (**Ferramentas do sistema > usuários e grupos locais > grupos > usuários de área de trabalho remota**).


## <a name="next-steps"></a>Próximas etapas
Se nenhuma desses erros ocorreu e você tiver um problema com a conexão usando RDP desconhecido, consulte o [guia de área de trabalho remota de solução de problemas](virtual-machines-windows-troubleshoot-rdp-connection.md).

- [Pacote de diagnóstico do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
- Para etapas ao acessar aplicativos em execução em uma máquina virtual de solução de problemas, consulte [Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure](virtual-machines-linux-troubleshoot-app-connection.md).
- Se você estiver tendo problemas usando Secure Shell (SSH) para se conectar a uma VM Linux no Azure, consulte [Solucionar problemas de SSH conexões para uma máquina virtual de Linux no Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).