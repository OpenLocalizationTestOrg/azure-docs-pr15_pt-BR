<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Marketo | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e Marketo."
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
    ms.date="09/07/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Tutorial: Integração com o Active Directory do Azure com Marketo

Neste tutorial, você aprenderá a integrar Marketo com o Azure Active Directory (AD Azure).

Integração Marketo com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Marketo
- Você pode habilitar os usuários para automaticamente obter conectado no Marketo (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Marketo, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único Marketo na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testar Azure AD logon único em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Marketo da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-marketo-from-the-gallery"></a>Adicionando Marketo da Galeria
Para configurar a integração do Marketo no Azure AD, você precisa adicionar Marketo da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar Marketo da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Marketo**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_01.png)

7. No painel de resultados, selecione **Marketo**e clique em **concluído** para adicionar o aplicativo.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
Nesta seção, configurar e testar logon único Azure AD com Marketo com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no Marketo a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em Marketo precisa ser estabelecida.

Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no Marketo.

Para configurar e testar o Azure AD logon único com Marketo, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
3. **[Criando um Marketo testar usuário](#creating-a-predictix-price-reporting-test-user)** - ter um representante de Britta Simon em Marketo que esteja vinculado à representação Azure AD dela.
4. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD

Nesta seção, você habilitar Azure AD logon único no portal do clássico e configurar o logon único em seu aplicativo de Marketo.


**Para configurar o logon único Azure AD com Marketo, execute as seguintes etapas:**

1. No portal do clássico, na página de integração do aplicativo **Marketo** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para assinar Marketo** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_03.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_04.png) 

    a. Na caixa de texto **identificador** , digite a URL usando o seguinte padrão:`https://saml.marketo.com/sp`
    
    b. Na caixa de texto **URL de resposta** , digite a URL usando o seguinte padrão:`https://login.marketo.com/saml/assertion/\<munchkinid\>`

    c. Clique em **Avançar**
 
4. Na página **Configurar logon único em Marketo** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_05.png)

    a. Clique em **baixar o certificado**e, em seguida, salve o arquivo em seu computador.

    b. Clique em **Avançar**.


5. Para obter identificação de Munchkin do seu aplicativo, faça logon em Marketo usando credenciais de administrador e execute seguintes ações:

    a. Fazer login no aplicativo de Marketo usando credenciais de administrador.

    b. Clique no botão administrador no painel de navegação superior.

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 

    c. Navegue até o menu de integração e clique no link Munchkin
    
    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)

    d. Copie a identificação de Munchkin mostrado na tela e concluir sua URL de resposta do Assistente de configuração do Azure AD.

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png)

6.  Para configurar o SSO no aplicativo, siga as etapas abaixo:

    a. Fazer login no aplicativo de Marketo usando credenciais de administrador.

    b. Clique no botão administrador no painel de navegação superior.

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 

    c. Navegue até o menu de integração e clique em logon único

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png) 

    d. Para habilitar as configurações de SAML clicar no botão Editar
    
    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png) 

    e. **Habilitar** Configurações de logon único

    f. Insira a ID do emissor, que você copiou do Assistente de configuração do Azure AD.

    g. Na caixa de texto ID da entidade insira a URL como **http://saml.marketo.com/sp**

    h. Selecione o local de ID de usuário como **elemento identificador de nome**

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)

    > [AZURE.NOTE] Se o identificador de usuário não for valor UPN depois de alterar o valor na guia atributo.
     
    Eu. Carregar o certificado que você baixou do Assistente de configuração do Azure AD. Salve as configurações.

    j. Editar as configurações de redirecionar páginas

    k. Copie a URL de Login do Assistente de configuração do Azure AD na caixa de texto **URL de Login** .

    l. Copie a URL de Logout do Assistente de configuração do Azure AD na caixa de texto **URL de Logout** .

    m. Na URL erro copie a URL da instância de Marketo e clique no botão Salvar para salvar as configurações.

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)

7. Para habilitar o SSO para usuários, complete as seguintes ações:

    a. Fazer login no aplicativo de Marketo usando credenciais de administrador.

    b. Clique no botão **administrador** no painel de navegação superior.

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 

    c. Navegue até o menu **segurança** e clique em **Configurações de logon** 

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)

    d. Marque a opção **Exigir SSO** e salvar as configurações.
    
    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)

8. No portal do clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.
    
    ![Azure AD Single Sign-On][10]

8. Na página **confirmação de logon única** , clique em **Concluir**.  
 
    ![Azure AD Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
Nesta seção, você criar um usuário de teste no portal do clássico chamado Britta Simon.


![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:  ![criação de um usuário de teste do Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas: ![criação de um usuário de teste do Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-an-marketo-test-user"></a>Criação de um usuário de teste Marketo

Nesta seção, você criar um usuário chamado Britta Simon no Marketo. Siga estas etapas para criar um usuário na plataforma de Marketo.

1. Fazer login no aplicativo de Marketo usando credenciais de administrador.

2. Clique no botão **administrador** no painel de navegação superior.

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 

3. Navegue até o menu **segurança** e clique em **usuários e funções**

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)  

4. Clique no link **Convidar novo usuário** na guia usuários

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png) 

5. No Assistente de convidar novo usuário preencher as seguintes informações

    a. Insira o endereço de **Email** do usuário na caixa de texto

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)

    b. Insira o **nome** na caixa de texto
    
    c. Insira o **Sobrenome** na caixa de texto

    d. Clique em Avançar

6. Na guia **permissões** , selecione as funções de usuário e clique em Avançar

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)

7. Clique no botão Enviar para enviar o convite de usuário

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)

8. Usuário receberão a notificação de email e tem clique no link e alterar a senha para ativar a conta. 


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

Nesta seção, você habilitar Britta Simon usar logon único Azure concedendo o acesso aos Marketo.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a Marketo, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Marketo**.

    ![Configurar o logon único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_50.png) 

3. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]


### <a name="testing-single-sign-on"></a>Teste de logon único

Nesta seção, você testar seu Azure AD único logon configuração usando o painel de acesso.

Quando você clica no bloco de Marketo no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de Marketo.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_205.png
