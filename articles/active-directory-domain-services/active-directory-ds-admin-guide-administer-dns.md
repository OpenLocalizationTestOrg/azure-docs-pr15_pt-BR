<properties
    pageTitle="Azure domínio serviços do Active Directory: Administrar DNS em domínios gerenciados | Microsoft Azure"
    description="Administrar DNS em domínios gerenciados do Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Administrar o DNS em um domínio gerenciado de serviços de domínio do Azure AD
Azure Active Directory Domain Services inclui um servidor DNS (resolução de nome de domínio) que fornece resolução DNS para o domínio gerenciado. Ocasionalmente, você talvez precise configurar o DNS do domínio gerenciado. Você talvez precise criar registros DNS para computadores que não fazem parte de domínio, configurar endereços IP virtuais para balanceadores de carga ou configurar os encaminhadores DNS externos. Por esse motivo, os usuários que pertencem ao grupo 'Administradores de DC AAD' são concedidos privilégios de administração de DNS no domínio gerenciado.


## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisa:

1. Uma **assinatura do Azure**válida.

2. Um **diretório do Azure AD** - seja sincronizado com um diretório local ou um diretório somente na nuvem.

3. **Serviços de domínio do Azure AD** deve estar habilitado para o diretório do Azure AD. Se você ainda não tiver feito isso, siga todas as tarefas descritas no [guia de Introdução](./active-directory-ds-getting-started.md).

4. Um **domínio de máquina virtual** do qual você administra os serviços de domínio do Azure AD gerenciadas domínio. Se você não tiver tal uma máquina virtual, siga todas as tarefas descritas no artigo intitulado [ingressar em uma máquina virtual Windows um domínio gerenciado](./active-directory-ds-admin-guide-join-windows-vm.md).

5. Você precisa as credenciais de uma **conta de usuário pertencentes ao grupo ' Administradores de DC AAD'** no seu diretório, para administrar o DNS do seu domínio gerenciado.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Tarefa 1 - provisionar uma máquina virtual de domínio para administrar remotamente o DNS para o domínio gerenciado
Azure domínios gerenciados de serviços de domínio do AD podem ser gerenciados remotamente usando familiares ferramentas administrativas do Active Directory como o Active Directory administrativo central (ADAC) ou o PowerShell do AD. Da mesma forma, pode ser administrado DNS para o domínio gerenciado remotamente usando as ferramentas de administração do servidor DNS.

Os administradores no diretório Azure AD não tem privilégios para se conectar a controladores de domínio no domínio gerenciado por meio de área de trabalho remota. Membros do grupo 'Administradores de DC AAD' podem administrar o DNS para domínios gerenciados remotamente usando as ferramentas de servidor DNS de um computador cliente/servidor do Windows que está associado ao domínio gerenciado. Ferramentas do servidor DNS podem ser instaladas como parte do recurso opcional de ferramentas de administração de servidor remoto (RSAT) no Windows Server e computadores cliente associados ao domínio gerenciado.

A primeira tarefa é provisionar uma máquina virtual do Windows Server que está associada ao domínio gerenciado. Para obter instruções, consulte o artigo intitulado [ingressar em uma máquina virtual Windows Server um domínio de serviços de domínio do Azure AD gerenciados](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Tarefa 2: ferramentas de instalação DNS Server na máquina virtual
Execute as seguintes etapas para instalar as ferramentas de administração do DNS na máquina virtual domínio associado. Para obter mais informações sobre como [instalar e usar as ferramentas de administração de servidor remoto](https://technet.microsoft.com/library/hh831501.aspx), consulte o Technet.

1. Navegue até o nó de **máquinas virtuais** no portal de clássico do Azure. Selecione a máquina virtual que você criou na tarefa 1 e clique em **Conectar** na barra de comandos na parte inferior da janela.

    ![Conectar-se a máquina virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. O portal de clássico solicitará que você abrir ou salvar um arquivo com uma extensão '. rdp', que é usada para conectar-se à máquina virtual. Quando o download for concluído, clique no arquivo.

3. No prompt de logon, use as credenciais de um usuário pertencente ao grupo 'Administradores de DC AAD'. Por exemplo, usamos 'bob@domainservicespreview.onmicrosoft.com' em nosso caso.

4. Na tela Iniciar, abra o **Gerenciador de servidor**. Clique em **Adicionar funções e recursos** no painel central da janela do Gerenciador de servidor.

    ![Inicie o Gerenciador de servidor na máquina virtual](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)

5. Na página do **Assistente de recursos e adicionar funções** **Antes de começar** , clique em **Avançar**.

    ![Antes de começar a página](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)

6. Na página **Tipo de instalação** , deixe a opção de **instalação baseado em função ou baseada em recursos** marcada e clique em **Avançar**.

    ![Página do tipo de instalação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)

7. Na página **Seleção** do servidor, selecione a máquina virtual atual do pool de servidor e clique em **Avançar**.

    ![Página de seleção de servidor](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)

8. Na página de **Funções de servidor** , clique em **Avançar**. Podemos ignorar esta página como podemos não estiver instalando funções no servidor.

9. Na página **recursos** , clique para expandir o nó de **Ferramentas de administração de servidor remoto** e, em seguida, clique para expandir o nó de **Ferramentas de administração de função** . Selecione o recurso de **Ferramentas do servidor DNS** na lista de ferramentas de administração de função.

    ![Página de recursos](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

10. Na página **confirmação** , clique em **instalar** para instalar o recurso de ferramentas do servidor DNS na máquina virtual. Quando a instalação do recurso for concluído com êxito, clique em **Fechar** para sair do assistente **para adicionar funções e recursos** .

    ![Página de confirmação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)


## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Tarefa 3 - inicia o console de gerenciamento de DNS para administrar DNS
Agora que o recurso de ferramentas do servidor DNS é instalado no domínio ingressou máquina virtual, podemos usar as ferramentas DNS para administrar o DNS do domínio gerenciado.

> [AZURE.NOTE] Você precisa ser um membro do grupo 'Administradores de DC AAD', para administrar o DNS do domínio gerenciado.

1. Na tela Iniciar, clique em **Ferramentas administrativas**. Você deve ver o console **DNS** instalado na máquina virtual.

    ![Ferramentas administrativas - Console DNS](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)

2. Clique em **DNS** para iniciar o console de gerenciamento de DNS.

3. Na caixa de diálogo **conectar-se ao servidor DNS** , clique na opção intitulada **o seguinte computador**e insira o nome de domínio DNS do domínio gerenciado (por exemplo, ' contoso100.com').

    ![Console DNS - se conectar ao domínio](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

4. O Console DNS se conecta ao domínio gerenciado.

    ![Console DNS - administrar domínio](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

5. Agora você pode usar o console DNS para adicionar entradas DNS para os computadores dentro da rede virtual no qual você habilitou AAD serviços de domínio.

> [AZURE.WARNING] Tenha cuidado ao administrar o DNS para o domínio gerenciado usando ferramentas de administração de DNS. Certifique-se de que você **não exclua ou modificar os registros DNS internos que são usados pelos serviços de domínio no domínio**. Os registros DNS internos incluem os registros DNS do domínio, registros de servidor de nomes e outros registros usados para DC local. Se você modificar esses registros, serviços de domínio são afetados em uma rede virtual.


Consulte o [artigo de ferramentas DNS no Technet](https://technet.microsoft.com/library/cc753579.aspx) para obter mais informações sobre o gerenciamento de DNS.


## <a name="related-content"></a>Conteúdo relacionado

- [Serviços de domínio Azure AD - guia de Introdução](./active-directory-ds-getting-started.md)

- [Ingressar em uma máquina de virtual do Windows Server em um domínio gerenciado de serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Administrar um domínio gerenciado de serviços de domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Ferramentas de administração de DNS](https://technet.microsoft.com/library/cc753579.aspx)
