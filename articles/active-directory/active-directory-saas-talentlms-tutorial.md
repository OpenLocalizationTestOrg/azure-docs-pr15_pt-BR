<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com TalentLMS | Microsoft Azure" 
    description="Saiba como usar TalentLMS com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Tutorial: Integração com o Active Directory do Azure com TalentLMS
  
O objetivo deste tutorial é mostrar a integração do Azure e TalentLMS.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário TalentLMS
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a TalentLMS poderão único entrar no aplicativo no site da sua empresa TalentLMS (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para TalentLMS
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-talentlms-tutorial/IC777289.png "Cenário")

##<a name="enabling-the-application-integration-for-talentlms"></a>Habilitar a integração de aplicativo para TalentLMS
  
O objetivo desta seção é como habilitar a integração de aplicativo para TalentLMS da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-talentlms-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para TalentLMS, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-talentlms-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-talentlms-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-talentlms-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-talentlms-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **TalentLMS**.

    ![Galeria de aplicativo] (./media/active-directory-saas-talentlms-tutorial/IC777290.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **TalentLMS**e clique em **concluído** para adicionar o aplicativo.

    ![TalentLMS] (./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar TalentLMS com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos. .  
Configurar single sign-on para TalentLMS requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **TalentLMS** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-talentlms-tutorial/IC777292.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar TalentLMS** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-talentlms-tutorial/IC777293.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **TalentLMS URL de entrada** , digite sua URL usando o seguinte padrão "https://*\<-nome do locatário\>. TalentLMSapp.com*"e, em seguida, clique em **Avançar**.

    ![Entrar em URL] (./media/active-directory-saas-talentlms-tutorial/IC777294.png "Entrar em URL")

4.  Na página **Configurar logon único em TalentLMS** , para baixar o certificado, clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado localmente como **c:\\TalentLMS.cer**.

    ![Configurar o logon único] (./media/active-directory-saas-talentlms-tutorial/IC777295.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa TalentLMS como administrador.

6.  Na seção **Configurações & de conta** , clique na guia **usuários** .

    ![& Configurações de conta] (./media/active-directory-saas-talentlms-tutorial/IC777296.png "& Configurações de conta")

7.  Clique **Logon único (SSO)**,

8.  Na seção Single Sign-On, execute as seguintes etapas:

    ![Logon único] (./media/active-directory-saas-talentlms-tutorial/IC777297.png "Logon único")

    1.  Na lista **tipo de integração de SSO** , selecione **SAML 2.0**.
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único em TalentLMS** , copie o valor de **ID do provedor de identidade** e, em seguida, cole a caixa de texto de **provedor de identidade (IdP)** .
    3.  Copie o valor de **impressão digital** do certificado exportado e, em seguida, cole a caixa de texto de **Impressão digital de certificado** .

        >[AZURE.TIP] Para obter mais detalhes, consulte [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    4.  No portal do clássico Azure, na página de diálogo **Configurar logon único em TalentLMS** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de entrada remota** .
    5.  No portal do clássico Azure, na página de diálogo **Configurar logon único em TalentLMS** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL de saída remota** .
    6.  Na caixa de texto **TargetedID** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**
    7.  Na caixa de texto **nome** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
    8.  Na caixa de texto **Sobrenome** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
    9.  Na caixa de texto **Email** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**
    10. Clique em **Salvar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-talentlms-tutorial/IC777298.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login TalentLMS, ele devem ser provisionados em TalentLMS.  
No caso de TalentLMS, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **TalentLMS** .

2.  Clique em **usuários**e, em seguida, clique em **Adicionar usuário**.

3.  Na página de diálogo **Adicionar usuário** , execute as seguintes etapas:

    ![Adicionar usuário] (./media/active-directory-saas-talentlms-tutorial/IC777299.png "Adicionar usuário")

    1.  Digite os valores de atributo relacionado da conta de usuário do Azure AD nos seguintes caixas de texto: **nome**, **Sobrenome**, **endereço de Email**.
    2.  Clique em **Adicionar usuário**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros TalentLMS usuário conta ou APIs fornecidas pela TalentLMS para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-talentlms-perform-the-following-steps"></a>Para atribuir usuários a TalentLMS, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **TalentLMS **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-talentlms-tutorial/IC777300.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-talentlms-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).