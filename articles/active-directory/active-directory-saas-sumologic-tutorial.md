<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com SumoLogic | Microsoft Azure" 
    description="Saiba como usar SumoLogic com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Tutorial: Integração com o Active Directory do Azure com SumoLogic
  
O objetivo deste tutorial é mostrar a integração do Azure e SumoLogic.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário SumoLogic
  
Depois de concluir este tutorial, os usuários do Azure AD você atribuiu a SumoLogicwill ser capaz de única entrada para o aplicativo em seu SumoLogic empresa site (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para SumoLogic
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-sumologic-tutorial/IC778549.png "Cenário")

##<a name="enabling-the-application-integration-for-sumologic"></a>Habilitar a integração de aplicativo para SumoLogic
  
O objetivo desta seção é como habilitar a integração de aplicativo para SumoLogic da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-sumologic-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para SumoLogic, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-sumologic-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-sumologic-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-sumologic-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-sumologic-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **sumologic**.

    ![Galeria de aplicativo] (./media/active-directory-saas-sumologic-tutorial/IC778550.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **SumoLogic**e clique em **concluído** para adicionar o aplicativo.

    ![SumoLogic] (./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar SumoLogic com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário para carregar um certificado codificado base 64 em seu SumoLogictenant.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **SumoLogic** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-sumologic-tutorial/IC778552.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar SumoLogic** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-sumologic-tutorial/IC778553.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **SumoLogic URL de entrada** , digite sua URL usando o seguinte padrão "https://*\<-nome do locatário\>. SumoLogic.com*"e, em seguida, clique em **Avançar**.

    ![Configurar aoo URL] (./media/active-directory-saas-sumologic-tutorial/IC778554.png "Configurar aoo URL")

4.  Na página **Configurar logon único em SumoLogic** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-sumologic-tutorial/IC778555.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa SumoLogic como administrador.

6.  Vá para **Gerenciar \> segurança**.

    ![Gerenciar] (./media/active-directory-saas-sumologic-tutorial/IC778556.png "Gerenciar")

7.  Clique em **SAML**.

    ![Configurações de segurança global] (./media/active-directory-saas-sumologic-tutorial/IC778557.png "Configurações de segurança global")

8.  Na lista **Selecione uma configuração ou crie um novo** , selecione **Azure AD**e clique em **Configurar**.

    ![Configurar SAML 2.0] (./media/active-directory-saas-sumologic-tutorial/IC778558.png "Configurar SAML 2.0")

9.  Na caixa de diálogo **Configurar SAML 2.0** , execute as seguintes etapas:

    ![Configurar SAML 2.0] (./media/active-directory-saas-sumologic-tutorial/IC778559.png "Configurar SAML 2.0")

    1.  Na caixa de texto **Nome de configuração** , digite **Azure AD**.
    2.  Selecione **o modo de depuração**.
    3.  No portal do clássico Azure, na página da caixa de diálogo **Configurar logon único em SumoLogic** , copie o valor de **URL do emissor** e, em seguida, cole a caixa de texto do **emissor** .
    4.  No portal do clássico Azure, na página da caixa de diálogo **Configurar logon único em SumoLogic** , copie o valor de **URL de solicitação de autenticação** e, em seguida, cole a caixa de texto **URL de solicitação de Authn** .
    5.  Crie um arquivo **codificado na Base 64** do seu certificado baixado.  

        >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e, em seguida, cole o certificado inteiro na caixa de texto de **Certificado x. 509** .
    7.  Como **Atributo de Email**, selecione **usar SAML assunto**.
    8.  Selecione **SP iniciou a configuração de logon**.
    9.  Na caixa de texto **Caminho de Login** , digite **Azure**.
    10. Clique em **Salvar**.

10. No portal do clássico Azure, na página da caixa de diálogo **Configurar logon único em SumoLogic** , selecione a confirmação de configuração de logon único e, em seguida, clique em **Concluir**.

    ![Configurar o logon único] (./media/active-directory-saas-sumologic-tutorial/IC778560.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login SumoLogic, ele devem ser provisionados para SumoLogic.  
No caso de SumoLogic, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **SumoLogic** .

2.  Vá para **Gerenciar \> usuários**.

    ![Usuários] (./media/active-directory-saas-sumologic-tutorial/IC778561.png "Usuários")

3.  Clique em **Adicionar**.

    ![Usuários] (./media/active-directory-saas-sumologic-tutorial/IC778562.png "Usuários")

4.  Na caixa de diálogo **Novo usuário** , execute as seguintes etapas:

    ![Novo usuário] (./media/active-directory-saas-sumologic-tutorial/IC778563.png "Novo usuário")

    1.  Digite as informações relacionadas da conta do Azure AD que deseja provisionar em caixas de texto **nome**, **Sobrenome** e **Email** .
    2.  Selecione uma função.
    3.  Como **Status**, selecione **ativo**.
    4.  Clique em **Salvar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros SumoLogic usuário conta ou APIs fornecidas pela SumoLogic para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-sumologic-perform-the-following-steps"></a>Para atribuir usuários a SumoLogic, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **SumoLogic** , clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-sumologic-tutorial/IC778564.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-sumologic-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).