<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Cherwell | Microsoft Azure" 
    description="Saiba como usar Cherwell com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="10/14/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Tutorial: Integração com o Active Directory do Azure com Cherwell

O objetivo deste tutorial é mostrar a integração do Azure e Cherwell. O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Cherwell logon único habilitado para assinatura

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Cherwell poderão único entrar no aplicativo no site da sua empresa Cherwell (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Cherwell
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-cherwell-tutorial/IC798988.png "Cenário")
##<a name="enabling-the-application-integration-for-cherwell"></a>Habilitar a integração de aplicativo para Cherwell

O objetivo desta seção é como habilitar a integração de aplicativo para Cherwell da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-cherwell-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Cherwell, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-cherwell-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-cherwell-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-cherwell-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-cherwell-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Cherwell**.

    ![Cherwell] (./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")

7.  No painel de resultados, selecione **Cherwell**e clique em **concluído** para adicionar o aplicativo.
##<a name="configuring-single-sign-on"></a>Configurando o logon único

    ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798996.png "Cherwell")

O objetivo desta seção é como habilitar usuários autenticar Cherwell com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Cherwell** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-cherwell-tutorial/IC798990.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Cherwell** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-cherwell-tutorial/IC798991.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , execute as seguintes etapas:

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-cherwell-tutorial/IC798992.png "Configurar a URL do aplicativo")

    a.  Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para entrar em seu **Cherwell** (por exemplo: *https://\<o nome da empresa\>.cherwellondemand.com/cherwellclient*).

    b.  Clique em **Avançar**

4.  Na página **Configurar logon único em Cherwell** , execute as seguintes etapas:

    ![Configurar o logon único] (./media/active-directory-saas-cherwell-tutorial/IC798993.png "Configurar o logon único")

    a.  Clique em **baixar o certificado**e, em seguida, salve o certificado localmente em seu computador.

    b.  Copie a **URL do provedor de identidade**.

    c.  Copie a **URL do serviço Single Sign-On**.

    d.  Clique em **Avançar**.

5.  Envie o certificado baixado, a **URL do provedor de identidade** e **O URL de serviço de logon único** para sua equipe de suporte de Cherwell.

    >[AZURE.NOTE] A equipe de suporte Cherwell tem que fazer a configuração de SSO real.
Você receberá uma notificação quando o SSO foi ativado para sua assinatura.

6.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-cherwell-tutorial/IC798994.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login Cherwell, ele devem ser provisionados em Cherwell.  
No caso de Cherwell, as contas de usuário precisam ser criados pela equipe de suporte Cherwell.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Cherwell usuário conta ou APIs fornecidas pela Cherwell provisionar o Active Directory do Azure contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-cherwell-perform-the-following-steps"></a>Para atribuir usuários a Cherwell, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Cherwell** , clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-cherwell-tutorial/IC798995.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-cherwell-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
