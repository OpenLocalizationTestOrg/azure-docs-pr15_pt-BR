<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Spring CM | Microsoft Azure" 
    description="Saiba como usar Spring CM com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-spring-cm"></a>Tutorial: Integração com o Active Directory do Azure com Spring CM
  
O objetivo deste tutorial é mostrar como configurar o logon único entre o Active Directory do Azure e SpringCM.
  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um SpringCM logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Active Directory do Azure que você atribuiu a SpringCM poderão logon único usando o painel de acesso AAD.

1.  Habilitar a integração de aplicativo para SpringCM
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-spring-cm-tutorial/IC797044.png "Cenário")

##<a name="enabling-the-application-integration-for-springcm"></a>Habilitar a integração de aplicativo para SpringCM
  
O objetivo desta seção é como habilitar a integração de aplicativo para SpringCM da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-springcm-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para SpringCM, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-spring-cm-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-spring-cm-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-spring-cm-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **SpringCM**.

    ![Galeria de aplicativo] (./media/active-directory-saas-spring-cm-tutorial/IC797045.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **SpringCM**e clique em **concluído** para adicionar o aplicativo.

    ![SpringCM] (./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
Esta seção descreve como habilitar usuários autenticar SpringCM com sua conta do Azure Active Directory, usando a federação com base no protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **SpringCM** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-spring-cm-tutorial/IC797047.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar SpringCM** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-spring-cm-tutorial/IC797048.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **SpringCM entrada na URL** , digite a URL usada pelos usuários para entrar seu aplicativo de SpringCM e clique em **Avançar**. 

    A URL do aplicativo é seu locatário SpringCM (por exemplo: *https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*):

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-spring-cm-tutorial/IC797049.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em SpringCM** , para baixar o certificado, clique em **baixar o certificado**e salvar o arquivo de certificado localmente em seu computador.

    ![Configurar sessão única] (./media/active-directory-saas-spring-cm-tutorial/IC797050.png "Configurar sessão única")

5.  Em uma janela de navegador da web diferente, entre site da sua empresa **SpringCM** como administrador.

6.  No menu na parte superior, clique em **Ir para**, clique em **Preferências**e, em seguida, na seção **Preferências da conta** , clique em **SAML SSO**.

    ![SAML SSO] (./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SAML SSO")

7.  Na seção Configuração do provedor de identidade, execute as seguintes etapas:

    ![Configuração de provedor de identidade] (./media/active-directory-saas-spring-cm-tutorial/IC797052.png "Configuração de provedor de identidade")

    1.  Para carregar seu certificado do Active Directory do Azure baixado, clique em **Selecionar certificado de emissor** ou **Alterar emissor**.
    2.  No portal do clássico Azure, na página **Configurar logon único em SpringCM** , copie o valor de **URL do emissor** e, em seguida, cole a caixa de texto do **emissor** .
    3.  No portal clássico do Azure, na página **Configurar logon único em SpringCM** , copie o valor de **URL do serviço de logon de um** e, em seguida, cole a caixa de texto de **Ponto de extremidade do provedor de serviço (SP) iniciada** .
    4.  Como **SAML habilitado**, selecione **Ativar**.
    5.  Clique em **Salvar**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar sessão única] (./media/active-directory-saas-spring-cm-tutorial/IC797053.png "Configurar sessão única")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que os usuários do Active Directory do Azure fazer login SpringCM, eles devem ser provisionados em SpringCM.  
No caso de SpringCM, provisionamento é uma tarefa manual.

>[AZURE.NOTE] Para obter mais detalhes, consulte [criar e editar um usuário SpringCM](http://knowledge.springcm.com/create-and-edit-a-springcm-user)

###<a name="to-provision-a-user-account-to-springcm-perform-the-following-steps"></a>Para configurar uma conta de usuário para SpringCM, execute as seguintes etapas:

1.  Faça logon site da sua empresa **SpringCM** como administrador.

2.  Clique em **Ir para**e clique em **Catálogo de endereços**.

    ![Criar usuário] (./media/active-directory-saas-spring-cm-tutorial/IC797054.png "Criar usuário")

3.  Clique em **Criar usuário**.

4.  Selecione uma **função de usuário**.

5.  Selecione **Enviar Email de ativação**.

6.  Digite o nome, sobrenome e endereço de email de uma conta de usuário do Active Directory do Azure válida que deseja provisionar em caixas de texto relacionadas.

7.  Adicione o usuário a um **grupo de segurança**.

8.  Clique em **Salvar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros SpringCM usuário conta ou APIs fornecidas pela SpringCM para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-springcm-perform-the-following-steps"></a>Para atribuir usuários a SpringCM, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **SpringCM** , clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-spring-cm-tutorial/IC797055.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-spring-cm-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).




