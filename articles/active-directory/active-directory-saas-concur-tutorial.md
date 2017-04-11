<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Concur | Microsoft Azure" 
    description="Saiba como usar Concur com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-concur"></a>Tutorial: Integração com o Active Directory do Azure com Concur  


O objetivo deste tutorial é mostrar a integração do Azure e Concur.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário em Concur

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Concur
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-concur-tutorial/IC769766.png "Cenário")

>[AZURE.NOTE] A configuração da sua assinatura do Concur para SSO federado via SAML é uma tarefa separada, você deve contatar o Concur para executar.

##<a name="enabling-the-application-integration-for-concur"></a>Habilitar a integração de aplicativo para Concur

O objetivo desta seção é como habilitar a integração de aplicativo para Concur da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-concur-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Concur, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-concur-tutorial/IC700993.png "Do Active Directory")

2.  Na lista **pasta** , selecione o diretório para o qual deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-concur-tutorial/IC700994.png "Aplicativos")

4.  Para abrir a **Galeria de aplicativo**, clique em **Adicionar um aplicativo**e clique em **Adicionar um aplicativo para minha organização usar**.

    ![o que você deseja fazer?] (./media/active-directory-saas-concur-tutorial/IC700995.png "o que você deseja fazer?")

5.  Na **caixa de pesquisa**, digite **Concur**.

    ![Galeria de aplicativo] (./media/active-directory-saas-concur-tutorial/IC721727.png "Galeria de aplicativo")

6.  No painel de resultados, selecione **Concur**e clique em **concluído** para adicionar o aplicativo.

    ![Coincidem] (./media/active-directory-saas-concur-tutorial/IC721728.png "Coincidem")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar Concur com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

>[AZURE.NOTE] A configuração da sua assinatura do Concur para SSO federado via SAML é uma tarefa separada, você deve contatar o Concur para executar.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Concur **, clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-concur-tutorial/IC769767.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Concur** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-concur-tutorial/IC769768.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Coincidem sinal em URL** , digite a URL de entrada do locatário concur e clique em **Avançar**: 

    ![Configurar entrar URL] (./media/active-directory-saas-concur-tutorial/IC769769.png "Configurar entrar URL")

4.  Na página **Configurar logon único em Concur** , execute as seguintes etapas.

    ![Configurar entrar URL] (./media/active-directory-saas-concur-tutorial/IC769770.png "Configurar entrar URL")

    1.  Clique em baixar os metadados e seguro, em seguida, o arquivo de dados no seu computador.
    2.  Contate a equipe de suporte de Concur para configurar o SSO do seu locatário.
    3.  Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .  

    >[AZURE.NOTE] A configuração da sua assinatura do Concur para SSO federado via SAML é uma tarefa separada, você deve contatar o Concur para executar.

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

O objetivo desta seção é como habilitar o provisionamento de contas de usuário do Active Directory para Concur da estrutura de tópicos.

Para permitir que aplicativos no serviço de despesas, há deve ser configuração correta e o uso de um perfil de administrador do serviço Web. Basta não adicione a função de administrador de WS ao seu perfil de administrador existente que você usa para T & E funções administrativas.

Coincidem consultores ou o administrador do cliente deve criar um perfil de administrador de serviço Web distintos e o administrador de cliente deve usar este perfil para as funções de administrador de serviços da Web (por exemplo, habilitando aplicativos). Esses perfis devem ser mantidos separados do cliente do perfil do administrador diária T & E administrador (o perfil de administrador T & E não deve ter a função de WSAdmin atribuída).

Quando você cria o perfil a ser usado para habilitar o aplicativo, insira o cliente do nome do administrador para os campos de perfil de usuário. Isso atribuirá a propriedade para o perfil. Depois que os perfis é criado, o cliente deve fazer com este perfil clicar no botão "*Permitir*" para um aplicativo de parceiro dentro do menu de serviços da Web.

Pelos seguintes motivos, essa ação não deve ser feita com o perfil usarem para administração de T & E normal.

1.  O cliente deve ser aquele que clicar em "*Sim*" na janela de caixa de diálogo que é exibida após um aplicativo está habilitado. Este clique reconhece que o cliente está disposto para o aplicativo de parceiro para acessar seus dados, para que você ou o parceiro não clique no botão Sim.
2.  Se um administrador de cliente que tenha habilitado um aplicativo usando o administrador de T & E perfil deixa a empresa (resultando no perfil sendo desativado), qualquer aplicativos ativados usando que perfil não funcionará até que o aplicativo esteja habilitado com outro perfil de administrador WS ativo. É por isso você precisa para criar distintas WS administrador perfis.
3.  Se um administrador deixar a empresa, o nome do associado ao perfil WS administração pode ser alterado para o administrador de substituição se desejado sem afetar que o aplicativo habilitado porque esse perfil não precisa desativado

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **Concur** .

2.  No menu **Administração** , selecione **Serviços Web**.

    ![Concur locatário] (./media/active-directory-saas-concur-tutorial/IC721729.png "Concur locatário")

3.  No lado esquerdo, o painel de **Serviços Web** , selecione **Habilitar o aplicativo do parceiro**.

    ![Habilitar o aplicativo do parceiro] (./media/active-directory-saas-concur-tutorial/IC721730.png "Habilitar o aplicativo do parceiro")

4.  Na lista de **Habilitar o aplicativo** , selecione **Active Directory do Azure**e clique em **Habilitar**.

    ![Active Directory do Microsoft Azure] (./media/active-directory-saas-concur-tutorial/IC721731.png "Active Directory do Microsoft Azure")

5.  Clique em **Sim** para fechar a caixa de diálogo **Confirmar a ação** .

    ![Confirmar ação] (./media/active-directory-saas-concur-tutorial/IC721732.png "Confirmar ação")

6.  No portal do Azure clássico, selecione **Concur** na lista de aplicativos para abrir a página de diálogo **Concur** .

7.  Para abrir a página de diálogo **Configurar provisionamento do usuário** , clique em **configurar provisionamento de usuário**.

8.  Insira o nome de usuário e a senha do administrador do Concur e clique em **Avançar**.

9.  Para concluir a configuração, na página de **confirmação** , clique no botão **concluído** .

Agora você pode criar uma conta de teste, aguarde 10 minutos e verifique se que a conta foi sincronizada para Concur.
##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-concur-perform-the-following-steps"></a>Para atribuir usuários a Concur, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Concur **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-concur-tutorial/IC769771.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-concur-tutorial/IC767830.png "Sim")

Agora, você deve aguardar de 10 minutos e confirmar que a conta foi sincronizada para Concur.

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
