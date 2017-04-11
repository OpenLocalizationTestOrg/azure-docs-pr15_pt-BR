<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Boomi | Microsoft Azure" 
    description="Saiba como usar Boomi com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-boomi"></a>Tutorial: Integração com o Active Directory do Azure com Boomi

O objetivo deste tutorial é mostrar a integração do Azure e Boomi.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Boomi logon único habilitado para assinatura

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Boomi poderão único entrar no aplicativo no site da sua empresa Boomi (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Boomi
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-boomi-tutorial/IC791134.png "Cenário")
##<a name="enabling-the-application-integration-for-boomi"></a>Habilitar a integração de aplicativo para Boomi

O objetivo desta seção é como habilitar a integração de aplicativo para Boomi da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-boomi-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Boomi, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-boomi-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-boomi-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-boomi-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-boomi-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Boomi**.

    ![Galeria de aplicativo] (./media/active-directory-saas-boomi-tutorial/IC790822.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Boomi**e clique em **concluído** para adicionar o aplicativo.

    ![Boomi] (./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar Boomi com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Boomi** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-boomi-tutorial/IC790824.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Boomi** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-boomi-tutorial/IC790825.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **URL de resposta Boomi** , digite a **URL de logon de AtomSphere Boomi** (por exemplo: "*https://platform.boomi.com/sso/AccountName/saml*") e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-boomi-tutorial/IC790826.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Boomi** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-boomi-tutorial/IC790827.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Boomi como administrador.

6.  Na barra de ferramentas na parte superior, clique em seu nome de empresa e **a instalação**.

    ![Configuração] (./media/active-directory-saas-boomi-tutorial/IC790828.png "Configuração")

7.  Clique em **Opções de SSO**.

    ![Opções de SSO] (./media/active-directory-saas-boomi-tutorial/IC790829.png "Opções de SSO")

8.  Na seção **Opções de logon único** , execute as seguintes etapas:

    ![Opções de logon única] (./media/active-directory-saas-boomi-tutorial/IC790830.png "Opções de logon única")

    1.  Selecione **Habilitar SAML Single Sign-On**.
    2.  Clique em **Importar**, para carregar o certificado baixado.
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Boomi** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login do provedor de identidade** .
    4.  Como **Local de Id de Federação**, selecione a **Id de Federação é no elemento NameID do assunto**.
    5.  Clique em **Salvar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-boomi-tutorial/IC775560.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login Boomi, ele devem ser provisionados em Boomi.  
No caso de Boomi, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **Boomi** como administrador.

2.  Vá para **gerenciamento de usuários \> usuários**.

    ![Usuários] (./media/active-directory-saas-boomi-tutorial/IC790831.png "Usuários")

3.  Clique em **Adicionar usuário**.

    ![Adicionar usuário] (./media/active-directory-saas-boomi-tutorial/IC790832.png "Adicionar usuário")

4.  Na página de diálogo **Adicionar funções de usuário** , execute as seguintes etapas:

    ![Adicionar usuário] (./media/active-directory-saas-boomi-tutorial/IC790833.png "Adicionar usuário")

    1.  Digite o **nome**, **Sobrenome** e **Email** de uma conta AAD válida que você deseja configurar para as caixas de texto relacionadas.
    2.  Clique em Okey.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Boomi usuário conta ou APIs fornecidas pela Boomi para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-boomi-perform-the-following-steps"></a>Para atribuir usuários a Boomi, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Boomi **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-boomi-tutorial/IC790834.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-boomi-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
