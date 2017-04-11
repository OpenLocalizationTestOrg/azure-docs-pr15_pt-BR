<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com ITRP | Microsoft Azure" 
    description="Saiba como usar ITRP com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="09/07/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-itrp"></a>Tutorial: Integração com o Active Directory do Azure com ITRP
  
O objetivo deste tutorial é mostrar a integração do Azure e ITRP.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário ITRP
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a ITRP poderão único entrar no aplicativo no site da sua empresa ITRP (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para ITRP
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-itrp-tutorial/IC775551.png "Cenário")
##<a name="enabling-the-application-integration-for-itrp"></a>Habilitar a integração de aplicativo para ITRP
  
O objetivo desta seção é como habilitar a integração de aplicativo para ITRP da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-itrp-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para ITRP, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-itrp-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-itrp-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-itrp-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-itrp-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **ITRP**.

    ![Galeria de aplicativo] (./media/active-directory-saas-itrp-tutorial/IC775565.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **ITRP**e clique em **concluído** para adicionar o aplicativo.

    ![ITRP] (./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar ITRP com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para ITRP requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **ITRP** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-itrp-tutorial/IC771709.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar ITRP** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-itrp-tutorial/IC775567.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **ITRP URL de entrada** , digite sua URL usando o seguinte padrão "https://*\<-nome do locatário\>. ITRP.com*"e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-itrp-tutorial/IC775568.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em ITRP** , para baixar o certificado, clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado localmente como **c:\\ITRP.cer**.

    ![Configurar o logon único] (./media/active-directory-saas-itrp-tutorial/IC775569.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa ITRP como administrador.

6.  Na barra de ferramentas na parte superior, clique em **configurações**.

    ![ITRP] (./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7.  No painel de navegação esquerdo, selecione **Single Sign-On**.

    ![Logon único] (./media/active-directory-saas-itrp-tutorial/IC775571.png "Logon único")

8.  Na seção de configuração Single Sign-On, execute as seguintes etapas:

    ![Logon único] (./media/active-directory-saas-itrp-tutorial/IC775572.png "Logon único")

    ![Logon único] (./media/active-directory-saas-itrp-tutorial/IC775573.png "Logon único")

    1.  Clique em **Ativar**.
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único em ITRP** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL Logout remota** .
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único em ITRP** , copie o valor de **SAML SSO URL** e cole-o na caixa de texto **URL de SSO SAML** .
    4.  Copie o valor de **impressão digital** do certificado exportado e, em seguida, cole a caixa de texto de **Impressão digital de certificado** .
        
        >[AZURE.TIP]Para obter mais detalhes, consulte [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    5.  Clique em **Salvar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-itrp-tutorial/IC775574.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login ITRP, ele devem ser provisionados em ITRP.  
No caso de ITRP, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **ITRP** .

2.  Na barra de ferramentas na parte superior, clique em **registros**.

    ![Administração] (./media/active-directory-saas-itrp-tutorial/IC775575.png "Administração")

3.  No menu pop-up, selecione **pessoas**.

    ![Pessoas] (./media/active-directory-saas-itrp-tutorial/IC775587.png "Pessoas")

4.  Clique em **Adicionar nova pessoa** ("+").

    ![Administração] (./media/active-directory-saas-itrp-tutorial/IC775576.png "Administração")

5.  Na caixa de diálogo Adicionar nova pessoa, execute as seguintes etapas:

    ![Usuário] (./media/active-directory-saas-itrp-tutorial/IC775577.png "Usuário")

    1.  Digite o **nome**, o **Email** de uma conta AAD válida que deseja provisionar.
    2.  Clique em **Salvar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros ITRP usuário conta ou APIs fornecidas pela ITRP para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-itrp-perform-the-following-steps"></a>Para atribuir usuários a ITRP, execute as seguintes etapas:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração do aplicativo **ITRP **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-itrp-tutorial/IC775588.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-itrp-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).