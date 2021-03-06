<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com dois Zscaler | Microsoft Azure" 
    description="Saiba como usar dois Zscaler com o Azure Active Directory para habilitar o logon único, automatizado de provisionamento e muito mais!." 
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

#<a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Tutorial: Integração com o Active Directory do Azure com Zscaler dois

O objetivo deste tutorial é mostrar a integração do Azure e ZScaler dois.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um ZScaler duas logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a ZScaler dois poderão logon único para o aplicativo em seu site de empresa ZScaler dois (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md)
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para ZScaler dois
2.  Configurando o logon único
3.  Definir configurações de proxy
4.  Configuração de provisionamento do usuário
5.  Atribuir usuários

![Configurar o logon único] (./media/active-directory-saas-zscaler-two-tutorial/IC800199.png "Configurar o logon único")

##<a name="enabling-the-application-integration-for-zscaler-two"></a>Habilitar a integração de aplicativo para ZScaler dois
  
O objetivo desta seção é como habilitar a integração de aplicativo para ZScaler dois da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-zscaler-two-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para ZScaler duas, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-zscaler-two-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-zscaler-two-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-zscaler-two-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-zscaler-two-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **ZScaler dois**.

    ![Galeria de aplicativo] (./media/active-directory-saas-zscaler-two-tutorial/IC800200.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **ZScaler dois**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![ZScaler dois] (./media/active-directory-saas-zscaler-two-tutorial/IC800201.png "ZScaler dois")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar ZScaler dois com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário para carregar um certificado codificado base 64 em seu locatário ZScaler dois.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de **Dois ZScaler** integração de aplicativo, clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-zscaler-two-tutorial/IC800202.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar ZScaler duas** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-zscaler-two-tutorial/IC800203.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **ZScaler dois entrada na URL** , digite a URL usada pelos usuários para logon para seu aplicativo ZScaler dois e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-zscaler-two-tutorial/IC800204.png "Configurar a URL do aplicativo")

    >[AZURE.NOTE] Você pode obter o valor real para seu ambiente de sua equipe de suporte ZScaler dois caso necessário.

4.  Na página **Configurar logon único em dois ZScaler** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-zscaler-two-tutorial/IC800205.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa ZScaler dois como administrador.

6.  No menu na parte superior, clique em **Administração**.

    ![Administração] (./media/active-directory-saas-zscaler-two-tutorial/IC800206.png "Administração")

7.  Em **Gerenciar administradores & funções**, clique em **Gerenciar usuários e autenticação**.

    ![Gerenciar usuários e autenticação] (./media/active-directory-saas-zscaler-two-tutorial/IC800207.png "Gerenciar usuários e autenticação")

8.  Na seção **Escolher opções de autenticação para a sua organização** , execute as seguintes etapas:

    ![Autenticação] (./media/active-directory-saas-zscaler-two-tutorial/IC800208.png "Autenticação")

    1.  Selecione **autenticar usando SAML Single Sign-On**.
    2.  Clique em **Configurar parâmetros de logon único SAML**.

9.  Na página de diálogo **Configurar SAML Single Sign-On parâmetros** , execute as seguintes etapas e clique em **concluído**:

    ![Logon único] (./media/active-directory-saas-zscaler-two-tutorial/IC800209.png "Logon único")

    1.  No portal clássico do Azure, na página de diálogo **Configurar logon único em dois ZScaler** , copie o valor de **URL de solicitação de autenticação** e, em seguida, cole o **URL do Portal SAML ao qual os usuários são enviados para a autenticação** de texto.
    2.  Na caixa de texto de **atributo que contém o nome de Login** , digite **NameID**.
    3.  Para carregar seu certificado baixado, clique em **Zscaler pem**.
    4.  Selecione **Habilitar SAML o provisionamento automático**.

10. Na página de diálogo **Configurar a autenticação do usuário** , execute as seguintes etapas:

    ![Administração] (./media/active-directory-saas-zscaler-two-tutorial/IC800210.png "Administração")

    1.  Clique em **Salvar**.
    2.  Clique em **Ativar agora**.

11. No portal do clássico Azure, na página de diálogo **Configurar logon único em dois ZScaler** , selecione a confirmação de configuração de logon único e, em seguida, clique em **Concluir**.

    ![Configurar o logon único] (./media/active-directory-saas-zscaler-two-tutorial/IC800211.png "Configurar o logon único")

##<a name="configuring-proxy-settings"></a>Definir configurações de proxy

###<a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir as configurações de proxy no Internet Explorer

1.  Inicie o **Internet Explorer**.

2.  Selecione **Opções** da Internet no menu **Ferramentas** para abrir a caixa de diálogo **Opções da Internet** .

    ![Opções da Internet] (./media/active-directory-saas-zscaler-two-tutorial/IC769492.png "Opções da Internet")

3.  Clique na guia **conexões** .

    ![Conexões] (./media/active-directory-saas-zscaler-two-tutorial/IC769493.png "Conexões")

4.  Clique em **configurações da LAN** para abrir a caixa de diálogo **Configurações da LAN** .

5.  Na seção de servidor Proxy, execute as seguintes etapas:

    ![Servidor proxy] (./media/active-directory-saas-zscaler-two-tutorial/IC769494.png "Servidor proxy")

    1.  Selecione usar um servidor proxy para sua LAN.
    2.  Na caixa de texto endereço, digite **gateway.zscalerone.net**.
    3.  Na caixa de texto porta, digite **80**.
    4.  Selecione **Ignorar servidor proxy para endereços locais**.
    5.  Clique **Okey** para fechar a caixa de diálogo **configurações da rede Local (LAN)** .

6.  Clique **Okey** para fechar a caixa de diálogo **Opções da Internet** .

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login ZScaler duas, ele devem ser provisionados para ZScaler dois.  
No caso de dois ZScaler, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **Zscaler** .

2.  Clique em **Administração**.

    ![Administração] (./media/active-directory-saas-zscaler-two-tutorial/IC781035.png "Administração")

3.  Clique em **gerenciamento de usuário**.

    ![Adicionar] (./media/active-directory-saas-zscaler-two-tutorial/IC781037.png "Adicionar")

4.  Na guia **usuários** , clique em **Adicionar**.

    ![Adicionar] (./media/active-directory-saas-zscaler-two-tutorial/IC781037.png "Adicionar")

5.  Na seção Adicionar usuário, execute as seguintes etapas:

    ![Adicionar usuário] (./media/active-directory-saas-zscaler-two-tutorial/IC781038.png "Adicionar usuário")

    1.  Digite a **ID de usuário** **Nome de exibição do usuário**, **senha**, **Confirme a senha**e selecione o **departamento** de uma conta AAD válida que deseja provisionar e **grupos** .
    2.  Clique em **Salvar**.

>[AZURE.NOTE] Você pode usar outras duas ZScaler usuário conta criação ferramentas ou APIs fornecidas pela ZScaler dois para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-zscaler-two-perform-the-following-steps"></a>Para atribuir usuários a ZScaler duas, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de **Dois ZScaler** integração de aplicativo, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-zscaler-two-tutorial/IC800212.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-zscaler-two-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).