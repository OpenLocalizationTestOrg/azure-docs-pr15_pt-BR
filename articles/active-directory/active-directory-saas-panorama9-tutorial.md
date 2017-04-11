<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Panorama9 | Microsoft Azure" 
    description="Saiba como usar Panorama9 com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Tutorial: Integração com o Active Directory do Azure com Panorama9
  
O objetivo deste tutorial é mostrar a integração do Azure e Panorama9.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Panorama9 logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Panorama9 poderão único entrar no aplicativo no site da sua empresa Panorama9 (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Panorama9
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-panorama9-tutorial/IC790016.png "Cenário")
##<a name="enabling-the-application-integration-for-panorama9"></a>Habilitar a integração de aplicativo para Panorama9
  
O objetivo desta seção é como habilitar a integração de aplicativo para Panorama9 da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-panorama9-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Panorama9, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-panorama9-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-panorama9-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-panorama9-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-panorama9-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Panorama9**.

    ![Galeria de aplicativo] (./media/active-directory-saas-panorama9-tutorial/IC790017.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Panorama9**e clique em **concluído** para adicionar o aplicativo.

    ![Panorama9] (./media/active-directory-saas-panorama9-tutorial/IC790018.png "Panorama9")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Panorama9 com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para Panorama9 requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Panorama9** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-panorama9-tutorial/IC790019.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Panorama9** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-panorama9-tutorial/IC790020.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Panorama9 entrada na URL** , digite sua URL usada pelos usuários para entrar no Panorama9 (por exemplo: "*https://dashboard.panorama9.com/saml/access/3262*") e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-panorama9-tutorial/IC790021.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Panorama9** , para baixar o certificado, clique em **baixar o certificado**e, em seguida, salve-a localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-panorama9-tutorial/IC790022.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Panorama9 como administrador.

6.  Na barra de ferramentas na parte superior, clique em **Gerenciar**e clique em **extensões**.

    ![Extensões] (./media/active-directory-saas-panorama9-tutorial/IC790023.png "Extensões")

7.  Na caixa de diálogo **extensões** , clique em **Logon único**.

    ![Logon único] (./media/active-directory-saas-panorama9-tutorial/IC790024.png "Logon único")

8.  Na seção **configurações** , execute as seguintes etapas:

    ![Configurações] (./media/active-directory-saas-panorama9-tutorial/IC790025.png "Configurações")

    1.  No portal clássico do Azure, na página de diálogo **Configurar logon único em Panorama9** , copie o valor de **Single Sign-On URL do serviço** e cole-o na caixa de texto **URL do provedor de identidade** .
    2.  Copie o valor de **impressão digital** do certificado exportado e, em seguida, cole a caixa de texto de **impressão digital de certificado** .  

        >[AZURE.TIP]Para obter mais detalhes, consulte [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    3.  Clique em **Salvar**.

9.  No portal de clássico do Azure AD, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-panorama9-tutorial/IC790026.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Panorama9, ele devem ser provisionados em Panorama9.  
No caso de Panorama9, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **Panorama9** como administrador.

2.  No menu na parte superior, clique em **Gerenciar**e, em seguida, clique em **usuários**.

    ![Usuários] (./media/active-directory-saas-panorama9-tutorial/IC790027.png "Usuários")

3.  Clique em **+**.

4.  Na seção de dados do usuário, execute as seguintes etapas:

    ![Usuários] (./media/active-directory-saas-panorama9-tutorial/IC790028.png "Usuários")

    1.  Na caixa de texto **Email** , digite o endereço de email de um usuário do Active Directory do Azure válido que deseja provisionar.
    2.  Clique em **Salvar**.

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros Panorama9 usuário conta ou APIs fornecidas pela Panorama9 para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-panorama9-perform-the-following-steps"></a>Para atribuir usuários a Panorama9, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Panorama9** , clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-panorama9-tutorial/IC790029.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-panorama9-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).