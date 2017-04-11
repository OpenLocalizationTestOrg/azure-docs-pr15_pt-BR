<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Huddle | Microsoft Azure" 
    description="Saiba como usar Huddle com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-huddle"></a>Tutorial: Integração com o Active Directory do Azure com Huddle
  
O objetivo deste tutorial é mostrar a integração do Azure e Huddle.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Huddle logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Huddle poderão único entrar no aplicativo no site da sua empresa Huddle (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Huddle
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Configurar o logon único] (./media/active-directory-saas-huddle-tutorial/IC787830.png "Configurar o logon único")
##<a name="enabling-the-application-integration-for-huddle"></a>Habilitar a integração de aplicativo para Huddle
  
O objetivo desta seção é como habilitar a integração de aplicativo para Huddle da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-huddle-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Huddle, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-huddle-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-huddle-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-huddle-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-huddle-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Huddle**.

    ![Galeria de aplicativo] (./media/active-directory-saas-huddle-tutorial/IC787831.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Huddle**e clique em **concluído** para adicionar o aplicativo.

    ![Huddle] (./media/active-directory-saas-huddle-tutorial/IC787832.png "Huddle")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Huddle com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Huddle** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-huddle-tutorial/IC787833.png "Configurar o logon único")

2.  Na página **como deseja usuários para entrar Huddle** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-huddle-tutorial/IC787834.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Huddle entrada na URL** , digite a URL do seu locatário Huddle usando o seguinte padrão "*http://company.huddle.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-huddle-tutorial/IC787835.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Huddle** , execute as seguintes etapas:

    ![Configurar o logon único] (./media/active-directory-saas-huddle-tutorial/IC787836.png "Configurar o logon único")

    1.  Clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.
    2.  Copie o valor de **URL do emissor** , o valor de **SAML SSO URL** e o certificado baixado e, em seguida, enviá-los para a equipe de suporte de Huddle.

    >[AZURE.NOTE] Logon único precisa ser ativado pela equipe de suporte do Huddle.
Você receberá uma notificação quando a configuração foi concluída.

5.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-huddle-tutorial/IC787837.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Huddle, ele devem ser provisionados em Huddle.  
No caso de Huddle, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon seu site de empresa **Huddle** como administrador.

2.  Clique em **espaço de trabalho**.

3.  Clique em **pessoas \> convidar pessoas**.

    ![Pessoas] (./media/active-directory-saas-huddle-tutorial/IC787838.png "Pessoas")

4.  Na seção **criar um novo convite** , execute as seguintes etapas:

    ![Novo convite] (./media/active-directory-saas-huddle-tutorial/IC787839.png "Novo convite")

    1.  Na lista **Escolher uma equipe para convidar pessoas para ingressar** , selecione **equipe**.
    2.  Digite o **Endereço de Email** de uma conta AAD válida que deseja provisionar na caixa de texto relacionada.
    3.  Clique em **Convidar**.

    >[AZURE.NOTE] O proprietário da conta do Azure AD receberão um email incluindo um link para confirmar a conta antes que ele fique ativo.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Huddle usuário conta ou APIs fornecidas pela Huddle provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-huddle-perform-the-following-steps"></a>Para atribuir usuários a Huddle, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Huddle **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-huddle-tutorial/IC787840.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-huddle-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).