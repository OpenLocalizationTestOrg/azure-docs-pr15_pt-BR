<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com ImageRelay | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e ImageRelay."
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
    ms.date="08/15/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-imagerelay"></a>Tutorial: Integração com o Active Directory do Azure com ImageRelay

O objetivo deste tutorial é mostram como integrar ImageRelay com o Azure Active Directory (AD Azure).  
Integração ImageRelay com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao ImageRelay
- Você pode habilitar os usuários para automaticamente obter conectado no ImageRelay (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ImageRelay, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um ImageRelay logon único habilitado para assinatura


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando ImageRelay da Galeria

2. Configurando e testando Azure AD logon único


## <a name="adding-imagerelay-from-the-gallery"></a>Adicionando ImageRelay da Galeria
Para configurar a integração do ImageRelay no Azure AD, você precisa adicionar ImageRelay da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar ImageRelay da galeria, execute as seguintes etapas:**

1. No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **ImageRelay**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)

7. No painel de resultados, selecione **ImageRelay**e clique em **concluído** para adicionar o aplicativo.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar logon único Azure AD com ImageRelay com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa de uma conta de usuário que representa o usuário relacionado em ImageRelay.  Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em ImageRelay precisa ser estabelecida.  
Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no ImageRelay.

Para configurar e testar o Azure AD logon único com ImageRelay, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um ImageRelay testar usuário](#creating-a-userecho-test-user)** - ter um representante de Britta Simon em ImageRelay que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando Azure AD Single Sign-On

O objetivo desta seção é habilitar Azure AD logon único no portal de clássico do Azure e configurar o logon único em seu aplicativo de ImageRelay.


**Para configurar o logon único Azure AD com ImageRelay, execute as seguintes etapas:**

1. No portal do clássico Azure, na página de integração do aplicativo **ImageRelay** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

     ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para assinar ImageRelay** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:

     ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) 

    a. Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para entrar seu aplicativo de ImageRelay (por exemplo: *https://fabrikam.ImageRelay.com/*).

    b. Clique em **Avançar**.

4. Na página **Configurar logon único em ImageRelay** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) 

    a. Clique em **baixar o certificado**e, em seguida, salve o arquivo em seu computador.

    b. Clique em **Avançar**.

5. Em outra janela do navegador, entre no site da sua empresa ImageRelay como um administrador.

1. Na barra de ferramentas na parte superior, clique na carga de trabalho de **usuários e permissões** .

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 

1. Clique em **Criar nova permissão**.

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) 

1. Na carga de trabalho de **Logon único em configurações** , marque a caixa de seleção **este grupo pode apenas entrar por meio do logon único** e clique em **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 

1. Vá para **configurações de conta**.

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 

1. Vá para a carga de trabalho de **Logon único em configurações** .

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)

1. Na caixa de diálogo **Configurações de SAML** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)

    a. No portal do Azure clássico, copie **O URL de serviço de logon único**e cole-o na caixa de texto **URL de Login** .


    b. No portal do Azure clássico, copie a **URL do serviço Sign-Out único**e cole-o na caixa de texto **URL de Logout** .

    c. Como o **Formato de nome de identificação**, selecione **urn: oasis: nomes: tc: SAML:1.1:nameid-formato: emailAddress**.

    
    d. Como **Opções de encadernação para solicitações do provedor de serviço (retransmissão de imagem)**, selecione a **Vinculação de POSTAGEM**.
   

    e. Em **x. 509 certificado**, clique em **Certificado de atualização**.

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Abra o certificado baixado no bloco de notas, copie o conteúdo e, em seguida, cole a caixa de texto de certificado x. 509.
  
    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Na seção de **Provisionamento do usuário Just-In-Time** , selecione o **Habilitar provisionamento do usuário Just-In-Time**.

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Selecione o grupo de permissão (por exemplo, **SSO básico**) que é permitido entrar somente por meio de logon único.

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    Eu. Clique em **Salvar**.

6. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.

    ![Azure AD Single Sign-On][10]

7. Na página **confirmação de logon única** , clique em **Concluir**.

    ![Azure AD Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico Azure chamado Britta Simon.

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-a-imagerelay-test-user"></a>Criar um usuário de teste ImageRelay

O objetivo desta seção é criar um usuário chamado Britta Simon no ImageRelay.

**Para criar um usuário chamado Britta Simon no ImageRelay, execute as seguintes etapas:**

1. Logon para o site de sua empresa ImageRelay como administrador.

1. Vá para **usuários e permissões** e selecione **Criar usuário SSO**.

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 

1. Insira o **Email**, **nome**, **Sobrenome** e **empresa** do usuário que você deseja provisionar e selecione o grupo de permissão (por exemplo, SSO básico) que é o grupo que pode entrar somente por meio de logon único.

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 

1. Clique em **criar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso aos ImageRelay.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a ImageRelay, execute as seguintes etapas:**

1. No portal do Azure clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **ImageRelay**.

    ![Configurar o logon único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]


### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.  
Quando você clica no bloco de ImageRelay no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de ImageRelay.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png
