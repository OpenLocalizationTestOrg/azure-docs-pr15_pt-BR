<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com HR2day por Merces | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e HR2day por Merces."
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


# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Tutorial: Integração com o Active Directory do Azure com HR2day por Merces

O objetivo deste tutorial é mostram como integrar HR2day por Merces com o Azure Active Directory (AD Azure).  
Integração HR2day por Merces com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao HR2day por Merces
- Você pode habilitar os usuários para automaticamente obter assinado no HR2day por Merces (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com HR2day por Merces, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um HR2day por logon único Merces na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando HR2day por Merces da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-hr2day-by-merces-from-the-gallery"></a>Adicionando HR2day por Merces da Galeria
Para configurar a integração de HR2day por Merces no Azure AD, você precisa adicionar HR2day por Merces da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar HR2day por Merces da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.
 
    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **HR2day por Merces**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_01.png)

7. No painel de resultados, selecione **HR2day por Merces**e, em seguida, clique em **concluído** para adicionar o aplicativo.


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar Azure AD logon único com HR2day por Merces com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no HR2day por Merces um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em HR2day por Merces precisa ser estabelecida.  
Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no HR2day por Merces.

Para configurar e testar o Azure AD logon único com HR2day por Merces, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um HR2day por Merces testar usuário](#creating-a-hr2day-by-merces-test-user)** - ter um representante de Britta Simon em HR2day por Merces que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando Azure AD Single Sign-On

O objetivo desta seção é como habilitar usuários autenticar HR2day por Merces com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

Seu HR2day pelo aplicativo de Merces espera as declarações SAML em um formato específico, solicitando que você adicione mapeamentos de atributo personalizado à sua configuração de atributos de token SAML. A captura de tela a seguir mostra um exemplo para isso. 

![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png) 

Antes de configurar a declaração SAML, você precise contatar a equipe de suporte HR2day via [servicedesk@merces.nl](mailto:servicedesk@merces.nl) e solicite que o valor do atributo identificador exclusivo do seu locatário. Você precisa esse valor para concluir as etapas na próxima seção.


**Para configurar o logon único Azure AD com HR2day por Merces, execute as seguintes etapas:**

1. No portal do clássico Azure, na página de integração de aplicativo de **HR2day por Merces** , no menu na parte superior, clique em **atributos** para abrir a caixa de diálogo de **Atributos de Token SAML** . 

    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_06.png) 

2. Para adicionar os mapeamentos de atributo obrigatório, execute as seguintes etapas, execute as seguintes etapas: 

    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_07.png) 


    a. Clique em **Adicionar atributo de usuário**.

    b. Na caixa de texto **Nome do atributo** , digite **"ATTR_LOGINCLAIM"**.

    c. A **Valor do atributo** , selecione lista **Join ()**. 

    d. Na lista de **cadeia1** , selecione **User.mail**. 

    e. Na caixa de texto **cadeia2** , digite o **identificador exclusivo** fornecidas pela sua equipe de HR2day. 

    f. Na caixa de texto **separador** , digite **@**.

    g. Clique em **Concluir**.

  
3. Clique em **Aplicar alterações**.


1. No menu na parte superior, clique em **Início rápido** para abrir a caixa de diálogo de **Início rápido** .

    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_general_08.png) 



1. Clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para assinar HR2day por Merces** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_03.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas: 

    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_04.png) 


    a. Na caixa de texto de entrada na URL, digite a URL usada pelos usuários para logon para sua HR2day pelo aplicativo de Merces usando o seguinte padrão: **"https://\<nome do locatário\>.force.com/\<nome da instância\>"**.

    b. Clique em **Avançar**.

4. Na página **Configurar logon único em HR2day por Merces** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_05.png) 

    a. Clique em **baixar o certificado**e, em seguida, salve o arquivo em seu computador.

    b. Clique em **Avançar**.


5. Para obter o SSO configurado para o seu aplicativo, contate seu HR2day pela equipe de suporte de Merces via [servicedesk@merces.nl](emailTo:servicedesk@merces.nl) e anexe o arquivo de certificado baixado ao seu email. Também forneça o SAML SSO URL, a URL de um Check-Out de entrada e a URL do emissor de forma que eles podem ser configurados para integração de SSO.


> [AZURE.NOTE] Por favor mencionar a equipe de Merces que essa integração necessário ID de entidade seja definido com este padrão **https://hr2day.force.com/INSTANCENAME**



6. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.

    ![Azure AD Single Sign-On][10]

7. Na página **confirmação de logon única** , clique em **Concluir**.  

    ![Azure AD Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico Azure chamado Britta Simon.  

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-a-hr2day-by-merces-test-user"></a>Criando um HR2day por usuário de teste Merces

O objetivo desta seção é criar um usuário chamado Britta Simon no HR2day por Merces. Trabalhe com HR2day pela equipe de suporte de Merces para adicionar os usuários na conta HR2day. 


> [AZURE.NOTE]Se você precisar criar um usuário manualmente, você precise contatar a HR2day pela equipe de suporte de Merces.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso aos HR2day por Merces.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a HR2day por Merces, execute as seguintes etapas:**

1. No portal do Azure clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **HR2day por Merces**.

    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_50.png) 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]



### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.  
Quando você clica na HR2day pelo bloco de Merces no painel de acesso, que deve receber automaticamente conectado no seu HR2day pelo aplicativo de Merces.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_205.png
