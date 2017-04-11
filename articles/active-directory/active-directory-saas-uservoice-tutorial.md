<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com UserVoice | Microsoft Azure" 
    description="Saiba como usar o UserVoice com o Azure Active Directory para habilitar o logon único, automatizado de provisionamento e muito mais!." 
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

#<a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Tutorial: Integração com o Active Directory do Azure com UserVoice
  
O objetivo deste tutorial é mostrar a integração do Azure e UserVoice.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário UserVoice
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a UserVoice poderão único entrar no aplicativo no site da sua empresa UserVoice (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para UserVoice
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-uservoice-tutorial/IC777514.png "Cenário")

##<a name="enabling-the-application-integration-for-uservoice"></a>Habilitar a integração de aplicativo para UserVoice
  
O objetivo desta seção é como habilitar a integração de aplicativo para UserVoice da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-uservoice-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para UserVoice, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-uservoice-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-uservoice-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-uservoice-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-uservoice-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **UserVoice**.

    ![Galeria de aplicativo] (./media/active-directory-saas-uservoice-tutorial/IC777513.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **UserVoice**e clique em **concluído** para adicionar o aplicativo.

    ![UserVoice] (./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar UserVoice com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para UserVoice requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **UserVoice** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-uservoice-tutorial/IC777515.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar UserVoice** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-uservoice-tutorial/IC777516.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **UserVoice URL de entrada** , digite sua URL usando o seguinte padrão "https://*\<-nome do locatário\>. UserVoice.com*"e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-uservoice-tutorial/IC777517.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em UserVoice** , para baixar o certificado, clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado localmente como **c:\\UserVoice.cer**.

    ![Configurar o logon único] (./media/active-directory-saas-uservoice-tutorial/IC777518.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa UserVoice como administrador.

6.  Na barra de ferramentas na parte superior, clique em configurações e, em seguida, selecione o portal da Web no menu.

    ![Configurações] (./media/active-directory-saas-uservoice-tutorial/IC777519.png "Configurações")

7.  Na guia **portal da Web** , na seção **autenticação de usuário** , clique em **Editar** para abrir a página de diálogo **Editar autenticação de usuário**

    ![Portal da Web] (./media/active-directory-saas-uservoice-tutorial/IC777520.png "Portal da Web")

8.  Na página de diálogo **Editar autenticação de usuário** , execute as seguintes etapas:

    ![Editar autenticação de usuário] (./media/active-directory-saas-uservoice-tutorial/IC777521.png "Editar autenticação de usuário")

    1.  Clique em **Logon único (SSO)**.
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único em UserVoice** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **Entrar remoto SSO** .
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único em UserVoice** , copie o valor de **URL Logout remota** e cole-o na **caixa de texto Sign-Out remoto SSO**.
    4.  Copie o valor de **impressão digital** do certificado exportado e, em seguida, cole a caixa de texto de **impressão digital SHA1 de certificado atual** .  

        >[AZURE.TIP] Para obter mais detalhes, consulte [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    5.  Clique em **Salvar configurações de autenticação**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-uservoice-tutorial/IC777522.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login UserVoice, ele devem ser provisionados em UserVoice.  
No caso de UserVoice, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **UserVoice** .

2.  Vá para **configurações**.

    ![Configurações] (./media/active-directory-saas-uservoice-tutorial/IC777811.png "Configurações")

3.  Clique em **Geral**.

4.  Clique em **permissões e agentes**.

    ![Agentes e permissões] (./media/active-directory-saas-uservoice-tutorial/IC777812.png "Agentes e permissões")

5.  Clique em **adicionar administradores**.

    ![Adicionar administradores] (./media/active-directory-saas-uservoice-tutorial/IC777813.png "Adicionar administradores")

6.  Na caixa de diálogo **Convidar administradores** , execute as seguintes etapas:

    ![Convidar administradores] (./media/active-directory-saas-uservoice-tutorial/IC777814.png "Convidar administradores")

    1.  Na texbox Emails, digite o endereço de email da conta que você deseja provisionar e clique em **Adicionar**.
    2.  Clique em **Convidar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros UserVoice usuário conta ou APIs fornecidas pela UserVoice para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-uservoice-perform-the-following-steps"></a>Para atribuir usuários a UserVoice, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **UserVoice **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-uservoice-tutorial/IC777523.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-uservoice-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).