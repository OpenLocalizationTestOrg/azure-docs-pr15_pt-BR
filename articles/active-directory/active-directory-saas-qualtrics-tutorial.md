<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Qualtrics | Microsoft Azure" 
    description="Saiba como usar Qualtrics com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-qualtrics"></a>Tutorial: Integração com o Active Directory do Azure com Qualtrics
  
O objetivo deste tutorial é mostrar a integração do Azure e Qualtrics.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Qualtrics logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Qualtrics poderão único entrar no aplicativo no site da sua empresa Qualtrics (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Qualtrics
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-qualtrics-tutorial/IC789542.png "Cenário")
##<a name="enabling-the-application-integration-for-qualtrics"></a>Habilitar a integração de aplicativo para Qualtrics
  
O objetivo desta seção é como habilitar a integração de aplicativo para Qualtrics da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-qualtrics-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Qualtrics, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-qualtrics-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-qualtrics-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-qualtrics-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-qualtrics-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Qualtrics**.

    ![Galeria de aplicativo] (./media/active-directory-saas-qualtrics-tutorial/IC789543.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Qualtrics**e clique em **concluído** para adicionar o aplicativo.

    ![Qualtrics] (./media/active-directory-saas-qualtrics-tutorial/IC789544.png "Qualtrics")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Qualtrics com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Qualtrics** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-qualtrics-tutorial/IC789545.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Qualtrics** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-qualtrics-tutorial/IC789546.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Qualtrics entrada na URL** , digite a URL (por exemplo: "*https://ssotest2ut1.qualtrics.com*") e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-qualtrics-tutorial/IC789547.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Qualtrics** , clique em **baixar metadados**e salve o arquivo de metadados em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-qualtrics-tutorial/IC789548.png "Configurar o logon único")

5.  Envie a metadatafile para a equipe de suporte de Qualtrics.

    >[AZURE.NOTE]A configuração de logon única deve ser executada pela equipe de suporte do Qualtrics. Você receberá uma notificação assim que a configuração foi concluída.

6.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-qualtrics-tutorial/IC789549.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Não há nenhum item de ação para configurar a Qualtrics de provisionamento do usuário.  
Quando um usuário atribuído tenta faça logon em Qualtrics usando o painel de acesso, Qualtrics verifica se o usuário existe.  
Se houver nenhuma conta de usuário ainda disponível, ele será criado automaticamente pelo Qualtrics.
##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-qualtrics-perform-the-following-steps"></a>Para atribuir usuários a Qualtrics, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Qualtrics **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-qualtrics-tutorial/IC789550.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-qualtrics-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).