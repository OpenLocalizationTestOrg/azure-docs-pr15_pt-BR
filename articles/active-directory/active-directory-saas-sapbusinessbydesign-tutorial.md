<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com SAP Business ByDesign | Microsoft Azure"
    description="Saiba como configurar o logon único entre Azure Active Directory e do SAP Business ByDesign."
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
    ms.date="09/09/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Tutorial: Integração com o Active Directory do Azure com ByDesign de negócios SAP

Neste tutorial, você aprenderá a integrar o SAP Business ByDesign com o Azure Active Directory (AD Azure).

Integração SAP Business ByDesign com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao ByDesign do SAP Business
- Você pode habilitar os usuários para automaticamente obter assinado no SAP Business ByDesign (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ByDesign de negócios SAP, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único SAP Business ByDesign na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testar Azure AD logon único em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando SAP Business ByDesign da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Adicionando SAP Business ByDesign da Galeria
Para configurar a integração do SAP Business ByDesign no Azure AD, você precisa adicionar SAP Business ByDesign da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o SAP Business ByDesign da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.


3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **ByDesign de negócios SAP**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_01.png)

7. No painel de resultados, selecione **SAP Business ByDesign**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Do Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
Nesta seção, configurar e testar logon único Azure AD com ByDesign de negócios SAP com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no SAP Business ByDesign a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SAP Business ByDesign precisa ser estabelecida.

Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** do SAP Business ByDesign.

Para configurar e testar o Azure AD logon único com ByDesign de negócios SAP, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
3. **[Criando um ByDesign de negócios SAP testar usuário](#creating-an-sap-business-bydesign-test-user)** - ter um representante de Britta Simon em ByDesign de negócios SAP que esteja vinculado à representação Azure AD dela.
4. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD

Nesta seção, você habilitar Azure AD logon único no portal do clássico e configurar o logon único em seu aplicativo do SAP Business ByDesign.

Aplicativo do SAP Business ByDesign espera as declarações SAML em um formato específico. Configure as seguintes declarações para este aplicativo. Você pode gerenciar os valores desses atributos da guia **"Atributo"** do aplicativo. A captura de tela a seguir mostra um exemplo para isso. 


**Para configurar o logon único Azure AD com ByDesign do SAP Business, execute as seguintes etapas:**


1. No portal do clássico Azure, na página de integração de aplicativo do **SAP Business ByDesign** , no menu na parte superior, clique em **atributos**.

    ![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_80.png) 


2. Na lista de atributos token SAML atributos, selecione o atributo de identificador de nome e clique em **Editar**.

    ![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_84.png) 

3. Na caixa de diálogo Editar atributo de usuário, execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_85.png) 

    a. A valor do atributo, selecione lista a função **ExtractMailPrefix()**

    b. Na lista de mensagens, selecione o atributo de usuário que você deseja usar para sua implementação. 
    Por exemplo, se você deseja usar EmployeeID como identificador exclusivo do usuário e você armazenou o valor do atributo na ExtensionAttribute2, selecione **user.extensionattribute2**. 

    c. Clique em **Concluir**. 
    

4. No portal do clássico, na página de integração de aplicativo do **SAP Business ByDesign** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o logon único][6] 

5. Na página **como você gostaria que usuários para entrar SAP Business ByDesign** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_03.png) 

6. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_04.png) 

    a. Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para logon para seu aplicativo do SAP Business ByDesign usando o seguinte padrão:`https://<servername>.sapbydesign.com`
    
    b. Clique em **Avançar**
 
7. Na página **Configurar logon único no SAP Business ByDesign** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_05.png)

    a. Clique em **baixar metadados**e salve o arquivo em seu computador.

    b. Clique em **Avançar**.


8. Para obter o SSO configurado para o seu aplicativo, execute as seguintes etapas:

    a. Entrar seu portal do SAP Business ByDesign com direitos de administrador.

    b. Navegue até o **aplicativo e tarefas comuns de gerenciamento de usuário** e clique na guia de **Provedor de identidade** .

    c. Clique em **Novo provedor de identidade** e selecione o arquivo XML de metadados que você baixou a partir do portal clássico Azure. Importando os metadados, o sistema carrega automaticamente o certificado de assinatura necessária e o certificado de criptografia.

    ![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

    d. Para incluir a **URL do serviço de consumidor de declaração** na solicitação de SAML, selecione **URL do serviço de consumidor declaração incluir**.

    e. Clique em **Ativar o logon único**.

    f. Salve as alterações.

    g. Clique na guia **Meu sistema** .

    ![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

    h. Copie a **URL de SSO** e colá-lo na caixa de texto **Azure AD entrada na URL** .

    ![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

    Eu. Especifique se o funcionário manualmente pode escolher entre logon com uma ID de usuário e senha ou SSO, selecionando a **Seleção do provedor de identidade Manual**.

    j. Na seção **URL de SSO** , especifique a URL que deve ser usada pelo funcionário para fazer logon no sistema. 
    No URL enviadas à lista de menu suspenso de funcionários, você pode escolher entre as seguintes opções:
    
    **URL não SSO**
 
    O sistema envia apenas a URL normal do sistema para o funcionário. O funcionário não pode efetuar logon usando o SSO e deve usar senha ou certificado, em vez disso.

    **URL DE SSO** 

    O sistema envia apenas a URL de SSO para o funcionário. O funcionário pode efetuar logon usando o SSO. Solicitação de autenticação é redirecionada por meio de IdP.

    **Seleção automática**
 
    Se SSO não estiver ativa, o sistema envia a URL normal do sistema para o funcionário. Se SSO estiver ativo, o sistema verifica se o funcionário tem uma senha. Se uma senha estiver disponível, tanto SSO não SSO URLs e são enviadas para o funcionário. No entanto, se o funcionário não tiver uma senha, apenas a URL de SSO é enviada para o funcionário.

    k. Salve as alterações.

9. No portal do clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.
    
    ![Azure AD Single Sign-On][10]

10. Na página **confirmação de logon única** , clique em **Concluir**.  
 
    ![Azure AD Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
Nesta seção, você criar um usuário de teste no portal do clássico chamado Britta Simon.


![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-an-sap-business-bydesign-test-user"></a>Criação de um usuário de teste do SAP Business ByDesign

Nesta seção, você criar um usuário chamado Britta Simon no SAP Business ByDesign. Trabalhe com a equipe de suporte do SAP Business ByDesign para adicionar os usuários na plataforma do SAP Business ByDesign. 

> [AZURE.NOTE] Certifique-se de que o valor de NameID deve coincidir com o campo de nome de usuário na plataforma do SAP Business ByDesign.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

Nesta seção, você habilitar Britta Simon usar logon único Azure concedendo o acesso aos ByDesign de negócios SAP.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a ByDesign do SAP Business, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **ByDesign de negócios SAP**.

    ![Configurar o logon único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_50.png) 

3. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]


### <a name="testing-single-sign-on"></a>Teste de logon único

Nesta seção, você testar seu Azure AD único logon configuração usando o painel de acesso.

Quando você clica no bloco do SAP Business ByDesign no painel de acesso, que deve receber automaticamente conectado no seu aplicativo do SAP Business ByDesign.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_205.png
