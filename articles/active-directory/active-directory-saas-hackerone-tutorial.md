<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com HackerOne | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e HackerOne."
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


# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Tutorial: Integração com o Active Directory do Azure com HackerOne

Neste tutorial, você pode integrar HackerOne com o Azure Active Directory (AD Azure).

Integração HackerOne com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao HackerOne
- Você pode habilitar os usuários para automaticamente obter assinado no HackerOne (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com HackerOne, você precisa dos seguintes itens:

- Uma assinatura do Azure
- Um logon único HackerOne na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, configurar e testar logon único Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando HackerOne da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-hackerone-from-the-gallery"></a>Adicionando HackerOne da Galeria
Para integrar HackerOne em Azure AD, você precisa adicionar HackerOne da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar HackerOne da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

![Aplicativos][4]

6. Na caixa de pesquisa, digite **HackerOne**.

![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_01.png)

7. No painel de resultados, selecione **HackerOne**e clique em **concluído** para adicionar o aplicativo.


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
Em seguida, configurar e testar logon único Azure AD com HackerOne com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no HackerOne para um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em HackerOne precisa ser estabelecida.  
Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no HackerOne.

Para configurar e testar o Azure AD logon único com HackerOne, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
3. **[Criando um HackerOne testar usuário](#creating-a-hackerone-test-user)** - ter um representante de Britta Simon em certificar que esteja vinculado à representação Azure AD dela.
4. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando Azure AD Single Sign-On

Em seguida, você habilitar Azure AD logon único no portal de clássico e configurar o logon único em seu aplicativo de HackerOne.

Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

**Para configurar o logon único Azure AD com HackerOne, execute as seguintes etapas:**

1. No portal do clássico Azure, na página de integração do aplicativo **HackerOne** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para assinar HackerOne** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_03.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas e clique em **Avançar**:

    ![Configurar o logon único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_04.png) 


    a. Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para logon para seu aplicativo de HackerOne usando o seguinte padrão: **"https://hackerone.com/\<nome da empresa\>/authentication"**. 

    b. Entre em contato com a equipe de suporte HackerOne via [support@hackerone.com](mailto:support@hackerone.com) para obter a URL de locatário se você não soubê-lo.

    c. Na caixa de texto **identificador** , digite a URL do locatário. 

    d. Clique em **Avançar**.


4. Na página **Configurar logon único em HackerOne** , execute as seguintes etapas e clique em **Avançar**:

    ![Configurar o logon único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_05.png) 

    a. Clique em **baixar o certificado**e, em seguida, salve o arquivo em seu computador.

    b. Clique em **Avançar**.


1. Logon em seu locatário HackerOne como um administrador.

1. No menu na parte superior, clique em **configurações**.

    ![Configurar o logon único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png) 

1. Navegue até "**autenticação**" e clique em "**Adicionar SAML configurações**".

    ![Configurar o logon único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png) 


1. Na caixa de diálogo **Configurações de SAML** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png) 

    a. Na caixa de texto **Domínio de Email** , digite um domínio registrado.

    b. No portal do Azure clássico, copie **O URL de serviço de logon único**e, em seguida, cole a caixa de texto de entrada única na URL.

    c. Crie um arquivo **codificado na base 64** do seu certificado baixado.  

       >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
    
    d. Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo para o **X509 certificado** caixa de texto.

    e. Clique em **Salvar**


1. Na caixa de diálogo Configurações de autenticação, execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png) 

    a. Clique em **Executar teste**.

    b. Se o valor do **Status** de campo igual a **última status de teste: criada**, entre em contato com sua equipe de suporte HackerOne via [support@hackerone.com](mailto:support@hackerone.com) para solicitar uma revisão de sua configuração.


6. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.

    ![Azure AD Single Sign-On][10]

7. Na página **confirmação de logon única** , clique em **Concluir**.  
 
    ![Azure AD Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD

Em seguida, você criar um usuário de teste no portal do clássico chamado Britta Simon.  

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste seguro ENTREGAR no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   


### <a name="creating-a-hackerone-test-user"></a>Criar um usuário de teste HackerOne

Em seguida, você criar um usuário chamado Britta Simon no HackerOne. HackerOne suporta em vez de provisionamento, que é ativada por padrão.

Não há nenhum item de ação para você nesta seção. Quando você acessa HackerOne, um novo usuário for criado, se ele ainda não existe. [Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Se você precisar criar um usuário manualmente, você precise contatar a equipe de suporte de certificar.




### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

Em seguida, habilitar Britta Simon usar logon único Azure concedendo o acesso aos HackerOne.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a HackerOne, execute as seguintes etapas:**

1. No portal do Azure clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **HackerOne**.

    ![Configurar o logon único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_50.png) 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]



### <a name="testing-single-sign-on"></a>Teste de logon único

Por fim, você teste sua Azure AD único logon configuração usando o painel de acesso.  
Quando você clica no bloco de HackerOne no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de HackerOne.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_205.png