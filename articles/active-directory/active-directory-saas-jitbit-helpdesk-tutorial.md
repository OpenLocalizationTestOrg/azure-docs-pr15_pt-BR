<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Jitbit Helpdesk | Microsoft Azure" 
    description="Saiba como usar Jitbit Helpdesk com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Tutorial: Integração com o Active Directory do Azure com Jitbit Helpdesk
  
O objetivo deste tutorial é mostrar a integração do Azure e Jitbit Helpdesk.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário Jitbit Helpdesk
  
Depois de concluir este tutorial, os usuários do Azure AD que atribuiu Jitbit Helpdesk poderão único entrar no aplicativo no site da sua empresa Jitbit Helpdesk (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Jitbit Helpdesk
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Cenário")
##<a name="enabling-the-application-integration-for-jitbit-helpdesk"></a>Habilitar a integração de aplicativo para Jitbit Helpdesk
  
O objetivo desta seção é como habilitar a integração de aplicativo para Jitbit Helpdesk da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-jitbit-helpdesk-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Jitbit Helpdesk, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Técnicos de Jitbit**.

    ![Galeria de aplicativo] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Técnicos de Jitbit**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![JitBit] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Jitbit Helpdesk com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos. .  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

>[AZURE.IMPORTANT] Para poder configurar o logon único em seu locatário Jitbit Helpdesk, é necessário primeiro contatar o suporte técnico Jitbit Helpdesk para acessar esse recurso ativado.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo de **Técnicos de Jitbit** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar técnicos de Jitbit** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Jitbit técnicos de URL de entrada** , digite sua URL usando o seguinte padrão "https://*\<-nome do locatário\>. Jitbit.com*"e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em técnicos de Jitbit** , para baixar o certificado, clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado localmente como **c:\\Jitbit Helpdesk.cer**.

    ![Configurar o logon único] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Jitbit Helpdesk como administrador.

6.  Na barra de ferramentas na parte superior, clique em **Administração**.

    ![Administração] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administração")

7.  Clique em **Configurações gerais**.

    ![Usuários, empresas e permissões] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Usuários, empresas e permissões")

8.  Na seção de configuração de **configurações de autenticação** , execute as seguintes etapas:

    ![Configurações de autenticação] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Configurações de autenticação")

    1.  Selecione **Habilitar SAML 2.0 único entrar em** entrar usando Single Sign-On (SSO) com **OneLogin**.
    2.  No portal do clássico Azure, na página da caixa de diálogo **Configurar logon único em técnicos de Jitbit** , copie o valor de **ponto de extremidade iniciado pelo provedor de serviço (SP)** e, em seguida, cole a **URL do ponto de extremidade** de caixa de texto.
    3.  Crie um arquivo **codificado na base 64** do seu certificado baixado.
        
        >[AZURE.TIP]Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abrir o certificado codificado de base 64, copie o conteúdo dela para sua área de transferência e colá-lo para a caixa de texto de **Certificado x. 509**
    5.  Clique em **Salvar alterações**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Jitbit Helpdesk, eles devem ser provisionados em Jitbit Helpdesk.  
No caso de Jitbit Helpdesk, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **Técnicos de Jitbit** .

2.  No menu na parte superior, clique em **Administração**.

    ![Administração] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administração")

3.  Clique em **usuários, empresas e permissões**.

    ![Usuários, empresas e permissões] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Usuários, empresas e permissões")

4.  Clique em **Adicionar usuário**.

    ![Adicionar usuário] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Adicionar usuário")

5.  Na seção criar, digite os dados da conta do Azure AD que deseja provisionar em caixas de texto as seguintes: **nome de usuário**, **Email**, **nome**, **Sobrenome**

    ![Criar] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Criar")

6.  Clique em **criar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Jitbit Helpdesk usuário conta ou APIs fornecidas pela Jitbit Helpdesk para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-jitbit-helpdesk-perform-the-following-steps"></a>Para atribuir usuários Jitbit Helpdesk, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo de **Técnicos de Jitbit **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).