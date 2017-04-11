<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com área restrita de Salesforce | Microsoft Azure"
    description="Saiba como usar a área restrita de Salesforce com o Azure Active Directory para habilitar o logon único, automatizado de provisionamento e muito mais!." 
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
    ms.date="10/28/2016" 
    ms.author="jeedes" />


#<a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutorial: Integração com o Active Directory do Azure com área restrita de Salesforce
>[AZURE.TIP]Para enviar comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=521878).
  
O objetivo deste tutorial é mostrar a integração do Azure e seguro de Salesforce.  
Caixas oferecem a capacidade de criar várias cópias de sua organização em ambientes separadas para diversas finalidades, como desenvolvimento, teste e treinamento, sem comprometer os dados e aplicativos em sua organização de produção de Salesforce.  
Para obter mais detalhes, consulte [Visão geral de área restrita](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)
  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma área restrita no Salesforce.com
  
Se você ainda não tem uma área restrita válida no Salesforce.com, você precise contatar a equipe de vendas.
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo de área restrita de Salesforce
2.  Configurando o logon único
3.  Habilitando seu domínio
4.  Configuração de provisionamento do usuário
5.  Atribuir usuários

![Cenário] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Cenário")
##<a name="enabling-the-application-integration-for-salesforce-sandbox"></a>Habilitar a integração de aplicativo de área restrita de Salesforce
  
O objetivo desta seção é como habilitar a integração de aplicativo de área restrita de Salesforce da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-salesforce-sandbox-perform-the-following-steps"></a>Para habilitar a integração de aplicativo de área restrita de Salesforce, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "Aplicativos")

4.  Para abrir a **Galeria de aplicativo**, clique em **Adicionar um aplicativo**e clique em **Adicionar um aplicativo para minha organização usar**.

    ![o que você deseja fazer?] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "o que você deseja fazer?")

5.  Na **caixa de pesquisa**, digite **Área restrita de Salesforce**.

    ![Galeria de aplicativo] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Galeria de aplicativo")

6.  No painel de resultados, selecione **Área restrita de Salesforce**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Área restrita de SalesForce] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Área restrita de SalesForce")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Salesforce com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo de **Área restrita de Salesforce** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "Configurar o logon único")

2.  Na página **como você gostaria que usuários para entrar área restrita de Salesforce** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Área restrita de SalesForce] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Área restrita de SalesForce")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto de **Entrada na URL** , digite sua URL usando o seguinte padrão `http://company.my.salesforce.com`e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "Configurar a URL do aplicativo")

4. Se você já tiver configurado logon único para outra instância de área restrita de Salesforce no seu diretório, você também deve configurar o **identificador** para ter o mesmo valor que a **entrar na URL**. O campo **identificador** pode ser encontrado marcando a caixa de seleção **Mostrar configurações avançadas** na página **Configurar a URL do aplicativo** da caixa de diálogo.

4.  Na página **Configurar logon único em área restrita de Salesforce** , clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em sua área restrita Salesforce como administrador.

6.  No menu na parte superior, clique em **Configurar**.

    ![Configuração] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "Configuração")

7.  No painel de navegação à esquerda, clique em **Controles de segurança**e, em seguida, clique em **Configurações de logon único**.

    ![Configurações de logon único] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Configurações de logon único")

8.  Na seção configurações de logon único, execute as seguintes etapas:

    ![Configurações de logon único] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Configurações de logon único")

    a.  Selecione **SAML habilitado**.
    
    b.  Clique em **novo**.

9.  Na seção configurações de logon único SAML, execute as seguintes etapas:

    ![Configurações de logon único SAML] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "Configurações de logon único SAML")

    a.  Na caixa de texto Nome, digite o nome da configuração (por exemplo: *SPSSOWAAD\_teste*).
    
    b.  No portal do clássico Azure, na página da caixa de diálogo **Configurar logon único em área restrita de Salesforce** , copie o valor de **URL do emissor** e, em seguida, cole a caixa de texto do **emissor** .
    
    c.  Na caixa de texto **Id da entidade** , digite **https://test.salesforce.com** se esta for a primeira instância de área restrita de Salesforce que você está adicionando ao seu diretório. Se você já tiver adicionado uma instância de área restrita de Salesforce, em seguida, para o tipo de **ID de entidade** no **Sinal na URL**, que deve estar neste formato:`http://company.my.salesforce.com`
    
    d.  Clique em **Procurar** para carregar o certificado baixado.
    
    e.  Como **O tipo de identidade SAML**, selecione a **declaração contém a ID de Federação do objeto do usuário**.
    
    f.  Como **Local de identidade SAML**, selecione a **identidade é no elemento do identificador de nome da política de assunto**.
    
    g.  No portal do clássico Azure, na página da caixa de diálogo **Configurar logon único em área restrita de Salesforce** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login do provedor de identidade** .
    
    h.  SFDC não suporta SAML logout.  Como alternativa, cole 'https://login.windows.net/common/wsfederation?wa=wsignout1.0'-la na caixa de texto **URL de Logout do provedor de identidade** .
    
    Eu.  Como o **Serviço provedor iniciada solicitar encadernação**, selecione **HTTP POST**.
    
    j. Clique em **Salvar**.

10. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Configurar o logon único")

##<a name="enabling-your-domain"></a>Habilitando seu domínio
  
Esta seção pressupõe que você já tiver criado um domínio.  
Para obter mais detalhes, consulte [Definir seu nome de domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

###<a name="to-enable-your-domain-perform-the-following-steps"></a>Para habilitar o seu domínio, execute as seguintes etapas:

1.  No painel de navegação esquerdo, clique em **Gerenciamento de domínio**e clique em **Meu domínio.**

    ![Meu domínio] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "Meu domínio")

    >[AZURE.NOTE]Certifique-se de que seu domínio foi configurado corretamente.

2.  Na seção **Configurações de página de Login** , clique em **Editar**, em seguida, como o **Serviço de autenticação**, selecione o nome da SAML Single Sign-On configuração da seção anterior e finalmente clique em **Salvar**.

    ![Meu domínio] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "Meu domínio")
  
Assim que você tiver um domínio configurado, seus usuários devem usar a URL de domínio para logon ao Salesforce modo seguro.  
Para obter o valor da URL, clique no perfil SSO que você criou na seção anterior.
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
O objetivo desta seção é como habilitar o usuário provisionamento de contas de usuário do Active Directory à equipe de vendas de proteção de estrutura de tópicos.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  No portal do Salesforce, na barra de navegação superior, selecione seu nome para expandir o menu de usuário:

    ![Minhas configurações] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "Minhas configurações")

2.  Do seu menu de usuário, selecione **Configurações de meu** abrir a página **Minhas configurações** .

3.  No painel esquerdo, clique em **pessoal** para expandir a seção pessoal e clique em **Redefinir minhas símbolo de segurança**:

    ![Minhas configurações] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "Minhas configurações")

4.  Na página **Redefinir minhas símbolo de segurança** , clique em **Redefinir o Token de segurança** para solicitar um email que contenha o token de segurança Salesforce.com.

    ![Novo símbolo] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "Novo símbolo")

5.  Marque sua caixa de entrada de email para um email da Salesforce.com com "**confirmação de segurança de salesforce.com.com**" como assunto.

6.  Examine o e-mail e copie o valor de token de segurança.

7.  No portal do clássico Azure, na página de integração de aplicativo de **salesforce área restrita** , clique em **configurar provisionamento de usuário** para abrir a caixa de diálogo **Configurar provisionamento do usuário** .

    ![Configurar provisionamento de usuário] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "Configurar provisionamento de usuário")

8.  Na página **Insira suas credenciais de área restrita de Salesforce para habilitar o provisionamento automático de usuário** , forneça as seguintes configurações:

    ![Área restrita de SalesForce] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Área restrita de SalesForce")

    a.  Na caixa de texto **Nome de usuário de administrador de área restrita de Salesforce** , digite um nome de conta de área restrita de Salesforce que tenha o perfil de **Administrador do sistema** no Salesforce.com atribuído.

    b.  Na caixa de texto **Senha de administrador de área restrita de Salesforce** , digite a senha para essa conta.

    c.  Na caixa de texto **Token de segurança do usuário** , cole o valor de token de segurança.

    d.  Clique em **Validar** para verificar a configuração.

    e.  Clique no botão **próximo** para abrir a página de **confirmação** .

9.  Na página **confirmação** , clique em **concluído** para salvar sua configuração.
##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-salesforce-sandbox-perform-the-following-steps"></a>Para atribuir usuários a área restrita de Salesforce, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo de **Área restrita de Salesforce **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Sim")
  
Agora, você deve aguardar de 10 minutos e confirmar que a conta foi sincronizada a área restrita de Salesforce.
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](https://msdn.microsoft.com/library/dn308586).
