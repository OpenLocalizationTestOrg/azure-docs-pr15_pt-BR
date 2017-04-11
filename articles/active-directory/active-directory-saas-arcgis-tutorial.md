<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com ArcGIS | Microsoft Azure" 
    description="Saiba como usar ArcGIS com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-arcgis"></a>Tutorial: Integração com o Active Directory do Azure com ArcGIS

O objetivo deste tutorial é mostrar a integração do Azure e ArcGIS. O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um ArcGIS logon único habilitado para assinatura

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a ArcGIS poderão único entrar no aplicativo no site da sua empresa ArcGIS (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para ArcGIS
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-arcgis-tutorial/IC784735.png "Cenário")
##<a name="enabling-the-application-integration-for-arcgis"></a>Habilitar a integração de aplicativo para ArcGIS

O objetivo desta seção é como habilitar a integração de aplicativo para ArcGIS da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-arcgis-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para ArcGIS, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-arcgis-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-arcgis-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-arcgis-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-arcgis-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **ArcGIS**.

    ![Galeria de Applcation] (./media/active-directory-saas-arcgis-tutorial/IC784736.png "Galeria de Applcation")

7.  No painel de resultados, selecione **ArcGIS**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![ArcGIS] (./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar ArcGIS com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo **ArcGIS** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-arcgis-tutorial/IC784738.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar ArcGIS** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-arcgis-tutorial/IC784739.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **ArcGIS URL de entrada** , digite o URL usada pelos usuários para entrar usando o seguinte padrão "*https://company.maps.arcgis.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-arcgis-tutorial/IC784740.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em ArcGIS** , clique em **baixar metadados**e salve o arquivo de metadados localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-arcgis-tutorial/IC784741.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa ArcGIS como administrador.

6.  Clique em **Editar configurações**.

    ![Editar configurações] (./media/active-directory-saas-arcgis-tutorial/IC784742.png "Editar configurações")

7.  Clique em **segurança**.

    ![Segurança] (./media/active-directory-saas-arcgis-tutorial/IC784743.png "Segurança")

8.  Em **Logon de empresa**, clique em **Definir o provedor de identidade**.

    ![Enterprise logon] (./media/active-directory-saas-arcgis-tutorial/IC784744.png "Enterprise logon")

9.  Na página de configuração do **Provedor de identidade definir** , execute as seguintes etapas:

    ![Defina o provedor de identidade] (./media/active-directory-saas-arcgis-tutorial/IC784745.png "Defina o provedor de identidade")

    1.  Na caixa de texto Nome, digite o nome da sua organização.
    2.  Para **que metadados para o provedor de identidade Enterprise serão fornecido usando**, selecione **Um arquivo**.
    3.  Para carregar o arquivo de metadados baixado, clique em **Escolher arquivo**.
    4.  Clique em **definir o provedor de identidade**.

10. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-arcgis-tutorial/IC784746.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login ArcGIS, ele devem ser provisionados em ArcGIS.  
No caso de ArcGIS, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **ArcGIS** .

2.  Clique em **Convidar participantes**.

    ![Convidar membros] (./media/active-directory-saas-arcgis-tutorial/IC784747.png "Convidar membros")

3.  Selecione **Adicionar membros automaticamente sem enviando um email**e clique em **Avançar**.

    ![Adicionar membros automaticamente] (./media/active-directory-saas-arcgis-tutorial/IC784748.png "Adicionar membros automaticamente")

4.  Na página de diálogo de **membros** , execute as seguintes etapas:

    ![Adicionar e revisão] (./media/active-directory-saas-arcgis-tutorial/IC784749.png "Adicionar e revisão")

    1.  Insira o **nome**, o **Sobrenome** e o **Email** de uma conta AAD válida desejada para provisionar.
    2.  Clique em **Adicionar e analise**.

5.  Examine os dados que você inseriu e clique em **Adicionar membros**.

    ![Adicionar membro] (./media/active-directory-saas-arcgis-tutorial/IC784750.png "Adicionar membro")

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros ArcGIS usuário conta ou APIs fornecidas pela ArcGIS para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-arcgis-perform-the-following-steps"></a>Para atribuir usuários a ArcGIS, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **ArcGIS **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-arcgis-tutorial/IC784751.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-arcgis-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
