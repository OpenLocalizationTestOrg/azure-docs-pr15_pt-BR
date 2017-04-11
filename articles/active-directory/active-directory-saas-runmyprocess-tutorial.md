<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com RunMyProcess | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e RunMyProcess."
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
    ms.date="10/21/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Tutorial: Integração com o Active Directory do Azure com RunMyProcess

O objetivo deste tutorial é mostram como integrar RunMyProcess com o Azure Active Directory (AD Azure).

Integração RunMyProcess com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao RunMyProcess
- Você pode habilitar os usuários para automaticamente obter assinado no RunMyProcess (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com RunMyProcess, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único RunMyProcess na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando RunMyProcess da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-runmyprocess-from-the-gallery"></a>Adicionando RunMyProcess da Galeria
Para configurar a integração do RunMyProcess no Azure AD, você precisa adicionar RunMyProcess da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar RunMyProcess da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **RunMyProcess**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_01.png)

7. No painel de resultados, selecione **RunMyProcess**e clique em **concluído** para adicionar o aplicativo.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar logon único Azure AD com RunMyProcess com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no RunMyProcess a um usuário no Azure AD é. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em RunMyProcess precisa ser estabelecida.

Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no RunMyProcess.

Para configurar e testar o Azure AD logon único com RunMyProcess, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
3. **[Criando um RunMyProcess testar usuário](#creating-a-runmyprocess-test-user)** - ter um representante de Britta Simon em RunMyProcess que esteja vinculado à representação Azure AD dela.
4. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.


### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD

Nesta seção, você habilitar Azure AD logon único no portal do clássico e configurar o logon único em seu aplicativo de RunMyProcess.

**Para configurar o logon único Azure AD com RunMyProcess, execute as seguintes etapas:**

1. No portal do clássico, na página de integração do aplicativo **RunMyProcess** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para assinar RunMyProcess** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_03.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_04.png) 

    a. Na caixa de texto de **Entrada na URL** , digite uma URL usando o seguinte padrão: `https://live.runmyprocess.com/live/<tenant id>`.
        
    b. Clique em **Avançar**

    > [AZURE.NOTE] Observe que você precisa atualizar o valor com o sinal real na URL. Para obter este valor, contate a equipe de suporte RunMyProcess via <mailto:support@runmyprocess.com>.
 
4. Na página **Configurar logon único em RunMyProcess** , clique em **Baixar o certificado** e, em seguida, salve o arquivo em seu computador:

    ![Configurar o logon único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_05.png)

5. Em uma janela de navegador da web diferente, logon em seu locatário RunMyProcess como um administrador.

6. No painel de navegação esquerdo, clique em **conta** e selecione **configuração**.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_001.png)

7. Vá para a seção **método de autenticação** e executar etapas abaixo:

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Como **método**, selecione **SSO com Samlv2**.

    b. No **SSO redirecionar** a caixa de texto coloca o valor da **URL do SAML SSO** do Assistente de configuração de aplicativo do Azure AD.

    c. No **Logout redirecionar** a caixa de texto coloca o valor da **URL do serviço Sign-Out único** do Assistente de configuração de aplicativo do Azure AD.

    d. No **Formato de Id do nome da** caixa de texto coloca o valor de **Formato de nome de identificador** do Assistente de configuração de aplicativo do Azure AD.

    e. Copie o conteúdo do arquivo baixado certificado e, em seguida, cole a caixa de texto de **certificado** . 

    f. Clique em ícone **Salvar** .
    
8. No portal do clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.
    
    ![Azure AD Single Sign-On][10]

9. Na página **confirmação de logon única** , clique em **Concluir**.  
 
    ![Azure AD Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico chamado Britta Simon.

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_01.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_02.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_03.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:  ![criação de um usuário de teste do Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_04.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas: ![criação de um usuário de teste do Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_05.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_06.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_07.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   

### <a name="creating-a-runmyprocess-test-user"></a>Criar um usuário de teste RunMyProcess
  
Para permitir que usuários do Azure AD fazer login RunMyProcess, ele devem ser provisionados em RunMyProcess. No caso de RunMyProcess, provisionamento é uma tarefa manual.

#### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa RunMyProcess como administrador.

2.  Clique em **conta** e selecione **usuários** no painel de navegação esquerdo, clique em **Novo usuário**.

    ![Novo usuário] (./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_003.png "Novo usuário")

3.  Na seção **Configurações de usuário** , execute as seguintes etapas:

    ![Perfil] (./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_004.png "Perfil")

    a. Digite o **nome** e o **email** de uma conta AAD válida que deseja provisionar em caixas de texto relacionadas.

    b. Selecione um **idioma IDE**, um **idioma** e um **perfil**.

    c. Selecione **Enviar email de criação de conta para mim**.

    d. Clique em **Salvar**.

    >[AZURE.NOTE] Você pode usar as ferramentas de criação de outros RunMyProcess usuário conta ou APIs fornecidas pela RunMyProcess provisionar o Active Directory do Azure contas de usuário.
    

### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso aos RunMyProcess.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a RunMyProcess, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **RunMyProcess**.

    ![Configurar o logon único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_50.png) 

3. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Britta Simon**.

5. barra na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]


### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.
 
Quando você clica no bloco de RunMyProcess no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de RunMyProcess.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_205.png
