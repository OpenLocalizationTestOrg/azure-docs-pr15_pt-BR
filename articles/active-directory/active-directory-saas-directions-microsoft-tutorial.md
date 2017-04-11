<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com direções no Microsoft | Microsoft Azure" 
    description="Saiba como usar direções na Microsoft com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-directions-on-microsoft"></a>Tutorial: Integração com o Active Directory do Azure com direções no Microsoft

O objetivo deste tutorial é mostrar a integração do Active Directory do Azure e direções na Microsoft.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um direções na assinatura do Microsoft

Se você não tiver um direções federados na assinatura do Microsoft ainda, uma solicitação de email "*service@DirectionsOnMicrosoft.com*".

Depois de concluir este tutorial, os usuários do Active Directory do Azure que você atribuiu direções no Microsoft será capazes de logon único para o aplicativo usando logon único.

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração do aplicativo para obter instruções sobre Microsoft
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Cenário")
##<a name="enabling-the-application-integration-for-directions-on-microsoft"></a>Habilitar a integração do aplicativo para obter instruções sobre Microsoft

O objetivo desta seção é como habilitar a integração do aplicativo para obter instruções sobre Microsoft da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-directions-on-microsoft-perform-the-following-steps"></a>Para habilitar a integração do aplicativo para obter instruções sobre a Microsoft, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **direções no Microsoft**.

    ![Galeria de aplicativo] (./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **direções no Microsoft**e clique em **concluído** para adicionar o aplicativo.

    ![Cenário] (./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Cenário")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar direções no Microsoft com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo **direções no Microsoft** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Habilitar o logon único] (./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Habilitar o logon único")

2.  Na página **como você gostaria que os usuários para assinar direções no Microsoft** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Microsoft Azure AD um logon] (./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Microsoft Azure AD um logon")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto de entrada na URL, digite **https://www.directionsonmicrosoft.com/user/login**e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em direções no Microsoft** , clique em **baixar metadados**e salve o arquivo de metadados localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Configurar o logon único")

5.  Enviar o arquivo de metadados para as instruções na equipe de suporte da Microsoft (*service@DirectionsOnMicrosoft.com*). Para habilitar as instruções na equipe de suporte da Microsoft localizar sua participação no site federado, inclua informações da sua empresa em seu email.

    >[AZURE.NOTE] Logon único para obter instruções sobre o Microsoft deve ser habilitado pelas direções da equipe de suporte da Microsoft.
Você receberá uma notificação quando o logon único foi ativado.

6.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Não há nenhum item de ação de configurar direções no Microsoft de provisionamento do usuário.  
Quando um usuário atribuído tenta login direções no Microsoft usando o painel de acesso, direções no Microsoft verifica se o usuário existe. Se há nenhuma conta de usuário ainda disponível, ele será criado automaticamente pelo direções no Microsoft.
##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-directions-on-microsoft-perform-the-following-steps"></a>Para atribuir usuários direções no Microsoft, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo **direções no Microsoft **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Sim")
