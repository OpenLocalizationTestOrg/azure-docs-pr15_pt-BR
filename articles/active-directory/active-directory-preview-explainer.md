<properties
    pageTitle="Explicador de visualização do Azure Active Directory | Microsoft Azure"
    description="Um tópico que explica as diferenças entre Azure Active Directory no portal do clássico e a visualização do Azure Active Directory no portal do Azure."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="curtand"/>


# <a name="preview-of-the-azure-active-directory-management-experience-in-the-azure-portal"></a>Visualização da experiência de gerenciamento do Active Directory do Azure no portal do Azure

A experiência de gerenciamento do Azure Active Directory (AD Azure) está no modo de visualização no portal do Azure. Você pode experimentar ao entrar no [portal do Azure](https://portal.azure.com) como um administrador global do seu diretório. Em seguida, selecione Azure Active Directory na lista de serviços, se ela estiver visível ou selecione **mais serviços** para exibir a lista de todos os serviços. Você não precisa de uma assinatura do Azure para usar o Azure experiência de gerenciamento do AD no portal do Azure.


## <a name="capabilities-of-the-preview-experience"></a>Recursos da experiência de visualização

A experiência de visualização permite que você gerencie muitos recursos de diretório como usuários, grupos e aplicativos, bem como configurações de diretório, no portal do Azure. Estamos aperfeiçoando esta experiência para incluir todos os recursos que existem no Azure experiência de gerenciamento do AD no [Azure portal clássico](https://manage.windowsazure.com). Até lá, há algumas tarefas que você ainda deve concluir no portal do clássico de gerenciamento de diretório.

## <a name="manage-the-same-azure-ad-tenants"></a>Gerenciar os mesmos locatários Azure AD

A experiência de visualização lê e grava mesmo locatário Azure Active Directory como o portal de clássico e o Centro de administração do Office 365. As alterações feitas em qualquer um desses portais são refletidas em todos os outros.

## <a name="use-the-same-authorization-logic"></a>Use a mesma lógica de autorização

A experiência de visualização usa a mesma lógica de autorização como os clientes existentes do Active Directory. Os usuários estão autorizados a fazer alterações em recursos de diretório com base em suas funções de diretório, como administrador global, o administrador do usuário, o administrador de senha. Ter uma função em recursos Azure ou uma assinatura do Azure não autorizar um usuário para gerenciar recursos de diretório. Para obter mais informações, funções de gerenciamento do Azure AD, consulte [Atribuindo funções de administrador no Active Directory do Azure](active-directory-assign-admin-roles.md). 

A experiência de visualização é otimizada para os administradores globais. Se você usar a experiência de visualização enquanto conectado como um usuário que não seja um administrador global, você pode ter uma experiência degradada. Por exemplo, você poderá selecionar um botão que permite que você inicie uma tarefa que você não conseguir concluir no diretório. Estamos aperfeiçoando esta experiência em breve.
 
## <a name="tell-us-what-you-think"></a>Conte-no que você pensa

Você pode fornecer comentários sobre a experiência de visualização da seção de portal de administração do [Fórum de comentários do Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc).
