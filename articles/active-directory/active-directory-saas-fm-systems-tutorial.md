<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com FM: sistemas | Microsoft Azure" 
    description="Saiba como usar FM: sistemas com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-fm-systems"></a>Tutorial: Integração com o Active Directory do Azure com FM: sistemas
  
O objetivo deste tutorial é mostrar a integração do Azure e FM:Systems.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um FM:Systems logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a FM:Systems poderão único entrar no aplicativo no site da sua empresa FM:Systems (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para FM:Systems
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Cenário")
##<a name="enabling-the-application-integration-for-fmsystems"></a>Habilitar a integração de aplicativo para FM:Systems
  
O objetivo desta seção é como habilitar a integração de aplicativo para FM:Systems da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-fmsystems-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para FM:Systems, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **FM:Systems**.

    ![Galeria de aplicativo] (./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **FM:Systems**e clique em **concluído** para adicionar o aplicativo.

    ![FM: sistemas] (./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM: sistemas")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar FM:Systems com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **FM:Systems** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar FM:Systems** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , execute as seguintes etapas:

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-fm-systems-tutorial/IC795902.png "Configurar a URL do aplicativo")

    1.  Na caixa de texto **FM:Systems entrada na URL** , digite sua FM:Systems **URL de resposta** (por exemplo: *https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*).  

        >[AZURE.WARNING] Você pode obter esse valor do seu FM: equipe de suporte de sistemas.

    2.  Clique em **Avançar**

4.  Na página **Configurar logon único em FM:Systems** , para baixar os metadados, clique em **baixar metadados**e salve os metadados em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Configurar o logon único")

5.  Enviar o arquivo baixado metadados ao seu FM: equipe de suporte de sistemas.

    >[AZURE.NOTE] Seu FM: Equipe de suporte de sistemas tem que fazer a configuração de SSO real.
Você receberá uma notificação quando o SSO foi ativado para sua assinatura.

6.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login FM:Systems, ele devem ser provisionados em FM:Systems.  
No caso de FM:Systems, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Na janela do navegador da web, faça logon em seu site de empresa FM:Systems como administrador.

2.  Vá para **Administração do sistema \> gerenciar segurança \> usuários \> lista usuário**.

    ![Administração do sistema] (./media/active-directory-saas-fm-systems-tutorial/IC795905.png "Administração do sistema")

3.  Clique em **Criar novo usuário**.

    ![Criar novo usuário] (./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Criar novo usuário")

4.  Na seção **Criar usuário** , execute as seguintes etapas:

    ![Criar usuário] (./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Criar usuário")

    1.  Digite o nome de usuário, senha e sua confirmação, o endereço de email e a ID do funcionário de uma conta do Active Directory do Azure válida que deseja provisionar em caixas de texto relacionadas.
    2.  Clique em **Avançar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros FM:Systems usuário conta ou APIs fornecidas pela FM:Systems para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-fmsystems-perform-the-following-steps"></a>Para atribuir usuários a FM:Systems, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **FM:Systems **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).