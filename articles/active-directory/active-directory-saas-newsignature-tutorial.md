<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com o Portal de gerenciamento de nuvem para Microsoft Azure | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e o Portal de gerenciamento de nuvem para Microsoft Azure."
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
    ms.date="08/16/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Tutorial: Integração com o Active Directory do Azure com o Portal de gerenciamento de nuvem para Microsoft Azure

O objetivo deste tutorial é mostram como integrar o Portal de gerenciamento de nuvem para Microsoft Azure com o Azure Active Directory (AD Azure).  
Integrar o Portal de gerenciamento de nuvem para Microsoft Azure AD Azure fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Portal de gerenciamento de nuvem do Microsoft Azure
- Você pode habilitar os usuários para automaticamente obter assinado no Portal de gerenciamento de nuvem para Microsoft Azure (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure


Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Portal de gerenciamento de nuvem para Microsoft Azure, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um Portal de gerenciamento de nuvem para logon único Microsoft Azure na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Portal de gerenciamento de nuvem para Microsoft Azure da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Adicionando o Portal de gerenciamento de nuvem para Microsoft Azure da Galeria
Para configurar a integração do Portal de gerenciamento de nuvem do Microsoft Azure no Azure AD, é necessário adicionar o Portal de gerenciamento de nuvem para Microsoft Azure da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Portal de gerenciamento de nuvem para Microsoft Azure da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite o **Portal de gerenciamento de nuvem para Microsoft Azure**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_01.png)

7. No painel de resultados, selecione o **Portal de gerenciamento de nuvem para Microsoft Azure**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar Azure AD logon único com o Portal de gerenciamento de nuvem para Microsoft Azure com base em um usuário de teste chamado "Britta Simon".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no Portal de gerenciamento de nuvem do Microsoft Azure para um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Portal de gerenciamento de nuvem para Microsoft Azure precisa ser estabelecida.  
Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no Portal de gerenciamento de nuvem do Microsoft Azure.

Para configurar e testar o Azure AD logon único com o Portal de gerenciamento de nuvem para Microsoft Azure, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criar um Portal de gerenciamento de nuvem do Microsoft Azure testar usuário](#creating-a-newsignature-test-user)** - ter um representante de Britta Simon no Portal de gerenciamento de nuvem do Microsoft Azure que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando Azure AD Single Sign-On

O objetivo desta seção é habilitar Azure AD logon único no portal de clássico do Azure e configurar o logon único no seu Portal de gerenciamento de nuvem para o aplicativo do Microsoft Azure.



**Para configurar o logon único Azure AD com o Portal de gerenciamento de nuvem para Microsoft Azure, execute as seguintes etapas:**

1. No portal do clássico Azure, na página de integração do aplicativo do **Portal de gerenciamento de nuvem para Microsoft Azure** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para entrar Portal de gerenciamento de nuvem para Microsoft Azure** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_03.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:.

    ![Configurar o logon único](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_04.png) 

    a. Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para logon para seu Portal de gerenciamento de nuvem para o aplicativo do Microsoft Azure usando o seguinte padrão:`https://portal.igcm.com/<instance name>`

    b. Clique em **Avançar**.


4. Na página **Configurar logon único no Portal de gerenciamento de nuvem para Microsoft Azure** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_05.png) 

    a. Clique em **baixar o certificado**e, em seguida, salve o arquivo em seu computador.

    b. Clique em **Avançar**.


5. Para obter SSO configurado para o seu aplicativo, entre em contato com seu Portal de gerenciamento de nuvem para a equipe de suporte do Microsoft Azure no [jczernuszka@newsignature.com](mailTo:jczernuszka@newsignature.com) e o arquivo de certificado baixado do anexo de email. Também forneça a URL do emissor, SAML SSO URL e logon único-Out URL de serviço de forma que eles podem ser configurados para integração de SSO.


6. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.

    ![Azure AD Single Sign-On][10]

7. Na página **confirmação de logon única** , clique em **Concluir**.  

    ![Azure AD Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico Azure chamado Britta Simon.  

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-a-cloud-management-portal-for-microsoft-azure-test-user"></a>Criar um Portal de gerenciamento de nuvem para usuário de teste do Microsoft Azure

O objetivo desta seção é criar um usuário chamado Britta Simon no Portal de gerenciamento de nuvem para o Microsoft Azure. Trabalhe com o Portal de gerenciamento de nuvem para equipe de suporte do Microsoft Azure para adicionar os usuários no Portal de gerenciamento de nuvem para a conta do Microsoft Azure. 


> [AZURE.NOTE]Se você precisar criar um usuário manualmente, é necessário contatar o Portal de gerenciamento de nuvem para a equipe de suporte do Microsoft Azure.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso ao Portal de gerenciamento de nuvem para Microsoft Azure.

![Atribuir usuário][200] 

**Para atribuir Britta Simon ao Portal de gerenciamento de nuvem para Microsoft Azure, execute as seguintes etapas:**

1. No portal do Azure clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione o **Portal de gerenciamento de nuvem para Microsoft Azure**.

    ![Configurar o logon único](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_50.png) 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]



### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.  
Quando você clica o Portal de gerenciamento de nuvem para o bloco do Microsoft Azure no painel de acesso, que deve receber automaticamente conectado no seu Portal de gerenciamento de nuvem para o aplicativo do Microsoft Azure.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_205.png
