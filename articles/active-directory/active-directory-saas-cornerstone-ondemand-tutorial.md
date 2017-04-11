<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com pilares OnDemand | Microsoft Azure" 
    description="Saiba como usar pilares OnDemand com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>Tutorial: Integração com o Active Directory do Azure com pilares OnDemand

O objetivo deste tutorial é mostrar a integração do Azure e pilares OnDemand.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário pilares OnDemand

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a pilares OnDemand poderão único entrar no aplicativo no site da sua empresa pilares OnDemand (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para pilares OnDemand
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Cenário")
##<a name="enabling-the-application-integration-for-cornerstone-ondemand"></a>Habilitar a integração de aplicativo para pilares OnDemand

O objetivo desta seção é como habilitar a integração de aplicativo para OnDemand pilares da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-cornerstone-ondemand-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para pilares OnDemand, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **pilares ondemand**.

    ![Galeria de aplicativo] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Pilares OnDemand**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Pilares OnDemand] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Pilares OnDemand")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar pilares OnDemand com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo **Pilares OnDemand** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Habilitar o logon único] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Habilitar o logon único")

2.  Na página **como você gostaria que os usuários para assinar pilares OnDemand** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Microsoft Azure AD Single Sign-On")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Pilares OnDemand URL de entrada** , digite sua URL usando o seguinte padrão "*http://company.csod.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em pilares OnDemand** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configurar o logon único")

5.  Envie a que equipe de suporte de itens a seguir para o OnDemand pilares:

    1.  O certificado baixado
    2.  O valor de **URL de logon remoto**
    3.  O valor de **URL Logout remota** .

    >[AZURE.NOTE] Logon único precisa ser configurada pela equipe de suporte do pilares OnDemand.
Você receberá uma notificação com a equipe de suporte do quando a configuração foi concluída.

6.  Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login pilares OnDemand, ele devem ser provisionados em pilares OnDemand.  
No caso de pilares OnDemand, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Enviar as informações (por exemplo: nome, Emial) sobre o usuário do Azure AD que deseja provisionar para o OnDemand pilares equipe de suporte.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros pilares OnDemand usuário conta ou APIs fornecidas pela pilares OnDemand para provisionar contas de usuário AAD.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-cornerstone-ondemand-perform-the-following-steps"></a>Para atribuir usuários a pilares OnDemand, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Pilares OnDemand **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Atribuir aos usuários] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Atribuir aos usuários")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
