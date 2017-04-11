<properties
    pageTitle="Serviços de domínio do Azure AD: Criar o grupo de administradores de DC AAD | Microsoft Azure"
    description="Introdução ao Azure Active Directory Domain Services"
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

# <a name="get-started-with-azure-ad-domain-services"></a>Começar a usar os serviços de domínio do Azure AD

Este artigo explica as tarefas de configuração necessárias para ativar os serviços de domínio do Azure AD do seu locatário do Azure AD.

## <a name="task-1-create-the-aad-dc-administrators-group"></a>Tarefa 1: Criar o grupo 'Administradores de DC AAD'
A primeira tarefa é criar um grupo administrativo no seu locatário do Active Directory do Azure. Este grupo administrativo especial é chamado **AAD DC administradores**. Membros deste grupo recebem privilégios administrativos em máquinas que são o domínio para o domínio de serviços de domínio do Azure AD gerenciado. Em máquinas de domínio, esse grupo é adicionado ao grupo 'Administradores'. Além disso, membros deste grupo podem usar a área de trabalho remota para se conectar remotamente ao domínio máquinas.  

> [AZURE.NOTE] Você não tem privilégios de administrador corporativo ou administrador de domínio no domínio gerenciado criado usando os serviços de domínio do Azure AD. Em domínios gerenciados, esses privilégios são reservados pelo serviço em não serão disponibilizados aos usuários dentro do locatário. No entanto, você pode usar o grupo de administrador especial criado nesta tarefa de configuração, para executar algumas operações privilegiadas. Essas operações incluem ingressando em computadores ao domínio, pertencentes ao grupo de administradores no domínio máquinas, configurando etc de política de grupo.

Nesta tarefa de configuração, você cria o grupo administrativo e adicionar um ou mais usuários no seu diretório ao grupo. Execute as seguintes etapas para criar o grupo administrativo para os serviços de domínio do Azure AD:

1. Navegue até o **portal clássico Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com))

2. Selecione o nó do **Active Directory** no painel esquerdo.

3. Selecione o locatário do Azure AD (diretório) para o qual você gostaria de ativar os serviços de domínio do Azure AD. Você só pode criar um domínio para cada diretório do Azure AD.

    ![Selecione Directory do Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Clique na guia **grupos** .

5. Para adicionar um grupo a seu locatário do Azure AD, clique em **Adicionar grupo** do painel de tarefas na parte inferior da página.

    ![Adicionar botão de grupo](./media/active-directory-domain-services-getting-started/add-group-button.png)

6. Crie um grupo chamado **AAD DC administradores**. Definir o **tipo de grupo** de **segurança**.

    > [AZURE.WARNING] Para habilitar o acesso dentro de seus serviços de domínio do Azure AD gerenciado domínio, crie um grupo com este nome exato.

    ![Criar grupo de administrador](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. Adicione uma descrição para este grupo para que outras pessoas entendam o que este grupo é usado para conceder privilégios administrativos nos serviços de domínio do Azure AD.

8. Após o grupo tiver sido criado, clique no nome do grupo para ver as propriedades desse grupo. Para adicionar usuários como membros deste grupo, clique no botão **Adicionar membros** no painel inferior.

    ![Adicionar botão de membros do grupo](./media/active-directory-domain-services-getting-started/add-group-members-button.png)

9. Na caixa de diálogo **Adicionar membros** , selecione os usuários que devem ser membros deste grupo e marque a caixa de seleção quando você terminar.

    ![Adicionar usuários ao grupo de administrador](./media/active-directory-domain-services-getting-started/add-group-members.png)

<br>

## <a name="task-2-create-or-select-an-azure-virtual-network"></a>Tarefa 2: Criar ou selecionar uma rede virtual Azure
A próxima tarefa de configuração é [criar ou selecionar uma rede virtual Azure](active-directory-ds-getting-started-vnet.md).
