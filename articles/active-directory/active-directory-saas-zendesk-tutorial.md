<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Zendesk | Microsoft Azure" 
    description="Saiba como usar Zendesk com o Azure Active Directory para habilitar o logon único, automatizado de provisionamento e muito mais!." 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Tutorial: Integração com o Active Directory do Azure com Zendesk
  
O objetivo deste tutorial é mostrar a integração do Azure e Zendesk.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário Zendesk
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Zendesk poderão único entrar no aplicativo no site da sua empresa Zendesk (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Zendesk
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-zendesk-tutorial/IC773083.png "Cenário")

##<a name="enabling-the-application-integration-for-zendesk"></a>Habilitar a integração de aplicativo para Zendesk
  
O objetivo desta seção é como habilitar a integração de aplicativo para Zendesk da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-zendesk-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Zendesk, execute as seguintes etapas:

1.  No Portal de gerenciamento do Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-zendesk-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-zendesk-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-zendesk-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-zendesk-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Zendesk**.

    ![Galeria de aplicativo] (./media/active-directory-saas-zendesk-tutorial/IC773084.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Zendesk**e clique em **concluído** para adicionar o aplicativo.

    ![Zendesk] (./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Zendesk com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para Zendesk requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do Azure AD, na página de integração do aplicativo **Zendesk** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Logon único] (./media/active-directory-saas-zendesk-tutorial/IC773086.png "Logon único")

2.  Na página **como você gostaria que os usuários para assinar Zendesk** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-zendesk-tutorial/IC773087.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , execute as seguintes etapas:

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configurar a URL do aplicativo")
  
    a. Na caixa de texto **Zendesk URL de entrada** , digite sua URL usando o seguinte padrão:`https://<tenant-name>.zendesk.com`

    b. Clique em **Avançar**.



4.  Na página **Configurar logon único em Zendesk** , clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado localmente em seu compiter.

    ![Configurar o logon único] (./media/active-directory-saas-zendesk-tutorial/IC777534.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Zendesk como administrador.

6.  Clique em **administrador**.

7.  No painel de navegação esquerdo, clique em **configurações**e, em seguida, clique em **segurança**.

    ![Segurança] (./media/active-directory-saas-zendesk-tutorial/IC773089.png "Segurança")

8.  Na página de **segurança** , clique na guia **Administração e agentes** .

9.  Selecione **sign-on (SSO) único e SAML**e selecione **SAML**.

10. No portal do Azure AD, na página **Configurar logon único em Zendesk** , copie o valor de **SAML SSO URL** e cole-o na caixa de texto **URL de SSO SAML** .

11. No portal do Azure AD, na página **Configurar logon único em Zendesk** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL Logout remota** .

    ![Logon único] (./media/active-directory-saas-zendesk-tutorial/IC773090.png "Logon único")

12. Copie o valor de **impressão digital** do certificado exportado e, em seguida, cole a caixa de texto de **Impressão digital de certificado** .

    >[AZURE.TIP] Para obter mais detalhes, consulte [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

13. Clique em **Salvar**.

14. No portal do Azure AD, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-zendesk-tutorial/IC773093.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login **Zendesk**, ele devem ser provisionados em **Zendesk**.  
No caso de **Zendesk**, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-account-to-zendesk-perform-the-following-steps"></a>Para configurar uma conta de usuário para Zendesk, execute as seguintes etapas:

1.  Faça logon em seu locatário **Zendesk** .

2.  Selecione a guia **Lista de clientes** .

3.  Selecione a guia de **usuário** e clique em **Adicionar**.

    ![Adicionar usuário] (./media/active-directory-saas-zendesk-tutorial/IC773632.png "Adicionar usuário")

4.  Digite o endereço de email de uma conta existente do Azure AD você deseja provisionar e clique em **Salvar**.

    ![Novo usuário] (./media/active-directory-saas-zendesk-tutorial/IC773633.png "Novo usuário")

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Zendesk usuário conta ou APIs fornecidas pela Zendesk para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-zendesk-perform-the-following-steps"></a>Para atribuir usuários a Zendesk, execute as seguintes etapas:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração do aplicativo **Zendesk **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-zendesk-tutorial/IC773094.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-zendesk-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
