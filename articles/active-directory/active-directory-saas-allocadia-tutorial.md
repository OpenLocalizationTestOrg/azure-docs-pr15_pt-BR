<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Allocadia | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e Allocadia."
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
    ms.date="09/19/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-allocadia"></a>Tutorial: Integração com o Active Directory do Azure com Allocadia

Neste tutorial, você aprenderá a integrar Allocadia com o Azure Active Directory (AD Azure).

Integração Allocadia com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Allocadia
- Você pode habilitar os usuários para automaticamente obter assinado no Allocadia (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Allocadia, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único Allocadia na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testar Azure AD logon único em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Allocadia da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-allocadia-from-the-gallery"></a>Adicionando Allocadia da Galeria
Para configurar a integração do Allocadia no Azure AD, você precisa adicionar Allocadia da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar Allocadia da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Allocadia**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_01.png)

7. No painel de resultados, selecione **Allocadia**e clique em **concluído** para adicionar o aplicativo.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_06.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
Nesta seção, configurar e testar logon único Azure AD com Allocadia com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no Allocadia a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em Allocadia precisa ser estabelecida.
Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no Allocadia.

Para configurar e testar o Azure AD logon único com Allocadia, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um Allocadia testar usuário](#creating-an-allocadia-test-user)** - ter um representante de Britta Simon em Allocadia que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando Azure AD Single Sign-On

Nesta seção, você habilitar Azure AD logon único no portal do clássico e configurar o logon único em seu aplicativo de Allocadia.


Aplicativo de Allocadia espera as declarações SAML em um formato específico. Configure as seguintes declarações para este aplicativo. Você pode gerenciar os valores desses atributos da guia **"Atributo"** do aplicativo. A captura de tela a seguir mostra um exemplo para isso. 

![Configurar o logon único](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_07.png) 

**Para configurar o logon único Azure AD com Hightail, execute as seguintes etapas:**


1. No portal do clássico Azure, na página de integração do aplicativo **Allocadia** , no menu na parte superior, clique em **atributos**.

    ![Configurar o logon único](./media/active-directory-saas-allocadia-tutorial/tutorial_general_80.png) 


2. Na caixa de diálogo **atributos de token SAML** , para cada linha mostrada na tabela a seguir, execute as seguintes etapas:

  	| Nome do atributo | Valor do atributo |
  	| --- | --- |    
  	| nome | User.givenName |
  	| Sobrenome  | User.surname |
  	| Email | User.mail |
    

    a. Clique em **Adicionar atributo de usuário** para abrir a caixa de diálogo **Adicionar Attribure de usuário** .

    ![Configurar o logon único](./media/active-directory-saas-allocadia-tutorial/tutorial_general_81.png) 


    b. Na caixa de texto **Nome Attrubute** , digite o nome do atributo mostrado para aquela linha.

    c. Na lista de **Valor do atributo** , selsect o valor do atributo mostrado para aquela linha.

    d. Clique em **Concluir**.  
    

3. No menu na parte superior, clique em **Início rápido**.

    ![Configurar o logon único](./media/active-directory-saas-allocadia-tutorial/tutorial_general_83.png)  


4. Na página **como você gostaria que os usuários para assinar Allocadia** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.
    
    ![Configurar o logon único](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_03.png) 

5. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:.

    ![Configurar o logon único](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_04.png) 

    a. Na caixa identificador, digite a URL no seguinte padrão: para uso do ambiente de teste a URL como **"https://na2standby.allocadia.com"** e do ambiente de produção usar **"https://na2.allocadia.com"**

    b. A URL de resposta, digite a URL no seguinte padrão: para uso do ambiente de teste o padrão de URL, como **"https://na2standby.allocadia.com/allocadia/saml/SSO"** e do ambiente de produção usar **"https://na2.allocadia.com/allocadia/saml/SSO"**


6. Na página **Configurar logon único em Allocadia** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_05.png) 

    a. Clique em **baixar metadados**e salve o arquivo em seu computador.

    b. Clique em **Avançar**.


7.  Para obter o SSO configurado para o seu aplicativo, a equipe de [Suporte de Allocadia](mailTo:support@allocadia.com) contato e eles auxiliarão configurar SSO. Por favor anotação que você precisa enviar email e anexar download de arquivo de metadados para configurar o SSO do lado Allocadia.
 
    > [AZURE.NOTE] Certifique-se de que a equipe Allocadia definir o valor de identificador no ambiente de teste, como **"https://na2standby.allocadia.com"** e do ambiente de produção, deve ser: **"https://na2.allocadia.com"**


8. No portal do clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.
    
    ![Azure AD Single Sign-On][10]

9. Na página **confirmação de logon única** , clique em **Concluir**.  
    
    ![Azure AD Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD

Nesta seção, você criar um usuário de teste no portal do clássico chamado Britta Simon.
Na lista de usuários, selecione **Britta Simon**.

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:
 
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-an-allocadia-test-user"></a>Criação de um usuário de teste Allocadia

Nesta seção, você criar um usuário chamado Britta Simon no Allocadia. Suporte a aplicativo Allocadia apenas em vez de provisionamento de usuário. Se você tiver configurado as declarações conforme indicado acima na seção **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** -será provisionar usuários no aplicativo. 


> [AZURE.NOTE] Se você precisar criar um usuário manualmente ou lote de usuários, você precise contatar a equipe de suporte de Allocadia.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

Nesta seção, você habilitar Britta Simon usar logon único Azure concedendo o acesso aos Allocadia.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a Allocadia, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Allocadia**.

    ![Configurar o logon único](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_50.png) 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]


### <a name="testing-single-sign-on"></a>Teste de logon único

Nesta seção, você testar seu Azure AD único logon configuração usando o painel de acesso.
Quando você clica no bloco de Allocadia no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de Allocadia.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_205.png
