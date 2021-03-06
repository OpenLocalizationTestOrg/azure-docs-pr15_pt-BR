<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com 15Five | Microsoft Azure" 
    description="Saiba como usar 15Five com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-15five"></a>Tutorial: Integração com o Active Directory do Azure com 15Five

O objetivo deste tutorial é mostrar a integração do Azure e 15Five. O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um 15Five logon único habilitado para assinatura

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a 15Five poderão único entrar no aplicativo no site da sua empresa 15Five (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para 15Five
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-15five-tutorial/IC784667.png "Cenário")
##<a name="enabling-the-application-integration-for-15five"></a>Habilitar a integração de aplicativo para 15Five

O objetivo desta seção é como habilitar a integração de aplicativo para 15Five da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-15five-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para 15Five, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-15five-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-15five-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-15five-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-15five-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **15Five**.

    ![Galeria de aplicativo] (./media/active-directory-saas-15five-tutorial/IC784668.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **15Five**e clique em **concluído** para adicionar o aplicativo.

    ![15Five] (./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar 15Five com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **15Five** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-15five-tutorial/IC784670.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar 15Five** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-15five-tutorial/IC784671.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **15Five URL de entrada** , digite sua URL usando o seguinte padrão "*https://company.15Five.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-15five-tutorial/IC784672.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em 15Five** , clique em **baixar metadados**e depois encaminhar o arquivo de metadados para a equipe de suporte de 15Five.

    ![Configurar o logon único] (./media/active-directory-saas-15five-tutorial/IC784673.png "Configurar o logon único")

    >[AZURE.NOTE] Logon único precisa ser ativado pela equipe de suporte do 15Five.

5.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-15five-tutorial/IC784674.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login 15Five, ele devem ser provisionados em 15Five.  
No caso de 15Five, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **15Five** como administrador.

2.  Vá para **Gerenciar da empresa**.

    ![Gerenciar da empresa] (./media/active-directory-saas-15five-tutorial/IC784675.png "Gerenciar da empresa")

3.  Vá para **pessoas \> adicionar pessoas**.

    ![Pessoas] (./media/active-directory-saas-15five-tutorial/IC784676.png "Pessoas")

4.  Na seção Adicionar nova pessoa, execute as seguintes etapas:

    ![Adicionar nova pessoa] (./media/active-directory-saas-15five-tutorial/IC784677.png "Adicionar nova pessoa")

    1.  Digite o **nome**, o **Sobrenome**, o **título**, o **endereço de Email** de uma conta do Active Directory do Azure válida que deseja provisionar em caixas de texto relacionadas.
    2.  Clique em **concluído**.

    >[AZURE.NOTE] O proprietário da conta do Azure AD receberão um email incluindo um link para confirmar a conta antes que ele fique ativo.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros 15Five usuário conta ou APIs fornecidas pela 15Five para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-15five-perform-the-following-steps"></a>Para atribuir usuários a 15Five, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **15Five **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-15five-tutorial/IC784678.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-15five-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
