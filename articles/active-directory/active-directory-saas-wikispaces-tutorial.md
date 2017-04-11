<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Wikispaces | Microsoft Azure" 
    description="Saiba como usar Wikispaces com o Azure Active Directory para habilitar o logon único, automatizado de provisionamento e muito mais!." 
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

#<a name="tutorial-azure-active-directory-integration-with-wikispaces"></a>Tutorial: Integração com o Active Directory do Azure com Wikispaces
  
O objetivo deste tutorial é mostrar a integração do Azure e Wikispaces.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Wikispaces logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Wikispaces poderão único entrar no aplicativo no site da sua empresa Wikispaces (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Wikispaces
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Sceanrio] (./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Sceanrio")

##<a name="enabling-the-application-integration-for-wikispaces"></a>Habilitar a integração de aplicativo para Wikispaces
  
O objetivo desta seção é como habilitar a integração de aplicativo para Wikispaces da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-wikispaces-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Wikispaces, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Wikispaces**.

    ![Galeria de aplicativo] (./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Wikispaces**e clique em **concluído** para adicionar o aplicativo.

    ![Wikispaces] (./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Wikispaces com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Wikispaces** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Wikispaces** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Wikispaces entrada na URL** , digite sua URL usando o seguinte padrão "*http://company.wikispaces.net*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Wikispaces** , clique em **baixar metadados**e salve o arquivo de metadados em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Configurar o logon único")

5.  Envie a metadatafile para a equipe de suporte de Wikispaces.

    >[AZURE.NOTE] A configuração de logon única deve ser executada pela equipe de suporte do Wikispaces. Você receberá uma notificação assim que a configuração foi concluída.

6.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Wikispaces, ele devem ser provisionados em Wikispaces.  
No caso de Wikispaces, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **Wikispaces** como administrador.

2.  Vá para **membros**.

    ![Membros] (./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Membros")

3.  Clique na **Convidar pessoas**.

    ![Convidar pessoas] (./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Convidar pessoas")

4.  Na seção **Convidar pessoas** , execute as seguintes etapas:

    ![Convidar pessoas] (./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Convidar pessoas")

    1.  Digite os **nomes de usuário ou endereço de Email** de uma conta AAD válida que deseja provisionar em caixas de texto relacionadas.
    2.  Clique em **Enviar**.  

        >[AZURE.NOTE] O proprietário da conta do Active Directory do Azure recebe um email incluindo um link para confirmar a conta antes que ele fique ativo.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Wikispaces usuário conta ou APIs fornecidas pela Wikispaces para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-wikispaces-perform-the-following-steps"></a>Para atribuir usuários a Wikispaces, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Wikispaces **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).