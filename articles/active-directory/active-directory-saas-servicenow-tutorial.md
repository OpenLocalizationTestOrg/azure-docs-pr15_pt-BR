<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com ServiceNow e ServiceNow Express | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e ServiceNow e ServiceNow Express."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-servicenow-and-servicenow-express"></a>Tutorial: Integração do Active Directory do Azure com ServiceNow e ServiceNow Express.


Neste tutorial, você aprenderá a integrar ServiceNow e ServiceNow Express com o Azure Active Directory (AD Azure).

Integração ServiceNow e ServiceNow Express com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao ServiceNow e ServiceNow Express
- Você pode habilitar os usuários para automaticamente obter conectado em ServiceNow e ServiceNow Express (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ServiceNow e ServiceNow Express, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Para ServiceNow, uma instância ou Locatário de ServiceNow, versão Calgary ou superior
- Para ServiceNow Express, uma instância de ServiceNow Express, versão Helsinque ou superior
- ServiceNow locatário deve ter [Vários provedor única entrada no plug-in](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) habilitado. Isso pode ser feito enviando uma solicitação de serviço em <https://hi.service-now.com/> 


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testar Azure AD logon único em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando ServiceNow da Galeria
2. Configurando e testando Azure AD single sign-on para ServiceNow ou ServiceNow Express


## <a name="adding-servicenow-from-the-gallery"></a>Adicionando ServiceNow da Galeria
Para configurar a integração de ServiceNow ou ServiceNow Express em Azure AD, você precisa adicionar ServiceNow da galeria à sua lista de aplicativos de SaaS gerenciados. 

**Para adicionar ServiceNow da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **ServiceNow**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)

7. No painel de resultados, selecione **ServiceNow**e clique em **concluído** para adicionar o aplicativo.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
Nesta seção, configure e teste Azure AD SSO com ServiceNow ou ServiceNow Express com base em um usuário de teste chamado "Britta Simon".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no ServiceNow a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em ServiceNow precisa ser estabelecida.
Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no ServiceNow. Para configurar e testar o Azure AD logon único com ServiceNow, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On para ServiceNow](#configuring-azure-ad-single-sign-on-for-servicenow)** - para permitir que seus usuários usar este recurso.
2. **[Configurando Azure AD Single Sign-On para ServiceNow Express](#configuring-azure-ad-single-sign-on-for-servicenow-express)** - para permitir que seus usuários usar este recurso.
3. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um ServiceNow testar usuário](#creating-a-servicenow-test-user)** - ter um representante de Britta Simon em ServiceNow que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
6. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

> [AZURE.NOTE] Se você quiser configurar ServiceNow pular a etapa 2. Da mesma forma, se você quiser configurar ServiceNow Express pular a etapa 1.

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>Configurando Azure AD Single Sign-On para ServiceNow

1.  No portal clássico do Azure AD, na página de integração do aplicativo **ServiceNow** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar ServiceNow** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurar o logon único")

3.  Na página **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurar a URL do aplicativo")

    a. Na caixa de texto **ServiceNow entrada na URL** , digite sua URL usada pelos usuários para logon para seu aplicativo ServiceNow seguindo o padrão: `https://<instance-name>.service-now.com`.

    b. Na caixa de texto **identificador** , digite sua URL usada pelos usuários para logon para seu aplicativo ServiceNow seguindo o padrão: `https://<instance-name>.service-now.com`.

    c. Clique em **Avançar**

4.  Para que o Azure AD configurar automaticamente ServiceNow para autenticação baseada em SAML, insira seu nome de instância ServiceNow, nome de usuário de administrador e senha de administrador no formulário **Auto configurar logon único** e clique em *Configurar*. Observe que o nome de usuário de administrador fornecido deve ter a função de **security_admin** atribuída em ServiceNow para que isso funcione. Caso contrário, para configurar manualmente ServiceNow para usar o Azure AD como um provedor de identidade SAML, clique em **Configurar manualmente o aplicativo para logon único**, clique em **Avançar** e conclua as etapas a seguir.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurar a URL do aplicativo")



5.  Na página **Configurar logon único em ServiceNow** , clique em **certificado de Download**, salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurar o logon único")

1. Logon no aplicativo ServiceNow como administrador.
2. Ative o plug-in _integração - vários provedor Single Sign-On Installer_ seguindo as próximas etapas:

    a. No painel de navegação no lado esquerdo, vá para a seção de **Definição de sistema** e clique em **Plug-ins**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "Ativar plug-in")
    
    b. Procurar _integração - vários Single Sign-On instalador do fornecedor_.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "Ativar plug-in")

    c. Selecione o plug-in. Rigth clique e selecione **Ativar/atualização**.
    
    d. Clique no botão **Ativar** .

2. No painel de navegação no lado esquerdo, clique em **Propriedades**.  

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Configurar a URL do aplicativo")


3. Na caixa de diálogo **Várias propriedades de SSO do provedor** , execute as seguintes etapas:

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configurar a URL do aplicativo")

    a. Como **Habilitar o provedor múltiplo SSO**, selecione **Sim**.

    b. Como **Habilitar log de depuração adquiriu o provedor de vários integração de SSO**, selecione **Sim**.

    c. Na caixa de texto de **campo no usuário tabela que...** , digite **nome_de_usuário**.

    d. Clique em **Salvar**.



1. No painel de navegação no lado esquerdo, clique em **certificados x509**.

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Configurar o logon único")


1. Na caixa de diálogo **Certificados x. 509** , clique em **novo**.

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configurar o logon único")


1. Na caixa de diálogo **Certificados x. 509** , execute as seguintes etapas:

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurar o logon único")

    a. Clique em **novo**.

    b. Na caixa de texto **nome** , digite um nome para a sua configuração (por exemplo: **TestSAML2.0**).

    c. Selecione **ativo**.

    d. Como **Formatar**, selecione **PEM**.

    e. Como **tipo**, selecione o **Certificado do repositório de confiabilidade**.
    
    f. Abrir o certificado de codificação Base64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo à caixa de texto **PEM certificado** .

    g. Clique em **Atualizar**.


1. No painel de navegação no lado esquerdo, clique em **Provedores de identidade**.

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Configurar o logon único")

1. Na caixa de diálogo **Provedores de identidade** , clique em **novo**:

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configurar o logon único")


1. Na caixa de diálogo **Provedores de identidade** , clique em **SAML2 Update1?**:

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configurar o logon único")


1. Na caixa de diálogo Propriedades de Update1 SAML2, execute as seguintes etapas:

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configurar o logon único")


    a. Na caixa de texto **nome** , digite um nome para a sua configuração (por exemplo: **SAML 2.0**).

    b. No **Campo usuário** caixa de texto, digite **email** ou **user_id**, dependendo de qual campo é usado para identificar os usuários na sua implantação ServiceNow exclusivamente. 
    
    > [AZURE.NOTE] Você pode configurar Azure AD para emitir a ID de usuário do Azure AD (UPN) ou o endereço de email como o identificador exclusivo no token SAML indo para o **ServiceNow > atributos > Single Sign-On** seção de portal clássico do Azure e o mapeamento de campo desejado para o atributo de **identificador de nome** . O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome de usuário principal) deve corresponder o valor armazenado no ServiceNow do campo inserido (por exemplo, user_id)

    c. No portal de clássico do Azure AD, copie o valor de **ID do provedor de identidade** e cole-o na caixa de texto **URL do provedor de identidade** .

    d. No portal de clássico do Azure AD, copie o valor de **URL de solicitação de autenticação** e, em seguida, cole a caixa de texto **AuthnRequest do provedor de identidade** .

    e. No portal de clássico do Azure AD, copie o valor de **URL do serviço Sign-Out único** e cole-o na caixa de texto **SingleLogoutRequest do provedor de identidade** .

    f. Na caixa de texto **Home page do ServiceNow** , digite a URL da sua home page instância de ServiceNow.

    > [AZURE.NOTE] A home page instância de ServiceNow é uma concatenação de seus **ServieNow locatário URL** e **/navpage.do** (por exemplo:`https://fabrikam.service-now.com/navpage.do`).
 

    g. No **ID da entidade / emissor** caixa de texto, digite a URL do seu locatário ServiceNow.

    h. Na caixa de texto **URL de audiência** , digite a URL do seu locatário ServiceNow. 

    Eu. Na caixa de texto de **Vinculação de protocolo para SingleLogoutRequest do IDP** , digite **urn: oasis: nomes: tc: SAML:2.0:bindings:HTTP-redirecionar**.

    j. Na caixa de texto NameID política, digite **urn: oasis: nomes: tc: SAML:1.1:nameid-formato: não especificado**.

    k. Desmarque a opção de **criar um AuthnContextClass**.

    l. No **Método AuthnContextClassRef**, digite `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`. Isso só é necessário se você estiver nuvem única organização. Se você estiver usando local ADFS ou MFA para autenticação, em seguida, você não deve configurar esse valor. 

    m. Na caixa de texto de **Relógio inclinar** , digite **60**.

    n. Como o **Logon único em Script**, selecione **MultiSSO_SAML2_Update1**.

    o. Como **x509 certificado**, selecione o certificado que você criou na etapa anterior.

    p. Clique em **Enviar**. 



6. No portal de clássico do Azure AD, selecione a confirmação de configuração de logon único e clique em **Avançar**. 

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurar o logon único")

7. Na página **confirmação de logon única** , clique em **Concluir**.
 
    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurar o logon único")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>Configurando Azure AD Single Sign-On para ServiceNow Express

1.  No portal clássico do Azure AD, na página de integração do aplicativo **ServiceNow** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar ServiceNow** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurar o logon único")

3.  Na página **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurar a URL do aplicativo")

    a. Na caixa de texto **ServiceNow entrada na URL** , digite sua URL usada pelos usuários para logon para seu aplicativo ServiceNow seguindo o padrão: `https://<instance-name>.service-now.com`.

    b. Na caixa de texto **URL do emissor** , digite sua URL usada pelos usuários para logon para seu aplicativo ServiceNow seguindo o padrão `https://<instance-name>.service-now.com`.

    c. Clique em **Avançar**

4.  Clicar em **definir manualmente o aplicativo para logon único**, e em seguida, clique em **Avançar** e concluir as etapas a seguir.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurar a URL do aplicativo")

5.  Na página **Configurar logon único em ServiceNow** , clique em **baixar o certificado**, salve o arquivo de certificado localmente em seu computador e clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurar o logon único")

6. Logon no aplicativo ServiceNow Express como administrador.

7. No painel de navegação no lado esquerdo, clique em **Logon único**.  

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Configurar a URL do aplicativo")


8. Na caixa de diálogo **Single Sign-On** , clique no ícone de configuração no canto superior direito e defina as seguintes propriedades:

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Configurar a URL do aplicativo")

    a. Alternar entre **ativar vários provedor SSO** à direita.

    b. Alternar entre **Ativar o registro em log para a integração de SSO vários do provedor de depuração** para a direita.

    c. Na caixa de texto de **campo no usuário tabela que...** , digite **nome_de_usuário**.


9. Na caixa de diálogo **Single Sign-On** , clique em **Adicionar novo certificado**.

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Configurar o logon único")



10. Na caixa de diálogo **Certificados x. 509** , execute as seguintes etapas:

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurar o logon único")

    a. Na caixa de texto **nome** , digite um nome para a sua configuração (por exemplo: **TestSAML2.0**).

    b. Selecione **ativo**.

    c. Como **Formatar**, selecione **PEM**.

    d. Como **tipo**, selecione o **Certificado do repositório de confiabilidade**.

    e. Crie um arquivo de codificação Base64 do seu certificado baixado.
   
    > [AZURE.NOTE] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).
    
    f. Abrir o certificado de codificação Base64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo à caixa de texto **PEM certificado** .

    g. Clique em **Atualizar**.


11. Na caixa de diálogo **Single Sign-On** , clique em **Adicionar novo IdP**.

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Configurar o logon único")


12. Na caixa de diálogo **Adicionar novo provedor de identidade** , em **Configurar provedor de identidade**, execute as seguintes etapas:

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Configurar o logon único")


    a. Na caixa de texto **nome** , digite um nome para a sua configuração (por exemplo: **SAML 2.0**).

    b. No portal de clássico do Azure AD, copie o valor de **ID do provedor de identidade** e cole-o na caixa de texto **URL do provedor de identidade** .

    c. No portal de clássico do Azure AD, copie o valor de **URL de solicitação de autenticação** e, em seguida, cole a caixa de texto **AuthnRequest do provedor de identidade** .

    d. No portal de clássico do Azure AD, copie o valor de **URL do serviço Sign-Out único** e cole-o na caixa de texto **SingleLogoutRequest do provedor de identidade** .

    e. **Certificado de provedor de identidade**, selecione o certificado que você criou na etapa anterior.


13. Clique em **Configurações avançadas**e em **Propriedades de provedor de identidade adicionais**, execute as seguintes etapas:

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Configurar o logon único")

    a. Na caixa de texto de **Vinculação de protocolo para SingleLogoutRequest do IDP** , digite **urn: oasis: nomes: tc: SAML:2.0:bindings:HTTP-redirecionar**.

    b. Na caixa de texto **NameID política** , digite **urn: oasis: nomes: tc: SAML:1.1:nameid-formato: não especificado**.    

    c. No **Método AuthnContextClassRef**, digite **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.
    
    d. Desmarque a opção de **criar um AuthnContextClass**.

14. Em **Propriedades de provedor de serviço adicionais**, execute as seguintes etapas:

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Configurar o logon único")

    a. Na caixa de texto **Home page do ServiceNow** , digite a URL da sua home page instância de ServiceNow.

    > [AZURE.NOTE] A home page instância de ServiceNow é uma concatenação de seus **ServieNow locatário URL** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).

    b. No **ID da entidade / emissor** caixa de texto, digite a URL do seu locatário ServiceNow.

    c. Na caixa de texto **URI da audiência** , digite a URL do seu locatário ServiceNow. 

    d. Na caixa de texto de **Relógio inclinar** , digite **60**.

    e. No **Campo usuário** caixa de texto, digite **email** ou **user_id**, dependendo de qual campo é usado para identificar os usuários na sua implantação ServiceNow exclusivamente.
    
    > [AZURE.NOTE] Você pode configurar Azure AD para emitir a ID de usuário do Azure AD (UPN) ou o endereço de email como o identificador exclusivo no token SAML indo para o **ServiceNow > atributos > Single Sign-On** seção de portal clássico do Azure e o mapeamento de campo desejado para o atributo de **identificador de nome** . O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome de usuário principal) deve corresponder o valor armazenado no ServiceNow do campo inserido (por exemplo, user_id)

    f. Clique em **Salvar**. 


15. No portal de clássico do Azure AD, selecione a confirmação de configuração de logon único e clique em **Avançar**. 

    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurar o logon único")

16. Na página **confirmação de logon única** , clique em **Concluir**.
 
    ![Configurar o logon único] (./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
O objetivo desta seção é como habilitar o usuário provisionamento de contas de usuário do Active Directory para ServiceNow da estrutura de tópicos.


### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1. No portal clássico de gerenciamento do Azure, na página de integração do aplicativo **ServiceNow** , clique em **configurar provisionamento de usuário**. 

    ![Provisionamento do usuário] (./media/active-directory-saas-servicenow-tutorial/IC769498.png "Provisionamento do usuário")


2. Na página **Insira suas credenciais de ServiceNow para habilitar o provisionamento automático de usuário** , forneça as seguintes configurações: configurar provisionamento do usuário 

     a. Na caixa de texto **Nome da instância ServiceNow** , digite o nome da instância ServiceNow.

     b. Na caixa de texto **Nome de usuário de administrador ServiceNow** , digite o nome da conta de administrador ServiceNow.

     c. Na caixa de texto **Senha de administrador do ServiceNow** , digite a senha para essa conta.

     d. Clique em **Validar** para verificar a configuração.

     e. Clique no botão **próximo** para abrir a página de **próximas etapas** .

     f. Se você quiser provisionar todos os usuários para este aplicativo, selecione "**provisionar automaticamente todas as contas de usuário no diretório para este aplicativo**". 

    ![Próximas etapas] (./media/active-directory-saas-servicenow-tutorial/IC698804.png "Próximas etapas")

     g. Na página **próximas etapas** , clique em **concluído** para salvar sua configuração.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
Nesta seção, você criar um usuário de teste no portal do clássico chamado Britta Simon.

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:
 
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   


### <a name="creating-a-servicenow-test-user"></a>Criar um usuário de teste ServiceNow

Nesta seção, você criar um usuário chamado Britta Simon no ServiceNow. Nesta seção, você criar um usuário chamado Britta Simon no ServiceNow. Se você não souber como adicionar um usuário em sua conta ServiceNow ou ServiceNow Express, contate a equipe de suporte de ServiceNow.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

Nesta seção, você habilitar Britta Simon usar logon único Azure concedendo o acesso aos ServiceNow.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a ServiceNow, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **ServiceNow**.

    ![Configurar o logon único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de todos os usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]


### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.

Quando você clica no bloco de ServiceNow no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de ServiceNow.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png
