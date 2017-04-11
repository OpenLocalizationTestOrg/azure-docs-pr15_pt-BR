<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Suite adaptativa | Microsoft Azure"
    description="Saiba como usar o pacote adaptativa com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>Tutorial: Integração com o Active Directory do Azure com Suite adaptativa

O objetivo deste tutorial é mostrar a integração do Azure e Suite adaptativa.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário Suite adaptativa

Depois de concluir este tutorial, os usuários do Azure AD que atribuiu no pacote adaptativa poderão único entrar no aplicativo no site da sua empresa Suite adaptativa (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para pacote adaptativa
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Cenário")
##<a name="enabling-the-application-integration-for-adaptive-suite"></a>Habilitar a integração de aplicativo para pacote adaptativa

O objetivo desta seção é como habilitar a integração de aplicativo para pacote adaptativa da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-adaptive-suite-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para adaptativa pacote, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Suite adaptativa**.

    ![Galeria de aplicativo] (./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Pacote adaptativa**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Pacote adaptativa] (./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Pacote adaptativa")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar no pacote adaptativa com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar o logon único para pacote adaptativa requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo do **Pacote adaptativa** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Configurar o logon único")

2.  Na página **como você gostaria que usuários para entrar pacote adaptativa** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Configurar o logon único")

3.  Na página **Definir configurações de aplicativo** , na caixa de texto **URL de resposta** , digite sua URL usando o seguinte padrão "*samlsso/https://login.adaptiveinsights.com:443/RlJFRVRSSUFMMTI3MTE =*" e, em seguida, clique em **Avançar**.

    >[AZURE.NOTE] Você pode obter esse valor da página de **Configurações de SSO SAML** da suíte adaptativa.

    ![Definir configurações de aplicativo] (./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Definir configurações de aplicativo")

4.  Na página **Configurar logon único no pacote adaptativa** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Suite adaptativa como administrador.

6.  Vá para **administrador**.

    ![Administração] (./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Administração")

7.  Na seção **usuários e funções** , clique em **Gerenciar configurações de SSO SAML**.

    ![Gerenciar configurações de SAML SSO] (./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Gerenciar configurações de SAML SSO")

8.  Na página **Configurações de SSO SAML** , execute as seguintes etapas:

    ![Configurações de SSO SAML] (./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "Configurações de SSO SAML")

    1.  Na caixa de texto **nome do provedor de identidade** , digite um nome para a sua configuração.
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único no pacote adaptativa** , copie o valor de **ID de entidade** e, em seguida, cole a caixa de texto de **provedor de identidade ID de entidade** .
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único no pacote adaptativa** , copie o valor de **SAML SSO URL** e, em seguida, cole a caixa de texto de **provedor de identidade URL de SSO** .
    4.  No portal do clássico Azure, na página de diálogo **Configurar logon único no pacote adaptativa** , copie o valor de **SAML SSO URL** e cole-o na caixa de texto **URL personalizada de logout** .
    5.  Para carregar seu certificado baixado, clique em **Escolher arquivo**.
    6.  Como **id de usuário SAML**, selecione **adaptativa obtenção de informações de nome de usuário**.
    7.  Como **local de id de usuário SAML**, selecione a **id de usuário no NameID de assunto**.
    8.  Como **Formatar SAML NameID**, selecione o **endereço de Email**.
    9.  Como **Habilitar SAML**, selecione **Permitir SAML SSO e direto ideias adaptativa login**.
    10. Clique em **Salvar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD efetuar login no pacote adaptativa, ele devem ser provisionados no Suite adaptativa.  
No caso de pacote do adaptativa provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **Suite adaptativa** como administrador.

2.  Vá para **administrador**.

    ![Administração] (./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Administração")

3.  Na seção **usuários e funções** , clique em **Adicionar usuário**.

    ![Adicionar usuário] (./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Adicionar usuário")

4.  Na seção **Novo usuário** , execute as seguintes etapas:

    ![Enviar] (./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Enviar")

    1.  Digite o **nome**, o **Login**, o **Email**, a **senha** de um usuário do Active Directory do Azure válida que deseja provisionar em caixas de texto relacionadas.
    2.  Selecione uma **função**.
    3.  Clique em **Enviar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação outros Suite adaptativa usuário conta ou APIs fornecidas pela Suite adaptativa para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-adaptive-suite-perform-the-following-steps"></a>Para atribuir usuários a adaptativa pacote, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo do **Pacote adaptativa **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
