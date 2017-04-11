<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com o Portal de financiamento | Microsoft Azure"
    description="Saiba como configurar o logon único entre Azure Active Directory e o Portal de financiamento."
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
    ms.date="09/02/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-the-funding-portal"></a>Tutorial: Integração com o Active Directory do Azure com o Portal de financiamento

Neste tutorial, você aprenderá a integrar o Portal de financiamento com o Azure Active Directory (AD Azure).

Integrar o Portal de financiamento Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Portal do financiamento
- Você pode habilitar os usuários para automaticamente obter conectado no Portal do financiamento (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Portal de financiamento, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um **Portal de financiamento do** single sign-na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testar Azure AD logon único em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Portal de financiamento da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-the-funding-portal-from-the-gallery"></a>Adicionando o Portal de financiamento da Galeria
Para configurar a integração do Portal do financiamento no Azure AD, é necessário adicionar o Portal de financiamento da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Portal de financiamento da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **O Portal de financiamento**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_01.png)

7. No painel de resultados, selecione **O Portal de financiamento**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
Nesta seção, configurar e testar logon único Azure AD com o Portal de financiamento com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no Portal do financiamento a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Portal do financiamento precisa ser estabelecida.
Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no Portal do financiamento.

Para configurar e testar o Azure AD logon único com o Portal de financiamento, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criar um Portal de financiamento o teste usuário](#creating-a-the-funding-portal-test-user)** - ter um representante de Britta Simon no Portal do financiamento que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD

O objetivo desta seção é para habilitar Azure AD logon único no portal de clássico do Azure e configurar o logon único em seu aplicativo do Portal de financiamento.

O aplicativo de Portal de financiamento espera as declarações SAML para conter um atributo chamado "externalId1". O valor de "externalId1" deve ser um studentID reconhecido. Configure a declaração de "externalId1" para este aplicativo. Você pode gerenciar os valores desses atributos da guia **"Os atributos do"** do aplicativo. A captura de tela a seguir mostra um exemplo para isso.

![Configurar o logon único](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_03.png) 

**Para configurar o logon único Azure AD com o Portal de financiamento, execute as seguintes etapas:**

1. No portal do clássico Azure, na página de integração do **Portal de financiamento do** aplicativo, no menu na parte superior, clique em **atributos**.
     
    ![Configurar o logon único][5]

2. Na caixa de diálogo de atributos token SAML, adicione o atributo "externalId1".

    a. Clique em **Adicionar atributo de usuário** para abrir a caixa de diálogo **Adicionar atributo de usuário** . 
    
    ![Configurar o logon único](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_05.png)

    b. Na caixa de texto **Nome do atributo** , digite o nome do atributo "externalId1".

    c. Na lista de **Valor do atributo** , selecione o atributo que você deseja usar para sua implementação. Por exemplo, se você armazenou o valor de StudentID na ExtensionAttribute1, selecione user.extensionattribute1.

    d. Clique em **Concluir**. Em seguida, clique em **Aplicar alterações**.

1. No menu na parte superior, clique em **Início rápido**.

    ![Configurar o logon único][6]

2. No portal do clássico, na página de integração do **Portal de financiamento do** aplicativo, clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][7] 

3. Na página **como você gostaria que os usuários para entrar Portal do financiamento** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.
    
    ![Configurar o logon único](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_06.png)

4. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas: 

    ![Configurar o logon único](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_07.png)


    a. Na caixa de texto de entrada na URL, digite uma URL usando o seguinte padrão: `https://<subdomain>.regenteducation.net/`.

    b. Clique em **Avançar**.

5. Na página **Configurar logon único no Portal do financiamento** , clique em **baixar metadados**e, em seguida, salve o arquivo em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_08.png)

6. Para obter o SSO configurado para o seu aplicativo, contate o suporte do Portal de financiamento. Eles auxiliarão com o canal adequado para configurar o SSO. Por favor observe que você precisa enviar email e anexar baixados arquivo de metadados para info@regenteducation.com.

7. No portal do clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.
    
    ![Azure AD Single Sign-On][10]

8. Na página **confirmação de logon única** , clique em **Concluir**.  
    
    ![Azure AD Single Sign-On][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
Nesta seção, você criar um usuário de teste no portal do clássico chamado Britta Simon.

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:
 
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-a-the-funding-portal-test-user"></a>Criar um usuário de teste o Portal de financiamento

Nesta seção, você criar um usuário chamado Britta Simon no Portal do financiamento. Se você não souber como adicionar Britta Simon no Portal do financiamento, trabalhe com a equipe de suporte o Portal de financiamento para adicionar o usuário de teste e habilitar o SSO. Entre em contato com eles em info@regenteducation.com.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

Nesta seção, você habilitar Britta Simon usar logon único Azure concedendo o acesso para o Portal de financiamento.

![Atribuir usuário][200] 

**Para atribuir Britta Simon para o Portal de financiamento, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **O Portal de financiamento**.

    ![Configurar o logon único](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_09.png) 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de todos os usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]


### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.

Quando você clica no bloco do Portal de financiamento no painel de acesso, que deve obter automaticamente conectado no seu aplicativo do Portal de financiamento.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_205.png
