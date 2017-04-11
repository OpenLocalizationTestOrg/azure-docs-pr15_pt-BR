<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Salesforce | Microsoft Azure"
    description="Saiba como usar Salesforce com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="05/16/2016"
    ms.author="asmalser-msft"/>

#<a name="tutorial-how-to-integrate-salesforce-with-azure-active-directory"></a>Tutorial: Como integrar Salesforce com o Active Directory do Azure

Este tutorial mostrará como conectar o seu ambiente de Salesforce ao Azure Active Directory. Você aprenderá como configurar o logon único para a equipe de vendas, como habilitar o provisionamento automatizado de usuário e como atribuir usuários tenham acesso à equipe de vendas.

##<a name="prerequisites"></a>Pré-requisitos

1. Para acessar o Active Directory do Azure por meio do [portal clássico Azure](https://manage.windowsazure.com), primeiro você deve ter uma assinatura válida do Azure.

2. Você deve ter um locatário válido na [Salesforce.com](https://www.salesforce.com/).

> [AZURE.IMPORTANT] Se você estiver usando uma conta de **avaliação** Salesforce.com, não será possível configurar provisionamento automatizado de usuário. Contas de avaliação não tem o acesso de API necessário ativado até que eles são comprados.
> 
> Você pode contornar essa limitação usando uma [conta de desenvolvedor gratuita](https://developer.salesforce.com/signup) para concluir este tutorial.

Se você estiver usando um ambiente de área restrita de Salesforce, consulte o [tutorial de integração de área restrita de Salesforce](https://go.microsoft.com/fwLink/?LinkID=521879).

##<a name="video-tutorials"></a>Tutoriais em vídeo

Você pode seguir este tutorial usando os vídeos abaixo.

**Tutorial em vídeo parte 1: como habilitar o logon único**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**Tutorial em vídeo parte dois: Como automatizar provisionamento do usuário**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##<a name="step-1-add-salesforce-to-your-directory"></a>Etapa 1: Adicionar Salesforce ao seu diretório

1. No [portal do Azure clássico](https://manage.windowsazure.com), no painel de navegação esquerdo, clique em **Active Directory**.

    ![Selecione o Active Directory do painel de navegação à esquerda.][0]

2. Na lista de **diretório** , selecione o diretório que você gostaria de adicionar Salesforce para.

3. Clique em **aplicativos** no menu superior.

    ![Clique em aplicativos.][1]

4. Clique em **Adicionar** na parte inferior da página.

    ![Clique em Adicionar para adicionar um novo aplicativo.][2]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Clique em Adicionar um aplicativo da Galeria.][3]

6. Na **caixa de pesquisa**, digite **Salesforce**. Selecione **Salesforce** dos resultados e clique em **concluído** para adicionar o aplicativo.

    ![Adicione equipe de vendas.][4]

7. Agora, você verá a página de início rápido para a equipe de vendas:

    ![Página de início rápido do SalesForce no Azure AD][5]

##<a name="step-2-enable-single-sign-on"></a>Etapa 2: Habilitar logon único

1. Antes de configurar o logon único, você deve configurar e implantar um domínio personalizado para seu ambiente de Salesforce. Para obter instruções sobre como fazer isso, consulte [Definir para cima um nome de domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US).

2. Na página início rápido do Salesforce no Azure AD, clique no botão de **logon único configurar** .

    ![O único logon botão Configurar][6]

3. Uma caixa de diálogo será aberta e você verá uma tela que pergunta "Como você gostaria usuários para entrar equipe de vendas?" Selecione **Azure AD Single Sign-On**e clique em **Avançar**.

    ![Selecione Azure AD Single Sign-On][7]

    > [AZURE.NOTE] Para aprender mais sobre as diferentes única logon opções, [clique aqui](../active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

4. Na página **Definir configurações de aplicativo** , preencha a **Entrada na URL** digitando na sua URL de domínio Salesforce usando o seguinte formato:
 - Conta da empresa:`https://<domain>.my.salesforce.com`
 - Conta de desenvolvedor:`https://<domain>-dev-ed.my.salesforce.com` 

    ![Digite sua URL de logon][8]

5. Na página **Configurar logon único em Salesforce** , clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado localmente em seu computador.

    ![Baixar certificado][9]

6. Abra uma nova guia em seu navegador e faça logon em sua conta de administrador Salesforce.

7. No painel de navegação de **administrador** , clique em **Controles de segurança** para expandir a seção relacionada. Em seguida, clique em **Configurações de logon único**.

    ![Clique em configurações de logon única em controles de segurança][10]

8. Na página **Configurações de logon único** , clique no botão **Editar** .

    ![Clique no botão Editar][11]

    > [AZURE.NOTE] Se você não consegue habilitar configurações de logon único para sua conta de Salesforce, você talvez precise contatar o suporte da equipe de vendas para o recurso habilitado para você.

9. Selecione **SAML habilitado**e clique em **Salvar**.

    ![Selecione SAML habilitado][12]

10. Para definir sua única SAML logon configurações, clique em **novo**.

    ![Selecione SAML habilitado][13]

11. Na página **SAML Single Sign-On configuração Editar** , verifique as seguintes configurações:

    ![Captura de tela das configurações que você deve fazer][14]

 - Para o campo de **nome** , digite um nome amigável para esta configuração. Fornecer um valor para **nome** preencher automaticamente a caixa de texto **Nome da API** .

 - No Azure AD, copie o valor de **URL do emissor** e colá-lo no campo **emissor** de Salesforce.

 - Na **caixa de texto de Id de entidade**, digite seu nome de domínio de Salesforce usando o seguinte padrão:
     - Conta da empresa:`https://<domain>.my.salesforce.com`
     - Conta de desenvolvedor:`https://<domain>-dev-ed.my.salesforce.com`

 - Clique em **Procurar** ou **Escolher arquivo** para abrir a caixa de diálogo **Escolher arquivo para carregar** , selecione o certificado de Salesforce e, em seguida, clique em **Abrir** para carregar o certificado.

 - Para **Tipo de identidade SAML**, selecione a **declaração contém o nome de usuário de salesforce.com do usuário**.

 - **Local de identidade SAML**, selecione **identidade é no elemento do identificador de nome da política de assunto**

 - No Azure AD, copie o valor de **URL de Login remoto** e colá-lo no campo **URL de Login do provedor de identidade** de Salesforce.

 - Para o **Serviço provedor iniciada solicitar encadernação**, selecione **Redirecionamento HTTP**.

 - Por fim, clique em **Salvar** para aplicar sua única SAML logon configurações.

12. No painel de navegação esquerdo no Salesforce, clique em **Gerenciamento de domínios** para expandir a seção relacionada e, em seguida, clique em **Meu domínio**.

    ![Clique em meu domínio][15]

13. Role para baixo até a seção **Configuração de autenticação** e clique no botão **Editar** .

    ![Clique no botão Editar][16]

14. Na seção **Serviço de autenticação** , selecione o nome amigável da configuração SAML SSO e clique em **Salvar**.

    ![Selecione a configuração de SSO][17]

    > [AZURE.NOTE] Se mais de um serviço de autenticação estiver selecionado, em seguida, quando os usuários tentam iniciar logon único para seu ambiente de Salesforce, ele será solicitado para selecionar qual serviço de autenticação que gostaria de entrar. Se você não quiser que isso aconteça, você deve **deixar todos os outros serviços de autenticação desmarcados**.

15. No Azure AD, marque a caixa de seleção de confirmação de configuração de logon único para habilitar o certificado que você carregou em Salesforce. Clique em **Avançar**.

    ![Marque a caixa de seleção de confirmação][18]

16. Na página final da caixa de diálogo, digite um endereço de email se você gostaria de receber notificações de email para erros e avisos relacionados à manutenção dessa configuração de logon único. 

    ![Digite seu endereço de email.][19]

17. Clique em **Concluir** para fechar a caixa de diálogo. Para testar sua configuração, consulte a seção abaixo, intitulada [Atribuir usuários a equipe de vendas](#step-4-assign-users-to-salesforce).

##<a name="step-3-enable-automated-user-provisioning"></a>Etapa 3: Ative provisionamento automatizado de usuário

1. Na página de início rápido do Azure AD de Salesforce, clique no botão **configurar provisionamento de usuário** .

    ![Clique no botão Configurar provisionamento do usuário][20]

2. Na caixa de diálogo **configurar provisionamento de usuário** , digite seu nome de usuário de administrador de Salesforce e a senha.

    ![Digite sua senha ou o nome de usuário de administrador][21]

    > [AZURE.NOTE] Se você estiver configurando um ambiente de produção, a prática recomendada é criar uma nova conta de administrador no Salesforce especificamente para esta etapa. Essa conta deve ter o perfil de **Administrador do sistema** atribuído no Salesforce.

3. Para obter a segurança de Salesforce token, abra uma nova guia e entre para a mesma conta de administrador de Salesforce. No canto superior direito da página, clique no seu nome e, em seguida, clique em **Configurações do meu**.

    ![Clique no seu nome e clique em minhas configurações][22]

4. No painel de navegação esquerdo, clique em **pessoal** para expandir a seção relacionada e clique em **Redefinir minhas símbolo de segurança**.

    ![Clique no seu nome e clique em minhas configurações][23]

5. Na página **Redefinir minhas símbolo de segurança** , clique no botão **Redefinir o Token de segurança** .

    ![Leia os avisos.][24]

6. Marque a caixa de entrada de email associada a essa conta de administrador. Procure um email do Salesforce.com que contém o novo token de segurança.

7. Copie o token, vá para a janela do Azure AD e cole-o no campo **Token de segurança do usuário** . Clique em **Avançar**.

    ![Colar no token de segurança][25]

8. Na página confirmação, você pode optar por receber notificações de email para quando ocorrem falhas de provisionamento. Clique em **Concluir** para fechar a caixa de diálogo.

    ![Digite seu endereço de email para receber notificações][26]

##<a name="step-4-assign-users-to-salesforce"></a>Etapa 4: Atribuir usuários a equipe de vendas

1. Para testar sua configuração, comece criando uma nova conta de teste no diretório.

2. Na página de início rápido do Salesforce, clique no botão **Atribuir usuários** .

    ![Clique em atribuir usuários][27]

3. Selecione o usuário de teste e clique no botão **atribuir** na parte inferior da tela:

 - Se ainda não habilite provisionamento automatizado de usuário, você verá a seguinte solicitação para confirmar:

        ![Confirm the assignment.][28]

 - Se você tiver habilitado o provisionamento automatizado do usuário, você verá um aviso para definir o tipo de perfil de Salesforce que o usuário deve ter. Usuários recentemente provisionados devem aparecer no seu ambiente de Salesforce após alguns minutos.

        ![Confirm the assignment.][29]

        > [AZURE.IMPORTANT] Se você é provisionamento para um ambiente de **desenvolvedor** do Salesforce, você terá um número muito limitado de licenças disponíveis para cada perfil. Portanto, é melhor para provisionar usuários para o perfil de **Usuário livre Chatter** , que tem 4.999 licenças disponíveis.

4. Para testar suas configurações de logon única, abra o painel de acesso em [https://myapps.microsoft.com](https://myapps.microsoft.com/), e em seguida, entre na conta do teste e clique em **Salesforce**.

##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png
