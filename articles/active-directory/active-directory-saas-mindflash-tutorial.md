<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Mindflash | Microsoft Azure" 
    description="Saiba como usar Mindflash com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-mindflash"></a>Tutorial: Integração com o Active Directory do Azure com Mindflash
  
O objetivo deste tutorial é mostrar a integração do Azure e Mindflash.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Mindflash logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Mindflash poderão único entrar no aplicativo no site da sua empresa Mindflash (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Mindflash
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-mindflash-tutorial/IC787132.png "Cenário")
##<a name="enabling-the-application-integration-for-mindflash"></a>Habilitar a integração de aplicativo para Mindflash
  
O objetivo desta seção é como habilitar a integração de aplicativo para Mindflash da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-mindflash-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Mindflash, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-mindflash-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-mindflash-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-mindflash-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-mindflash-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Mindflash**.

    ![Galeria de aplicativo] (./media/active-directory-saas-mindflash-tutorial/IC787133.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Mindflash**e clique em **concluído** para adicionar o aplicativo.

    ![Mindflash] (./media/active-directory-saas-mindflash-tutorial/IC787134.png "Mindflash")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Mindflash com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Mindflash** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-mindflash-tutorial/IC787135.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Mindflash** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-mindflash-tutorial/IC787136.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto de **Entrada na URL** , digite sua URL usando o seguinte padrão "*http://company.mindflash.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-mindflash-tutorial/IC787137.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Mindflash** , clique em **baixar metadados**e salve o arquivo de metadados em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-mindflash-tutorial/IC787138.png "Configurar o logon único")

5.  Envie a metadatafile para a equipe de suporte de Mindflash.

    >[AZURE.NOTE] A configuração de logon única deve ser executada pela equipe de suporte do Mindflash. Você receberá uma notificação assim que a configuração foi concluída.

6.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-mindflash-tutorial/IC787139.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Mindflash, ele devem ser provisionados em Mindflash.  
No caso de Mindflash, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **Mindflash** como administrador.

2.  Vá para **Gerenciar usuários**.

    ![Gerenciar usuários] (./media/active-directory-saas-mindflash-tutorial/IC787140.png "Gerenciar usuários")

3.  Clique em **Adicionar usuários**e, em seguida, clique em **novo**.

4.  Na seção **Adicionar novos usuários** , execute as seguintes etapas:

    ![Adicionar novos usuários] (./media/active-directory-saas-mindflash-tutorial/IC787141.png "Adicionar novos usuários")

    1.  Digite o **nome**, o **Sobrenome** e o **Email** de uma conta AAD válida que deseja provisionar em caixas de texto relacionadas.
    2.  Clique em **Adicionar**.

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros Mindflash usuário conta ou APIs fornecidas pela Mindflash para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-mindflash-perform-the-following-steps"></a>Para atribuir usuários a Mindflash, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Mindflash **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-mindflash-tutorial/IC787142.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-mindflash-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).