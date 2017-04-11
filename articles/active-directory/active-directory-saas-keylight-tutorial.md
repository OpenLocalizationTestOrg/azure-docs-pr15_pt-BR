<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Keylight | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e Keylight."
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
    ms.date="09/29/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-keylight"></a>Tutorial: Integração com o Active Directory do Azure com Keylight

Neste tutorial, você aprenderá a integrar Keylight com o Azure Active Directory (AD Azure).

Integração Keylight com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Keylight
- Você pode habilitar os usuários para automaticamente obter assinado no Keylight (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Keylight, você precisa dos seguintes itens:

- Uma assinatura do Azure
- Um logon único Keylight na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testar Azure AD logon único em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Keylight da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-keylight-from-the-gallery"></a>Adicionando Keylight da Galeria
Para configurar a integração do Keylight no Azure AD, você precisa adicionar Keylight da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar Keylight da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Keylight**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_01.png)

7. No painel de resultados, selecione **Keylight**e clique em **concluído** para adicionar o aplicativo.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
Nesta seção, configurar e testar logon único Azure AD com Keylight com base em um usuário de teste chamado "Simon Britta".

Para configurar e testar o Azure AD logon único com Keylight, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um Keylight testar usuário](#creating-a-keylight-test-user)** - ter um representante de Britta Simon em Keylight que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando Azure AD Single Sign-On

Nesta seção, você habilitar Azure AD logon único no portal de clássico do Azure e configurar o logon único em seu aplicativo de Keylight.


**Para configurar o logon único Azure AD com Keylight, execute as seguintes etapas:**

1. No portal do clássico Azure, na página de integração do aplicativo **Keylight** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][6] 


2. Na página **como você gostaria que os usuários para assinar Keylight** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_03.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:
 
    ![Configurar o logon único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_04.png) 


    a. Na caixa de texto de entrada na URL, digite a URL usada pelos usuários para logon para seu aplicativo de Keylight usando o seguinte padrão: **"https://\<nome da empresa\>.keylightgrc.com/Login.aspx?saml=1"**.


4. Na página **Configurar logon único em Keylight** , execute as seguintes etapas:
 
    ![Configurar o logon único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_05.png) 

    a. Clique em **baixar o certificado**e, em seguida, salve o arquivo em seu computador.

    b. Clique em **Avançar**.


5. Para habilitar SSO no Keylight, execute as seguintes etapas:
 
    a. Logon à sua conta de Keylight como administrador.

    b. No menu na parte superior, clique em **pessoas**e selecionar **A configuração de Keylight**.
       
    ![Configurar o logon único](./media/active-directory-saas-keylight-tutorial/401.png) 

    c. Em treeview à esquerda, clique em **SAML**.

    ![Configurar o logon único](./media/active-directory-saas-keylight-tutorial/402.png) 

    d. Na caixa de diálogo **Configurações de SAML** , clique em **Editar**.

    ![Configurar o logon único](./media/active-directory-saas-keylight-tutorial/404.png) 
  

5. Na página de diálogo **Editar configurações de SAML** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-keylight-tutorial/405.png) 

    a. Definir **a autenticação SAML** para **ativo**.


    b. No portal de clássico do Azure AD, copie o valor de **SAML SSO URL** e cole-o na caixa de texto **URL de Login do provedor de identidade** .

    c. No portal de clássico do Azure AD, copie o valor de **URL do serviço Sign-Out único** e cole-o na caixa de texto **URL de Logout do provedor de identidade** .

    d. Clique em **Escolher arquivo** para selecionar o certificado de Keylight baixado e clique em **Abrir** para carregar o certificado.


    e. Definir **local do Id de usuário de SAML** elemento **identificador de nome da política de assunto**.
   
    f. Forneça o **provedor de serviços de Keylight usando o seguinte padrão: **https://&lt;nome da empresa&gt;. keylightgrc.com**.

    g. Definir **Autoprovisionar usuários** como **ativo**.

    h. Defina o **tipo de conta automática provisionar** como **Usuário completo**.

    Eu. Como a **função de segurança de provisionamento automático**, selecione **Usuário padrão com SAML**.
   
    j. Como **provisionar automático configuração de segurança**, selecione **A configuração padrão do usuário**.
   
    k. Na caixa de texto de atributo Email, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    l. Na caixa de texto do **atributo de nome** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    m. Na caixa de texto do **último atributo de nome** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    n. Clique em **Salvar**.
   
  
   
  
6. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.

    ![Azure AD Single Sign-On][10]

7. Na página **confirmação de logon única** , clique em **Concluir**.  

    ![Azure AD Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
Nesta seção, você criar um usuário de teste no portal do clássico Azure chamado Britta Simon.

Na lista de usuários, selecione **Britta Simon**.

![Criar usuário do Azure AD][20]



**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **Portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_09.png) 


2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 


4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-a-keylight-test-user"></a>Criar um usuário de teste Keylight

Nesta seção, você criar um usuário chamado Britta Simon no Keylight. Keylight suporta em vez de provisionamento, que é ativada por padrão.

Não há nenhum item de ação para você nesta seção. Um novo usuário é criado quando estiver acessando Keylight se o usuário ainda não existe. 

> [AZURE.NOTE] Se você precisar criar um usuário manualmente, você precise contatar a equipe de suporte de Keylight.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

Nesta seção, você habilitar Britta Simon usar logon único Azure concedendo o acesso aos Keylight.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a Keylight, execute as seguintes etapas:**

1. No portal do Azure clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Keylight**.

    ![Configurar o logon único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_50.png) 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]



### <a name="testing-single-sign-on"></a>Teste de logon único

Nesta seção, você testar seu Azure AD único logon configuração usando o painel de acesso.

Quando você clica no bloco de Keylight no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de Keylight.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_205.png
