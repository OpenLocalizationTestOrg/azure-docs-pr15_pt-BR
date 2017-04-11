<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Parabéns | Microsoft Azure" 
    description="Saiba como usar Parabéns com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-kudos"></a>Tutorial: Integração com o Active Directory do Azure com Parabéns
  
O objetivo deste tutorial é mostrar a integração do Azure e Parabéns.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário Parabéns
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Parabéns poderão único entrar no aplicativo no site da sua empresa Parabéns (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Parabéns
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-kudos-tutorial/IC787799.png "Cenário")
##<a name="enabling-the-application-integration-for-kudos"></a>Habilitar a integração de aplicativo para Parabéns
  
O objetivo desta seção é como habilitar a integração de aplicativo para Parabéns da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-kudos-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Parabéns, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-kudos-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-kudos-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-kudos-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-kudos-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Parabéns**.

    ![Galeria de aplicativo] (./media/active-directory-saas-kudos-tutorial/IC787800.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Parabéns**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Parabéns] (./media/active-directory-saas-kudos-tutorial/IC787801.png "Parabéns")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Parabéns com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo **Parabéns** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-kudos-tutorial/IC787802.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Parabéns** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-kudos-tutorial/IC787803.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Parabéns entrada na URL** , digite sua URL usando o seguinte padrão "*https://company.kudosnow.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-kudos-tutorial/IC787804.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Parabéns** , clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-kudos-tutorial/IC787805.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Parabéns como administrador.

6.  No menu na parte superior, clique em **configurações**.

    ![Configurações] (./media/active-directory-saas-kudos-tutorial/IC787806.png "Configurações")

7.  Clique em **integrações \> SSO**.

8.  Na seção **SSO** , execute as seguintes etapas:

    ![SSO] (./media/active-directory-saas-kudos-tutorial/IC787807.png "SSO")

    1.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Parabéns** , copie o valor de **Single Sign-On URL do serviço** e cole-o na caixa de texto **entrar na URL** .
    2.  Crie um arquivo **codificado na base 64** do seu certificado baixado.  

        >[AZURE.TIP]
        Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    3.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo para a caixa de texto de **certificado x. 509**
    4.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Parabéns** , copie o valor de **URL do serviço Sign-Out único** e cole-o na caixa de texto **Logout para URL** .
    5.  Na caixa de texto **URL do seu Parabéns** , digite o nome da sua empresa.
    6.  Clique em **Salvar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-kudos-tutorial/IC787808.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Parabéns, ele devem ser provisionados em Parabéns.  
No caso de Parabéns, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **Parabéns** como administrador.

2.  No menu na parte superior, clique em **configurações**.

    ![Configurações] (./media/active-directory-saas-kudos-tutorial/IC787806.png "Configurações")

3.  Clique em **administrador do usuário**.

4.  Clique na guia **usuários** e clique em **Adicionar um usuário**.

    ![Administração de usuário] (./media/active-directory-saas-kudos-tutorial/IC787809.png "Administração de usuário")

5.  Na seção **Adicionar um usuário** , execute as seguintes etapas:

    ![Adicionar um usuário] (./media/active-directory-saas-kudos-tutorial/IC787810.png "Adicionar um usuário")

    1.  Digite o **nome**, **Sobrenome**, **Email** e outros detalhes de uma conta do Active Directory do Azure válida que deseja provisionar em caixas de texto relacionadas.
    2.  Clique em **Criar usuário**.

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros Parabéns usuário conta ou APIs fornecidas pela Parabéns para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-kudos-perform-the-following-steps"></a>Para atribuir usuários a Parabéns, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Parabéns **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-kudos-tutorial/IC787811.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-kudos-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).