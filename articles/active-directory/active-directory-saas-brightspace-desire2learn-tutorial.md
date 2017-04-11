<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Brightspace por Desire2Learn | Microsoft Azure" 
    description="Saiba como usar Brightspace por Desire2Learn com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Tutorial: Integração com o Active Directory do Azure com Brightspace por Desire2Learn

O objetivo deste tutorial é mostrar a integração do Azure e Brightspace por Desire2Learn.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Brightspace por logon único Desire2Learn habilitado para assinatura

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Brightspace por Desire2Learn poderão logon único para o aplicativo em seu Brightspace site de empresa Desire2Learn (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Brightspace por Desire2Learn
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Cenário")
##<a name="enabling-the-application-integration-for-brightspace-by-desire2learn"></a>Habilitar a integração de aplicativo para Brightspace por Desire2Learn

O objetivo desta seção é como habilitar a integração de aplicativo para Brightspace por Desire2Learn da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-brightspace-by-desire2learn-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Brightspace por Desire2Learn, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Brightspace por Desire2Learn**.

    ![Galeria de Apllication] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Galeria de Apllication")

7.  No painel de resultados, selecione **Brightspace por Desire2Learn**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Brightspace por Desire2Learn] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace por Desire2Learn")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar Brightspace por Desire2Learn com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Brightspace por Desire2Learn** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Brightspace por Desire2Learn** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , execute as seguintes etapas:

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Configurar a URL do aplicativo")

    1.  Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para entrar em seu **Brightspace por Desire2Learn** (por exemplo: *https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*).
    2.  Clique em **Avançar**

4.  Na página **Configurar logon único em Brightspace por Desire2Learn** , para baixar os metadados, clique em **baixar metadados**e salve os metadados em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Configurar o logon único")

5.  Envie o arquivo baixado metadados para sua Brightspace pela equipe de suporte de Desire2Learn.

    >[AZURE.NOTE] Seu Brightspace pela equipe de suporte de Desire2Learn tem que fazer a configuração de SSO real.
Você receberá uma notificação quando o SSO foi ativado para sua assinatura.

6.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login Brightspace por Desire2Learn, ele devem ser provisionados em Brightspace por Desire2Learn.  
No caso de Brightspace por Desire2Learn, as contas de usuário precisam ser criado pelo seu Brightspace pela equipe de suporte de Desire2Learn.

>[AZURE.NOTE] Você pode usar qualquer outro Brightspace por Desire2Learn ferramentas de criação de conta de usuário ou APIs fornecidas pela Brightspace por Desire2Learn provisionar o Active Directory do Azure contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-brightspace-by-desire2learn-perform-the-following-steps"></a>Para atribuir usuários a Brightspace por Desire2Learn, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Brightspace por Desire2Learn **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
