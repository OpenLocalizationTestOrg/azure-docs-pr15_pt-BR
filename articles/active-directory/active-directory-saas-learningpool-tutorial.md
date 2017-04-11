<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Learningpool | Microsoft Azure" 
    description="Saiba como usar Learningpool com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-learningpool"></a>Tutorial: Integração com o Active Directory do Azure com Learningpool
  
O objetivo deste tutorial é mostrar a integração do Azure e Learningpool.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Learningpool logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Learningpool poderão único entrar no aplicativo no site da sua empresa Learningpool (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Learningpool
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-learningpool-tutorial/IC791166.png "Cenário")
##<a name="enabling-the-application-integration-for-learningpool"></a>Habilitar a integração de aplicativo para Learningpool
  
O objetivo desta seção é como habilitar a integração de aplicativo para Learningpool da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-learningpool-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Learningpool, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-learningpool-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-learningpool-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-learningpool-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-learningpool-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Learningpool**.

    ![Galeria de aplicativo] (./media/active-directory-saas-learningpool-tutorial/IC795073.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Learningpool**e clique em **concluído** para adicionar o aplicativo.

    ![Learningpool] (./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Learningpool com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.
  
Seu aplicativo Learningpool espera as declarações SAML em um formato específico, o que requer que você adicionar os mapeamentos de atributo personalizado à sua configuração de **atributos de token saml** .  
A captura de tela a seguir mostra um exemplo para isso.

![Atributos de Token de SAML] (./media/active-directory-saas-learningpool-tutorial/IC795074.png "Atributos de Token de SAML")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Learningpool** , no menu na parte superior, clique em **atributos** para abrir a caixa de diálogo de **Atributos de Token SAML** .

    ![Atributos] (./media/active-directory-saas-learningpool-tutorial/IC795075.png "Atributos")

2.  Para adicionar os mapeamentos de atributo obrigatório, execute as seguintes etapas:

    ###  

  	|Nome do atributo                |Valor do atributo            |
  	|------------------------------|---------------------------|

     urn: oid:1.2.840.113556.1.4.221 | User.userPrincipalName
  	|-------------------------------|--------------------------|  
     urn: oid:2.5.4.42|User.givenName   
  	|urn: oid:0.9.2342.19200300.100.1.3|User.mail
  	|urn: oid:2.5.4.4|User.surname

    1.  Para cada linha de dados na tabela acima, clique em **Adicionar atributo de usuário**.
    2.  Na caixa de texto **Nome do atributo** , digite o nome do atributo mostrado para aquela linha.
    3.  Na lista de **Valor do atributo** , selecione o valor do atributo mostrado para aquela linha.
    4.  Clique em **Concluir**.

3.  Clique em **Aplicar alterações**.

4.  No seu navegador, clique em **Voltar** para abrir a caixa de diálogo **Quick Start** novamente.

5.  Clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar um logon] (./media/active-directory-saas-learningpool-tutorial/IC795076.png "Configurar um logon")

6.  Na página **como você gostaria que os usuários para assinar Learningpool** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configurar o logon único")

7.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Learningpool entrada na URL** , digite a URL usada pelos usuários para entrar seu aplicativo de Learningpool (por exemplo: https://parliament.preview.learningpool.com/auth/shibboleth/index.php) e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-learningpool-tutorial/IC795078.png "Configurar a URL do aplicativo")

8.  Na página **Configurar logon único em Learningpool** , para baixar os metadados, clique em **baixar metadados**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configurar o logon único")

9.  Encaminhe desse arquivo de metadados para sua equipe de suporte de Learningpool.

    >[AZURE.NOTE]Logon único deve ser habilitado pela equipe de suporte do Learningpool.

10. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Learningpool, ele devem ser provisionados em Learningpool.
  
Não há nenhum item de ação para configurar a Learningpool de provisionamento do usuário.  
Os usuários precisam ser criados por sua equipe de suporte de Learningpool.

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros Learningpool usuário conta ou APIs fornecidas pela Learningpool para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-learningpool-perform-the-following-steps"></a>Para atribuir usuários a Learningpool, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Learningpool **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-learningpool-tutorial/IC795081.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-learningpool-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).