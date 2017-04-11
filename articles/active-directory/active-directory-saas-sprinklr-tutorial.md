<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Sprinklr | Microsoft Azure" 
    description="Saiba como usar Sprinklr com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Tutorial: Integração com o Active Directory do Azure com Sprinklr
  
O objetivo deste tutorial é mostrar a integração do Azure e Sprinklr.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário Sprinklr
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Sprinklr poderão único entrar no aplicativo no site da sua empresa Sprinklr (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Sprinklr
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Cenário")

##<a name="enabling-the-application-integration-for-sprinklr"></a>Habilitar a integração de aplicativo para Sprinklr
  
O objetivo desta seção é como habilitar a integração de aplicativo para Sprinklr da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-sprinklr-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Sprinklr, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Sprinklr**.

    ![Galeria de aplicativo] (./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Sprinklr**e clique em **concluído** para adicionar o aplicativo.

    ![Sprinklr] (./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Sprinklr com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Sprinklr** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Sprinklr** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Sprinklr URL de entrada** , digite sua URL usando o seguinte padrão "*https://\<nome do locatário\>. sprinklr.com*" e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Sprinklr** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Sprinklr como administrador.

6.  Vá para **administração \> configurações**.

    ![Administração] (./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administração")

7.  Vá para **parceiro gerenciar \> Single Sign** no painel esquerdo.

    ![Gerenciar o parceiro] (./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Gerenciar o parceiro")

8.  Clique em **+ Complementos do logon único**.

    ![Complementos de logon único] (./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Complementos de logon único")

9.  Na página de **Logon único** , execute as seguintes etapas:

    ![Complementos de logon único] (./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Complementos de logon único")

    1.  Na caixa de texto **nome** , digite um nome para a sua configuração (por exemplo: *WAADSSOTest*).
    2.  Selecione **ativada**.
    3.  Selecione **Usar novo certificado de SSO**.
    4.  Crie um arquivo **codificado na base 64** do seu certificado baixado.  

        >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    5.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo à caixa de texto do **Certificado de provedor de identidade** ,
    6.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Sprinklr** , copie o valor de **ID do provedor de identidade** e, em seguida, cole a caixa de texto de **Id de entidade** .
    7.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Sprinklr** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login do provedor de identidade** .
    8.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Sprinklr** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL de Logout do provedor de identidade** .
    9.  Como o **Tipo de ID de usuário SAML**, selecione **declaração contém usuário "s sprinklr.com username**.
    10. Como **Local de ID de usuário SAML**, selecione a **ID de usuário é no elemento do identificador do nome da política de assunto**.
    11. Feche a **Salvar**.

        ![SAML] (./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para usuários AAD conseguir entrar, eles devem ser provisionados para acesso dentro do aplicativo Sprinklr.  
Esta seção descreve como criar contas de usuário AAD dentro Sprinklr.

###<a name="to-provision-a-user-account-in-sprinklr-perform-the-following-steps"></a>Para configurar uma conta de usuário no Sprinklr, execute as seguintes etapas:

1.  Faça logon em seu site de empresa Sprinklr como administrador.

2.  Vá para **administração \> configurações**.

    ![Administração] (./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administração")

3.  Vá para **cliente gerenciar \> usuários** no painel esquerdo.

    ![Configurações] (./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Configurações")

4.  Clique em **Adicionar usuário**.

    ![Configurações] (./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Configurações")

5.  Na caixa de diálogo **Editar usuário** , execute as seguintes etapas:

    ![Editar usuário] (./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Editar usuário")

    1.  Em **Email**, caixas de texto **nome** e **Sobrenome** , digite as informações de uma conta de usuário do Azure AD deseja provisionar.
    2.  Selecione **senha desabilitada**.
    3.  Selecione um **idioma**.
    4.  Selecione um **tipo de usuário**.
    5.  Clique em **Atualizar**.

    >[AZURE.IMPORTANT] **Senha desabilitado** deve estar marcada para habilitar um usuário efetuar login por meio de um provedor de identidade.

6.  Vá para a **função**e, em seguida, execute as seguintes etapas:

    ![Funções de parceiro] (./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Funções de parceiro")

    1.  Na lista **Global** , selecione **todas as\_permissões**.
    2.  Clique em **Atualizar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Sprinklr usuário conta ou APIs fornecidas pela Sprinklr para provisionar contas de usuário do Azure AD.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-sprinklr-perform-the-following-steps"></a>Para atribuir usuários a Sprinklr, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Sprinklr **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).