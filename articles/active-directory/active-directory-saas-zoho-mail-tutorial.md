<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com email Zoho | Microsoft Azure" 
    description="Saiba como usar o email Zoho com o Azure Active Directory para habilitar o logon único, automatizado de provisionamento e muito mais!." 
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
    ms.date="09/09/2016" 
    ms.author="markvi" />

#<a name="tutorial-azure-active-directory-integration-with-zoho-mail"></a>Tutorial: Integração com o Active Directory do Azure com Zoho Mail
  
O objetivo deste tutorial é mostrar a integração do Azure e Zoho Mail.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário Zoho Mail
  
Depois de concluir este tutorial, os usuários do Azure AD que atribuiu Zoho postal poderão único entrar no aplicativo no site da sua empresa Zoho Mail (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo Zoho emails
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "Cenário")

##<a name="enabling-the-application-integration-for-zoho-mail"></a>Habilitar a integração de aplicativo Zoho emails
  
O objetivo desta seção é como habilitar a integração de aplicativo para email de Zoho da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-zoho-mail-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Zoho email, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Zoho Mail**.

    ![Galeria de aplicativo] (./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Zoho email**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Email de Zoho] (./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Email de Zoho")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar postal Zoho com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Zoho Mail** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Configurar o logon único")

2.  Na página **como você gostaria que usuários para entrar Zoho Mail** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , execute as seguintes etapas:

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "Configurar a URL do aplicativo")

    a. Na caixa de texto **Zoho Mail entrada na URL** , digite sua URL usando o seguinte padrão:`http://<company name>.ZohoMail.com`

    b. Clique em **Avançar**.


4.  Na página **Configurar logon único na Zoho emails** , clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Zoho email como administrador.

6.  Vá para o **Painel de controle**.

    ![Painel de controle] (./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "Painel de controle")

7.  Clique na guia **Autenticação de SAML** .

    ![Autenticação de SAML] (./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "Autenticação de SAML")

8.  Na seção de **Detalhes de autenticação SAML** , execute as seguintes etapas:

    ![Detalhes de autenticação de SAML] (./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "Detalhes de autenticação de SAML")

    1.  No portal do clássico Azure, na página de diálogo **Configurar logon único no email Zoho** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login** .
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único no email Zoho** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL de Logout** .
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único no email Zoho** , copie o valor de **Alterar senha URL** e cole-o na caixa de texto **Alterar senha URL** .
    4.  Crie um arquivo **codificado na base 64** do seu certificado baixado.  

        >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    5.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo à caixa de texto **PublicKey** .
    6.  Como **algoritmo**, selecione **RSA**.
    7.  Clique em **Okey**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD efetuar logon no email Zoho, ele devem ser provisionados em um email de Zoho.  
No caso de email Zoho, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **Zoho email** como administrador.

2.  Vá para **Painel de controle \> email e documentos**.

3.  Vá para **detalhes do usuário \> adicionar usuário**.

    ![Adicionar usuário] (./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "Adicionar usuário")

4.  Na caixa de diálogo **Adicionar usuários** , execute as seguintes etapas:

    ![Adicionar usuário] (./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "Adicionar usuário")

    1.  Digite o **nome**, o **Sobrenome**, a **ID de Email**e a **senha** de uma conta do Active Directory do Azure válida que deseja provisionar em caixas de texto relacionadas.
    2.  Clique em **Okey**.  

        >[AZURE.NOTE] O proprietário da conta do Active Directory do Azure receberão um email com um link para confirmar a conta antes que ela se torna ativa.

>[AZURE.NOTE] Você pode usar qualquer ferramentas de criação de outros Zoho email usuário conta ou APIs fornecidas pela Zoho Mail para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-zoho-mail-perform-the-following-steps"></a>Para atribuir usuários a Zoho Mail, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Zoho Mail **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).