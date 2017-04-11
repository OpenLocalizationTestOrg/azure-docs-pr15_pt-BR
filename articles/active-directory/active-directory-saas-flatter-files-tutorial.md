<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com arquivos simples | Microsoft Azure"
    description="Saiba como configurar o logon único entre Azure Active Directory e arquivos simples."
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


# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Tutorial: Integração com o Active Directory do Azure com arquivos simples

O objetivo deste tutorial é mostram como integrar arquivos simples com o Azure Active Directory (AD Azure).  
Integração de arquivos simples com Azure AD fornece os seguintes benefícios: 

- Você pode controlar no Azure AD quem tem acesso aos arquivos simples 
- Você pode habilitar os usuários para automaticamente obter conectado em arquivos simples (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - portal clássico do Active Directory do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos 

Para configurar a integração do Azure AD com arquivos simples, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único arquivos simples na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição de arquivos simples da Galeria 
2. Configurando e testando Azure AD logon único


## <a name="adding-flatter-files-from-the-gallery"></a>Adição de arquivos simples da Galeria
Para configurar a integração de arquivos simples no Azure AD, é necessário adicionar arquivos simples da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar arquivos simples da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Arquivos simples**.


    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_01.png)

7. No painel de resultados, selecione **Arquivos simples**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_500.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar logon único Azure AD com arquivos simples com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente em arquivos simples a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em arquivos simples precisa ser estabelecida.  
Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** em arquivos simples.
 
Para configurar e testar o logon único Azure AD com arquivos simples, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um arquivos simples testar usuário](#creating-a-halogen-software-test-user)** - ter um representante de Britta Simon em arquivos simples que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD

O objetivo desta seção é habilitar Azure AD logon único no portal de clássico do Azure AD e configurar o logon único em seu aplicativo de arquivos simples. Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64. Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

Para configurar o logon único para arquivos simples, você precisa de um domínio registrado. Se você não tiver um domínio registrado ainda, contato seus arquivos simples equipe por meio de suporte [support@flatterfiles.com](mailto:support@flatterfiles.com).  



**Para configurar o logon único Azure AD com arquivos simples, execute as seguintes etapas:**

1. No portal clássico do Azure AD, na página de integração de aplicativo de **Arquivos simples** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para assinar arquivos simples** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.
 
    ![Configurar o logon único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_02.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_03.png) 

    > [AZURE.NOTE] Arquivos simples usa a mesma URL de login SSO para todos os clientes: [https://www.flatterfiles.com/site/login/sso/](https://www.flatterfiles.com/site/login/sso/).
.
 
 
4. Na página **Configurar logon único em arquivos simples** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_04.png)  

    a. Clique em **baixar o certificado**e, em seguida, salve o arquivo em seu computador.

    b. Clique em **Avançar**.


1. Logon para seu aplicativo de arquivos simples como administrador.

2. Clique em Painel de controle. 

    ![Configurar o logon único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png)  



2. Clique em **configurações**e, em seguida, execute as seguintes etapas na guia **empresa** : 

    ![Configurar o logon único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png)  

    a. Selecione **usar SAML 2.0 para autenticação**.

    b. Clique em **Configurar SAML**.



2. Na caixa de diálogo **Configuração de SAML** , execute as seguintes etapas: 

    ![Configurar o logon único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png)  

    a. Na caixa de texto domínio, digite seu domínio registrado.

    > [AZURE.NOTE] Se você não tiver um domínio registrado ainda, contato seus arquivos simples equipe por meio de suporte [support@flatterfiles.com](mailto:support@flatterfiles.com).
    
    b. No Azure portal clássico, em Configurar único logon na caixa de diálogo de arquivos simples, copt a URL de serviço de logon único e cole-o na caixa de texto URL do provedor de identidade.

    c.  Crie um arquivo **codificado na base 64** do seu certificado baixado.  

    >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    d.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo à caixa de texto **FlatterFiles certificado de provedor de identidade** .

    e. Clique em **Atualizar**.

6. No portal de clássico do Azure AD, selecione a confirmação de configuração de logon único e clique em **Avançar**. 

    ![Azure AD Single Sign-On][10]

7. Na página **confirmação de logon única** , clique em **Concluir**.  

    ![Azure AD Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico Azure chamado Britta Simon.

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) 
 
4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**. 

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas: 

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png)  

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas: 

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png) 
 
    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.
    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png) 
 
8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png) 
  
    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   

  
 
### <a name="creating-a-flatter-files-test-user"></a>Criar um usuário de teste de arquivos simples

O objetivo desta seção é criar um usuário chamado Britta Simon em arquivos simples.

**Para criar um usuário chamado Britta Simon em arquivos simples, execute as seguintes etapas:**

1. Entrar seu site de empresa de **Arquivos simples** como administrador.

2. No painel de navegação à esquerda, clique em **configurações**e, em seguida, clique na **guia**de usuários.

    ![Cfreate um usuário de arquivos simples](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Clique em **Adicionar usuário**. 

4. Na caixa de diálogo **Adicionar usuário** , execute as seguintes etapas:

    ![Cfreate um usuário de arquivos simples](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. Na caixa de texto **nome** , digite **Britta**.

    b. Na caixa de texto **Sobrenome** , digite **Simon**. 

    c. Na caixa de texto **Endereço de Email** , digite o endereço de email de Britta no portal de clássico do Azure.

    d. Clique em **Enviar**.   


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso aos arquivos simples.

![Atribuir usuário][200] 

**Para atribuir Britta Simon aos arquivos simples, execute as seguintes etapas:**

1. No portal do Azure clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Arquivos simples**.

    ![Atribuir usuário](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_11.png) 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]



### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.  
Quando você clica no bloco de arquivos simples no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de arquivos simples.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_205.png






