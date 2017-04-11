<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Pagerduty | Microsoft Azure" 
    description="Saiba como usar Pagerduty com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Tutorial: Integração com o Active Directory do Azure com Pagerduty
  
O objetivo deste tutorial é mostrar a integração do Azure e Pagerduty.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário Pagerduty
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Pagerduty poderão único entrar no aplicativo no site da sua empresa Pagerduty (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Pagerduty
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Cenário")
##<a name="enabling-the-application-integration-for-pagerduty"></a>Habilitar a integração de aplicativo para Pagerduty
  
O objetivo desta seção é como habilitar a integração de aplicativo para Pagerduty da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-pagerduty-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Pagerduty, execute as seguintes etapas:

1.  No Portal de gerenciamento do Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Pagerduty**.

    ![Galeria de aplicativo] (./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Pagerduty**e clique em **concluído** para adicionar o aplicativo.

    ![PagerDuty] (./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Pagerduty com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Pagerduty** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Pagerduty** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Pagerduty URL de entrada** , digite sua URL usando o seguinte padrão "https://*\<-nome do locatário\>. Pagerduty.com*"e, em seguida, clique em **Avançar**.

    ![Configurar aplicativo url] (./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Configurar aplicativo url")

4.  Na página **Configurar logon único em Pagerduty** , clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Pagerduty como administrador.

6.  No menu na parte superior, clique em **Configurações de conta**.

    ![Configurações de conta] (./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Configurações de conta")

7.  Clique em **logon único**.

    ![Logon único] (./media/active-directory-saas-pagerduty-tutorial/IC778536.png "Logon único")

8.  Na página de habilitar o logon único (SSO), execute as seguintes etapas:

    ![Habilitar o logon único] (./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Habilitar o logon único")

    1.  Crie um arquivo **codificado na base 64** do seu certificado baixado.  

        >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    2.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo para a caixa de texto de **Certificado x. 509**
    3.  No portal do clássico Azure, na página da caixa de diálogo **Configurar logon único em Pagerduty** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login** .
    4.  No portal do clássico Azure, na página da caixa de diálogo **Configurar logon único em Pagerduty** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL de Logout** .
    5.  Selecione **Ativar o logon único**.
    6.  Clique em **Salvar alterações**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Pagerduty, ele devem ser provisionados em Pagerduty.  
No caso de Pagerduty, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **Pagerduty** .

2.  No menu na parte superior, clique em **usuários**.

3.  Clique em **Adicionar usuários**.

    ![Adicionar usuários] (./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Adicionar usuários")

4.  Na caixa de diálogo **Convidar sua equipe** , digite o **primeiro e último nome** e o endereço de **Email** do usuário do Azure AD que deseja provisionar, clique em **Adicionar**e clique em **Enviar convites**.

    ![Convidar sua equipe] (./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Convidar sua equipe")

    >[AZURE.NOTE] Todos os usuários adicionados receberá um convite para criar uma conta de PagerDuty.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Pagerduty usuário conta ou APIs fornecidas pela Pagerduty para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-pagerduty-perform-the-following-steps"></a>Para atribuir usuários a Pagerduty, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Pagerduty **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).