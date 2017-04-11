<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Software de iglu | Microsoft Azure" 
    description="Saiba como usar o Software de iglu com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="10/20/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Tutorial: Integração com o Active Directory do Azure com Software de iglu
  
O objetivo deste tutorial é mostrar a integração do Azure e iglu Software.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um [Software de iglu](http://www.igloosoftware.com/) logon único habilitado assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que atribuiu ao Software de iglu poderão único entrar no aplicativo no site da sua empresa iglu Software (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo de Software de iglu
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-igloo-software-tutorial/IC783961.png "Cenário")
##<a name="enabling-the-application-integration-for-igloo-software"></a>Habilitar a integração de aplicativo de Software de iglu
  
O objetivo desta seção é como habilitar a integração de aplicativo de Software de iglu da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-igloo-software-perform-the-following-steps"></a>Para habilitar a integração de aplicativo de Software de iglu, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-igloo-software-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-igloo-software-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-igloo-software-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Iglu Software**.

    ![Galeria de aplicativo] (./media/active-directory-saas-igloo-software-tutorial/IC783962.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Iglu Software**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Iglu] (./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Iglu")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar ao Software de iglu com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário para carregar um certificado codificado base 64 em seu locatário de área de trabalho Central.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo do **Software de iglu** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-igloo-software-tutorial/IC783964.png "Configurar o logon único")

2.  Na página **como você gostaria que usuários para entrar Software de iglu** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Microsoft Azure AD Single Sign-On")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Iglu Software URL de entrada** , digite sua URL usando o seguinte padrão "*https://company.igloocommunities.com/?signin*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-igloo-software-tutorial/IC773625.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único na iglu Software** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-igloo-software-tutorial/IC783966.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa de Software de iglu como administrador.

6.  Vá para o **Painel de controle**.

    ![Painel de controle] (./media/active-directory-saas-igloo-software-tutorial/IC799949.png "Painel de controle")

7.  Na guia **associação** , clique em **Configurações de entrada**.

    ![Entrar nas configurações] (./media/active-directory-saas-igloo-software-tutorial/IC783968.png "Entrar nas configurações")

8.  Na seção Configuração de SAML, clique em **Configurar a autenticação de SAML**.

    ![Configuração de SAML] (./media/active-directory-saas-igloo-software-tutorial/IC783969.png "Configuração de SAML")

9.  Na seção **Configuração geral** , execute as seguintes etapas:

    ![Configuração geral] (./media/active-directory-saas-igloo-software-tutorial/IC783970.png "Configuração geral")

    1.  Na caixa de texto **Nome da Conexão** , digite um nome personalizado para a sua configuração.
    2.  No portal do clássico Azure, na página da caixa de diálogo **Configurar logon único na iglu Software** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de logon IdP** .
    3.  No portal do clássico Azure, na página da caixa de diálogo **Configurar logon único na iglu Software** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **IdP Logout URL** .
    4.  Como **resposta de Logout e Request HTTP Type**, selecione **postar**.
    5.  Crie um arquivo de texto do certificado baixado.
        
        >[AZURE.TIP]Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Remover a primeira linha e a última linha da versão de arquivo de texto do seu certificado, copie o texto restante do certificado e, em seguida, cole a caixa de texto de **Certificado pública** .

10. Na **resposta e configuração de autenticação**, execute as seguintes etapas:

    ![Configuração de autenticação e resposta] (./media/active-directory-saas-igloo-software-tutorial/IC783971.png "Configuração de autenticação e resposta")

    1.  Como **Provedor de identidade**, selecione **Microsoft ADFS**.
    2.  Como **Identificador de tipo**, selecione o **Endereço de Email**.
    3.  Na caixa de texto do **Atributo de Email** , digite **emailaddress**.
    4.  Na caixa de texto do **Atributo de nome** , digite **givenname**.
    5.  Na caixa de texto do **Último atributo de nome** , digite **Sobrenome**.

11. Executar as seguintes etapas para concluir a configuração:

    ![Criação de usuário na entrada] (./media/active-directory-saas-igloo-software-tutorial/IC783972.png "Criação de usuário na entrada")

    1.  Como a **criação de usuário na entrada**, selecione **criar um novo usuário em seu site quando eles entrar**.
    2.  Como **entrar em configurações**, selecione o **botão de uso SAML na tela "Entrar"**.
    3.  Clique em **Salvar**.

12. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-igloo-software-tutorial/IC783973.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Não há nenhum item de ação de configurar provisionamento do usuário ao Software de iglu.  
Quando um usuário atribuído tenta efetuar login no Software de iglu usando o painel de acesso, o Software de iglu verifica se o usuário existe.  
Se houver nenhuma conta de usuário ainda disponível, ele será criado automaticamente pelo Software iglu.
##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-igloo-software-perform-the-following-steps"></a>Para atribuir usuários a iglu Software, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo de **Software iglu **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-igloo-software-tutorial/IC783974.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-igloo-software-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).