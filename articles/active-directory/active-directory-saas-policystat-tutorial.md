<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com PolicyStat | Microsoft Azure" 
    description="Saiba como usar PolicyStat com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-policystat"></a>Tutorial: Integração com o Active Directory do Azure com PolicyStat
  
O objetivo deste tutorial é mostrar a integração do Azure e PolicyStat.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário PolicyStat
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a PolicyStat poderão único entrar no aplicativo no site da sua empresa PolicyStat (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para PolicyStat
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-policystat-tutorial/IC808662.png "Cenário")
##<a name="enabling-the-application-integration-for-policystat"></a>Habilitar a integração de aplicativo para PolicyStat
  
O objetivo desta seção é como habilitar a integração de aplicativo para PolicyStat da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-policystat-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para PolicyStat, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-policystat-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-policystat-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-policystat-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-policystat-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **PolicyStat**.

    ![Galeria de aplicativo] (./media/active-directory-saas-policystat-tutorial/IC808627.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **PolicyStat**e clique em **concluído** para adicionar o aplicativo.

    ![PolicyStat] (./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar PolicyStat com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Seu aplicativo PolicyStat espera as declarações SAML em um formato específico, o que requer que você adicionar os mapeamentos de atributo personalizado à sua configuração de **atributos de token saml** .  
A captura de tela a seguir mostra um exemplo para isso.

![Atributos] (./media/active-directory-saas-policystat-tutorial/IC808628.png "Atributos")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **PolicyStat** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-policystat-tutorial/IC808629.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar PolicyStat** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-policystat-tutorial/IC808630.png "Configurar o logon único")

3.  Na página **Definir configurações de aplicativo** , na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para logon para seu aplicativo PolicyStat de URL (por exemplo: *"https://demo-azure.policystat.com"*) e, em seguida, clique em **Avançar**.

    ![Definir configurações de aplicativo] (./media/active-directory-saas-policystat-tutorial/IC808631.png "Definir configurações de aplicativo")

4.  Na página **Configurar logon único em PolicyStat** , clique em **baixar metadados**e salve o arquivo de metadados em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-policystat-tutorial/IC808632.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa PolicyStat como administrador.

6.  Clique na guia **Administração** e clique em **Configuração de logon único** no painel de navegação à esquerda.

    ![Menu administrador] (./media/active-directory-saas-policystat-tutorial/IC808633.png "Menu administrador")

7.  Na seção **configuração** , selecione **integração de logon único habilitar**.

    ![Configuração de logon único] (./media/active-directory-saas-policystat-tutorial/IC808634.png "Configuração de logon único")

8.  Clique em **Configurar atributos**e, em seguida, na seção **Configurar atributos** , execute as seguintes etapas:

    ![Configuração de logon único] (./media/active-directory-saas-policystat-tutorial/IC808635.png "Configuração de logon único")

    1.  Na caixa de texto **Atributo Username** , digite **uid**.
    2.  Na caixa de texto do **Atributo de nome** , digite o **nome**.
    3.  Na caixa de texto do **Último atributo de nome** , digite **Sobrenome**.
    4.  Na caixa de texto do **Atributo de Email** , digite **emailaddress**.
    5.  Clique em **Salvar alterações**.

9.  Clique em **Seu IDP metadados**e, em seguida, na seção **Your IDP metadados** , execute as seguintes etapas:

    ![Configuração de logon único] (./media/active-directory-saas-policystat-tutorial/IC808635.png "Configuração de logon único")

    1.  Abra o arquivo baixado metadados, copie o conteúdo e, em seguida, cole a caixa de texto de **Sua metadados de provedor de identidade**
    2.  Clique em **Salvar alterações**.

10. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-policystat-tutorial/IC771723.png "Configurar o logon único")

11. 12. No menu na parte superior, clique em **atributos** para abrir a caixa de diálogo de **Atributos de Token SAML** .

    ![Atributos] (./media/active-directory-saas-policystat-tutorial/IC795920.png "Atributos")

13. Para adicionar os mapeamentos de atributo obrigatório, execute as seguintes etapas:

    ![Atributos] (./media/active-directory-saas-policystat-tutorial/IC804823.png "Atributos")

    1.  Clique em **Adicionar atributo de usuário**.
    2.  Na caixa de texto **Nome do atributo** , digite **uid**.
    3.  Na caixa de texto **Valor do atributo** , selecione **ExtractMailPrefix()**.
    4.  Na lista de **mensagens** , selecione **User.mail**.
    5.  Clique em **Concluir**.
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login PolicyStat, ele devem ser provisionados em PolicyStat.  
PolicyStat suporta apenas em vez de provisionamento de usuário. Isso significa, você não necessário adicionar usuários manualmente para PolicyStat.  
Os usuários serão obter adicionados automaticamente em seu primeiro login por meio de logon único em.

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros PolicyStat usuário conta ou APIs fornecidas pela PolicyStat para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-policystat-perform-the-following-steps"></a>Para atribuir usuários a PolicyStat, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **PolicyStat **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-policystat-tutorial/IC808636.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-policystat-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).