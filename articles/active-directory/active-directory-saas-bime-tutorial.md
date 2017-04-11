<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Bime | Microsoft Azure" 
    description="Saiba como usar Bime com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-bime"></a>Tutorial: Integração com o Active Directory do Azure com Bime

O objetivo deste tutorial é mostrar a integração do Azure e Bime.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário Bime

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Bime poderão único entrar no aplicativo no site da sua empresa Bime (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Bime
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-bime-tutorial/IC775552.png "Cenário")
##<a name="enabling-the-application-integration-for-bime"></a>Habilitar a integração de aplicativo para Bime

O objetivo desta seção é como habilitar a integração de aplicativo para Bime da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-bime-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Bime, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-bime-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-bime-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-bime-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-bime-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Bime**.

    ![Galeria de aplicativo] (./media/active-directory-saas-bime-tutorial/IC775553.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Bime**e clique em **concluído** para adicionar o aplicativo.

    ![Bime] (./media/active-directory-saas-bime-tutorial/IC775554.png "Bime")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar Bime com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para Bime requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Bime** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-bime-tutorial/IC771709.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Bime** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-bime-tutorial/IC775555.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Bime URL de entrada** , digite sua URL usando o seguinte padrão "https://*\<-nome do locatário\>. Bimeapp.com*"e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-bime-tutorial/IC775556.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Bime** , para baixar o certificado, clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado localmente como **c:\\Bime.cer**.

    ![Configurar o logon único] (./media/active-directory-saas-bime-tutorial/IC775557.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Bime como administrador.

6.  Na barra de ferramentas, clique em **administrador**e, em seguida **conta**.

    ![Administração] (./media/active-directory-saas-bime-tutorial/IC775558.png "Administração")

7.  Na página de configuração de conta, execute as seguintes etapas:

    ![Configurar o logon único] (./media/active-directory-saas-bime-tutorial/IC775559.png "Configurar o logon único")

    1.  Selecione **Habilitar SAML autenticação**.
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Bime** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login remoto** .
    3.  Copie o valor de **impressão digital** do certificado exportado e, em seguida, cole a caixa de texto de **Impressão digital de certificado** .  

        >[AZURE.TIP] Para obter mais detalhes, consulte [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    4.  Clique em **Salvar**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-bime-tutorial/IC775560.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login Bime, ele devem ser provisionados em Bime.  
No caso de Bime, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **Bime** .

2.  Na barra de ferramentas, clique em **administrador**e, em seguida **usuários**.

    ![Administração] (./media/active-directory-saas-bime-tutorial/IC775561.png "Administração")

3.  Na **Lista de usuários**, clique em **Adicionar novo usuário** ("+").

    ![Usuários] (./media/active-directory-saas-bime-tutorial/IC775562.png "Usuários")

4.  Na página de diálogo de **Detalhes do usuário** , execute as seguintes etapas:

    ![Detalhes do usuário] (./media/active-directory-saas-bime-tutorial/IC775563.png "Detalhes do usuário")

    1.  Insira o nome, o sobrenome, o Login, o Email de uma conta AAD válida que deseja provisionar.
    2.  Clique em Salvar.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Bime usuário conta ou APIs fornecidas pela Bime para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-bime-perform-the-following-steps"></a>Para atribuir usuários a Bime, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Bime **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-bime-tutorial/IC775564.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-bime-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
