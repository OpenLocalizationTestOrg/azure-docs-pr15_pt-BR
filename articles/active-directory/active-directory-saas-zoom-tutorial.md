<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Zoom | Microsoft Azure" 
    description="Saiba como usar o Zoom com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-zoom"></a>Tutorial: Integração com o Active Directory do Azure com Zoom
  
O objetivo deste tutorial é mostrar a integração do Azure e Zoom.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário de Zoom
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Zoom poderão logon único para o aplicativo no site da sua empresa Zoom (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md)
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo de Zoom
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-zoom-tutorial/IC784693.png "Cenário")

##<a name="enabling-the-application-integration-for-zoom"></a>Habilitar a integração de aplicativo de Zoom
  
O objetivo desta seção é destacar como habilitar a integração de aplicativo para Zoom.

###<a name="to-enable-the-application-integration-for-zoom-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para o Zoom, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-zoom-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-zoom-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-zoom-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-zoom-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Zoom**.

    ![Galeria de aplicativo] (./media/active-directory-saas-zoom-tutorial/IC784694.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Zoom**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Aplicar zoom] (./media/active-directory-saas-zoom-tutorial/IC784695.png "Aplicar zoom")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar para Zoom com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **de Zoom** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-zoom-tutorial/IC784696.png "Configurar o logon único")

2.  Na página **como deseja usuários para entrar Zoom** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-zoom-tutorial/IC784697.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Zoom URL de entrada** , digite sua URL usando o seguinte padrão "*http://company.zoom.us*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-zoom-tutorial/IC784698.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Zoom** , clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-zoom-tutorial/IC784699.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa de Zoom como administrador.

6.  Clique na guia **Single Sign-On** .

    ![Logon único] (./media/active-directory-saas-zoom-tutorial/IC784700.png "Logon único")

7.  Clique na guia **Controle de segurança** e vá para as configurações de **Logon único** .

8.  Na seção Single Sign-On, execute as seguintes etapas:

    ![Logon único] (./media/active-directory-saas-zoom-tutorial/IC784701.png "Logon único")

    1.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Zoom** , copie o valor de **Single Sign-On URL do serviço** e, em seguida, cole o **URL da página de entrada** de texto.
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Zoom** , copie o valor de **URL do serviço Sign-Out único** e, em seguida, cole a **URL da página de saída** de caixa de texto.
    3.  Crie um arquivo **codificado na base 64** do seu certificado baixado.  

        >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo para a caixa de texto de **certificado de provedor de identidade**
    5.  No portal clássico do Azure, na página de diálogo **Configurar logon único em Zoom** , copie o valor de **URL do emissor** e, em seguida, cole a caixa de texto do **emissor** .
    6.  Clique em **Salvar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-zoom-tutorial/IC784702.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Zoom, ele devem ser provisionados em Zoom.  
No caso de Zoom, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon seu site de empresa **Zoom** como administrador.

2.  Clique na guia **Gerenciamento de conta** e clique em **Gerenciamento de usuário**.

3.  Na seção Gerenciamento de usuários, clique em **Adicionar usuários**.

    ![Gerenciamento de usuários] (./media/active-directory-saas-zoom-tutorial/IC784703.png "Gerenciamento de usuários")

4.  Na página **Adicionar usuários** , execute as seguintes etapas:

    ![Adicionar usuários] (./media/active-directory-saas-zoom-tutorial/IC784704.png "Adicionar usuários")

    1.  Como o **Tipo de usuário**, selecione **básica**.
    2.  Na caixa de texto de **Emails** , digite o endereço de email de uma conta AAD válida que deseja provisionar.
    3.  Clique em **Adicionar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação outros Zoom usuário conta ou APIs fornecidas pela Zoom para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-zoom-perform-the-following-steps"></a>Para atribuir usuários a Zoom, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **de Zoom **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-zoom-tutorial/IC784705.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-zoom-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).