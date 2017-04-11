<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Zscaler | Microsoft Azure" 
    description="Saiba como usar Zscaler com o Azure Active Directory para habilitar o logon único, automatizado de provisionamento e muito mais!." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Tutorial: Integração com o Active Directory do Azure com Zscaler
  
O objetivo deste tutorial é mostrar a integração do Azure e Zscaler. O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário em Zscaler
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Zscaler
2.  Configurando o logon único
3.  Definir configurações de proxy
4.  Configuração de provisionamento do usuário
5.  Atribuir usuários

![Cenário] (./media/active-directory-saas-zscaler-tutorial/IC769226.png "Cenário")

##<a name="enabling-the-application-integration-for-zscaler"></a>Habilitar a integração de aplicativo para Zscaler
  
O objetivo desta seção é como habilitar a integração de aplicativo para Zscaler da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-zscaler-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Zscaler, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-zscaler-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-zscaler-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-zscaler-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-zscaler-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Zscaler**.

    ![Galeria de aplicativo] (./media/active-directory-saas-zscaler-tutorial/IC769227.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Zscaler**e clique em **concluído** para adicionar o aplicativo.

    ![Zscaler] (./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Zscaler com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, você é necessárias para carregar um certificado no Zscaler.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Zscaler** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Habilitar o logon único] (./media/active-directory-saas-zscaler-tutorial/IC769229.png "Habilitar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Zscaler** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar único entrar em] (./media/active-directory-saas-zscaler-tutorial/IC769230.png "Configurar único entrar em")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Zscaler URL de entrada** , digite sua entrada na URL obtido do Zscaler e clique em **Avançar**: 

    >[AZURE.NOTE] Entre em contato com a equipe de suporte Zscaler se você não souber qual é sua entrada da URL.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-zscaler-tutorial/IC769231.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Zscaler** , execute as seguintes etapas:

    ![Configurar o logon único] (./media/active-directory-saas-zscaler-tutorial/IC769232.png "Configurar o logon único")

    1.  Clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado localmente como **c:\\Zscaler.cer**.
    2.  Copie a **URL de solicitação de autenticação** para sua área de transferência.

5.  Entre em seu locatário Zscaler.

6.  No menu na parte superior, clique em **Administração**.

    ![Administração] (./media/active-directory-saas-zscaler-tutorial/IC769486.png "Administração")

7.  Em **Gerenciar administradores & funções**, clique em **Gerenciar usuários e autenticação**.

    ![Gerenciar administradores e funções] (./media/active-directory-saas-zscaler-tutorial/IC769487.png "Gerenciar administradores e funções")

8.  Na seção **Escolher a opção de autenticação para a sua organização** , execute as seguintes etapas:

    ![Escolha opções de autenticação] (./media/active-directory-saas-zscaler-tutorial/IC769488.png "Escolha opções de autenticação")

    1.  Selecione **autenticar usando SAML Single Sign-on**.
    2.  Clique em **Configurar parâmetros de logon único SAML**.

9.  Na página de diálogo **Configurar SAML Single Sign-On parâmetros** , execute as seguintes etapas e clique em **concluído**:

    ![Carregar certificado] (./media/active-directory-saas-zscaler-tutorial/IC769489.png "Carregar certificado")

    1.  Na caixa de texto **URL do Portal SAML ao qual os usuários são enviados para autenticação** , cole o valor do campo **URL de solicitação de autenticação** do Azure portal clássico.
    2.  Na caixa de texto de **atributo que contém o nome de Login** , digite **NameID**.
    3.  No campo **Carregar certificado SSL de público** , carregue o certificado que você baixou a partir do portal clássico Azure.
    4.  Selecione **Habilitar SAML o provisionamento automático**.

10. Na página de diálogo **Configurar a autenticação do usuário** , execute as seguintes etapas:

    ![Configurar autenticação de usuário] (./media/active-directory-saas-zscaler-tutorial/IC769490.png "Configurar autenticação de usuário")

    1.  Clique em **Salvar**.
    2.  Clique em **Ativar agora**.

11. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-zscaler-tutorial/IC769491.png "Configurar o logon único")

##<a name="configuring-proxy-settings"></a>Definir configurações de proxy

###<a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir as configurações de proxy no Internet Explorer

1.  Inicie o **Internet Explorer**.

2.  Selecione **Opções** da Internet no menu **Ferramentas** para abrir a caixa de diálogo **Opções da Internet** .

    ![Opções da Internet] (./media/active-directory-saas-zscaler-tutorial/IC769492.png "Opções da Internet")

3.  Clique na guia **conexões** .

    ![Conexões] (./media/active-directory-saas-zscaler-tutorial/IC769493.png "Conexões")

4.  Clique em **configurações da LAN** para abrir a caixa de diálogo **Configurações da LAN** .

5.  Na seção de servidor Proxy, execute as seguintes etapas:

    ![Servidor proxy] (./media/active-directory-saas-zscaler-tutorial/IC769494.png "Servidor proxy")

    1.  Selecione usar um servidor proxy para sua LAN.
    2.  Na caixa de texto endereço, digite **gateway.zscalertwo.net**.
    3.  Na caixa de texto porta, digite **80**.
    4.  Selecione **Ignorar servidor proxy para endereços locais**.
    5.  Clique **Okey** para fechar a caixa de diálogo **configurações da rede Local (LAN)** .

6.  Clique **Okey** para fechar a caixa de diálogo **Opções da Internet** .

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Zscaler, ele devem ser provisionados em Zscaler.  
No caso de Zscaler, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **Zscaler** .

2.  Clique em **Administração**.

    ![Administração] (./media/active-directory-saas-zscaler-tutorial/IC781035.png "Administração")

3.  Clique em **gerenciamento de usuário**.

    ![Gerenciamento de usuários] (./media/active-directory-saas-zscaler-tutorial/IC781036.png "Gerenciamento de usuários")

4.  Na guia **usuários** , clique em **Adicionar**.

    ![Adicionar] (./media/active-directory-saas-zscaler-tutorial/IC781037.png "Adicionar")

5.  Na seção Adicionar usuário, execute as seguintes etapas:

    ![Adicionar usuário] (./media/active-directory-saas-zscaler-tutorial/IC781038.png "Adicionar usuário")

    1.  Digite a **ID de usuário** **Nome de exibição do usuário**, **senha**, **Confirme a senha**e selecione o **departamento** de uma conta AAD válida que deseja provisionar e **grupos** .
    2.  Clique em **Salvar**.

>[AZURE.NOTE] Você pode usar qualquer ferramenta de criação outros Zscaler usuário conta ou APIs fornecidas pela Zscaler para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-zscaler-perform-the-following-steps"></a>Para atribuir usuários a Zscaler, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Zscaler** , clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-zscaler-tutorial/IC769495.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-zscaler-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
