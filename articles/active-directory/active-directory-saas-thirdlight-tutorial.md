<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Thirdlight | Microsoft Azure" 
    description="Saiba como usar Thirdlight com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Tutorial: Integração com o Active Directory do Azure com Thirdlight
  
O objetivo deste tutorial é mostrar a integração do Azure e Thirdlight.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Thirdlight logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Thirdlight poderão único entrar no aplicativo no site da sua empresa Thirdlight (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Thirdlight
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-thirdlight-tutorial/IC805836.png "Cenário")

##<a name="enabling-the-application-integration-for-thirdlight"></a>Habilitar a integração de aplicativo para Thirdlight
  
O objetivo desta seção é como habilitar a integração de aplicativo para Thirdlight da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-thirdlight-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Thirdlight, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-thirdlight-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-thirdlight-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-thirdlight-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-thirdlight-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Thirdlight**.

    ![Galeria de aplicativo] (./media/active-directory-saas-thirdlight-tutorial/IC805837.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Thirdlight**e clique em **concluído** para adicionar o aplicativo.

    ![ThirdLight] (./media/active-directory-saas-thirdlight-tutorial/IC805838.png "ThirdLight")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Thirdlight com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para Thirdlight requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Thirdlight** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-thirdlight-tutorial/IC805839.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Thirdlight** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-thirdlight-tutorial/IC805840.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Thirdlight URL de entrada** , digite a URL usada pelos usuários para entrar seu aplicativo Thirdlight (por exemplo: "*http://azuresso2.thirdlight.com/*") e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-thirdlight-tutorial/IC805841.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Thirdlight** , para baixar os metadados, clique em **baixar metadados**e salve o arquivo de metadados localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-thirdlight-tutorial/IC805842.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Thirdlight como administrador.

6.  Vá para **configuração \> administração do sistema**e clique em **SAML2**.

    ![Administração do sistema] (./media/active-directory-saas-thirdlight-tutorial/IC805843.png "Administração do sistema")

7.  Na seção de configuração SAML2, execute as seguintes etapas:

    ![SAML Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805844.png "SAML Single Sign-On")

    1.  Selecione **Habilitar SAML2 Single Sign-On**.
    2.  Como **fonte para IdP metadados**, selecione **Metadados de IdP de carga de XML**.
    3.  Abra o arquivo baixado metadados, copie o conteúdo e, em seguida, cole a caixa de texto **IdP metadados XML** .
    4.  Clique em **Salvar SAML2 configurações**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-thirdlight-tutorial/IC805845.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Thirdlight, ele devem ser provisionados em Thirdlight.  
No caso de Thirdlight, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **Thirdlight** como administrador.

2.  Vá para a guia **usuários** .

3.  Selecione **usuários e grupos**.

4.  Clique em botão **Adicionar novo usuário** .

5.  Insira **o nome de usuário, nome ou descrição, Email, escolher um valor predefinido ou de novos membros do grupo** de uma conta AAD válida desejada para provisionar.

6.  Clique em **criar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Thirdlight usuário conta ou APIs fornecidas pela Thirdlight para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-thirdlight-perform-the-following-steps"></a>Para atribuir usuários a Thirdlight, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Thirdlight **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-thirdlight-tutorial/IC805846.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-thirdlight-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).