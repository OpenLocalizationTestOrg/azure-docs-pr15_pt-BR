<properties
    pageTitle="Tutorial: Active Directory do Azure integra-quadro-negro Saiba - Shibboleth | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e aprenda-quadro-negro - Shibboleth."
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
    ms.date="09/10/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>Tutorial: Integração com o Active Directory do Azure com-quadro-negro Saiba - Shibboleth

Neste tutorial, você aprenderá a integrar-quadro-negro Saiba - Shibboleth com o Azure Active Directory (AD Azure).

Integração-quadro-negro Saiba - Shibboleth com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao-quadro-negro Saiba - Shibboleth
- Você pode habilitar os usuários para automaticamente obter assinados-on-quadro-negro saber - Shibboleth (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com-quadro-negro Saiba - Shibboleth, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Aprenda uma-quadro-negro - logon único Shibboleth na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testar Azure AD logon único em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando-quadro-negro Saiba - Shibboleth da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-blackboard-learn---shibboleth-from-the-gallery"></a>Adicionando-quadro-negro Saiba - Shibboleth da Galeria
Para configurar a integração do-quadro-negro Saiba - Shibboleth no Azure AD, você precisa adicionar-quadro-negro Saiba - Shibboleth da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar-quadro-negro Saiba - Shibboleth da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory][1]
2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **-Quadro-negro Saiba - Shibboleth**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_01.png)

7. No painel de resultados, selecione **-Quadro-negro Saiba - Shibboleth**e, em seguida, clique em **concluído** para adicionar o aplicativo.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
Nesta seção, configurar e testar Azure AD logon único com-quadro-negro Saiba - Shibboleth com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber que o usuário correspondente no-quadro-negro Saiba - Shibboleth é um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no-quadro-negro Saiba - Shibboleth deve ser feita.

Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **Username** no-quadro-negro Saiba - Shibboleth.

Para configurar e testar Azure AD logon único com-quadro-negro Saiba - Shibboleth, é necessário concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
3. **[Criando um saiba de-quadro-negro - usuário de teste de Shibboleth](#creating-a-blackboard-learn-shibboleth-test-user)** - ter um representante de Britta Simon em-Quadro-negro Saiba - Shibboleth que esteja vinculado à representação Azure AD dela.
4. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD

Nesta seção, você habilitar Azure AD logon único no portal do clássico e configurar logon único em seu aprender de-quadro-negro - Shibboleth aplicativo.


**Para configurar o logon único Azure AD com-quadro-negro Saiba - Shibboleth, execute as seguintes etapas:**

1. No portal do clássico, na página de integração do aplicativo **-Quadro-negro Saiba - Shibboleth** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para assinar-quadro-negro Saiba - Shibboleth** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_03.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_04.png) 

    a. Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para logon para seu aprender do-quadro-negro - aplicativo de Shibboleth usando o seguinte padrão: **https://\<yourblackoardlearnserver\>.blackboardlearn.com/Shibboleth.sso/Login**
    
    b. Na caixa de texto **identificador** , digite a URL usando o seguinte padrão: **https://\<yourblackoardlearnserver\>.blackboardlearn.com/shibboleth-sp**

    c. Na caixa de texto **URL de resposta** , digite a URL usando o seguinte padrão: **https://\<yourblackoardlearnserver\>.blackboardlearn.com/Shibboleth.sso/SAML2/POST**

    > [AZURE.NOTE] Você será capaz de localizar todos esses valores na doucument de metadados de Federação fornecida pelo seu parceiro-quadro-negro saiba.

    d. Clique em **Avançar**
 
4. Na página **Configurar logon único em-Quadro-negro Saiba - Shibboleth** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_05.png)

    a. Clique em **baixar metadados**e salve o arquivo em seu computador.

    b. Clique em **Avançar**.


5. Para obter o SSO configurado para o seu aplicativo, entre em contato com seu saiba de-quadro-negro - Shibboleth parceiro e forneça a ele com o seguinte:

    • Baixado **metadados**

    • A **URL do emissor**

    • A **URL de SAML SSO**

    • A **URL do serviço de saída único**

6. No portal do clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.
    
    ![Azure AD Single Sign-On][10]

7. Na página **confirmação de logon única** , clique em **Concluir**.  
 
    ![Azure AD Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
Nesta seção, você criar um usuário de teste no portal do clássico chamado Britta Simon.


![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:  ![criação de um usuário de teste do Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas: ![criação de um usuário de teste do Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   


### <a name="creating-an-blackboard-learn---shibboleth-test-user"></a>Criando um saiba de-quadro-negro - usuário de teste Shibboleth

Nesta seção, você criar um usuário chamado Britta Simon no-quadro-negro Saiba - Shibboleth. Trabalhe com seu parceiro-quadro-negro saber para adicionar os usuários a aprender do-quadro-negro - plataforma de Shibboleth.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

Nesta seção, você habilitar Britta Simon usar logon único Azure concedendo o acesso-quadro-negro saber - Shibboleth.

![Atribuir usuário][200] 

**Para atribuir Britta Simon-quadro-negro saber - Shibboleth, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **-Quadro-negro Saiba - Shibboleth**.

    ![Configurar o logon único](./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearnshibboleth_50.png) 

3. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]


### <a name="testing-single-sign-on"></a>Teste de logon único

Nesta seção, você testar seu Azure AD único logon configuração usando o painel de acesso.

Quando você clica em-Quadro-negro Saiba - bloco Shibboleth no painel de acesso, que deve receber automaticamente conectado no seu aprender de-quadro-negro - Shibboleth aplicativo.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-blackboard-learn-shibboleth-tutorial/tutorial_general_205.png