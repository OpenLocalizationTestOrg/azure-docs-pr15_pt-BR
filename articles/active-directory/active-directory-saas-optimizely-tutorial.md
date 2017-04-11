<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Optimizely | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e Optimizely."
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
    ms.date="09/11/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Tutorial: Integração com o Active Directory do Azure com Optimizely

Neste tutorial, você aprenderá a integrar Optimizely com o Azure Active Directory (AD Azure).

Integração Optimizely com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Optimizely
- Você pode habilitar os usuários para automaticamente obter conectado no Optimizely (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Optimizely, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único **Optimizely** na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testar Azure AD logon único em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Optimizely da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-optimizely-from-the-gallery"></a>Adicionando Optimizely da Galeria
Para configurar a integração do Optimizely no Azure AD, você precisa adicionar Optimizely da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar Optimizely da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Optimizely**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_01.png)

7. No painel de resultados, selecione **Optimizely**e clique em **concluído** para adicionar o aplicativo.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
Nesta seção, configurar e testar logon único Azure AD com Optimizely com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no Optimizely a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em Optimizely precisa ser estabelecida.
Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no Optimizely.

Para configurar e testar o Azure AD logon único com Optimizely, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um Optimizely testar usuário](#creating-an-optimizely-test-user)** - ter um representante de Britta Simon em Optimizely que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando Azure AD Single Sign-On

O objetivo desta seção é habilitar Azure AD logon único no portal de clássico do Azure e configurar o logon único em seu aplicativo de Optimizely.

Aplicativo de Optimizely espera as declarações SAML para conter um atributo chamado "email". O valor de "email" deve ser um email de Optimizely reconhecida que pode obter autenticado pelo Azure AD. Configure a declaração de "email" para este aplicativo. Você pode gerenciar os valores desses atributos da guia **"Os atributos do"** do aplicativo. A captura de tela a seguir mostra um exemplo para isso. 


![Configurar o logon único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_03.png) 


**Para configurar o logon único Azure AD com Optimizely, execute as seguintes etapas:**

1. No portal do clássico Azure, na página de integração do aplicativo **Optimizely** , no menu na parte superior, clique em **atributos**.
     
    ![Configurar o logon único][5]

2. Na caixa de diálogo de atributos token SAML, adicione o atributo "email".

    a. Clique em **Adicionar atributo de usuário** para abrir a caixa de diálogo **Adicionar atributo de usuário** . 
    
    ![Configurar o logon único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_05.png)

    b. Na caixa de texto **Nome do atributo** , digite o nome do atributo "email".

    c. Na lista de **Valor do atributo** , selecione o valor do atributo "userprincipalname" ou qualquer valor que contém um email reconhecido pelo Azure AD e Optimizely.

    d. Clique em **Concluir**.
3. No menu na parte superior, clique em **Início rápido**.

    ![Configurar o logon único][6]
4. No portal do clássico, na página de integração do aplicativo **Optimizely** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][7] 

5. Na página **como você gostaria que os usuários para assinar Optimizely** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.
    
    ![Configurar o logon único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_06.png)

6. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas: 

    ![Configurar o logon único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)


    a. Na caixa de texto de **Entrada na URL** , digite:`https://app.optimizely.net/contoso`

    b. Na caixa de texto **identificador** , digite:`urn:auth0:optimizely:contoso`

    c. Clique em **Avançar**. 


    > [AZURE.NOTE] Os valores para a **Entrada na URL** e **identificador** são somente espaços reservados para os valores reais. Você pode encontrar instruções para aquiring os valores reais de Optimizely posteriormente neste tutorial.

7. Na página **Configurar logon único em Optimizely** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_08.png)

    a. Clique em **baixar o certificado**e, em seguida, salve o arquivo em seu computador.

    b. Copie a **URL do serviço Single Sign-On**.

8. Para obter o SSO configurado para o seu aplicativo, entre em contato com seu gerente de conta Optimizely e forneça as seguintes informações:

    - Seu certificado baixado 
    - A URL do serviço Single Sign-On
 
    Em resposta ao seu email, Optimizely oferece a entrada na URL (SP iniciada SSO) e os valores de identificador (ID de entidade de provedor de serviço).

9. Volte à página de diálogo **Definir configurações de aplicativo** e, em seguida, execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)

    a. Na caixa de texto de **Entrada na URL** , digite a **URL de SSO iniciado pelo SP** fornecidos pelo Optimizely.

    b. Na caixa de texto **identificador** , digite a **ID de entidade de provedor de serviço** fornecida pelo Optimizely.

    c. Clique em **Avançar**.

10. Na página **Configurar logon único em Optimizely** , execute as seguintes etapas:
    
    ![Azure AD Single Sign-On][10]

    a. Selecione a confirmação de configuração de logon único.

    b. Clique em **Avançar**.

11. Na página **confirmação de logon única** , clique em **Concluir**.  
    
    ![Azure AD Single Sign-On][11]

12. Em uma janela de navegador diferente, logon para seu aplicativo Optimizely.
13. Clique em nome no canto superior direito e, em seguida, **Configurações de conta**da conta.

    ![Azure AD Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

14. Na guia conta, marque a caixa **Habilitar SSO** em logon único na seção **Visão geral** .

    ![Azure AD Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
Nesta seção, você criar um usuário de teste no portal do clássico chamado Britta Simon.
Na lista de usuários, selecione **Britta Simon**.

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:
 
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-an-optimizely-test-user"></a>Criação de um usuário de teste Optimizely

Nesta seção, você criar um usuário chamado Britta Simon no Optimizely.

1. Na página inicial, selecione a guia de **colaboradores**
2. Clique em **Novo Colaborador** para adicionar um novo Colaborador ao projeto.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3.  Preencha o endereço de email e atribuí-las uma função. Clique em **Convidar**.


    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. Eles receberão um convite por e-mail. Usando o endereço de email. eles terão a fazer logon no Optimizely.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

Nesta seção, você habilitar Britta Simon usar logon único Azure concedendo o acesso aos Optimizely.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a Optimizely, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Optimizely**.

    ![Configurar o logon único](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_50.png) 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de todos os usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]


### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.

Quando você clica no bloco de Optimizely no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de Optimizely.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-optimizely-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_205.png
