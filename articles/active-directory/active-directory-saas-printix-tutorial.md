<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Printix | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e Printix."
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


# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Tutorial: Integração com o Active Directory do Azure com Printix

Neste tutorial, você aprenderá a integrar Printix com o Azure Active Directory (AD Azure).

Integração Printix com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Printix
- Você pode habilitar os usuários para automaticamente obter conectado no Printix (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Printix, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único Printix na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testar Azure AD logon único em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Printix da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-printix-from-the-gallery"></a>Adicionando Printix da Galeria
Para configurar a integração do Printix no Azure AD, você precisa adicionar Printix da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar Printix da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory][1]
2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Printix**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-printix-tutorial/tutorial_printix_01.png)
7. No painel de resultados, selecione **Printix**e clique em **concluído** para adicionar o aplicativo.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
Nesta seção, configurar e testar logon único Azure AD com Printix com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no Printix a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em Printix precisa ser estabelecida.

Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no Printix.

Para configurar e testar o Azure AD logon único com Printix, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
3. **[Criando um Printix testar usuário](#creating-a-printix-test-user)** - ter um representante de Britta Simon em Printix que esteja vinculado à representação Azure AD dela.
4. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD

Nesta seção, você habilitar Azure AD logon único no portal do clássico e configurar o logon único em seu aplicativo de Printix.


**Para configurar o logon único Azure AD com Printix, execute as seguintes etapas:**

1. No portal do clássico, na página de integração do aplicativo **Printix** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para assinar Printix** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-printix-tutorial/tutorial_printix_03.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-printix-tutorial/tutorial_printix_04.png) 

    a. Na caixa de texto **URL de resposta** , digite `https://auth.printix.net/saml/SSO`.
    
    b. Clique em **Avançar**
 
4. Na página **Configurar logon único em Printix** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-printix-tutorial/tutorial_printix_05.png)

    a. Clique em **baixar metadados**e salve o arquivo em seu computador.

    b. Clique em **Avançar**.


5. Logon em seu locatário Printix como um administrador.


6. No menu na parte superior, clique no ícone no canto superior direito e selecione "**autenticação**".

    ![Configurar o logon único](./media/active-directory-saas-printix-tutorial/tutorial_printix_06.png)

7. Na guia **configuração** , selecione **autenticação de habilitar o Azure/Office 365**

    ![Configurar o logon único](./media/active-directory-saas-printix-tutorial/tutorial_printix_07.png)

8. Na guia **Azure** , entrada URL de metadados de federação para a caixa de texto de "**documento de metadados de Federação**". 
    
    ![Configurar o logon único](./media/active-directory-saas-printix-tutorial/tutorial_printix_08.png)

    a. Anexado o arquivo xml de metadados que você baixou na etapa 4 à equipe de suporte de Printix via "**support@printix.net**". Em seguida, eles serão carregar o arquivo xml e fornecer uma URL de metadados de federação com você.


9. Clique no botão "**Testar**" e clique em botão de "**Okey**" se o teste foi bem-sucedido.

    a. Página do active directory do Azure mostrará após clicar no botão de **teste** . "O teste foi bem-sucedido" aqui significa depois de inserir as credenciais da sua conta de teste Azure ele será popo uma mensagem "Configurações testadas Okey". Clique no botão **Okey** .

    ![Configurar o logon único](./media/active-directory-saas-printix-tutorial/tutorial_printix_09.png)


10. Clique no botão **Salvar** na página "**autenticação**".


11. No portal do clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.
    
    ![Azure AD Single Sign-On][10]

12. Na página **confirmação de logon única** , clique em **Concluir**.  
 
    ![Azure AD Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
Nesta seção, você criar um usuário de teste no portal do clássico chamado Britta Simon.


![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-an-printix-test-user"></a>Criação de um usuário de teste Printix

O objetivo desta seção é criar um usuário chamado Britta Simon no Printix. Printix suporta em vez de provisionamento, que é por padrão ativado.

Não há nenhum item de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar Printix se ele ainda não existe. [Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Se você precisar criar um usuário manualmente, você precise contatar a equipe de suporte de Printix.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

Nesta seção, você habilitar Britta Simon usar logon único Azure concedendo o acesso aos Printix.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a Printix, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Printix**.

    ![Configurar o logon único](./media/active-directory-saas-printix-tutorial/tutorial_printix_50.png) 

3. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]


### <a name="testing-single-sign-on"></a>Teste de logon único

Nesta seção, você testar seu Azure AD único logon configuração usando o painel de acesso.

Quando você clica no bloco de Printix no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de Printix.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-printix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-printix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-printix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-printix-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-printix-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-printix-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-printix-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-printix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-printix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-printix-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-printix-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-printix-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-printix-tutorial/tutorial_general_205.png
