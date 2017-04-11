<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com bambu HR | Microsoft Azure" 
    description="Saiba como usar bambu HR com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-bamboo-hr"></a>Tutorial: Integração com o Active Directory do Azure com bambu HR

O objetivo deste tutorial é mostrar a integração do Azure e BambooHR.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um BambooHR logon único habilitado para assinatura

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a BambooHR poderão único entrar no aplicativo no site da sua empresa BambooHR (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para BambooHR
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Cenário")
##<a name="enabling-the-application-integration-for-bamboohr"></a>Habilitar a integração de aplicativo para BambooHR

O objetivo desta seção é como habilitar a integração de aplicativo para BambooHR da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-bamboohr-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para BambooHR, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **BambooHR**.

    ![Galeria de aplicativo] (./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **BambooHR**e clique em **concluído** para adicionar o aplicativo.

    ![BambooHR] (./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar BambooHR com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **BambooHR** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Cenário] (./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Cenário")

2.  Na página **como você gostaria que os usuários para assinar BambooHR** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **BambooHR entrada na URL** , digite a URL usada pelos usuários para entrar seu aplicativo BambooHR (por exemplo: https://company.bamboohr.com) e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em BambooHR** , clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa BambooHR como administrador.

6.  Na página inicial, execute as seguintes etapas:

    ![Logon único] (./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "Logon único")

    1.  Clique em **aplicativos**.
    2.  No menu aplicativos à esquerda, clique em **Logon único**.
    3.  Clique em **SAML Single Sign-On**.

7.  Na seção **SAML Single Sign-On** , execute as seguintes etapas:

    ![SAML Single Sign-On] (./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "SAML Single Sign-On")

    1.  No portal do clássico Azure, na página de diálogo **Configurar logon único em BambooHR** , copie o valor de **Single Sign-On URL do serviço** e cole-o na caixa de texto **URL de Login de SSO** .
    2.  Crie um arquivo **codificado na base 64** do seu certificado baixado.  

        >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    3.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo para a caixa de texto de **Certificado x. 509**
    4.  Clique em **Salvar**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login BambooHR, ele devem ser provisionados em BambooHR.  
No caso de BambooHR, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon seu site de **BambooHR** como administrador.

2.  Na barra de ferramentas na parte superior, clique em **configurações**.

    ![Configuração] (./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Configuração")

3.  Clique em **Visão geral**.

4.  No painel de navegação esquerdo, vá para **segurança \> usuários**.

5.  Digite o endereço de email, senha e nome de usuário de uma conta AAD válida que deseja provisionar em caixas de texto relacionadas.

6.  Clique em **Salvar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros BambooHR usuário conta ou APIs fornecidas pela BambooHR para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-bamboohr-perform-the-following-steps"></a>Para atribuir usuários a BambooHR, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **BambooHR **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
