<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com ScreenSteps | Microsoft Azure" 
    description="Saiba como usar ScreenSteps com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Tutorial: Integração com o Active Directory do Azure com ScreenSteps
  
O objetivo deste tutorial é mostrar a integração do Azure e ScreenSteps.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário ScreenSteps
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a ScreenSteps poderão único entrar no aplicativo no site da sua empresa ScreenSteps (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para ScreenSteps
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-screensteps-tutorial/IC778516.png "Cenário")
##<a name="enabling-the-application-integration-for-screensteps"></a>Habilitar a integração de aplicativo para ScreenSteps
  
O objetivo desta seção é como habilitar a integração de aplicativo para ScreenSteps da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-screensteps-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para ScreenSteps, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-screensteps-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-screensteps-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-screensteps-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-screensteps-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **ScreenSteps**.

    ![Galeria de aplicativo] (./media/active-directory-saas-screensteps-tutorial/IC778517.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **ScreenSteps**e clique em **concluído** para adicionar o aplicativo.

    ![ScreenSteps] (./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar ScreenSteps com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **ScreenSteps** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar ScreenSteps** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **ScreenSteps URL de entrada** , digite sua URL usando o seguinte padrão "https://*\<-nome do locatário\>. ScreenSteps.com*"e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em ScreenSteps** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa ScreenSteps como administrador.

6.  Clique em **gerenciamento de conta**.

    ![Gerenciamento de conta] (./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gerenciamento de conta")

7.  Clique em **autenticação remota**.

    ![Autenticação remota] (./media/active-directory-saas-screensteps-tutorial/IC778524.png "Autenticação remota")

8.  Clique em **criar ponto de extremidade de autenticação**.

    ![Autenticação remota] (./media/active-directory-saas-screensteps-tutorial/IC778525.png "Autenticação remota")

9.  Na seção **criar um ponto de extremidade de autenticação** , execute as seguintes etapas:

    ![Criar um ponto de extremidade de autenticação] (./media/active-directory-saas-screensteps-tutorial/IC778526.png "Criar um ponto de extremidade de autenticação")

    1.  Na caixa de texto **título** , digite um título.
    2.  A **modo** , selecione lista **SAML**.
    3.  Clique em **criar**.

10. Na seção de **Ponto de extremidade de autenticação remoto** , execute as seguintes etapas:

    ![Ponto de extremidade de autenticação remota] (./media/active-directory-saas-screensteps-tutorial/IC778527.png "Ponto de extremidade de autenticação remota")

    1.  No portal do clássico Azure, na página **Configurar logon único em ScreenSteps** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login remoto** .
    2.  No portal do clássico Azure, na página **Configurar logon único em ScreenSteps** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL fazer logoff** .
    3.  Clique em **Escolher um arquivo**e carregue o certificado baixado.
    4.  Clique em **Atualizar**.

11. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login **ScreenSteps**, ele devem ser provisionados em **ScreenSteps**.  
No caso de **ScreenSteps**, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-account-to-screensteps-perform-the-following-steps"></a>Para configurar uma conta de usuário para ScreenSteps, execute as seguintes etapas:

1.  Faça logon em seu locatário **ScreenSteps** .

2.  Clique em **gerenciamento de conta**.

    ![Gerenciamento de conta] (./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gerenciamento de conta")

3.  Clique em **usuários**.

    ![Usuários] (./media/active-directory-saas-screensteps-tutorial/IC778544.png "Usuários")

4.  Clique em **criar um usuário**.

    ![Todos os usuários] (./media/active-directory-saas-screensteps-tutorial/IC778545.png "Todos os usuários")

5.  Na lista de **Função de usuário** , selecione uma função para o usuário.

6.  Na seção de função de usuário, digite o**"nome**, **Sobrenome**, **Email**, **Login**, **senha** e **Confirmação de senha"**de uma conta AAD válida que deseja provisionar em caixas de texto relacionadas.

    ![Novo usuário] (./media/active-directory-saas-screensteps-tutorial/IC778546.png "Novo usuário")

7.  Na seção de grupos, selecione **O grupo de autenticação (SAML)**e, em seguida, clique em **Criar usuário**.

    ![Grupos] (./media/active-directory-saas-screensteps-tutorial/IC778547.png "Grupos")

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros ScreenSteps usuário conta ou APIs fornecidas pela ScreenSteps para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-screensteps-perform-the-following-steps"></a>Para atribuir usuários a ScreenSteps, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **ScreenSteps **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-screensteps-tutorial/IC773094.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Atribuir aos usuários] (./media/active-directory-saas-screensteps-tutorial/IC778548.png "Atribuir aos usuários")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).