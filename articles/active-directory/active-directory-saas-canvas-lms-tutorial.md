<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com tela LMS | Microsoft Azure" 
    description="Saiba como usar LMS de tela com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Tutorial: Integração com o Active Directory do Azure com tela LMS

O objetivo deste tutorial é mostrar a integração do Azure e tela.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário de tela

Depois de concluir este tutorial, os usuários do Azure AD que você tiver atribuído a tela poderão único entrar no aplicativo no site da sua empresa tela (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo da tela
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "Cenário")
##<a name="enabling-the-application-integration-for-canvas"></a>Habilitar a integração de aplicativo da tela

O objetivo desta seção é como habilitar a integração de aplicativo da tela de estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-canvas-perform-the-following-steps"></a>Para habilitar a integração de aplicativo da tela, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **tela**.

    ![Galeria de aplicativo] (./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "Galeria de aplicativo")

7.  No painel de resultados, selecione a **tela**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Tela] (./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Tela")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar a tela com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para tela requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo da **tela** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para entrar tela** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto de **Tela de entrada em URL** , digite sua URL usando o seguinte padrão `https://<tenant-name>.instructure.com`e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único na tela** , para baixar o certificado, clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa de tela como administrador.

6.  Vá para **cursos \> gerenciado contas \> Microsoft**.

    ![Tela] (./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Tela")

7.  No painel de navegação à esquerda, selecione **autenticação**e clique em **Adicionar novo SAML Config**.

    ![Autenticação] (./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Autenticação")

8.  Na página de integração atual, execute as seguintes etapas:

    ![Integração atual] (./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Integração atual")

    1.  No portal do clássico Azure, na página de diálogo **Configurar logon único na tela** , copie o valor de **ID de entidade** e, em seguida, cole a caixa de texto de **ID de entidade IdP** .
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único na tela** , copie o valor de **URL de Login remota** e, em seguida, cole a caixa de texto do **Log na URL** .
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único na tela** , copie o valor de **URL de Login remota** e, em seguida, cole o **URL de Check-Out do Log** de texto.
    4.  No portal do clássico Azure, na página de diálogo **Configurar logon único na tela** , copie o valor de **Alterar senha URL** e, em seguida, cole a caixa de texto do **Link de alteração de senha** .
    5.  Copie o valor de **impressão digital** do certificado exportado e, em seguida, cole a caixa de texto de **Impressão digital de certificado** .  

        >[AZURE.TIP] Para obter mais detalhes, consulte [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    6.  Na lista de **Atributo de Login** , selecione **NameID**.
    7.  Na lista **Formato Identifier** , selecione **emailAddress**.
    8.  Clique em **Salvar configurações de autenticação**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD entrar em tela, ele devem ser provisionados em tela.  
No caso de tela, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário de **tela** .

2.  Vá para **cursos \> gerenciado contas \> Microsoft**.

    ![Tela] (./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Tela")

3.  Clique em **usuários**.

    ![Usuários] (./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Usuários")

4.  Clique em **Adicionar novo usuário**.

    ![Usuários] (./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Usuários")

5.  Na página Adicionar um novo usuário diálogo, execute as seguintes etapas:

    ![Adicionar usuário] (./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Adicionar usuário")

    1.  Na caixa de texto **Nome completo** , digite o nome do usuário.
    2.  Na caixa de texto **Email** , digite o endereço de email do usuário.
    3.  Na caixa de texto de **Login** , digite o endereço de email do Azure AD do usuário.
    4.  Selecione **o usuário sobre esta criação de conta de Email**.
    5.  Clique em **Adicionar usuário**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros tela usuário conta ou APIs fornecidas pela tela para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-canvas-perform-the-following-steps"></a>Para atribuir usuários a tela, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo da **tela **, clique em **atribuir aos usuários**.

    ![Atribuir usuários] (./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Atribuir usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
