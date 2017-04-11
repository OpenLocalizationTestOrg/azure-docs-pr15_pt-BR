<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com o construtor de e | Microsoft Azure" 
    description="Saiba como usar o construtor de e com o Active Directory do Azure para habilitar o logon único, automatizado de provisionamento e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-e-builder"></a>Tutorial: Integração com o Active Directory do Azure com e construtor
  
O objetivo deste tutorial é mostrar a integração do Azure e construtor de eletrônico.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário e construtor
  
Depois de concluir este tutorial, os usuários do Azure AD que atribuiu ao construtor e poderão único entrar no aplicativo no site da sua empresa e construtor (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para o construtor de e
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-e-builder-tutorial/IC777378.png "Cenário")
##<a name="enabling-the-application-integration-for-e-builder"></a>Habilitar a integração de aplicativo para o construtor de e
  
O objetivo desta seção é como habilitar a integração de aplicativo para o construtor de e da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-e-builder-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para o construtor de eletrônico, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-e-builder-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-e-builder-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-e-builder-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-e-builder-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **E construtor**.

    ![Galeria de aplicativo] (./media/active-directory-saas-e-builder-tutorial/IC777379.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Construtor de eletrônico**e clique em **concluído** para adicionar o aplicativo.

    ![Construtor de e] (./media/active-directory-saas-e-builder-tutorial/IC777380.png "Construtor de e")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar e-construtor com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo do **Construtor de e** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-e-builder-tutorial/IC777381.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para entrar construtor de e** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-e-builder-tutorial/IC777382.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **e construtor entrar URL** , digite sua URL usando o seguinte padrão "*https://\<nome do locatário\>.e Builder.com*" e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-e-builder-tutorial/IC777383.png "Configurar a URL do aplicativo")

4.  **Configurar logon único no construtor de e** página, para baixar os metadados, clique em **baixar metadados**e, em seguida, o arquivo de dados localmente como **c:\\BuilderMetaData.xml e**.

    ![Configurar o logon único] (./media/active-directory-saas-e-builder-tutorial/IC777384.png "Configurar o logon único")

5.  Encaminhar desse arquivo metadados ao construtor e equipe de suporte. As necessidades de equipe de suporte configura single sign-on para você.

6.  Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-e-builder-tutorial/IC777385.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Não há nenhum item de ação de configurar Builder e de provisionamento do usuário.  
Quando um usuário atribuído tenta efetuar logon no construtor de e usando o painel de acesso, o construtor de e verifica se o usuário existe.  
Se há nenhuma conta de usuário ainda disponível, ele será criado automaticamente pelo Builder e.
##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-e-builder-perform-the-following-steps"></a>Para atribuir usuários a e construtor, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo do **Construtor de e **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-e-builder-tutorial/IC777386.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-e-builder-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
