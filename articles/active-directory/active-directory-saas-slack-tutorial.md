<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com margem de atraso | Microsoft Azure" 
    description="Saiba como usar a margem de atraso com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-slack"></a>Tutorial: Integração com o Active Directory do Azure com margem de atraso
  
O objetivo deste tutorial é mostrar a integração do Azure e margem de atraso.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um margem de atraso logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a margem de atraso será capazes de logon único para o aplicativo em seu site de empresa margem de atraso (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo de margem de atraso
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-slack-tutorial/IC794980.png "Cenário")

##<a name="enabling-the-application-integration-for-slack"></a>Habilitar a integração de aplicativo de margem de atraso
  
O objetivo desta seção é como habilitar a integração de aplicativo de margem de atraso da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-slack-perform-the-following-steps"></a>Para habilitar a integração de aplicativo de margem de atraso, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-slack-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-slack-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-slack-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-slack-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite a **margem de atraso**.

    ![Galeria de aplicativo] (./media/active-directory-saas-slack-tutorial/IC794981.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **margem de atraso**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Cenário] (./media/active-directory-saas-slack-tutorial/IC796925.png "Cenário")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar a margem de atraso com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo de **margem de atraso** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-slack-tutorial/IC794982.png "Configurar o logon único")

2.  Na página **como deseja usuários para entrar margem de atraso** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-slack-tutorial/IC794983.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto de **Entrada de margem de atraso em URL** , digite a URL do seu locatário margem de atraso (por exemplo: "*https://azuread.slack.com*") e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-slack-tutorial/IC794984.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único na margem de atraso** , para baixar o certificado, clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-slack-tutorial/IC794985.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa margem de atraso como administrador.

6.  Vá para **à Microsoft Azure AD \> configurações de equipe**.

    ![Configurações de equipe] (./media/active-directory-saas-slack-tutorial/IC794986.png "Configurações de equipe")

7.  Na seção **Configurações de equipe** , clique na guia **autenticação** e, em seguida, clique em **Alterar configurações**.

    ![Configurações de equipe] (./media/active-directory-saas-slack-tutorial/IC794987.png "Configurações de equipe")

8.  Na caixa de diálogo **Configurações de autenticação de SAML** , execute as seguintes etapas:

    ![Configurações de SAML] (./media/active-directory-saas-slack-tutorial/IC794988.png "Configurações de SAML")

    1.  No portal do clássico Azure, na página de diálogo **Configurar logon único na margem de atraso** , copie o valor de **SAML SSO URL** e cole-o na caixa de texto **SAML 2.0 ponto de extremidade HTTP ()** .
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único na margem de atraso** , copie o valor de **URL do emissor** e, em seguida, cole a caixa de texto do **Emissor do provedor de identidade** .
    3.  Crie um arquivo **codificado na base 64** do seu certificado baixado.
    
        >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo para a caixa de texto de **Certificado pública** .
    5.  Desmarque **Permitir que usuários alterem seus endereços de email**.
    6.  Selecione **Permitir que os usuários escolham seu próprio nome de usuário**.
    7.  Como a **autenticação para sua equipe deve ser utilizada por**, selecione **é opcional**.
    8.  Clique em **Salvar configuração**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-slack-tutorial/IC794989.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login margem de atraso, ele devem ser provisionados em margem de atraso.
  
Não há nenhum item de ação para configurar a margem de atraso de provisionamento do usuário.  
Quando um usuário atribuído tenta login margem de atraso, uma conta de margem de atraso é criada automaticamente, se necessário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-slack-perform-the-following-steps"></a>Para atribuir usuários a margem de atraso, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo de **margem de atraso **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-slack-tutorial/IC794990.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-slack-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).