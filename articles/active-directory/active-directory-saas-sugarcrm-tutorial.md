<properties 
    pageTitle="Tutorial: Integração de integração Active Directory do Azure com SugarCRM | Microsoft Azure" 
    description="Saiba como usar SugarCRM com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-integration-with-sugarcrm"></a>Tutorial: Integração de integração Active Directory do Azure com SugarCRM
  
O objetivo deste tutorial é mostrar a integração do Azure e açúcar CRM.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um açúcar CRM logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a açúcar CRM poderão único entrar no aplicativo no site da sua empresa açúcar CRM (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para açúcar CRM
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "Cenário")

##<a name="enabling-the-application-integration-for-sugar-crm"></a>Habilitar a integração de aplicativo para açúcar CRM
  
O objetivo desta seção é como habilitar a integração de aplicativo para CRM da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-sugar-crm-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para açúcar CRM, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Açúcar CRM**.

    ![Galeria de aplicativo] (./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Açúcar CRM**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Açúcar CRM] (./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "Açúcar CRM")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar açúcar CRM com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário para carregar um certificado codificado base 64 em seu locatário açúcar CRM.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Da CRM** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "Configurar o logon único")

2.  Na página **como você gostaria que usuários para entrar CRM açúcar** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Da entrada na URL CRM** , digite a URL usada pelos usuários para logon para seu aplicativo de açúcar CRM (por exemplo: "*http://company.sugarondemand.com*" e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em açúcar CRM** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa açúcar CRM como administrador.

6.  Vá para **administrador**.

    ![Administração] (./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Administração")

7.  Na seção **Administração** , clique em **Gerenciamento de senha**.

    ![Administração] (./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "Administração")

8.  Selecione **Habilitar a autenticação de SAML**.

    ![Administração] (./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "Administração")

9.  Na seção **Autenticação de SAML** , execute as seguintes etapas:

    ![Autenticação de SAML] (./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "Autenticação de SAML")

    1.  No portal do clássico Azure, na página de diálogo **Configurar logon único em açúcar CRM** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login** .
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único em açúcar CRM** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL SLO** .
    3.  Crie um arquivo **codificado na Base 64** do seu certificado baixado.

        >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e, em seguida, cole o certificado inteiro na caixa de texto de **Certificado x. 509** .
    5.  Clique em **Salvar**.

10. No portal do clássico Azure, na página de diálogo **Configurar logon único em açúcar CRM** , selecione a confirmação de configuração de logon único e, em seguida, clique em **Concluir**.

    ![Configurar o logon único] (./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD de login da CRM, eles devem ser provisionados açúcar CRM.  
No caso de açúcar CRM, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **Da CRM** como administrador.

2.  Vá para **administrador**.

    ![Administração] (./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Administração")

3.  Na seção **Administração** , clique em **Gerenciamento de usuário**.

    ![Administração] (./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "Administração")

4.  Vá para **usuários \> criar novo usuário**.

    ![Criar novo usuário] (./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "Criar novo usuário")

5.  Na guia **Perfil de usuário** , execute as seguintes etapas:

    ![Novo usuário] (./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "Novo usuário")

    1.  Digite o nome de usuário, o último nome e endereço de email de um usuário válido do Active Directory do Azure para as caixas de texto relacionadas.

6.  Como **Status**, selecione **ativo**.

7.  Na guia senha, execute as seguintes etapas:

    ![Novo usuário] (./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "Novo usuário")

    1.  Digite a senha na caixa de texto relacionado.
    2.  Clique em **Salvar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação outros açúcar CRM usuário conta ou APIs fornecidas pela açúcar CRM para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-sugar-crm-perform-the-following-steps"></a>Para atribuir usuários a açúcar CRM, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Da CRM** , clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).