<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Lucidchart | Microsoft Azure" 
    description="Saiba como usar Lucidchart com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Tutorial: Integração com o Active Directory do Azure com Lucidchart
  
O objetivo deste tutorial é mostrar a integração do Azure e Lucidchart.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Lucidchart logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Lucidchart poderão único entrar no aplicativo no site da sua empresa Lucidchart (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Lucidchart
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Cenário")
##<a name="enabling-the-application-integration-for-lucidchart"></a>Habilitar a integração de aplicativo para Lucidchart
  
O objetivo desta seção é como habilitar a integração de aplicativo para Lucidchart da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-lucidchart-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Lucidchart, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Lucidchart**.

    ![Galeria de aplicativo] (./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Lucidchart**e clique em **concluído** para adicionar o aplicativo.

    ![Lucidchart] (./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Lucidchart com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Lucidchart** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Lucidchart** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Lucidchart entrada na URL** , digite a URL usada pelos usuários para entrar seu aplicativo de Lucidchart (por exemplo: "*https://chart2.office.lucidchart.com/saml/sso/azure*") e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Lucidchart** , para baixar os metadados, clique em **baixar metadados**e salve o arquivo de dados localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Lucidchart como administrador.

6.  No menu na parte superior, clique em **equipe**.

    ![Equipe] (./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Equipe")

7.  Clique em **aplicativo \> gerenciar SAML**.

    ![Gerenciar SAML] (./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Gerenciar SAML")

8.  Na página de diálogo de **Configurações de autenticação de SAML** , execute as seguintes etapas:

    1.  Selecione **Ativar SAML autenticação**e, em seguida, clique em **opcional**.
        ![Configurações de autenticação de SAML] (./media/active-directory-saas-lucidchart-tutorial/IC791192.png "Configurações de autenticação de SAML")
    2.  Na caixa de texto **domínio** , digite seu domínio e clique em **Alterar certificado**.
        ![Certificado de alteração] (./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Certificado de alteração")
    3.  Abra o arquivo baixado metadados, copie o conteúdo e, em seguida, cole a caixa de texto de **Carregar metadados** .
        ![Carregar metadados] (./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Carregar metadados")
    4.  Selecione **Adicionar automaticamente ao novo usuário a equipe**e, em seguida, clique em **Salvar alterações**.
        ![Salvar alterações] (./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Salvar alterações")

9.  Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Não há nenhum item de ação para configurar a Lucidchart de provisionamento do usuário.  
Quando um usuário atribuído tenta faça logon em Lucidchart usando o painel de acesso, Lucidchart verifica se o usuário existe.  
Se houver nenhuma conta de usuário ainda disponível, ele será criado automaticamente pelo Lucidchart.
##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-lucidchart-perform-the-following-steps"></a>Para atribuir usuários a Lucidchart, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Lucidchart **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).