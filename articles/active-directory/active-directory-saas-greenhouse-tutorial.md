<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com o efeito | Microsoft Azure" 
    description="Saiba como usar o efeito com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Tutorial: Integração com o Active Directory do Azure com efeito
  
O objetivo deste tutorial é mostrar a integração do Azure e efeito.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um efeito a única logon inscrição
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a efeito poderão único entrar no aplicativo no site da sua empresa efeito (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo de efeito
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-greenhouse-tutorial/IC790783.png "Cenário")
##<a name="enabling-the-application-integration-for-greenhouse"></a>Habilitar a integração de aplicativo de efeito
  
O objetivo desta seção é como habilitar a integração de aplicativo para o efeito de estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-greenhouse-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para o efeito, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-greenhouse-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-greenhouse-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-greenhouse-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-greenhouse-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **efeito**.

    ![Galeria de aplicativo] (./media/active-directory-saas-greenhouse-tutorial/IC790784.png "Galeria de aplicativo")

7.  No painel de resultados, selecione o **efeito**e clique em **concluído** para adicionar o aplicativo.

    ![Efeito] (./media/active-directory-saas-greenhouse-tutorial/IC790785.png "Efeito")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar efeito com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo do **efeito** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-greenhouse-tutorial/IC790786.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para entrar efeito** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-greenhouse-tutorial/IC790787.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto de **Entrada na URL** , digite sua URL usando o seguinte padrão "*https://company.greenhouse.io*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-greenhouse-tutorial/IC790788.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em efeito** , clique em **baixar metadados**e, em seguida, salve o arquivo de metadados localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-greenhouse-tutorial/IC790789.png "Configurar o logon único")

5.  Encaminhe desse arquivo metadados à equipe de suporte de efeito.

    >[AZURE.NOTE] Logon único deve ser habilitado pela equipe de suporte do efeito.

6.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-greenhouse-tutorial/IC790790.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD efetuar login no efeito, ele devem ser provisionados em efeito.  
No caso de efeito, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **efeito** como administrador.

2.  No menu na parte superior, clique em **Configurar**e, em seguida, clique em **usuários**.

    ![Usuários] (./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Usuários")

3.  Clique em **novos usuários**.

    ![Novo usuário] (./media/active-directory-saas-greenhouse-tutorial/IC790792.png "Novo usuário")

4.  Na seção **Adicionar novo usuário** , execute as seguintes etapas:

    ![Adicionar novo usuário] (./media/active-directory-saas-greenhouse-tutorial/IC790793.png "Adicionar novo usuário")

    1.  Na caixa de texto **Enter emails de usuário** , digite o endereço de email de uma conta do Active Directory do Azure válida que deseja provisionar.
    2.  Clique em **Salvar**.
        
        >[AZURE.NOTE] Os proprietários de contas do Active Directory do Azure receberão um email incluindo um link para confirmar a conta antes que ele fique ativo.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outro efeito usuário conta ou APIs fornecidas pela efeito para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-greenhouse-perform-the-following-steps"></a>Para atribuir usuários a efeito, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo do **efeito **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-greenhouse-tutorial/IC790794.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).