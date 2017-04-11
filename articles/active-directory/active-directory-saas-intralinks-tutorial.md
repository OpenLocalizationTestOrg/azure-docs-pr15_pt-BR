<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Intralinks | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e Intralinks."
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


# <a name="tutorial-azure-active-directory-integration-with-intralinks"></a>Tutorial: Integração com o Active Directory do Azure com Intralinks

Neste tutorial, você aprenderá a integrar Intralinks com o Azure Active Directory (AD Azure).

Integração Intralinks com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Intralinks
- Você pode habilitar os usuários para automaticamente obter assinado no Intralinks (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Intralinks, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único Intralinks na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testar Azure AD logon único em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Intralinks da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-intralinks-from-the-gallery"></a>Adicionando Intralinks da Galeria
Para configurar a integração do Intralinks no Azure AD, você precisa adicionar Intralinks da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar Intralinks da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Intralinks**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_01.png)

7. No painel de resultados, selecione **Intralinks**e clique em **concluído** para adicionar o aplicativo.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único

Nesta seção, configurar e testar logon único Azure AD com Intralinks com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no Intralinks a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em Intralinks precisa ser estabelecida.

Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no Intralinks.

Para configurar e testar o Azure AD logon único com Intralinks, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um Intralinks testar usuário](#creating-an-intralinks-test-user)** - ter um representante de Britta Simon em Intralinks que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando Azure AD Single Sign-On

Nesta seção, você habilitar Azure AD logon único no portal do clássico e configurar o logon único em seu aplicativo de Intralinks.


**Para configurar o logon único Azure AD com Intralinks, execute as seguintes etapas:**

1. No portal do clássico, na página de integração do aplicativo **Intralinks** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para assinar Intralinks** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_03.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:.

    ![Configurar o logon único](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_04.png) 

    a. Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para logon para seu aplicativo de Intralinks usando o seguinte padrão: **https://\<nome da empresa\>.Intralinks.com/?PartnerIdpId= https://sts.windows.net/\<ID do Azure AD locatário\>**.

    b. Clique em **Avançar**.
    
4. Na página **Configurar logon único em Intralinks** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_05.png)

    a. Clique em **baixar metadados**e salve o arquivo em seu computador.

    b. Clique em **Avançar**.


5. Para obter o SSO configurado para o seu aplicativo, entre em contato com a equipe de suporte de Intralinks e o arquivo de metadados baixado do anexo de email.


6. No portal do clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.
    
    ![Azure AD Single Sign-On][10]

7. Na página **confirmação de logon única** , clique em **Concluir**.  
 
    ![Azure AD Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
Nesta seção, você criar um usuário de teste no portal do clássico chamado Britta Simon.

Na lista de usuários, selecione **Britta Simon**.

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:  ![criação de um usuário de teste do Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas: ![criação de um usuário de teste do Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-an-intralinks-test-user"></a>Criação de um usuário de teste Intralinks

Nesta seção, você criar um usuário chamado Britta Simon no Intralinks. Trabalhe com a equipe de suporte Intralinks para adicionar os usuários na plataforma Intralinks.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

Nesta seção, você habilitar Britta Simon usar logon único Azure concedendo o acesso aos Intralinks.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a Intralinks, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Intralinks**.

    ![Configurar o logon único](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_50.png) 

3. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]

### <a name="adding-intralinks-via-or-elite-application"></a>Adicionando o aplicativo Intralinks VIA ou Elite
Intralinks usa a mesma plataforma de entrada única na identidade para todos os outros aplicativos de Intralinks excluindo aplicativo de negócio Nexus. Portanto, se você planeja usar qualquer outro aplicativo de Intralinks, em seguida, primeiro você precisa configurar o logon único para um aplicativo de Intralinks primária usando o procedimento descrito acima.

Depois que você pode seguir as abaixo procedimento para adicionar outro aplicativo Intralinks em seu locatário que pode aproveitar este aplicativo primário para logon único. 

> [AZURE.NOTE] Faça a anotação que este recurso está disponível somente para clientes do Azure AD Premium SKU e não está disponível gratuitamente ou clientes de SKU básico.

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory][1]
2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. De tabulação à esquerda clique na guia **personalizada**

    ![Adicionando o aplicativo Intralinks VIA ou Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_51.png)

7. Dê um nome apropriado para o aplicativo, por exemplo, **Intralinks Elite** e clique em Concluir botão.

8. Clique no botão **Configurar o logon único**

9. Selecione a opção **Existente logon único**

    ![Adicionando o aplicativo Intralinks VIA ou Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_52.png)

10. Obtenha o SP iniciada SSO URL de Intralinks de equipe para outro aplicativo Intralinks e insira-o conforme mostrado abaixo. 

    ![Adicionando o aplicativo Intralinks VIA ou Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_53.png)

    a. Na caixa de texto de entrada na URL, digite a URL usada pelos usuários para logon para seu aplicativo de Intralinks usando o seguinte padrão: **https://\<NomeDaEmpresa\>.Intralinks.com/?PartnerIdpId= https://sts.windows.net/\<AzureADTenantID\> **


11. Clique em **Avançar**.

12. Atribuir o aplicativo para usuários ou grupos, conforme mostrado na seção ** [atribuindo o usuário de teste do Azure AD](#assigning-the-azure-ad-test-user)**


### <a name="testing-single-sign-on"></a>Teste de logon único

Nesta seção, você testar seu Azure AD único logon configuração usando o painel de acesso.

Quando você clica no bloco de Intralinks no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de Intralinks.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_205.png
