<properties
    pageTitle="Serviços de domínio Active Directory do Azure: Guia de administração | Microsoft Azure"
    description="Criar uma unidade organizacional (OU) em domínios de serviços de domínio do Azure AD gerenciados"
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
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Criar uma unidade organizacional (OU) em um domínio gerenciado de serviços de domínio do Azure AD
Azure domínios gerenciados de serviços de domínio do AD incluem dois contêineres internos chamados 'AADDC computadores' e 'AADDC usuários', respectivamente. O contêiner 'AADDC computadores' tem objetos de computador para todos os computadores que fazem parte de domínio gerenciado. O contêiner 'AADDC usuários' inclui usuários e grupos no locatário Azure AD. Ocasionalmente, pode ser necessário criar contas de serviço no domínio gerenciado para implantar cargas de trabalho. Para essa finalidade, você pode criar um personalizado OU (unidade organizacional) no domínio gerenciado e criar contas de serviço dessa ou. Este artigo mostra como criar uma unidade Organizacional no seu domínio gerenciado.


## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Instalar ferramentas de administração do AD em um computador de virtual domínio para administração remota
Azure domínios gerenciados de serviços de domínio do AD podem ser gerenciados remotamente usando familiares ferramentas administrativas do Active Directory como o Active Directory administrativo central (ADAC) ou o PowerShell do AD. Administradores de inquilinos não tem privilégios para se conectar a controladores de domínio no domínio gerenciado por meio de área de trabalho remota. Para administrar o domínio gerenciado, instale o recurso de ferramentas de administração do AD em uma máquina virtual associada ao domínio gerenciado. Consulte o artigo intitulado [administrar um domínio de serviços de domínio do Azure AD gerenciados](active-directory-ds-admin-guide-administer-domain.md) para obter instruções.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Criar uma unidade organizacional no domínio gerenciado
Agora que as ferramentas administrativas do AD são instaladas no domínio ingressou máquina virtual, podemos usar essas ferramentas para criar uma unidade organizacional no domínio gerenciado. Execute as seguintes etapas:

> [AZURE.NOTE] Somente os membros do grupo 'Administradores de DC AAD' têm os privilégios necessários para criar uma OU personalizada. Certifique-se de que você execute as etapas a seguir como um usuário que pertence a este grupo.

1. Na tela Iniciar, clique em **Ferramentas administrativas**. Você deve ver as ferramentas administrativas do AD instaladas na máquina virtual.

    ![Ferramentas administrativas instaladas no servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Clique em **Centro Administrativo do Active Directory**.

    ![Centro Administrativo do Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Para exibir o domínio, clique no nome de domínio no painel à esquerda (por exemplo, ' contoso100.com').

    ![ADAC - domínio de modo de exibição](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

4. No painel de **tarefas** do lado direito, clique em **novo** sob o nó de nome de domínio. Neste exemplo, podemos clique em **novo** sob o nó 'contoso100(local)' no painel de **tarefas** do lado direito.

    ![ADAC - nova OU](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

5. Você verá a opção para criar uma unidade organizacional. Clique em **Unidade organizacional** para abrir a caixa de diálogo **Criar unidade organizacional** .

6. Na caixa de diálogo **Criar unidade organizacional** , especifique um **nome** para a nova OU. Forneça uma breve descrição para a unidade Organizacional. Você também pode definir o campo **Gerenciado por** para a unidade Organizacional. Para criar a OU personalizada, clique em **Okey**.

    ![ADAC - caixa de diálogo OU criar](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

7. A unidade Organizacional recém-criado agora deve aparecer no AD administrativo central (ADAC).

    ![ADAC - OU criada](./media/active-directory-domain-services-admin-guide/create-ou-done.png)


## <a name="permissionssecurity-for-newly-created-ous"></a>Permissões/segurança para unidades organizacionais recém-criado
Por padrão, o usuário (membro do grupo 'Administradores de DC AAD') que criou a OU personalizada é concedido privilégios administrativos (controle total) ao longo da unidade Organizacional. O usuário pode ir adiante e conceder privilégios para outros usuários ou ao grupo 'Administradores de DC AAD' conforme desejado. Como visto na captura de tela seguinte, o usuário 'bob@domainservicespreview.onmicrosoft.com' quem criou a nova unidade organizacional de 'MyCustomOU' é concedido controle total sobre ele.

 ![ADAC - nova segurança da unidade Organizacional](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)


## <a name="notes-on-administering-custom-ous"></a>Notas sobre como administrar unidades organizacionais personalizados
Agora que você criou uma OU personalizada, você pode ir adiante e criar usuários, grupos, computadores e contas de serviço esta unidade organizacional. Você não pode mover usuários ou grupos de 'AAD DC usuários' OU para unidades organizacionais personalizados.

> [AZURE.WARNING] Contas de usuário, grupos, contas de serviço e objetos de computador que você criar em unidades organizacionais personalizados não estão disponíveis no seu locatário do Azure AD. Em outras palavras, esses objetos não mostram o uso da API do Azure AD Graph ou na interface do usuário do Azure AD. Esses objetos só estão disponíveis no seu domínio de serviços de domínio do Azure AD gerenciado.


## <a name="related-content"></a>Conteúdo relacionado

- [Administrar um domínio gerenciado de serviços de domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Centro Administrativo do Active Directory: Guia de Introdução](https://technet.microsoft.com/library/dd560651.aspx)

- [Guia passo a passo de contas de serviço](https://technet.microsoft.com/library/dd548356.aspx)
