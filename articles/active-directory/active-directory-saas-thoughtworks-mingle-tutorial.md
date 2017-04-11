<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Thoughtworks mesclar | Microsoft Azure" 
    description="Aprenda a usar Thoughtworks mesclar com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Tutorial: Integração com o Active Directory do Azure com Thoughtworks mesclar
  
O objetivo deste tutorial é mostrar a integração do Azure e mesclar os Thoughtworks.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário Thoughtworks mesclar
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para mesclar os Thoughtworks
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "Cenário")

##<a name="enabling-the-application-integration-for-thoughtworks-mingle"></a>Habilitar a integração de aplicativo para mesclar os Thoughtworks
  
O objetivo desta seção é como habilitar a integração de aplicativo para mesclar os Thoughtworks da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-thoughtworks-mingle-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para mesclar os Thoughtworks, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **thoughtworks mesclar**.

    ![Galeria de aplicativo] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Mesclar Thoughtworks**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Mesclar os ThoughtWorks] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Mesclar os ThoughtWorks")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar para Thoughtworks conversar com sua conta no Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, você precisa carregar um certificado para mesclar os Thoughtworks.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Thoughtworks mesclar **, clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "Configurar o logon único")

2.  Na página **como você gostaria que usuários para entrar Thoughtworks mesclar** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Thoughtworks mesclar locatário URL** , digite sua URL usando o seguinte padrão "*http://company.mingle.thoughtworks.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Thoughtworks mesclar** , clique em Download metadados e, em seguida, salve-o no seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "Configurar o logon único")

5.  Faça logon seu site de empresa **Thoughtworks mesclar** como administrador.

6.  Clique na guia **Administração** e, em seguida, clique em **Configuração de SSO**.

    ![Configuração SSO] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "Configuração SSO")

7.  Na seção **Config SSO** , execute as seguintes etapas:

    ![Configuração SSO] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "Configuração SSO")

    1.  Para carregar o arquivo de metadados, clique em **Escolher arquivo**.
    2.  Clique em **Salvar alterações**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para usuários AAD conseguir entrar, eles devem ser provisionados para o aplicativo Thoughtworks Mesclar usando seus nomes de usuário do Active Directory do Azure.  
No caso de Thoughtworks mesclar, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon seu site de empresa Thoughtworks mesclar como administrador.

2.  Clique em **perfil**.

    ![Seu primeiro projeto] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "Seu primeiro projeto")

3.  Clique na guia **Administração** e, em seguida, clique em **usuários**.

    ![Usuários] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "Usuários")

4.  Clique em **novo usuário**.

    ![Novo usuário] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "Novo usuário")

5.  Na página de diálogo **Novo usuário** , execute as seguintes etapas:

    ![Novo usuário] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "Novo usuário")

    1.  Digite o **nome de entrada**, o **nome de exibição**, a **senha de escolha**, a **Confirmar senha** de uma conta AAD válida que deseja provisionar em caixas de texto relacionadas.
    2.  Como **tipo de usuário**, selecione **usuário completo**.
    3.  Clique em **criar esse perfil**.

>[AZURE.NOTE] Você pode usar qualquer outras Thoughtworks mesclar usuário conta ferramentas de criação ou APIs fornecidas pela Thoughtworks Mesclar para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-thoughtworks-mingle-perform-the-following-steps"></a>Para atribuir usuários a mesclar Thoughtworks, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Thoughtworks mesclar** , clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
