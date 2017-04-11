<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com AppDynamics | Microsoft Azure" 
    description="Saiba como usar AppDynamics com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Tutorial: Integração com o Active Directory do Azure com AppDynamics

O objetivo deste tutorial é mostrar a integração do Azure e AppDynamics. O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um AppDynamics logon único habilitado para assinatura

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a AppDynamics poderão único entrar no aplicativo no site da sua empresa AppDynamics (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para AppDynamics
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Cenário")
##<a name="enabling-the-application-integration-for-appdynamics"></a>Habilitar a integração de aplicativo para AppDynamics

O objetivo desta seção é como habilitar a integração de aplicativo para AppDynamics da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-appdynamics-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para AppDynamics, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **AppDynamics**.

    ![Galeria de aplicativo] (./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **AppDynamics**e clique em **concluído** para adicionar o aplicativo.

    ![AppDynamics] (./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar AppDynamics com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **AppDynamics** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar sessão única] (./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configurar sessão única")

2.  Na página **como você gostaria que os usuários para assinar AppDynamics** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar sessão única] (./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configurar sessão única")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **AppDynamics entrada na URL** , digite a URL usada pelos usuários para logon para AppDynamics ("*https://companyname.saas.appdynamics.com*") e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em AppDynamics** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.

    ![Configurar sessão única] (./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configurar sessão única")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa AppDynamics como administrador.

6.  Na barra de ferramentas na parte superior, clique em **configurações**e, em seguida, clique em **Administração**.

    ![Administração] (./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administração")

7.  Clique na guia de **Provedor de autenticação** .

    ![Provedor de autenticação] (./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Provedor de autenticação")

8.  Na seção de **Provedor de autenticação** , execute as seguintes etapas:

    ![Configuração de SAML] (./media/active-directory-saas-appdynamics-tutorial/IC790225.png "Configuração de SAML")

    1.  Como **Provedor de autenticação**, selecione **SAML**.
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único em AppDynamics** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login** .
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único em AppDynamics** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL de Logout** .
    4.  Crie um arquivo **codificado na base 64** do seu certificado baixado.  

        >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    5.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo para a caixa de texto de **certificado**
    6.  Clique em **Salvar**.
        ![Salvar] (./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Salvar")

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar sessão única] (./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configurar sessão única")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login AppDynamics, ele devem ser provisionados em AppDynamics.  
No caso de AppDynamics, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon em seu site de empresa AppDynamics como administrador.

2.  Vá para **usuários**e, em seguida, clique em **+** para abrir a caixa de diálogo **Criar usuário** .

    ![Usuários] (./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Usuários")

3.  Na seção **Criar usuário** , execute as seguintes etapas:

    ![Criar usuário] (./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Criar usuário")

    1.  Digite o **nome de usuário**, **nome**, **Email**, **Nova senha**, **Repetir nova senha** de uma conta AAD válida que deseja provisionar em caixas de texto relacionadas.
    2.  Clique em **Salvar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros AppDynamics usuário conta ou APIs fornecidas pela AppDynamics para provisionar contas de usuário do Azure AD.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-appdynamics-perform-the-following-steps"></a>Para atribuir usuários a AppDynamics, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **AppDynamics **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
