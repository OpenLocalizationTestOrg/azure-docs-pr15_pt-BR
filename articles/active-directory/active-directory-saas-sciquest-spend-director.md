<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com SciQuest passam Director | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e SciQuest passam Director."
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>Tutorial: Integração com o Active Directory do Azure com SciQuest passam Director

O objetivo deste tutorial é mostram como integrar SciQuest passam Director com o Azure Active Directory (AD Azure).  
Integração SciQuest passam Director com o Azure AD fornece os seguintes benefícios: 

- Você pode controlar no Azure AD quem tem acesso ao SciQuest passam Diretor 
- Você pode habilitar os usuários para automaticamente obter assinado no SciQuest passam Director (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos 

Para configurar a integração do Azure AD com SciQuest passam Director, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único SciQuest passam diretor na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando SciQuest passam Diretor da Galeria 
2. Configurando e testando Azure AD logon único


## <a name="adding-sciquest-spend-director-from-the-gallery"></a>Adicionando SciQuest passam Diretor da Galeria
Para configurar a integração do SciQuest passam Director no Azure AD, você precisa adicionar SciQuest passam Diretor da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar SciQuest passam Diretor da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **sciQuest passam director**.

    ![Aplicativos][5]

7. No painel de resultados, selecione **SciQuest passam Director**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Aplicativos][6]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar logon único Azure AD com SciQuest passam Director com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no SciQuest passam Director para um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SciQuest passam Director precisa ser estabelecida.  
Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no SciQuest passam Director.
 
Para configurar e testar o Azure AD logon único com SciQuest passam Director, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD único Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um SciQuest passam Director testar usuário](#creating-a-halogen-software-test-user)** - ter um representante de Britta Simon no Director passam SciQuest que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configurando Azure AD Single Sign-On único

O objetivo desta seção é habilitar Azure AD logon único no portal de clássico do Azure e configurar o logon único em seu aplicativo SciQuest passam diretor.

**Para configurar o logon único Azure AD com SciQuest passam diretor, execute as seguintes etapas:**

1. No portal do clássico Azure, na página de integração do aplicativo **SciQuest passam Director** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][8]

2. Na página **como você gostaria que usuários para entrar SciQuest passam Director** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Azure AD Single Sign-On][9]

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas: 

    ![Definir configurações de aplicativo][10]
 
     3.1. Na caixa de texto de **Entrada na URL** , digite sua URL usada pelos usuários para entrar seu aplicativo de SciQuest passam diretor usando o seguinte padrão: *https://.* SciQuest.com/.**

     3,2. Na caixa de texto **URL de resposta** , digite o mesmo valor que você digitou na caixa de texto de **Entrada na URL** . 

     3.3. Clique em **Avançar**.
 
4. Na página **Configurar logon único em SciQuest passam Director** , clique em **baixar metadados**e salve o arquivo de metadados localmente em seu computador.

    ![O que é Azure AD Connect][11]

5. Contato SciQuest suporte para habilitar este método de autenticação usando os metadados baixado acima.

6. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** . 

    ![O que é Azure AD Connect][15]

10. Na página **confirmação de logon única** , clique em **Concluir**.  

    




### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico Azure chamado Britta Simon.

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![O que é Azure AD Connect][100] 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![O que é Azure AD Connect][101] 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**. 

    ![O que é Azure AD Connect][102] 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:

    ![O que é Azure AD Connect][103] 

    a. Como **Tipo de usuário**, selecione **novo usuário na sua organização**.
  
    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.
  
    c. Clique em Avançar.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas: 

    ![O que é Azure AD Connect][104] 

    a. Na caixa de texto **nome** , digite **Britta**.  
  
    b. No **Sobrenome** txtbox, tipo, **Simon**.
  
    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.
  
    d. Na lista de **função** , selecione o **usuário**.
  
    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![O que é Azure AD Connect][105]  

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![O que é Azure AD Connect][106]   

    a. Anote o valor da **Nova senha**.
  
    b. Clique em **Concluir**.   
  
 
### <a name="creating-a-sciquest-spend-director-test-user"></a>Criar um usuário de teste SciQuest passam Diretor

O objetivo desta seção é criar um usuário chamado Britta Simon no SciQuest passam Director.

É necessário contatar a equipe de suporte SciQuest passam diretor e forneça a ele com os detalhes sobre a sua conta de teste para colocá-lo criado.

Como alternativa, você também pode aproveitar just-in-time provisionamento, um recurso de logon único que é suportado pelo SciQuest passam Director.  
Se provisionamento just-in-time estiver habilitada, os usuários são criados automaticamente pelo Diretor da passam SciQuest durante uma tentativa de logon única se eles não existem. Este recurso elimina a necessidade de criar manualmente os usuários do representante de logon único.

Para obter just-in-time provisionamento habilitados, você precise contatar estiver sua equipe de suporte SciQuest passam Director.
  

### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso aos SciQuest passam Director.

![O que é Azure AD Connect][200]

**Para atribuir Britta Simon a SciQuest passam diretor, execute as seguintes etapas:**

1. No portal do Azure clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![O que é Azure AD Connect][201]

2. Na lista de aplicativos, selecione **SciQuest passam Director**.

    ![O que é Azure AD Connect][202]

1. No menu na parte superior, clique em **usuários**.

    ![O que é Azure AD Connect][203]

1. Na lista de usuários, selecione **Britta Simon**.

    ![O que é Azure AD Connect][204]

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![O que é Azure AD Connect][205]



### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.  
Quando você clica no bloco de SciQuest passam Director no painel de acesso, que deve receber automaticamente conectado no seu aplicativo SciQuest passam diretor.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_01.png
[6]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_05.png
[8]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_09.png 
[101]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_10.png 
[102]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_11.png 
[103]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_12.png 
[104]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_13.png 
[105]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_14.png 
[106]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_15.png 
[200]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_16.png 
[201]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_17.png 
[202]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_06.png
[203]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_18.png
[204]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_19.png
[205]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_20.png

