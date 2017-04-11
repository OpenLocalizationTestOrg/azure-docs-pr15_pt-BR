<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com SmarterU | Microsoft Azure" 
    description="Saiba como usar SmarterU com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Tutorial: Integração com o Active Directory do Azure com SmarterU
  
O objetivo deste tutorial é mostrar a integração do Azure e SmarterU.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário SmarterU
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a SmarterU poderão único entrar no aplicativo no site da sua empresa SmarterU (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para SmarterU
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-smarteru-tutorial/IC777320.png "Cenário")

##<a name="enabling-the-application-integration-for-smarteru"></a>Habilitar a integração de aplicativo para SmarterU
  
O objetivo desta seção é como habilitar a integração de aplicativo para SmarterU da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-smarteru-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para SmarterU, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-smarteru-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-smarteru-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-smarteru-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-smarteru-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **SmarterU**.

    ![Fallery de aplicativo] (./media/active-directory-saas-smarteru-tutorial/IC777321.png "Fallery de aplicativo")

7.  No painel de resultados, selecione **SmarterU**e clique em **concluído** para adicionar o aplicativo.

    ![SmarterU] (./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar SmarterU com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **SmarterU** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-smarteru-tutorial/IC777323.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar SmarterU** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-smarteru-tutorial/IC777324.png "Configurar o logon único")

3.  **Configurar logon único em SmarterU** página, para baixar os metadados, clique em **baixar metadados**e, em seguida, o arquivo de dados localmente como **c:\\SmarterUMetaData.cer**.

    ![Configurar o logon único] (./media/active-directory-saas-smarteru-tutorial/IC777325.png "Configurar o logon único")

4.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa SmarterU como administrador.

5.  Na barra de ferramentas na parte superior, clique em **Configurações de conta**.

    ![Configurações de conta] (./media/active-directory-saas-smarteru-tutorial/IC777326.png "Configurações de conta")

6.  Na página de configuração de conta, execute as seguintes etapas:

    ![Autorização externa] (./media/active-directory-saas-smarteru-tutorial/IC777327.png "Autorização externa")

    1.  Selecione **Habilitar autorização externa**.
    2.  Na seção **Controle de Login de mestre** , selecione a guia **SmarterU** .
    3.  Na seção de **Logon do usuário padrão** , selecione a guia **SmarterU** .
    4.  Selecione **Ativar Okta**.
    5.  Copie o conteúdo do arquivo baixado metadados e cole-o na caixa de texto **Okta metadados** .
    6.  Clique em **Salvar**.

7.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-smarteru-tutorial/IC777328.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login SmarterU, ele devem ser provisionados em SmarterU.  
No caso de SmarterU, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **SmarterU** .

2.  Vá para **usuários**.

3.  Na seção de usuário, execute as seguintes etapas:

    ![Novo usuário] (./media/active-directory-saas-smarteru-tutorial/IC777329.png "Novo usuário")

    1.  Clique em **+ usuário**.
    2.  Digite os valores de atributo relacionado da conta de usuário do Azure AD nos seguintes caixas de texto: **principal de Email**, **ID do funcionário**, **senha**, **Verifique se a senha**, **dado nome**, **Sobrenome**.
    3.  Clique em **ativo**.
    4.  Clique em **Salvar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros SmarterU usuário conta ou APIs fornecidas pela SmarterU para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-smarteru-perform-the-following-steps"></a>Para atribuir usuários a SmarterU, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **SmarterU **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-smarteru-tutorial/IC777330.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-smarteru-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).