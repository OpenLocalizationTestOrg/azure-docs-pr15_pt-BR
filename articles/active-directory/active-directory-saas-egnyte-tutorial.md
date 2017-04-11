<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Egnyte | Microsoft Azure" 
    description="Saiba como usar Egnyte com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Tutorial: Integração com o Active Directory do Azure com Egnyte
  
O objetivo deste tutorial é mostrar a integração do Azure e Egnyte.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Egnyte logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Egnyte poderão logon único para o aplicativo no site da sua empresa Egnyte (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md)
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Egnyte
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-egnyte-tutorial/IC787812.png "Cenário")
##<a name="enabling-the-application-integration-for-egnyte"></a>Habilitar a integração de aplicativo para Egnyte
  
O objetivo desta seção é como habilitar a integração de aplicativo para Egnyte da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-egnyte-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Egnyte, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-egnyte-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-egnyte-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-egnyte-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-egnyte-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **egnyte**.

    ![Galeria de aplicativo] (./media/active-directory-saas-egnyte-tutorial/IC787813.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Egnyte**e clique em **concluído** para adicionar o aplicativo.

    ![Egnyte] (./media/active-directory-saas-egnyte-tutorial/IC787814.png "Egnyte")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Egnyte com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Egnyte** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-egnyte-tutorial/IC787815.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Egnyte** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-egnyte-tutorial/IC787816.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Egnyte URL de entrada** , digite sua URL usando o seguinte padrão "*https://company.egnyte.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-egnyte-tutorial/IC787817.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Egnyte** , clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-egnyte-tutorial/IC787818.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Egnyte como administrador.

6.  Clique em **configurações**.

    ![Configurações] (./media/active-directory-saas-egnyte-tutorial/IC787819.png "Configurações")

7.  No menu, clique em **configurações**.

    ![Configurações] (./media/active-directory-saas-egnyte-tutorial/IC787820.png "Configurações")

8.  Clique na guia **configuração** e clique em **segurança**.

    ![Segurança] (./media/active-directory-saas-egnyte-tutorial/IC787821.png "Segurança")

9.  Na seção **Autenticação de logon único** , execute as seguintes etapas:

    ![Autenticação de logon único] (./media/active-directory-saas-egnyte-tutorial/IC787822.png "Autenticação de logon único")

    1.  Como **autenticação de logon único**, selecione **SAML 2.0**.
    2.  Como **provedor de identidade**, selecione **AzureAD**.
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Egnyte** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de login do provedor de identidade** .
    4.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Egnyte** , copie o valor de **ID de entidade** e, em seguida, cole a caixa de texto de **ID de entidade de provedor de identidade** .
    5.  Crie um arquivo **codificado na base 64** do seu certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo para a caixa de texto de **certificado de provedor de identidade** .
    7.  Como **padrão de mapeamento de usuário**, selecione o **endereço de Email**.
    8.  Como **usar o valor de emissor específico do domínio**, selecione **desabilitado**.
    9.  Clique em **Salvar**.

10. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-egnyte-tutorial/IC787823.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Egnyte, ele devem ser provisionados em Egnyte.  
No caso de Egnyte, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa do Egnyte **Egnyte** como administrador.

2.  Vá para **configurações \> usuários e grupos**.

3.  Clique em **Adicionar novo usuário**e, em seguida, selecione o tipo de usuário que você deseja adicionar.

    ![Usuários] (./media/active-directory-saas-egnyte-tutorial/IC787824.png "Usuários")

4.  Na seção **Novo usuário padrão** , execute as seguintes etapas:

    ![Novo usuário padrão] (./media/active-directory-saas-egnyte-tutorial/IC787825.png "Novo usuário padrão")

    1.  Digite o **Email**, **nome de usuário** e outros detalhes de uma conta do Active Directory do Azure válida que deseja provisionar.
    2.  Clique em **Salvar**.

    >[AZURE.NOTE] O proprietário da conta do Active Directory do Azure receberão um email de notificação.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Egnyte usuário conta ou APIs fornecidas pela Egnyte para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-egnyte-perform-the-following-steps"></a>Para atribuir usuários a Egnyte, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Egnyte **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-egnyte-tutorial/IC787826.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-egnyte-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).