<properties
    pageTitle="Tutorial: Integração do Active Directory do Azure com @Task| Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e @Task."
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


# <a name="tutorial-azure-active-directory-integration-with-task"></a>Tutorial: Integração do Active Directory do Azure com@Task

O objetivo deste tutorial é mostram como integrar @Task com o Azure Active Directory (AD Azure).  
Integração @Task com o Azure AD fornece os seguintes benefícios: 

- Você pode controlar no Azure AD quem tem acesso a@Task
- Você pode habilitar os usuários para obter automaticamente conectado em @Task (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico Portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos 

Configurar integração do Azure AD com @Task, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um @Task logon único na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em três blocos de construção principais:

1. Adicionando @Task da Galeria 
2. Configurando e testando Azure AD logon único


## <a name="adding-task-from-the-gallery"></a>Adicionando @Task da Galeria
Para configurar a integração de @Task no Azure AD, você precisa adicionar @Task da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar @Task da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1] 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2] 

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3] 

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4] 

6. Na caixa de pesquisa, digite **@Task**.

    ![Aplicativos][5] 

7. No painel de resultados, selecione **@Task**e clique em **concluído** para adicionar o aplicativo.

    ![Aplicativos][30] 



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único

O objetivo desta seção é mostram como configurar e testar o Azure AD logon único com @Task com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber que o usuário correspondente em @Task para um usuário no Azure AD é. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em @Task precisa ser estabelecida.   
Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no @Task.
 
Para configurar e testar Azure AD logon único com @Task, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um @Tasktest usuário](#creating-a-halogen-software-test-user) ** - ter um representante de Britta Simon no @Taskthat está vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando Azure AD Single Sign-On

O objetivo desta seção é para habilitar Azure AD logon único no portal de clássico do Azure e configurar o logon único em seu @Task aplicativo.

**Para configurar Azure AD logon único com @Task, executar as seguintes etapas:**

1. No portal do Azure clássico, na **@Task** integração com o aplicativo da página, clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][6] 

2. Sobre o **como você gostaria que os usuários para entrar no @Task ** página, selecione **Azure AD Single Sign-On**e clique em **Avançar**.

    ![Azure AD Single Sign-On][7] 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Definir configurações de aplicativo][8] 
 
     a. Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para logon para sua @Task aplicativo (por exemplo:*https://<Tenant name>.attask ondemand.com*).

     b. Clique em **Avançar**.

4. Sobre o **Configurar o logon único em @Task ** de página, clique em **baixar metadados**, salve o arquivo de metadados localmente em seu computador e clique em **Avançar**.

    ![O que é Azure AD Connect][9] 



1. Logon para sua @Task site de empresa como administrador.

2. Vá para **configuração de logon único**.


1. Na caixa de diálogo **Single Sign-On** , execute as seguintes etapas

    ![Configurar o logon único][23]

    a. Como **tipo**, selecione **SAML 2.0**.

    b. Selecione a **identificação do provedor de serviço**.

    c. No portal do Azure clássico, copie a **URL de Login remoto**e cole-o na caixa de texto **URL do Portal de logon** .

    d. No portal do Azure clássico, copie a **URL do serviço Sign-Out único**e cole-o na caixa de texto **URL de Sign-Out** .

    e. No portal do Azure clássico, copie a **URL de senha de alteração**e cole-o na caixa de texto **Alterar senha URL** .

    f. Clique em **Salvar**.

6. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**. 

    ![O que é Azure AD Connect][10]

7. Na página **confirmação de logon única** , clique em **Concluir**.  

    ![O que é Azure AD Connect][11]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico Azure chamado Britta Simon.  

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
 
4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**. 

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas: 

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas: 

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
 
    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.
    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
 
8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
  
    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   

  
 
### <a name="creating-an-task-test-user"></a>Criando um @Task usuário de teste

O objetivo desta seção é criar um usuário chamado Britta Simon no @Task.


**Para criar um usuário chamado Britta Simon no @Task, executar as seguintes etapas:**

1. Entrar na sua @Task site de empresa como administrador.

2. No menu na parte superior, clique em **pessoas**.

3. Clique em **nova pessoa**. 

4. Na caixa de diálogo nova pessoa, execute as seguintes etapas:

    ![Criar um @Task usuário de teste][21] 

    a. Na caixa de texto **nome** , digite "Britta".

    b. Na caixa de texto **Sobrenome** , digite "Simon".

    c. Na caixa de texto **Endereço de Email** , digite o endereço de email de Britta Simon no Active Directory do Azure.

    d. Clique em **Adicionar pessoa**.




### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso a @Task.

![Atribuir usuário][200] 

**Para atribuir Britta Simon para @Task, executar as seguintes etapas:**

1. No portal do Azure clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **@Task**.

    ![Atribuir usuário][202] 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]



### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.  
Quando você clica na @Task lado a lado no painel de acesso, você deve obter automaticamente conectado no seu @Task aplicativo.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png






