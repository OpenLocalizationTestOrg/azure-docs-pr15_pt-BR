<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Onit | Microsoft Azure" 
    description="Saiba como usar Onit com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-onit"></a>Tutorial: Integração com o Active Directory do Azure com Onit
  
O objetivo deste tutorial é mostrar a integração do Azure e Onit.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Onit logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Onit poderão único entrar no aplicativo no site da sua empresa Onit (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Onit
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-onit-tutorial/IC791166.png "Cenário")
##<a name="enabling-the-application-integration-for-onit"></a>Habilitar a integração de aplicativo para Onit
  
O objetivo desta seção é como habilitar a integração de aplicativo para Onit da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-onit-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Onit, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-onit-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-onit-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-onit-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-onit-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Onit**.

    ![Galeria de aplicativo] (./media/active-directory-saas-onit-tutorial/IC791167.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Onit**e clique em **concluído** para adicionar o aplicativo.

    ![Onit] (./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Onit com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para Onit requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).
  
Seu aplicativo Onit espera as declarações SAML em um formato específico, o que requer que você adicionar os mapeamentos de atributo personalizado à sua configuração de **atributos de token saml** .  
A captura de tela a seguir mostra um exemplo para isso.

![Logon único] (./media/active-directory-saas-onit-tutorial/IC791168.png "Logon único")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Onit** , no menu na parte superior, clique em **atributos** para abrir a caixa de diálogo de **Atributos de Token SAML** .

    ![Atributos] (./media/active-directory-saas-onit-tutorial/IC791169.png "Atributos")

2.  Para adicionar os mapeamentos de atributo obrigatório, execute as seguintes etapas:

    
  	|Nome do atributo|Valor do atributo|
  	|---|---|
  	|nome|User.userPrincipalName|
  	|Email|User.mail|

    1.  Para cada linha de dados na tabela acima, clique em **Adicionar atributo de usuário**.
    2.  Na caixa de texto **Nome do atributo** , digite o nome do atributo mostrado para aquela linha.
    3.  Na lista de **Valor do atributo** , selecione o valor do atributo mostrado para aquela linha.
    4.  Clique em **Concluir**.

3.  Clique em **Aplicar alterações**.

4.  No seu navegador, clique em **Voltar** para abrir a caixa de diálogo **Quick Start** novamente.

5.  Clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-onit-tutorial/IC791170.png "Configurar o logon único")

6.  Na página **como você gostaria que os usuários para assinar Onit** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-onit-tutorial/IC791171.png "Configurar o logon único")

7.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Onit entrada na URL** , digite a URL usada pelos usuários para entrar seu aplicativo de Onit (por exemplo: "*https://ms-sso-test.onit.com*") e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-onit-tutorial/IC791172.png "Configurar a URL do aplicativo")

8.  Na página **Configurar logon único em Onit** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-onit-tutorial/IC791173.png "Configurar o logon único")

9.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Onit como administrador.

10. No menu na parte superior, clique em **Administração**.

    ![Administração] (./media/active-directory-saas-onit-tutorial/IC791174.png "Administração")

11. Clique em **Editar Corporation**.

    ![Editar Corporation] (./media/active-directory-saas-onit-tutorial/IC791175.png "Editar Corporation")

12. Clique na guia **segurança** .

    ![Informações da empresa de edição] (./media/active-directory-saas-onit-tutorial/IC791176.png "Informações da empresa de edição")

13. Na guia **segurança** , execute as seguintes etapas:

    ![Logon único] (./media/active-directory-saas-onit-tutorial/IC791177.png "Logon único")

    1.  Como **Estratégia de autenticação**, selecione **logon e a senha**.
    2.  No portal clássico do Azure, na página de diálogo **Configurar logon único em Onit** , copie o valor de **URL de Login remota** e, em seguida, cole a **URL de destino Idp** de caixa de texto.
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Onit** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **Idp logout URL** .
    4.  Copie o valor de **impressão digital** do certificado exportado e, em seguida, cole a caixa de texto de **Impressão digital do certificado Idp (SHA1)** .  

        >[AZURE.TIP] Para obter mais detalhes, consulte [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    5.  Como o **Tipo de SSO**, selecione **SAML**.
    6.  Na caixa de texto do **texto do botão de logon SSO** , digite um texto de botão que desejar.
    7.  Selecione **Login com SSO: necessário para os seguintes domínios/usuários**, digite o endereço de email de um usuário de teste na caixa de texto relacionada e clique em **Atualizar**.
        ![Editar Corporation] (./media/active-directory-saas-onit-tutorial/IC791178.png "Editar Corporation")

14. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-onit-tutorial/IC791179.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Onit, ele devem ser provisionados em Onit.  
No caso de Onit, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Entre site da sua empresa **Onit** como um administrador.

2.  Clique em **Adicionar usuário**.

    ![Administração] (./media/active-directory-saas-onit-tutorial/IC791180.png "Administração")

3.  Na página de diálogo **Adicionar usuário** , execute as seguintes etapas:

    ![Adicionar usuário] (./media/active-directory-saas-onit-tutorial/IC791181.png "Adicionar usuário")

    1.  Digite o **nome** e o **Endereço de Email** de uma conta AAD válida que você deseja configurar para as caixas de texto relacionadas.
    2.  Clique em **criar**.  

        >[AZURE.NOTE] O proprietário da conta receberão um email incluindo um link para confirmar a conta antes que ele fique ativo.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Onit usuário conta ou APIs fornecidas pela Onit para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-onit-perform-the-following-steps"></a>Para atribuir usuários a Onit, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Onit **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-onit-tutorial/IC791182.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-onit-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).