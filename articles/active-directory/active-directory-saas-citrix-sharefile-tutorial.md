<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Citrix ShareFile | Microsoft Azure" 
    description="Saiba como usar o Citrix ShareFile com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Tutorial: Integração com o Active Directory do Azure com Citrix ShareFile

O objetivo deste tutorial é mostrar a integração do Azure e Citrix ShareFile.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário Citrix ShareFile

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Citrix ShareFile poderão único entrar no aplicativo no site da sua empresa Citrix ShareFile (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Citrix ShareFile
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Cenário")
##<a name="enabling-the-application-integration-for-citrix-sharefile"></a>Habilitar a integração de aplicativo para Citrix ShareFile

O objetivo desta seção é como habilitar a integração de aplicativo para Citrix ShareFile da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-citrix-sharefile-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Citrix ShareFile, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Citrix ShareFile**.

    ![Galeria de aplicativo] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Citrix ShareFile**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Citrix ShareFile] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar Citrix ShareFile com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo do **Citrix ShareFile** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Habilitar o logon único] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Habilitar o logon único")

2.  Na página **como você gostaria que usuários para entrar Citrix ShareFile** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Citrix ShareFile entrada na URL** , digite sua URL usando o seguinte padrão `https://<tenant-name>.shareFile.com`e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Citrix ShareFile** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.

    ![ConfigureSingle logon] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "ConfigureSingle logon")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa **Citrix ShareFile** como administrador.

6.  Na barra de ferramentas na parte superior, clique em **administrador**.

7.  No painel de navegação esquerdo, selecione **Configurar Single Sign-On**.

    ![Administração de conta] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Administração de conta")

8.  Sobre o **Single Sign-On / SAML 2.0 configuração** página de diálogo em **Configurações básicas**, execute as seguintes etapas:

    ![Logon único] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "Logon único")

    1.  Clique em **Ativar SAML**.
    2.  No portal clássico do Azure, na página de diálogo **Configurar logon único em Citrix ShareFile** , copie o valor de **Identificação de entidade** e colá-lo na **o emissor IDP / ID da entidade** caixa de texto.
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Citrix ShareFile** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login** .
    4.  No portal do clássico Azure, na página de diálogo **a configurar logon único em Citrix ShareFile** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL de Logout** .
    5.  Clique em **Alterar** ao lado do campo de **Certificado x. 509** e carregue o certificado que você baixou a partir do portal de clássico do Azure AD.
        ![Configurações básicas] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Configurações básicas")

9.  Clique em **Salvar** no portal de gerenciamento do Citrix ShareFile.

10. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login Citrix ShareFile, eles devem ser provisionados em Citrix ShareFile.  
No caso de Citrix ShareFile, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **Citrix ShareFile** .

2.  Clique em **Gerenciar usuários \> gerenciar usuários Home \> + criar funcionário**.

    ![Criar funcionário] (./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Criar funcionário")

3.  Insira o **Email**, **nome** e **Sobrenome** de um anúncio Azure válido de conta que você deseja provisionar.

    ![Informações básicas] (./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Informações básicas")

4.  Clique em **Adicionar usuário**.

    >[AZURE.NOTE] O proprietário de conta AAD será recebe um email e siga um link para confirmar sua conta antes que ele fique ativo.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Citrix ShareFile usuário conta ou APIs fornecidas pela Citrix ShareFile para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-citrix-sharefile-perform-the-following-steps"></a>Para atribuir usuários a Citrix ShareFile, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo do **Citrix ShareFile **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
