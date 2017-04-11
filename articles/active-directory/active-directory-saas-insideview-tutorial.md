<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com InsideView | Microsoft Azure" 
    description="Saiba como usar InsideView com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-insideview"></a>Tutorial: Integração com o Active Directory do Azure com InsideView
  
O objetivo deste tutorial é mostrar a integração do Azure e InsideView.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário InsideView
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a InsideView poderão único entrar no aplicativo no site da sua empresa InsideView (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para InsideView
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-insideview-tutorial/IC794128.png "Cenário")
##<a name="enabling-the-application-integration-for-insideview"></a>Habilitar a integração de aplicativo para InsideView
  
O objetivo desta seção é como habilitar a integração de aplicativo para InsideView da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-insideview-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para InsideView, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-insideview-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-insideview-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-insideview-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-insideview-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **InsideView**.

    ![Galeria de aplicativo] (./media/active-directory-saas-insideview-tutorial/IC794129.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **InsideView**e clique em **concluído** para adicionar o aplicativo.

    ![InsideView] (./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar InsideView com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **InsideView** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar sessão única] (./media/active-directory-saas-insideview-tutorial/IC794131.png "Configurar sessão única")

2.  Na página **como você gostaria que os usuários para assinar InsideView** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar sessão única] (./media/active-directory-saas-insideview-tutorial/IC794132.png "Configurar sessão única")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **URL de resposta InsideView** , digite a URL de SSO InsideView (por exemplo: `https://my.insideview.com/iv/<STS Name>/login.iv`) e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-insideview-tutorial/IC794133.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em InsideView** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.

    ![Configurar sessão única] (./media/active-directory-saas-insideview-tutorial/IC794134.png "Configurar sessão única")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa InsideView como administrador.

6.  Na barra de ferramentas na parte superior, clique em **administrador**, **SingleSignOn configurações**e clique em **Adicionar SAML**.

    ![Logon único SAML configurações] (./media/active-directory-saas-insideview-tutorial/IC794135.png "Logon único SAML configurações")

7.  Na seção **Adicionar um novo SAML** , execute as seguintes etapas:

    ![Adicionar um novo SAML] (./media/active-directory-saas-insideview-tutorial/IC794136.png "Adicionar um novo SAML")

    1.  Na caixa de texto **Nome de STS** , digite um nome para a sua configuração.
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único em InsideView** , copie o valor de **Ponto de extremidade de iniciada do provedor de serviço (SP)** e, em seguida, cole a caixa de texto de **Ponto de extremidade do WS/SamlP-Fed Unsolicated** .
    3.  Crie um arquivo **codificado na base 64** do seu certificado baixado.
        
        >[AZURE.TIP]Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo para a caixa de texto de **Certificado STS**
    5.  Na caixa de texto de **Mapeamento de Id de usuário do Crm** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    6.  Na caixa de texto de **Mapeamento de Email do Crm** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  Na caixa de texto de **Mapeamento de nome de Crm** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    8.  Na caixa de texto de **mapeamento de Crm sobrenome** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    9.  Clique em **Salvar**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar sessão única] (./media/active-directory-saas-insideview-tutorial/IC794137.png "Configurar sessão única")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login InsideView, ele devem ser provisionados em InsideView.  
No caso de InsideView, provisionamento é uma tarefa manual.
  
Para obter os usuários ou contatos criados em InsideView, entre em contato com seu gerente de sucesso do cliente ou enviar email**support@insideview.com**

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros InsideView usuário conta ou APIs fornecidas pela InsideView para provisionar contas de usuário do Azure AD.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-insideview-perform-the-following-steps"></a>Para atribuir usuários a InsideView, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **InsideView **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-insideview-tutorial/IC794138.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-insideview-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).