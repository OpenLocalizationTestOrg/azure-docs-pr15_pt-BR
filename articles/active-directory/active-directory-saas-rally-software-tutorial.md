<properties 
    pageTitle="Tutorial: Active Directory do Azure integração com o Software de uma manifestação | Microsoft Azure" 
    description="Saiba como usar o Software realmente com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Tutorial: Active Directory do Azure integração com o Software de uma manifestação
  
O objetivo deste tutorial é mostrar a integração do Azure e realmente Software.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário realmente Software
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo de Software realmente
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-rally-software-tutorial/IC769525.png "Cenário")
##<a name="enabling-the-application-integration-for-rally-software"></a>Habilitar a integração de aplicativo de Software realmente
  
O objetivo desta seção é como habilitar a integração de aplicativo de Software realmente da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-rally-software-perform-the-following-steps"></a>Para habilitar a integração de aplicativo de Software realmente, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-rally-software-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-rally-software-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-rally-software-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-rally-software-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **uma manifestação**.

    ![Galeria de aplicativo] (./media/active-directory-saas-rally-software-tutorial/IC769526.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Realmente Software**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Realmente Software] (./media/active-directory-saas-rally-software-tutorial/IC769527.png "Realmente Software")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar ao Software realmente com sua conta no Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, você é necessárias para carregar um certificado no Software realmente.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo de **Software realmente **, clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configurar o logon único")

2.  Na página **como deseja usuários para entrar uma manifestação** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-rally-software-tutorial/IC769528.png "Microsoft Azure AD Single Sign-On")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **URL de locatário de Software realmente** , digite sua URL usando o seguinte padrão "*https://\<nome do locatário\>. rally.com*" e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em uma manifestação** , clique em Download metadados e, em seguida, salve-o no seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configurar o logon único")

5.  Faça logon em seu locatário **Realmente Software** .

6.  Na barra de ferramentas na parte superior, clique em **configuração**e, em seguida, selecione a **assinatura**.

    ![Assinatura] (./media/active-directory-saas-rally-software-tutorial/IC769531.png "Assinatura")

7.  Clique no botão de **ação** na barra de ferramentas na parte superior do lado direito e selecione **Editar assinatura**.

8.  Na página de diálogo de **assinatura** , execute as seguintes etapas e clique em **Salvar e fechar**:

    ![Autenticação] (./media/active-directory-saas-rally-software-tutorial/IC769542.png "Autenticação")

    1.  Selecione **autenticação uma manifestação ou SSO** autenticação na lista suspensa
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único realmente software** , copie o valor de **ID do provedor de identidade** e cole-o na caixa de texto **URL do provedor de identidade**
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único realmente software** , copie o valor de **URL Logout remota** .

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para usuários AAD conseguir entrar, ele devem ser provisionados para o aplicativo de Software realmente usando seus nomes de usuário do Active Directory do Azure.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário realmente Software.

2.  Vá para **configuração \> usuários**e clique em **+ Add New**.

    ![Usuários] (./media/active-directory-saas-rally-software-tutorial/IC781039.png "Usuários")

3.  Digite o nome na caixa de texto novo usuário e clique em **Adicionar com detalhes**.

4.  Na seção **Criar usuário** , execute as seguintes etapas:

    ![Criar usuário] (./media/active-directory-saas-rally-software-tutorial/IC781040.png "Criar usuário")

    1.  Na caixa de texto **Nome de usuário** , digite o nome do usuário do Azure AD que deseja provisionar.
    2.  Na caixa de texto **Endereço de Email** , digite o endereço de email do usuário do Azure AD que deseja provisionar.
    3.  Clique em **Salvar e fechar**.

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros Software realmente usuário conta ou APIs fornecidos pelo Software realmente para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-rally-software-perform-the-following-steps"></a>Para atribuir usuários a realmente Software, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo de **Software realmente** , clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-rally-software-tutorial/IC769548.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-rally-software-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).




