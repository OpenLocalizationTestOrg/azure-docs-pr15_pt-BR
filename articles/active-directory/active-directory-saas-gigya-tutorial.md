<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Gigya | Microsoft Azure" 
    description="Saiba como usar Gigya com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="09/01/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-gigya"></a>Tutorial: Integração com o Active Directory do Azure com Gigya
  
O objetivo deste tutorial é mostrar a integração do Azure e Gigya.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Gigya logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Gigya poderão único entrar no aplicativo no site da sua empresa Gigya (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Gigya
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Configurar o logon único] (./media/active-directory-saas-gigya-tutorial/IC789512.png "Configurar o logon único")
##<a name="enabling-the-application-integration-for-gigya"></a>Habilitar a integração de aplicativo para Gigya
  
O objetivo desta seção é como habilitar a integração de aplicativo para Gigya da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-gigya-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Gigya, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-gigya-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-gigya-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-gigya-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-gigya-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Gigya**.

    ![Galeria de aplicativo] (./media/active-directory-saas-gigya-tutorial/IC789513.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Gigya**e clique em **concluído** para adicionar o aplicativo.

    ![Gigya] (./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Gigya com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Gigya** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-gigya-tutorial/IC789528.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Gigya** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-gigya-tutorial/IC789529.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Gigya entrada na URL** , digite sua URL usando o seguinte padrão "*http://company.gigya.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-gigya-tutorial/IC789530.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Gigya** , clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-gigya-tutorial/IC789531.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Gigya como administrador.

6.  Vá para **configurações \> SAML Login**e, em seguida, clique no botão **Adicionar** .

    ![SAML Login] (./media/active-directory-saas-gigya-tutorial/IC789532.png "SAML Login")

7.  Na seção **SAML Login** , execute as seguintes etapas:

    ![Configuração de SAML] (./media/active-directory-saas-gigya-tutorial/IC789533.png "Configuração de SAML")

    1.  Na caixa de texto **nome** , digite um nome para a sua configuração.
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Gigya** , copie o valor de **URL do emissor** e, em seguida, cole a caixa de texto do **emissor** .
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Gigya** , copie o valor de **Single Sign-On URL do serviço** e, em seguida, cole a caixa de texto de **Single Sign-On URL do serviço** .
    4.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Gigya** , copie o valor de **Formato de nome de identificador** e, em seguida, cole a caixa de texto de **Formato de ID de nome** .
    5.  Crie um arquivo **codificado na base 64** do seu certificado baixado.
        
        >[AZURE.TIP]Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo para a caixa de texto de **Certificado x. 509** .
    7.  Clique em **Salvar configurações**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-gigya-tutorial/IC789534.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Gigya, ele devem ser provisionados em Gigya.  
No caso de Gigya, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **Gigya** como administrador.

2.  Vá para **administrador \> gerenciar usuários**e clique em **Convidar usuários**.

    ![Gerenciar usuários] (./media/active-directory-saas-gigya-tutorial/IC789535.png "Gerenciar usuários")

3.  Na caixa de diálogo Convidar usuários, execute as seguintes etapas:

    ![Convidar usuários] (./media/active-directory-saas-gigya-tutorial/IC789536.png "Convidar usuários")

    1.  Na caixa de texto **Email** , digite o alias de email de uma conta do Active Directory do Azure válida que deseja provisionar.
    2.  Clique em **Convidar usuário**.
    
        >[AZURE.NOTE] O proprietário da conta do Active Directory do Azure receberão um email que inclui um link para confirmar a conta antes que ela se torna ativa.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Gigya usuário conta ou APIs fornecidas pela Gigya para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-gigya-perform-the-following-steps"></a>Para atribuir usuários a Gigya, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Gigya **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-gigya-tutorial/IC789537.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-gigya-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).