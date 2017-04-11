<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com EmpCenter | Microsoft Azure" 
    description="Saiba como usar EmpCenter com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-empcenter"></a>Tutorial: Integração com o Active Directory do Azure com EmpCenter
  
O objetivo deste tutorial é mostrar a integração do Azure e EmpCenter.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um EmpCenter logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a EmpCenter poderão único entrar no aplicativo no site da sua empresa EmpCenter (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para EmpCenter
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-empcenter-tutorial/IC802916.png "Cenário")
##<a name="enabling-the-application-integration-for-empcenter"></a>Habilitar a integração de aplicativo para EmpCenter
  
O objetivo desta seção é como habilitar a integração de aplicativo para EmpCenter da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-empcenter-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para EmpCenter, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-empcenter-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-empcenter-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-empcenter-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-empcenter-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **EmpCenter**.

    ![Galeria de aplicativo] (./media/active-directory-saas-empcenter-tutorial/IC802917.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **EmpCenter**e clique em **concluído** para adicionar o aplicativo.

    ![EmpCentral] (./media/active-directory-saas-empcenter-tutorial/IC802918.png "EmpCentral")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar EmpCenter com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **EmpCenter** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-empcenter-tutorial/IC802919.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar EmpCenter** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-empcenter-tutorial/IC802920.png "Configurar o logon único")

3.  Na página **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Definir configurações de aplicativo] (./media/active-directory-saas-empcenter-tutorial/IC802921.png "Definir configurações de aplicativo")

    1.  Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para logon para seu aplicativo EmpCenter (por exemplo: *https://partner-authenticati.empcenter.com/workforce/SSO.do*).
    2.  Clique em **Avançar**

4.  Na página **Configurar logon único em EmpCenter** , para baixar os metadados, clique em **baixar metadados**e salve o arquivo de metadados em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-empcenter-tutorial/IC802922.png "Configurar o logon único")

5.  Envie o arquivo baixado metadados para sua equipe de suporte de EmpCenter.

    >[AZURE.NOTE] A equipe de suporte EmpCenter tem que fazer a configuração de SSO real.
Você receberá uma notificação quando o SSO foi ativado para sua assinatura.

6.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-empcenter-tutorial/IC802923.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login EmpCenter, ele devem ser provisionados em EmpCenter.  
No caso de EmpCenter, as contas de usuário precisam ser criados pela equipe de suporte EmpCenter.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros EmpCenter usuário conta ou APIs fornecidas pela EmpCenter provisionar o Active Directory do Azure contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-empcenter-perform-the-following-steps"></a>Para atribuir usuários a EmpCenter, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **EmpCenter **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-empcenter-tutorial/IC802924.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-empcenter-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).