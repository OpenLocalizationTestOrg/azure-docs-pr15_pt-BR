<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com LogicMonitor | Microsoft Azure" 
    description="Saiba como usar LogicMonitor com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Tutorial: Integração com o Active Directory do Azure com LogicMonitor
  
O objetivo deste tutorial é mostrar a integração do Azure e LogicMonitor.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário LogicMonitor
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para LogicMonitor
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Cenário")
##<a name="enabling-the-application-integration-for-logicmonitor"></a>Habilitar a integração de aplicativo para LogicMonitor
  
O objetivo desta seção é como habilitar a integração de aplicativo para LogicMonitor da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-logicmonitor-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para LogicMonitor, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **logicmonitor**.

    ![Galeria de aplicativo] (./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **LogicMonitor**e clique em **concluído** para adicionar o aplicativo.

    ![LogicMonitor] (./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar LogicMonitor com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **LogicMonitor **, clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar LogicMonitor** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para assinar LogicMonitor \(e, g,: "*http://company.logicmonitor.com*"\)e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em LogicMonitor** , clique em **baixar metadados**e, em seguida, salve-o no seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Configurar o logon único")

5.  Faça logon site da sua empresa **LogicMonitor** como administrador.

6.  No menu na parte superior, clique em **configurações**.

    ![Configurações] (./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Configurações")

7.  No imediatamente navegação no lado esquerdo, clique em **Logon único**

    ![Logon único] (./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Logon único")

8.  Na seção **configurações de logon único (SSO)** , execute as seguintes etapas:

    ![Configurações de logon único] (./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Configurações de logon único")

    1.  Selecione **Habilitar logon único**.
    2.  Como a **Atribuição de função padrão**, selecione **somente leitura**.
    3.  Abra o arquivo baixado metadados no bloco de notas e, em seguida, colar o conteúdo do arquivo na caixa de texto de **Metadados de provedor de identidade** .
    4.  Clique em **Salvar alterações**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para usuários AAD conseguir entrar, ele devem ser provisionados para o aplicativo de LogicMonitor usando seus nomes de usuário do Active Directory do Azure.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa LogicMonitor como administrador.

2.  No menu na parte superior, clique em **configurações**e, em seguida, clique em **usuários e funções**.

    ![Funções e usuários] (./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Funções e usuários")

3.  Clique em **Adicionar**.

4.  Na seção **Adicionar uma conta** , execute as seguintes etapas:

    ![Adicionar uma conta] (./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Adicionar uma conta")

    1.  Digite os valores de **nome de usuário**, **Email**, **senha** e **Confirmar senha** do usuário do Active Directory do Azure que deseja provisionar em caixas de texto relacionadas.
    2.  Selecione **funções**, **Exibir permissões** e o **Status**.
    3.  Clique em **Enviar**.

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros LogicMonitor usuário conta ou APIs fornecidas pela LogicMonitor provisionar o Active Directory do Azure contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-logicmonitor-perform-the-following-steps"></a>Para atribuir usuários a LogicMonitor, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **LogicMonitor** , clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).




