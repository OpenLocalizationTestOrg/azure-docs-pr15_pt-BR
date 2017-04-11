<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com BlueJeans | Microsoft Azure" 
    description="Saiba como usar BlueJeans com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-ad-integration-with-bluejeans"></a>Tutorial: Azure AD integração com BlueJeans

O objetivo deste tutorial é mostrar a integração do Azure e BlueJeans.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um BlueJeans logon único habilitado para assinatura

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a BlueJeans poderão único entrar no aplicativo no site da sua empresa BlueJeans (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para BlueJeans
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Cenário")
##<a name="enabling-the-application-integration-for-bluejeans"></a>Habilitar a integração de aplicativo para BlueJeans

O objetivo desta seção é como habilitar a integração de aplicativo para BlueJeans da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-bluejeans-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para BlueJeans, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **BlueJeans**.

    ![Galeria de aplicativo] (./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **BlueJeans**e clique em **concluído** para adicionar o aplicativo.

    ![BlueJeans] (./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar BlueJeans com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **BlueJeans** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar BlueJeans** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **BlueJeans entrada na URL** , digite sua URL usando o seguinte padrão "*https://company.BlueJeans.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em BlueJeans** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa **BlueJeans** como administrador.

6.  Vá para **administrador \> as configurações do grupo \> segurança**.

    ![Administração] (./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Administração")

7.  Na seção **segurança** , execute as seguintes etapas:

    ![SAML logon único] (./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML logon único")

    1.  Selecione **SAML logon único**.
    2.  Selecione **Habilitar provisionamento automático**.

8.  Mover com as seguintes etapas:

    ![Caminho de certificado] (./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Caminho de certificado")

    1.  Clique em **Escolher arquivo**e carregue o certificado baixado.
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único em BlueJeans** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login** .
    3.  No portal clássico do Azure, na página de diálogo **Configurar logon único em BlueJeans** , copie o valor de **Alterar senha URL** e cole-o na caixa de texto **URL de alteração de senha** .
    4.  No portal do clássico Azure, na página de diálogo **Configurar logon único em BlueJeans** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL de Logout** .

9.  Mover com as seguintes etapas:

    ![Salvar alterações] (./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Salvar alterações")

    1.  Na caixa de texto **id de usuário** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    2.  Na caixa de texto **Email** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    3.  Clique em **Salvar alterações**.

10. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login BlueJeans, ele devem ser provisionados em BlueJeans.  
No caso de BlueJeans, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **BlueJeans** como administrador.

2.  Vá para **administrador \> gerenciar usuários \> adicionar usuário**.

    ![Administração] (./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Administração")

    >[AZURE.IMPORTANT] Na guia **Adicionar usuário** só está disponível se, na **guia Segurança**, **habilite o fornecimento automático** está desmarcada.

3.  Na seção **Adicionar usuário** , execute as seguintes etapas:

    ![Adicionar usuário] (./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Adicionar usuário")

    1.  Digite um **Nome de usuário BlueJeans**, um **endereço de Email**, uma **ID de reunião BlueJeans**, uma **Senha de moderador**, um **Nome completo**, da **empresa** de uma conta AAD válida que você deseja configurar para as caixas de texto relacionadas.
    2.  Clique em **Adicionar usuário**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros BlueJeans usuário conta ou APIs fornecidas pela BlueJeans para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-bluejeans-perform-the-following-steps"></a>Para atribuir usuários a BlueJeans, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **BlueJeans **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
