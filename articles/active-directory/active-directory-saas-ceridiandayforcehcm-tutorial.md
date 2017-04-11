<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Ceridian Dayforce HCM | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e Ceridian Dayforce HCM."
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


# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Tutorial: Integração com o Active Directory do Azure com Ceridian Dayforce HCM

O objetivo deste tutorial é mostram como integrar Ceridian Dayforce HCM com o Azure Active Directory (AD Azure).  
Integração Ceridian Dayforce HCM com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Ceridian Dayforce HCM
- Você pode habilitar os usuários para automaticamente obter conectado em Ceridian Dayforce HCM (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure


Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Ceridian Dayforce HCM, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único Ceridian Dayforce HCM na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Ceridian Dayforce HCM da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Adicionando Ceridian Dayforce HCM da Galeria
Para configurar a integração de Ceridian Dayforce HCM no Azure AD, você precisa adicionar Ceridian Dayforce HCM da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar Ceridian Dayforce HCM da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Ceridian Dayforce HCM**.

    ![Aplicativos](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_01.png)

7. No painel de resultados, selecione **Ceridian Dayforce HCM**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Aplicativos](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_07.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar logon único Azure AD com Ceridian Dayforce HCM com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente em Ceridian Dayforce HCM para um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em Ceridian Dayforce HCM precisa ser estabelecida.

Para configurar e testar o Azure AD logon único com Ceridian Dayforce HCM, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um HCM de Dayforce Ceridian testar usuário](#creating-a-ceridian-dayforce-hcm-test-user)** - ter um representante de Britta Simon em Ceridian Dayforce HCM que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD

O objetivo desta seção é habilitar Azure AD logon único no portal de clássico do Azure e configurar o logon único em seu aplicativo de Ceridian Dayforce HCM.

Seu aplicativo de Ceridian Dayforce HCM espera as declarações SAML em um formato específico. Trabalhe com a equipe de Dayforce HCM primeiro para identificar o identificador de usuário corretas. Recomendamos utilizar o atributo **"nome"** como identificador de usuário. Você pode gerenciar o valor do atributo na caixa de diálogo **"Atributo"** . A captura de tela a seguir mostra um exemplo para isso. 

![Configurar o logon único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_02.png) 

**Para configurar o logon único Azure AD com Ceridian Dayforce HCM, execute as seguintes etapas:**




1. No portal do clássico Azure, na página de integração do aplicativo **Ceridian Dayforce HCM** , no menu na parte superior, clique em **atributos**.

    ![Configurar o logon único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_81.png) 


1. Na lista de **atributos de token de saml** atributos, selecione o atributo de nome e clique em **Editar**.

    ![Configurar o logon único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_06.png) 


1. Na caixa de diálogo **Editar atributo de usuário** , execute as seguintes etapas:
 
    a. Na lista de **Valor do atributo** , selecione o atributo de usuário que você deseja usar para sua implementação.  
    Por exemplo, se você deseja usar EmployeeID como identificador exclusivo do usuário e você armazenou o valor do atributo na ExtensionAttribute2, selecione **user.extensionattribute2**. 

    b. Clique em **Concluir**.  
    



1. No menu na parte superior, clique em **Início rápido**.

    ![Configurar o logon único](./media/active-directory-saas-hightail-tutorial/tutorial_general_83.png)  





1. No portal do clássico Azure, na página de integração do aplicativo **Ceridian Dayforce HCM** , clique em **Configurar o logon único**.

    ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para assinar Ceridian Dayforce HCM** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_03.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:.

    ![Configurar o logon único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_04.png) 


    a. Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para logon para seu aplicativo Ceridian Dayforce HCM. Para ambientes de produção, use o seguinte formato de URL:`https://sso.dayforcehcm.com/DayforcehcmNamespace`

    Para maior clareza, substitua DayforcehcmNamespace por namespace do seu ambiente ou sua ID de empresa; Por exemplo:`https://sso.dayforcehcm.com/contoso`
    
    Para ambientes de teste, use o seguinte formato de URL:`https://ssotest.dayforcehcm.com/DayforcehcmNamespace` 

    b. Na caixa de texto **URL de resposta** , digite a URL usada pelo Azure AD para postar a resposta.  
    Para ambientes de produção, use:`https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2`  
    Para ambientes de teste, use:`https://fs-test.dayforcehcm.com/sp/ACS.saml2`  
   

4. Na página **Configurar logon único em Ceridian Dayforce HCM** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_05.png) 

    a. Clique em **baixar o certificado**e, em seguida, salve o arquivo em seu computador.

    b. Clique em **Avançar**.


5. Para obter o SSO configurado para o seu aplicativo, entre em contato com sua equipe de suporte Ceridian Dayforce HCM via email e forneça a ele o seguinte:

    - O arquivo de certificado baixado
    - A **URL do emissor**
    - A **URL de SAML SSO** 
    - O **único sair a URL do serviço** 


6. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.

    ![Azure AD Single Sign-On][10]

7. Na página **confirmação de logon única** , clique em **Concluir**.  

    ![Azure AD Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico Azure chamado Britta Simon.

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-a-ceridian-dayforce-hcm-test-user"></a>Criar um usuário de teste Ceridian Dayforce HCM

O objetivo desta seção é criar um usuário chamado Britta Simon no Ceridian Dayforce HCM. 

Trabalhe com a equipe de suporte Ceridian Dayforce HCM para que os usuários adicionados à sua no aplicativo Ceridian Dayforce HCM. 





### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso aos Ceridian Dayforce HCM.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a Ceridian Dayforce HCM, execute as seguintes etapas:**

1. No portal do Azure clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Ceridian Dayforce HCM**.

    ![Configurar o logon único](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_50.png) 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]



### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.  
Quando você clica no bloco de Ceridian Dayforce HCM no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de Ceridian Dayforce HCM.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_205.png
