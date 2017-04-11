<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Picturepark | Microsoft Azure" 
    description="Saiba como usar Picturepark com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Tutorial: Integração com o Active Directory do Azure com Picturepark
  
O objetivo deste tutorial é mostrar a integração do Azure e Picturepark.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário Picturepark
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Picturepark poderão único entrar no aplicativo no site da sua empresa Picturepark (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Picturepark
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-picturepark-tutorial/IC795055.png "Cenário")

##<a name="enabling-the-application-integration-for-picturepark"></a>Habilitar a integração de aplicativo para Picturepark
  
O objetivo desta seção é como habilitar a integração de aplicativo para Picturepark da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-picturepark-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Picturepark, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-picturepark-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-picturepark-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-picturepark-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-picturepark-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Picturepark**.

    ![Galeria de aplicativo] (./media/active-directory-saas-picturepark-tutorial/IC795056.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Picturepark**e clique em **concluído** para adicionar o aplicativo.

    ![Picturepark] (./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Picturepark com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para Picturepark requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)..

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Picturepark** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-picturepark-tutorial/IC795058.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Picturepark** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-picturepark-tutorial/IC795059.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Picturepark entrada na URL** , digite sua URL usando o seguinte padrão "*http://company.picturepark.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-picturepark-tutorial/IC795060.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Picturepark** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-picturepark-tutorial/IC795061.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Picturepark como administrador.

6.  Na barra de ferramentas na parte superior, clique em **Ferramentas administrativas**e clique em **Console de gerenciamento**.

    ![Console de gerenciamento] (./media/active-directory-saas-picturepark-tutorial/IC795062.png "Console de gerenciamento")

7.  Clique em **autenticação**e clique em **provedores de identidade**.

    ![Autenticação] (./media/active-directory-saas-picturepark-tutorial/IC795063.png "Autenticação")

8.  Na seção **configuração de provedor de identidade** , execute as seguintes etapas:

    ![Configuração de provedor de identidade] (./media/active-directory-saas-picturepark-tutorial/IC795064.png "Configuração de provedor de identidade")

    1.  Clique em **Adicionar**.
    2.  Digite um nome para a sua configuração.
    3.  Selecione **Definir como padrão**.
    4.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Picturepark** , copie o valor de **SAML SSO URL** e, em seguida, cole a caixa de texto de **URI do emissor** .
    5.  Copie o valor de **impressão digital** do certificado exportado e, em seguida, cole a caixa de texto de **Impressão de polegar emissor confiável** .  

        >[AZURE.TIP]Para obter mais detalhes, consulte [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    6.  Clique em **JoinDefaultUsersGroup**.
    7.  Para definir o atributo **Emailaddress** na caixa de texto **declaração** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
        ![Configuração] (./media/active-directory-saas-picturepark-tutorial/IC795065.png "Configuração")
    8.  Clique em **Salvar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-picturepark-tutorial/IC795066.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Picturepark, ele devem ser provisionados em Picturepark.  
No caso de Picturepark, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **Picturepark** .

2.  Na barra de ferramentas na parte superior, clique em **Ferramentas administrativas**e clique em **usuários**.

    ![Usuários] (./media/active-directory-saas-picturepark-tutorial/IC795067.png "Usuários")

3.  Na guia **Visão geral de usuários** , clique em **novo**.

    ![Gerenciamento de usuários] (./media/active-directory-saas-picturepark-tutorial/IC795068.png "Gerenciamento de usuários")

4.  Na caixa de diálogo **Criar usuário** , execute as seguintes etapas:

    ![Criar usuário] (./media/active-directory-saas-picturepark-tutorial/IC795069.png "Criar usuário")

    1.  Digite a: **endereço de Email**, **senha**, **Confirme a senha**, **nome**, **Sobrenome**, **empresa**, **país**, **CEP**, **Cidade** do Azure Active Directory usuário válido que você quer provisionar to em caixas de texto relacionadas.
    2.  Selecione um **idioma**.
    3.  Clique em **criar**.

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros Picturepark usuário conta ou APIs fornecidas pela Picturepark para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-picturepark-perform-the-following-steps"></a>Para atribuir usuários a Picturepark, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Picturepark **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-picturepark-tutorial/IC795070.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-picturepark-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).