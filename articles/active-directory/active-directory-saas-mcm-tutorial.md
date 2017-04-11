<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com MCM | Microsoft Azure" 
    description="Saiba como usar MCM com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/30/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-mcm"></a>Tutorial: Integração com o Active Directory do Azure com MCM
  
O objetivo deste tutorial é mostrar a você como integrar MCM ao Azure Active Directory (AD Azure).

Integrar MCM Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao MCM
- Você pode habilitar os usuários para automaticamente obter assinado no MCM (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com MCM, você precisa dos seguintes itens:

- Uma assinatura válida do Azure
- Um logon único MCM na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando MCM da Galeria
2. Configurando e testando Azure AD logon único

## <a name="adding-mcm-from-the-gallery"></a>Adicionando MCM da Galeria
Para configurar a integração do MCM no Azure AD, você precisa adicionar MCM da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar MCM da galeria, execute as seguintes etapas:**

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-mcm-tutorial/tutorial_general_01.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-mcm-tutorial/tutorial_general_02.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-mcm-tutorial/tutorial_general_03.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-mcm-tutorial/tutorial_general_04.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **MCM**.

    ![Galeria de aplicativo] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_01.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **MCM**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![MCM] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_001.png "MCM")

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar logon único Azure AD com MCM com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no MCM para um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em MCM precisa ser estabelecida.

Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no MCM.

Para configurar e testar o Azure AD logon único com MCM, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
3. **[Criando um MCM testar usuário](#creating-a-mcm-test-user)** - ter um representante de Britta Simon em MCM que esteja vinculado à representação Azure AD dela.
4. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD
  
Nesta seção, você habilitar Azure AD logon único no portal do clássico e configurar o logon único em seu aplicativo MCM.

**Para configurar o logon único Azure AD com MCM, execute as seguintes etapas:**

1.  No portal do clássico Azure, na página de integração de aplicativo **MCM** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-mcm-tutorial/tutorial_general_05.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar MCM** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_03.png "Microsoft Azure AD Single Sign-On")

3.  Na página de diálogo Definir configurações de aplicativo, execute as seguintes etapas:

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_04.png "Configurar a URL do aplicativo")

    a. Na caixa de texto de **Entrada na URL** , digite: `https://myaba.co.uk/client-access/<company name>/saml.php`.
    
    b. Clique em **Avançar**

4.  Na página **Configurar logon único em MCM** , clique em **baixar metadados**e salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_05.png "Configurar o logon único")

5. Para obter o SSO configurado para o seu aplicativo, contate a equipe de suporte MCM. Anexe o arquivo baixado metadados e compartilhá-lo com a equipe MCM configurar SSO de seu lado.

6.  No portal do clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_06.png "Configurar o logon único")

7. Na página **confirmação de logon única** , clique em **Concluir**.

    ![Configurar o logon único] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_07.png "Configurar o logon único")


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no portal do clássico chamado Britta Simon.

![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_00.png)

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **Portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_01.png)

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_02.png)

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_03.png)

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_04.png)

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_05.png)

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_06.png)

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_07.png)

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   

###<a name="creating-a-mcm-test-user"></a>Criar um usuário de teste MCM
  
Nesta seção, você criar um usuário chamado Britta Simon no MCM. Trabalhe com a equipe de suporte MCM para adicionar os usuários na plataforma MCM.

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros MCM usuário conta ou APIs fornecidas pela MCM para provisionar AAD contas de usuário.


###<a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD
  
O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso aos MCM.
    
![Atribuir aos usuários] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_00.png "Atribuir aos usuários")

**Para atribuir Britta Simon a MCM, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.
    
    ![Atribuir aos usuários] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_01.png "Atribuir aos usuários")

2. Na lista de aplicativos, selecione **MCM**.
    
    ![Configurar o logon único](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_08.png)

1. No menu na parte superior, clique em **usuários**.
    
    ![Atribuir aos usuários] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_02.png "Atribuir aos usuários")

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.
    
    ![Atribuir aos usuários] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_03.png "Atribuir aos usuários")


### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.
 
Quando você clica no bloco MCM no painel de acesso, que deve receber automaticamente conectado no seu aplicativo MCM.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)