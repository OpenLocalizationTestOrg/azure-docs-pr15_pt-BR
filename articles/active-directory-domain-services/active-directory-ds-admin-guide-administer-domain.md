<properties
    pageTitle="Azure domínio serviços do Active Directory: Administrar um domínio gerenciado | Microsoft Azure"
    description="Administrar domínios gerenciados do Azure Active Directory Domain Services"
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
    ms.date="10/02/2016"
    ms.author="maheshu"/>

# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Administrar um domínio gerenciado do Azure Active Directory Domain Services
Este artigo mostra como administrar um domínio gerenciado de serviços de domínio do Azure Active Directory (AD).


## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisa:

1. Uma **assinatura do Azure**válida.

2. Um **diretório do Azure AD** - seja sincronizado com um diretório local ou um diretório somente na nuvem.

3. **Serviços de domínio do Azure AD** deve estar habilitado para o diretório do Azure AD. Se você ainda não tiver feito isso, siga todas as tarefas descritas no [guia de Introdução](./active-directory-ds-getting-started.md).

4. Um **domínio de máquina virtual** do qual você administra os serviços de domínio do Azure AD gerenciadas domínio. Se você não tiver tal uma máquina virtual, siga todas as tarefas descritas no artigo intitulado [ingressar em uma máquina virtual Windows um domínio gerenciado](./active-directory-ds-admin-guide-join-windows-vm.md).

5. Você precisa as credenciais de uma **conta de usuário pertencentes ao grupo ' Administradores de DC AAD'** no seu diretório, para administrar seu domínio gerenciado.

<br>


## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Tarefas administrativas, que você pode executar em um domínio gerenciado
Membros do grupo 'Administradores de DC AAD' são concedidos privilégios no domínio gerenciado que habilitá-las realizar tarefas como:

- Ingresse em máquinas ao domínio gerenciado.

- Configure o GPO interno para os contêineres 'AADDC computadores' e 'AADDC usuários' no domínio gerenciado.

- Administre o DNS do domínio gerenciado.

- Criar e administrar unidades organizacionais personalizado (organizacionais) no domínio gerenciado.

- Obter acesso administrativo para computadores associado ao domínio gerenciado.


## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Você não tem em um domínio gerenciado de privilégios administrativos
O domínio é gerenciado pela Microsoft, incluindo as atividades como patches, monitoramento e, a realização de backups. Portanto, o domínio estiver bloqueado e você não tem privilégios para executar certas tarefas administrativas no domínio. A seguir estão alguns exemplos de tarefas que você não pode executar.

- Você não recebem privilégios de administrador corporativo ou administrador de domínio para o domínio gerenciado.

- Você não pode estender o esquema do domínio gerenciado.

- Você não consegue se conectar a controladores de domínio para o domínio gerenciado usando a área de trabalho remota.

- Você não poderá adicionar controladores de domínio para o domínio gerenciado.


## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Tarefa 1 - provisionar uma máquina domínio virtual do Windows Server para administrar remotamente o domínio gerenciado
Azure domínios gerenciados de serviços de domínio do AD podem ser gerenciados usando familiares ferramentas administrativas do Active Directory como o Active Directory administrativo central (ADAC) ou o PowerShell do AD. Administradores de inquilinos não tem privilégios para se conectar a controladores de domínio no domínio gerenciado por meio de área de trabalho remota. Portanto, os membros do grupo 'Administradores de DC AAD' podem administrar domínios gerenciados remotamente usando as ferramentas administrativas do AD de um computador cliente/servidor do Windows que está associado ao domínio gerenciado. Ferramentas administrativas do AD podem ser instaladas como parte do recurso opcional de ferramentas de administração de servidor remoto (RSAT) no Windows Server e computadores cliente associados ao domínio gerenciado.

A primeira etapa é configurar uma máquina virtual do Windows Server que está associada ao domínio gerenciado. Para obter instruções, consulte o artigo intitulado [ingressar em uma máquina virtual Windows Server um domínio de serviços de domínio do Azure AD gerenciados](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Administrar o domínio gerenciado remotamente de um computador cliente (por exemplo, o Windows 10)
As instruções deste artigo usam uma máquina de virtual do Windows Server para administrar o AAD-DS gerenciadas domínio. No entanto, você também pode optar por usar um computador de virtual do Windows cliente (por exemplo, o Windows 10) para fazê-lo.

Você pode [instalar ferramentas de administração de servidor remoto (RSAT)](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) em um computador de virtual do Windows client seguindo as instruções no TechNet.


## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Tarefa 2: ferramentas de administração de instalação do Active Directory na máquina virtual
Execute as seguintes etapas para instalar as ferramentas de administração do Active Directory na máquina virtual domínio associado. Consulte o Technet para obter mais [informações sobre como instalar e usar as ferramentas de administração de servidor remoto](https://technet.microsoft.com/library/hh831501.aspx).

1. Navegue até o nó de **máquinas virtuais** no portal de clássico do Azure. Selecione a máquina virtual que você criou na tarefa 1 e clique em **Conectar** na barra de comandos na parte inferior da janela.

    ![Conectar-se a máquina virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. O portal de clássico solicitará que você abrir ou salvar um arquivo com uma extensão '. rdp', que é usada para conectar-se à máquina virtual. Clique para abrir o arquivo quando o download for concluído.

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

9. Na página **recursos** , clique para expandir o nó de **Ferramentas de administração de servidor remoto** e, em seguida, clique para expandir o nó de **Ferramentas de administração de função** . Selecione o recurso de **AD DS e ferramentas do AD LDS** na lista de ferramentas de administração de função.

    ![Página de recursos](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)

10. Na página **confirmação** , clique em **instalar** para instalar o AD e o recurso de ferramentas do AD LDS na máquina virtual. Quando a instalação do recurso for concluído com êxito, clique em **Fechar** para sair do assistente **para adicionar funções e recursos** .

    ![Página de confirmação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)


## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Tarefa 3: conectar a e explorar o domínio gerenciado
Agora que as ferramentas administrativas do AD são instaladas no domínio ingressou máquina virtual, podemos usar essas ferramentas para explorar e administrar o domínio gerenciado.

> [AZURE.NOTE] Você precisa ser um membro do grupo 'Administradores de DC AAD', para administrar o domínio gerenciado.

1. Na tela Iniciar, clique em **Ferramentas administrativas**. Você deve ver as ferramentas administrativas do AD instaladas na máquina virtual.

    ![Ferramentas administrativas instaladas no servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Clique em **Centro Administrativo do Active Directory**.

    ![Centro Administrativo do Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Para explorar o domínio, clique no nome de domínio no painel à esquerda (por exemplo, ' contoso100.com'). Observe que dois contêineres chamados 'AADDC computadores' e 'AADDC usuários', respectivamente.

    ![ADAC - domínio de modo de exibição](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)

4. Clique no contêiner chamado **AADDC usuários** para ver todos os usuários e grupos pertencentes ao domínio gerenciado. Você deverá ver contas de usuário e grupos a partir de seu Azure AD locatário Mostrar backup neste contêiner. Aviso neste exemplo, uma conta de usuário para o usuário chamado 'beto' e um grupo chamado 'AAD DC administradores' estão disponíveis neste contêiner.

    ![ADAC - usuários do domínio](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)

5. Clique no contêiner chamado **AADDC computadores** para ver os computadores que tenham ingressados nesse domínio gerenciado. Você deve ver uma entrada para a máquina virtual atual, que está associada ao domínio. Contas de computador para todos os computadores que fazem parte de domínio gerenciado serviços de domínio do Azure AD são armazenadas neste contêiner 'AADDC computadores'.

    ![ADAC - computadores integrado ao domínio](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Conteúdo relacionado

- [Serviços de domínio Azure AD - guia de Introdução](./active-directory-ds-getting-started.md)

- [Ingressar em uma máquina de virtual do Windows Server em um domínio gerenciado de serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Implantar ferramentas de administração de servidor remoto](https://technet.microsoft.com/library/hh831501.aspx)
