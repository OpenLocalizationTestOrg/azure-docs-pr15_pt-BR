<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com ciclo de vida de SCC | Microsoft Azure" 
    description="Saiba como usar o ciclo de vida de SCC com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Tutorial: Integração com o Active Directory do Azure com ciclo de vida de SCC
  
O objetivo deste tutorial é mostrar a integração do Azure e ciclo de vida de SCC.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um ciclo de vida de SCC logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que atribuiu ao ciclo de vida de SCC poderão único entrar no aplicativo no site da sua empresa ciclo de vida de SCC (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para ciclo de vida de SCC
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "Cenário")
##<a name="enabling-the-application-integration-for-scc-lifecycle"></a>Habilitar a integração de aplicativo para ciclo de vida de SCC
  
O objetivo desta seção é como habilitar a integração de aplicativo para ciclo de vida de SCC da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-scc-lifecycle-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para ciclo de vida de SCC, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite o **Ciclo de vida de SCC**.

    ![Galeria de aplicativo] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Galeria de aplicativo")

7.  No painel de resultados, selecione o **Ciclo de vida de SCC**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Ciclo de vida de SCC] (./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "Ciclo de vida de SCC")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar ao ciclo de vida de SCC com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo do **Ciclo de vida de SCC** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para entrar ciclo de vida de SCC** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para entrar seu aplicativo de ciclo de vida de SCC usando o seguinte padrão "*https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no ciclo de vida de SCC** , clique em **baixar metadados**e, em seguida, salve o arquivo de metadados localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Configurar o logon único")

5.  Encaminhe desse arquivo metadados à equipe de suporte de ciclo de vida de SCC.

    >[AZURE.NOTE]Logon único deve ser habilitado pela equipe de suporte do ciclo de vida de SCC.

6.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD efetuar login no ciclo de vida de SCC, ele devem ser provisionados no ciclo de vida de SCC.
  
Não há nenhum item de ação de configurar provisionamento do usuário ao ciclo de vida de SCC.  
Quando um usuário atribuído tenta efetuar logon no ciclo de vida de SCC, uma conta de ciclo de vida de SCC é criada automaticamente, se necessário.

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros ciclo de vida de SCC usuário conta ou APIs fornecida pelo ciclo de vida de SCC para contas de usuário do provisionar AAD.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-scc-lifecycle-perform-the-following-steps"></a>Para atribuir usuários a ciclo de vida de SCC, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo do **Ciclo de vida de SCC **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).