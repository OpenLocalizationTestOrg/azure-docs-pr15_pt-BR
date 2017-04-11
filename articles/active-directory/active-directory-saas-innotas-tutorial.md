<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Innotas | Microsoft Azure"
    description="Saiba como usar Innotas com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-innotas"></a>Tutorial: Integração com o Active Directory do Azure com Innotas
  
O objetivo deste tutorial é mostrar a integração do Azure e Innotas.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário Innotas
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Innotas poderão único entrar no aplicativo no site da sua empresa Innotas (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Innotas
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-innotas-tutorial/IC777331.png "Cenário")
##<a name="enabling-the-application-integration-for-innotas"></a>Habilitar a integração de aplicativo para Innotas
  
O objetivo desta seção é como habilitar a integração de aplicativo para Innotas da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-innotas-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Innotas, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-innotas-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-innotas-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-innotas-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-innotas-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Innotas**.

    ![Galeria de aplicativo] (./media/active-directory-saas-innotas-tutorial/IC777332.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Innotas**e clique em **concluído** para adicionar o aplicativo.

    ![Innotas] (./media/active-directory-saas-innotas-tutorial/IC777333.png "Innotas")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Innotas com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Innotas** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-innotas-tutorial/IC777334.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Innotas** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-innotas-tutorial/IC777335.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Innotas URL de entrada** , digite sua URL usando o seguinte padrão "https://*\<-nome do locatário\>. Innotas.com*"e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-innotas-tutorial/IC777336.png "Configurar a URL do aplicativo")

4.  **Configurar logon único em Innotas** página, para baixar os metadados, clique em **baixar metadados**e, em seguida, o arquivo de dados localmente como **c:\\InnotasMetaData.xml**.

    ![Configurar o logon único] (./media/active-directory-saas-innotas-tutorial/IC777337.png "Configurar o logon único")

5.  Encaminhar esse arquivo metadados Innotas equipe de suporte. As necessidades de equipe de suporte configura single sign-on para você.

6.  Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-innotas-tutorial/IC777338.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Não há nenhum item de ação para configurar a Innotas de provisionamento do usuário.  
Quando um usuário atribuído tenta faça logon em Innotas usando o painel de acesso, Innotas verifica se o usuário existe.  
Se houver nenhuma conta de usuário ainda disponível, ele será criado automaticamente pelo Innotas.
##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-innotas-perform-the-following-steps"></a>Para atribuir usuários a Innotas, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Innotas **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-innotas-tutorial/IC777339.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-innotas-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).