<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com ADP eTime | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e ADP eTime."
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
    ms.date="10/17/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-adp-etime"></a>Tutorial: Integração com o Active Directory do Azure com ADP eTime

O objetivo deste tutorial é mostram como integrar ADP eTime com o Azure Active Directory (AD Azure).  
Integração ADP eTime com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao ADP eTime
- Você pode habilitar os usuários para automaticamente obter assinado no eTime ADP (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure


Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ADP eTime, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um ADP eTime logon único na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando ADP eTime da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-adp-etime-from-the-gallery"></a>Adicionando ADP eTime da Galeria
Para configurar a integração do ADP eTime no Azure AD, você precisa adicionar ADP eTime da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar ADP eTime da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **ADP eTime**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_01.png)

7. No painel de resultados, selecione **ADP eTime**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_06.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar logon único Azure AD com ADP eTime com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente na eTime ADP para um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em ADP eTime precisa ser estabelecida.  
Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no ADP eTime.

Para configurar e testar o Azure AD logon único com ADP eTime, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um eTime ADP testar usuário](#creating-a-adpetime-test-user)** - tenha uma duplicata de Britta Simon em eTime ADP que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando Azure AD Single Sign-On

O objetivo desta seção é habilitar Azure AD logon único no portal de clássico do Azure e configurar o logon único em seu aplicativo de eTime ADP.

Seu aplicativo de eTime ADP espera as declarações SAML em um formato específico, solicitando que você adicione mapeamentos de atributo personalizado à sua configuração de atributos de token SAML. A captura de tela a seguir mostra um exemplo para isso. Nome da declaração sempre será **"PersonImmutableID"** e o valor dos quais nós tiver mapeado para ExtensionAttribute2 contém EmployeeID do usuário. Aqui a fron de mapeamento de usuário Azure AD para ADP eTime será realizada na EmployeeID, mas você pode mapear isso para um valor diferente também com base nas suas configurações de aplicativo. Portanto, o trabalho com a equipe de eTime ADP primeiro para usar o identificador correto de um usuário e mapear esse valor com a declaração de **"PersonImmutableID"** .  

![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_02.png) 

Antes de configurar a declaração SAML, é necessário contatar a equipe de suporte de eTime ADP e solicitar o valor do atributo identificador exclusivo do seu locatário. Você precisa esse valor para configurar a declaração personalizada para seu aplicativo.


**Para configurar o logon único Azure AD com ADP eTime, execute as seguintes etapas:**

1. No portal do clássico Azure, na página de integração de aplicativo **ADP eTime** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para assinar ADP eTime** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_03.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:.

    ![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_04.png) 


    a. Na caixa de texto **URL de resposta** , digite a URL usada pelos usuários para logon para seu aplicativo de eTime de ADP usando o seguinte padrão: `https://<server name>.adp.com/affwebservices/public/saml2assertionconsumer`.

    b. Clique em **Avançar**.

4. Na página **Configurar logon único em ADP eTime** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_05.png) 

    a. Clique em **baixar metadados**e salve o arquivo em seu computador.

    b. Clique em **Avançar**.


5. Para obter o SSO configurado para o seu aplicativo, entre em contato com sua equipe de suporte de eTime ADP e email o arquivo de metadados de anexar baixado, para que eles podem ser configurados para integração de SSO.

    > [AZURE.NOTE] Depois de equipe **ADP eTime** configurar a instância, obtenha o valor de **RelayState** delas. Siga as etapas para configurá-lo mencionadas a seguir. Após essa configuração, você pode testar a integração. Então Observe que se trata de configuração importantes para esta integração de aplicativo trabalhar.

6. Para configurar o valor de RelayState no Azure AD, execute as seguintes etapas: 
    
    a. Efetuar logon no [Portal de gerenciamento do Azure](https://portal.azure.com) como administrador.

    b. No painel de navegação esquerdo, clique em **Mais serviços**. 
    
    ![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_07.png)

    c. Na caixa de texto **Pesquisar** , digite o **Active Directory do Azure**e clique no link relacionado.
    
    ![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_08.png)

    d. Clique em **aplicativos corporativos**.

    ![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_09.png)

    e. Na seção **Gerenciar** , clique em **Todos os aplicativos**.
    
    ![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_10.png)

    f. Na caixa de texto **Pesquisar** , digite **ADP eTime**e clique no link relacionado. 
    
    ![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_11.png)

    g. Na seção **Gerenciar** , clique em **logon único**.

    ![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_12.png)

    h. Selecione **Mostrar URL configurações avançadas**.
    
    ![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_13.png)
    
    Eu. Na caixa de texto de **Estado de retransmissão** , digite um valor usando os seguintes padrões:
    
    - Ambiente de produção:`https://fed.adp.com/saml/fedlanding.html?<id>` 
    - Ambiente de teste:`https://fed-stag.adp.com/saml/fedlanding.html?PORTAL`

    ![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_14.png)

    j. Salve as configurações.

7. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.

    ![Azure AD Single Sign-On][10]

8. Na página **confirmação de logon única** , clique em **Concluir**.  

    ![Azure AD Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico Azure chamado Britta Simon.  
Na lista de usuários, selecione **Britta Simon**.

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_05.png) 

    a. Como **Tipo de usuário**, selecione **novo usuário na sua organização**.

    b. Na caixa de texto **Nome de usuário** , digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-a-adp-etime-test-user"></a>Criar um usuário de teste de eTime ADP

O objetivo desta seção é criar um usuário chamado Britta Simon no ADP eTime. Trabalhe com a equipe de suporte de eTime ADP para adicionar os usuários na conta eTime ADP. 


> [AZURE.NOTE]Se você precisar criar um usuário manualmente, você precise contatar a equipe de suporte de eTime ADP.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso a ADP eTime.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a ADP eTime, execute as seguintes etapas:**

1. No portal do Azure clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **ADP eTime**.

    ![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_50.png) 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]



### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.  
Quando você clica no bloco de eTime ADP no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de eTime ADP.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_205.png
