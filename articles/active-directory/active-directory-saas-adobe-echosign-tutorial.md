<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Adobe EchoSign | Microsoft Azure" 
    description="Saiba como usar o Adobe EchoSign com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-adobe-echosign"></a>Tutorial: Integração com o Active Directory do Azure com Adobe EchoSign

O objetivo deste tutorial é mostrar a integração do Azure e Adobe EchoSign.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um EchoSign Adobe único entrar na assinatura habilitada

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Adobe EchoSign poderão único entrar no aplicativo no site da sua empresa Adobe EchoSign (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Adobe EchoSign
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "Cenário")
##<a name="enabling-the-application-integration-for-adobe-echosign"></a>Habilitar a integração de aplicativo para Adobe EchoSign

O objetivo desta seção é como habilitar a integração de aplicativo para Adobe EchoSign da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-adobe-echosign-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Adobe EchoSign, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **EchoSign Adobe**.

    ![Galeria de aplicativo] (./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Adobe EchoSign**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Adobe EchoSign] (./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar Adobe EchoSign com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Adobe EchoSign** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Configurar o logon único")

2.  Na página **como você gostaria que usuários para entrar Adobe EchoSign** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto do **Adobe EchoSign entrada na URL** , digite sua URL usando o seguinte padrão "*https://company.echosign.com/*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no Adobe EchoSign** , clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Adobe EchoSign como administrador.

6.  No menu na parte superior, clique em **conta**e, em seguida, no painel de navegação o dado à esquerda, clique em **Configurações de SAML** em **Configurações de conta**.

    ![Conta] (./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "Conta")

7.  Na seção configurações de SAML, execute as seguintes etapas:

    ![Configurações de SAML] (./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "Configurações de SAML")

    1.  Como **O modo de SAML**, selecione **SAML obrigatório**.
    2.  Selecione **Permitir EchoSign conta aos administradores faça logon usando suas credenciais de EchoSign**.
    3.  Como **Criação do usuário**, selecione **Adicionar automaticamente os usuários autenticados por meio de SAML**.

8.  Mova, executando as seguintes etapas:

    ![Configurações de SAML] (./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "Configurações de SAML")

    1.  No portal do clássico Azure, na página de diálogo **Configurar logon único no Adobe EchoSign** , copie o valor de **ID de entidade** e, em seguida, cole a caixa de texto de **ID de entidade IdP** .
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único no Adobe EchoSign** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de logon IdP** .
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único no Adobe EchoSign** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **IdP Logout URL** .
    4.  Crie um arquivo **codificado na base 64** do seu certificado baixado.  

        >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o) 
 5.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo para a caixa de texto do **Certificado IdP** 6.  Clique em **Salvar alterações**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD efetuar login no Adobe EchoSign, ele devem ser provisionados em Adobe EchoSign.  
No caso de Adobe EchoSign, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **Adobe EchoSign** como administrador.

2.  No menu na parte superior, clique em **conta**e, em seguida, no painel de navegação o dado à esquerda, clique em **usuários e grupos**e, em seguida, clique em **criar um novo usuário**.

    ![Conta] (./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "Conta")

3.  Na seção **Criar novo usuário** , execute as seguintes etapas:

    ![Criar usuário] (./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "Criar usuário")

    1.  Digite o **Endereço de Email**, o **nome** e o **Sobrenome** de uma conta AAD válida que deseja provisionar em caixas de texto relacionadas.
    2.  Clique em **Criar usuário**.

        >[AZURE.NOTE] O proprietário da conta do Active Directory do Azure receberão um email que inclui um link para confirmar a conta antes que ela se torna ativa.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Adobe EchoSign usuário conta ou APIs fornecidas pela Adobe EchoSign para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-adobe-echosign-perform-the-following-steps"></a>Para atribuir usuários a Adobe EchoSign, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Adobe EchoSign **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
