<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com RightAnswers | Microsoft Azure" 
    description="Saiba como usar RightAnswers com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-rightanswers"></a>Tutorial: Integração com o Active Directory do Azure com RightAnswers
  
O objetivo deste tutorial é mostrar a integração do Azure e RightAnswers. O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um RightAnswers logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a RightAnswers será capazes de logon único para o aplicativo usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para RightAnswers
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-rightanswers-tutorial/IC802925.png "Cenário")
##<a name="enabling-the-application-integration-for-rightanswers"></a>Habilitar a integração de aplicativo para RightAnswers
  
O objetivo desta seção é como habilitar a integração de aplicativo para RightAnswers da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-rightanswers-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para RightAnswers, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-rightanswers-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-rightanswers-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-rightanswers-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-rightanswers-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **RightAnswers**.

    ![Galeria de aplicativo] (./media/active-directory-saas-rightanswers-tutorial/IC802926.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **RightAnswers**e clique em **concluído** para adicionar o aplicativo.
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar RightAnswers com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **RightAnswers** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-rightanswers-tutorial/IC802927.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar RightAnswers** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-rightanswers-tutorial/IC802928.png "Configurar o logon único")

3.  Na página **Definir configurações de aplicativo** , na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para logon para seu aplicativo RightAnswers (por exemplo: *https://fortify.rightanswers.com/portal/ss/*) e, em seguida, clique em **Avançar**.

    ![Definir configurações de aplicativo] (./media/active-directory-saas-rightanswers-tutorial/IC802929.png "Definir configurações de aplicativo")

4.  Na página **Configurar logon único em RightAnswers** , para baixar os metadados, clique em **baixar metadados**e salve o arquivo de metadados localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-rightanswers-tutorial/IC802930.png "Configurar o logon único")

5.  Envie o arquivo baixado metadados para sua equipe de suporte de RightAnswers.

    >[AZURE.NOTE] A equipe de suporte RightAnswers tem que fazer a configuração de SSO real.
Você receberá uma notificação quando o SSO foi ativado para sua assinatura.

6.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-rightanswers-tutorial/IC802931.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login RightAnswers, ele devem ser provisionados em RightAnswers.  
No caso de RightAnswers, provisionamento é uma tarefa automatizada.  
Não há nenhum item de ação para você.
  
Os usuários são criados automaticamente se for necessário durante a primeira única logon tentativa.

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros RightAnswers usuário conta ou APIs fornecidas pela RightAnswers para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-rightanswers-perform-the-following-steps"></a>Para atribuir usuários a RightAnswers, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **RightAnswers **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-rightanswers-tutorial/IC802932.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-rightanswers-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).