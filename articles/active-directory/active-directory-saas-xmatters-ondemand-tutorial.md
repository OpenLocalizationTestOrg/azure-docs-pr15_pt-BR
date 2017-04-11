<properties 
    pageTitle="Tutorial: Azure integração do Active Directory com xMatters OnDemand | Microsoft Azure"
    description="Saiba como usar xMatters OnDemand com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Tutorial: Azure integração do Active Directory com xMatters OnDemand
  
O objetivo deste tutorial é mostrar a integração do Azure e xMatters OnDemand. O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário de OnDemand xMatters
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a xMatters OnDemand poderão único entrar no aplicativo no xMatters OnDemand site da sua empresa (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para xMatters OnDemand
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Cenário")

##<a name="enabling-the-application-integration-for-xmatters-ondemand"></a>Habilitar a integração de aplicativo para xMatters OnDemand
  
O objetivo desta seção é como habilitar a integração de aplicativo para xMatters OnDemand da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-xmatters-ondemand-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para xMatters OnDemand, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **xMatters OnDemand**.

    ![Galeria de aplicativo] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **XMatters OnDemand**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![xMatters OnDemand] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters OnDemand")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar XMatters OnDemand com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **XMatters OnDemand** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar XMatters OnDemand** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , execute as seguintes etapas:

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Configurar a URL do aplicativo")

    a. Na caixa de texto **XMatters OnDemand URL de entrada** , digite sua URL usando o seguinte padrão:`https://<tenant-name>.XMattersOnDemandapp.com`

    b. Clique em **Avançar**.


4.  Na página **Configurar logon único em XMatters OnDemand** , para baixar o certificado, clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado localmente como **c:\\XMatters OnDemand.cer**.

    >[AZURE.IMPORTANT] Você precisa encaminhar o certificado para a equipe de suporte de xMatters. O certificado precisa ser carregado pela equipe de suporte do xMatters antes de você pode finalizar a configuração de logon única.

    ![Configurar único entrar em] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Configurar único entrar em")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa XMatters OnDemand como administrador.

6.  Na barra de ferramentas na parte superior, clique em **administrador**e clique em **Detalhes da empresa** na barra de navegação no lado esquerdo.

    ![Administração] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Administração")

7.  Na página **Configuração de SAML** , execute as seguintes etapas:

    ![Configuração de SAML] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "Configuração de SAML")

    1.  Selecione **Habilitar SAML**.
    2.  No portal clássico do Azure, na página de diálogo **Configurar logon único em XMatters OnDemand** , copie o valor de **ID do provedor de identidade** e cole-o na caixa de texto **Identificação do provedor de identidade** .
    3.  No portal clássico do Azure, na página de diálogo **Configurar logon único em XMatters OnDemand** , copie o valor de **Single Sign-On URL do serviço** e, em seguida, cole a caixa de texto de **Entrada única na URL** .
    4.  No portal do clássico Azure, na página de diálogo **Configurar logon único em XMatters OnDemand** , copie o valor de **URL do serviço Sign-Out único** e cole-o na caixa de texto **Única Logout URL** .
    5.  Na página Detalhes da empresa, na parte superior, clique em **Salvar alterações**.
        ![Detalhes da empresa] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Detalhes da empresa")

8.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar único entrar em] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Configurar único entrar em")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login XMatters OnDemand, eles devem ser provisionados em XMatters OnDemand.  
No caso de XMatters OnDemand, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **XMatters OnDemand** .

2.  Clique na guia **usuários** .

3.  Clique em **Adicionar usuário**.

    ![Usuários] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Usuários")

4.  Selecione **ativo**.

5.  Na seção **Adicionar um usuário** , execute as seguintes etapas:

    ![Adicionar um usuário] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Adicionar um usuário")

    1.  Insira a **ID de usuário**, o **nome**, o **Sobrenome**, o **Site** de uma conta AAD válida que deseja provisionar.
    2.  Clique em **Salvar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros XMatters OnDemand usuário conta ou APIs fornecidas pela XMatters OnDemand para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-xmatters-ondemand-perform-the-following-steps"></a>Para atribuir usuários a XMatters OnDemand, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **XMatters OnDemand **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).