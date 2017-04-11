<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com AirWatch | Microsoft Azure" 
    description="Saiba como usar AirWatch com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Tutorial: Integração com o Active Directory do Azure com AirWatch

O objetivo deste tutorial é mostrar a integração do Azure e AirWatch.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um AirWatch logon único habilitado para assinatura

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a AirWatch poderão único entrar no aplicativo no site da sua empresa AirWatch (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para AirWatch
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![AirWatch] (./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##<a name="enabling-the-application-integration-for-airwatch"></a>Habilitar a integração de aplicativo para AirWatch

O objetivo desta seção é como habilitar a integração de aplicativo para AirWatch da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-airwatch-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para AirWatch, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-airwatch-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-airwatch-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-airwatch-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-airwatch-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **AirWatch**.

    ![Galeria de aplicativo] (./media/active-directory-saas-airwatch-tutorial/IC791914.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **AirWatch**e clique em **concluído** para adicionar o aplicativo.

    ![AirWatch] (./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar AirWatch com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **AirWatch** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-airwatch-tutorial/IC791916.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar AirWatch** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-airwatch-tutorial/IC791917.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **AirWatch entrada na URL** , digite a URL usada pelos usuários para entrar em seu aplicativo de AirWatch (por exemplo: "*https:// companycode.awmdm.com/AirWatch/Login?gid=companycode*") e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-airwatch-tutorial/IC791918.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em AirWatch** , clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-airwatch-tutorial/IC791919.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa AirWatch como administrador.

6.  No painel de navegação esquerdo, clique em **contas**e, em seguida, clique em **administradores**.

    ![Administradores] (./media/active-directory-saas-airwatch-tutorial/IC791920.png "Administradores")

7.  Expanda o menu **configurações** e clique em **Serviços de diretório**.

    ![Configurações] (./media/active-directory-saas-airwatch-tutorial/IC791921.png "Configurações")

8.  Clique na guia do **usuário** , no campo **Base DN** de texto, digite seu nome de domínio e clique em **Salvar**.

    ![Usuário] (./media/active-directory-saas-airwatch-tutorial/IC791922.png "Usuário")

9.  Clique na guia **servidor** .

    ![Servidor] (./media/active-directory-saas-airwatch-tutorial/IC791923.png "Servidor")

10. Execute as seguintes etapas:

    ![Carregar] (./media/active-directory-saas-airwatch-tutorial/IC791924.png "Carregar")

    1.  Como **O tipo de diretório**, selecione **Nenhum**.
    2.  Selecione **usar SAML para autenticação**.
    3.  Para carregar o certificado baixado, clique em **carregar**.

11. Na seção **Solicitar** , execute as seguintes etapas:

    ![Solicitar] (./media/active-directory-saas-airwatch-tutorial/IC791925.png "Solicitar")

    1.  Como **Solicitar o tipo de vinculação**, selecione **postar**.
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Airwatch** , copie o valor de **Single Sign-On URL do serviço** e, em seguida, cole a caixa de texto de **Identidade provedor única entrada na URL** .
    3.  Como **Formatar NameID**, selecione o **Endereço de Email**.
    4.  Clique em **Salvar**.

12. Clique na guia **usuário** novamente.

    ![Usuário] (./media/active-directory-saas-airwatch-tutorial/IC791926.png "Usuário")

13. Na seção **atributo** , execute as seguintes etapas:

    ![Atributo] (./media/active-directory-saas-airwatch-tutorial/IC791927.png "Atributo")

    1.  Na caixa de texto **Identificador de objeto** , digite **http://schemas.microsoft.com/identity/claims/objectidentifier**.
    2.  Na caixa de texto **nome de usuário** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    3.  Na caixa de texto **Nome para exibição** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    4.  Na caixa de texto **nome** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    5.  Na caixa de texto **Sobrenome** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    6.  Na caixa de texto **Email** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  Clique em **Salvar**.

14. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-airwatch-tutorial/IC791928.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login AirWatch, ele devem ser provisionados em AirWatch.  
No caso de AirWatch, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **AirWatch** como administrador.

2.  No painel de navegação no lado esquerdo, clique em **contas**e, em seguida, clique em **usuários**.

    ![Usuários] (./media/active-directory-saas-airwatch-tutorial/IC791929.png "Usuários")

3.  No menu **usuários** , clique em **Modo de exibição de lista**e clique em **Adicionar \> adicionar usuário**.

    ![Adicionar usuário] (./media/active-directory-saas-airwatch-tutorial/IC791930.png "Adicionar usuário")

4.  Na caixa de diálogo **Adicionar / Editar usuário** , execute as seguintes etapas:

    ![Adicionar usuário] (./media/active-directory-saas-airwatch-tutorial/IC791931.png "Adicionar usuário")

    1.  Digite o **nome de usuário**, **senha**, **Confirmar senha**, **nome**, **Sobrenome**, **Endereço de Email** de uma conta do Active Directory do Azure válida que deseja provisionar em caixas de texto relacionadas.
    2.  Clique em **Salvar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros AirWatch usuário conta ou APIs fornecidas pela AirWatch para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-airwatch-perform-the-following-steps"></a>Para atribuir usuários a AirWatch, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **AirWatch **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-airwatch-tutorial/IC791932.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-airwatch-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
