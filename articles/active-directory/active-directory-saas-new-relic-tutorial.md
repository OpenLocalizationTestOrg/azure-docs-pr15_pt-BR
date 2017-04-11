<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Relíquia novo | Microsoft Azure" 
    description="Saiba como usar o novo Relíquia com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Tutorial: Integração com o Active Directory do Azure com Relíquia novo
  
O objetivo deste tutorial é mostrar como configurar o logon único entre o Active Directory do Azure e Relíquia novo.
  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Relíquia novo logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Active Directory do Azure que você atribuiu a nova Relíquia poderão logon único usando o painel de acesso AAD.

1.  Habilitar a integração de aplicativos para o novo Relíquia
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-new-relic-tutorial/IC797030.png "Cenário")
##<a name="enabling-the-application-integration-for-new-relic"></a>Habilitar a integração de aplicativos para o novo Relíquia
  
O objetivo desta seção é como habilitar a integração de aplicativos para o novo Relíquia da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-new-relic-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o novo Relíquia, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-new-relic-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-new-relic-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-new-relic-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-new-relic-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Relíquia novo**.

    ![Galeria de aplicativo] (./media/active-directory-saas-new-relic-tutorial/IC797031.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Relíquia novo**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Relíquia novo] (./media/active-directory-saas-new-relic-tutorial/IC797032.png "Relíquia novo")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
Esta seção descreve como habilitar usuários autenticar Relíquia novo com sua conta do Azure Active Directory, usando a federação com base no protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo **Relíquia novo** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-new-relic-tutorial/IC769534.png "Configurar o logon único")

2.  Na página **como você gostaria que usuários para entrar novo Relíquia** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-new-relic-tutorial/IC797033.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Nova entrada de Relíquia na URL** , digite a URL usada pelos usuários para entrar seu aplicativo Relíquia novo e clique em **Avançar**. 

    A URL do aplicativo é seu locatário Relíquia novo (por exemplo: *https://rpm.newrelic.com*):

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-new-relic-tutorial/IC797034.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no novo Relíquia** , para baixar o certificado, clique em **baixar o certificado**e salvar o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-new-relic-tutorial/IC797035.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, entre seu site de empresa **Relíquia novo** como administrador.

6.  No menu na parte superior, clique em **Configurações de conta**.

    ![Configurações de conta] (./media/active-directory-saas-new-relic-tutorial/IC797036.png "Configurações de conta")

7.  Clique na guia **segurança e autenticação** e clique na guia de **logon único** .

    ![Logon único] (./media/active-directory-saas-new-relic-tutorial/IC797037.png "Logon único")

8.  Na página de diálogo SAML, execute as seguintes etapas:

    ![SAML] (./media/active-directory-saas-new-relic-tutorial/IC797038.png "SAML")

    1.  Clique em **Escolher arquivo** para carregar seu certificado baixado do Active Directory do Azure.
    2.  No portal do clássico Azure, na página **Configurar logon único no novo Relíquia** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de logon remoto** .
    3.  No portal do clássico Azure, na página **Configurar logon único no novo Relíquia** , copie o valor de **URL Logout remota** e, em seguida, cole a caixa de texto de **Logout inicial URL** .
    4.  Clique em **salvar minhas alterações**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-new-relic-tutorial/IC797039.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que os usuários do Active Directory do Azure a fazer logon no novo Relíquia, ele devem ser provisionados em Relíquia novo.  
No caso de novo Relíquia, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-account-to-new-relic-perform-the-following-steps"></a>Para configurar uma conta de usuário ao novo Relíquia, execute as seguintes etapas:

1.  Faça logon site da sua empresa **Relíquia novo** como administrador.

2.  No menu na parte superior, clique em **Configurações de conta**.

    ![Configurações de conta] (./media/active-directory-saas-new-relic-tutorial/IC797040.png "Configurações de conta")

3.  No painel de **conta** no lado esquerdo, clique em **Resumo**e clique em **Adicionar usuário**.

    ![Configurações de conta] (./media/active-directory-saas-new-relic-tutorial/IC797041.png "Configurações de conta")

4.  Na caixa de diálogo **usuários ativos** , execute as seguintes etapas:

    ![Usuários ativos] (./media/active-directory-saas-new-relic-tutorial/IC797042.png "Usuários ativos")

    1.  Na caixa de texto **Email** , digite o endereço de email de um usuário do Active Directory do Azure válido que deseja provisionar.
    2.  Como **função** selecione **usuário**.
    3.  Clique em **Adicionar usuário**.

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros Relíquia novo usuário conta ou APIs fornecidas pela Relíquia novas contas de usuário do provisionar AAD.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-new-relic-perform-the-following-steps"></a>Para atribuir usuários a nova Relíquia, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo **Relíquia novo** , clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-new-relic-tutorial/IC797043.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-new-relic-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).




