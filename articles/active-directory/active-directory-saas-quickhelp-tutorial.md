<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com QuickHelp | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e QuickHelp."
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
    ms.date="08/16/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>Tutorial: Integração com o Active Directory do Azure com QuickHelp

O objetivo deste tutorial é mostram como integrar QuickHelp com o Azure Active Directory (AD Azure).  
Integração do QuickHelp com o Azure AD fornece os seguintes benefícios: 

- Você pode controlar no Azure AD quem tem acesso ao QuickHelp 
- Você pode habilitar os usuários para automaticamente obter conectado no QuickHelp (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos 

Para configurar a integração do Azure AD com QuickHelp, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único QuickHelp na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando QuickHelp da Galeria 
2. Configurando e testando Azure AD logon único


## <a name="adding-quickhelp-from-the-gallery"></a>Adicionando QuickHelp da Galeria
Para configurar a integração do QuickHelp no Azure AD, você precisa adicionar QuickHelp da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar QuickHelp da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **QuickHelp**.

    ![Aplicativos][5]

7. No painel de resultados, selecione **QuickHelp**e clique em **concluído** para adicionar o aplicativo.

    ![Aplicativos][500]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar logon único Azure AD com QuickHelp com base em um usuário de teste chamado "Simon Britta".


Para configurar e testar o Azure AD logon único com QuickHelp, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um QuickHelp testar usuário](#creating-a-quickhelp-test-user)** - ter um representante de Britta Simon em QuickHelp que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando Azure AD Single Sign-On

O objetivo desta seção é habilitar Azure AD logon único no portal de clássico do Azure e configurar o logon único em seu aplicativo de QuickHelp.


**Para configurar o logon único Azure AD com QuickHelp, execute as seguintes etapas:**

1. No portal do clássico Azure, na página de integração do aplicativo **QuickHelp** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para assinar QuickHelp** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Azure AD Single Sign-On][7] 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Definir configurações de aplicativo][8] 
 
     a. Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para logon ao seu site de QuickHelp (por exemplo:* https://quickhelp.com/bsiazure/*).

     > [AZURE.NOTE] Entre em contato com sua equipe de suporte do QuickHelp se você não souber o valor da entrada na URL.

     b. Clique em **Avançar**.

 
4. Na página **Configurar logon único em QuickHelp** , execute a seguinte etapas: clique em **baixar metadados**e salve o arquivo de metadados localmente em seu computador.

    ![O que é Azure AD Connect][9] 



1. Logon para o site de sua empresa QuickHelp como administrador.

2. No menu na parte superior, clique em **administrador**.

    ![Configurar o logon único][21]


1. No menu **Administração de QuickHelp** , clique em **configurações**.

    ![Configurar o logon único][22]

1. Clique em **configurações de autenticação**.

1. Na página **Configurações de autenticação** , execute as seguintes etapas

    ![Configurar o logon único][23]

    a. Como o **Tipo de SSO**, selecione **WSFederation**.

    b. Carregue seu arquivo de metadados Azure baixado, clique em **Procurar**, navegue até o arquivo, o fim, clique em **Carregar metadados**.

    c. Na caixa de texto **Email** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    d. Na caixa de texto **nome** , **digite http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. Na caixa de texto **Sobrenome** , **digite http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**

    f. Na **Barra de ações**, clique em **Salvar**.







6. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**. 

    ![O que é Azure AD Connect][10]

7. Na página **confirmação de logon única** , clique em **Concluir**.  

    ![O que é Azure AD Connect][11]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico Azure chamado Britta Simon.  
Na lista de usuários, selecione **Britta Simon**.

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_02.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_03.png) 
 
4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**. 

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas: 
 
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas: 

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_06.png) 
 
    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.
    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_07.png) 
 
8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_08.png) 
  
    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   

  
 
### <a name="creating-a-quickhelp-test-user"></a>Criar um usuário de teste QuickHelp

O objetivo desta seção é criar um usuário chamado Britta Simon no QuickHelp.
Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no QuickHelp para um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em QuickHelp precisa ser estabelecida.

QuickHelp suporta provisionamento just-in-time. Isso significa que, se necessário, uma conta de usuário é criada automaticamente no QuickHelp e a conta estiver vinculada à conta do Azure AD.

Não há nenhum item de ação para você nesta seção.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso aos QuickHelp.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a QuickHelp, execute as seguintes etapas:**

1. No portal do Azure clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **QuickHelp**.

    ![Atribuir usuário][202] 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]



### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.  
Quando você clica no bloco de QuickHelp no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de QuickHelp.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_01.png
[500]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_14.png


[6]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_02.png
[8]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_03.png
[9]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_04.png
[10]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_07.png
[24]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_08.png
[25]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_09.png
[26]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_10.png
[27]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_11.png
[28]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_12.png

[200]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_13.png
[203]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_400.png
[401]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_401.png
[402]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_402.png




