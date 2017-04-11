<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com o representante | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e o representante."
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
    ms.date="09/28/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Tutorial: Integração com o Active Directory do Azure com o representante

O objetivo deste tutorial é mostrar a você como integrar representante ao Azure Active Directory (AD Azure).

Integração de representante com Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao representante
- Você pode habilitar os usuários para automaticamente obter assinado no representante (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com representante, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único representante na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o representante da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-deputy-from-the-gallery"></a>Adicionando o representante da Galeria
Para configurar a integração do representante no Azure AD, você precisa adicionar representante da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o representante da galeria, execute as seguintes etapas:**

1. No **Portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.
    
    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.
    
    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **representante**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_01.png)

7. No painel de resultados, selecione o **representante**e clique em **concluído** para adicionar o aplicativo.

    ![Selecionando o aplicativo na Galeria](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar logon único Azure AD com o representante com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no representante para um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no representante deve ser feita.

Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no representante.

Para configurar e testar o Azure AD logon único com o representante, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
3. **[Criando um representante teste usuário](#creating-a-deputy-test-user)** - ter um representante de Britta Simon em representante que esteja vinculado à representação Azure AD dela.
4. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD

Nesta seção, você habilitar Azure AD logon único no portal do clássico e configurar o logon único em seu aplicativo de representante.

**Para configurar o logon único Azure AD com representante, execute as seguintes etapas:**

1. No portal do clássico, na página de integração de aplicativo de **representante** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para assinar representante** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.
    
    ![Configurar o logon único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_03.png)

3. Na página de diálogo **Definir configurações de aplicativo** , se desejar configurar o aplicativo no **IDP iniciada modo**, execute as seguintes etapas e clique em **Avançar**:

    ![Configurar o logon único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_04.png)

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://<your-subdomain>.<region>.deputy.com`.

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<your-subdomain>.<region>.deputy.com/exec/devapp/samlacs`.

    c. Clique em **Avançar**.

4. Se você desejar configurar o aplicativo no **SP iniciada modo** na página de diálogo **Definir configurações de aplicativo** , em seguida, clique em **"Mostrar configurações (opcionais) avançadas"** e digite a **Entrada na URL** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_05.png)

    a. Na caixa de texto de **Entrada na URL** , digite uma URL usando o seguinte padrão: `https://<your-subdomain>.<region>.deputy.com`.

    b. Clique em **Avançar**.

    > [AZURE.NOTE] Sufixo de região do representante é opitional, ou deve usar um destes procedimentos: au | disp | UE | como | la | af | um | ent-au | ent JavaScript | ent-UE | ent-como | ENT-la | ENT-af | ENT-um

5. Na página **Configurar logon único ao representante** , execute as seguintes etapas e clique em **Avançar**:

    ![Configurar o logon único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_06.png)

    a. Clique em **baixar o certificado**e, em seguida, salve o arquivo em seu computador.

    
6. Navegue até a seguinte URL: https://(your-subdomain).deputy.com/exec/config/system_config. Vá para **Configurações de segurança** e clique em **Editar**.

    ![Configurar o logon único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_004.png)

7. No portal do clássico Azure, sobre o configurar logon único na página de representante, copie a URL de SSO SAML. 

8. Nesta página de **Configurações de segurança** , execute etapas abaixo.

    ![Configurar o logon único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_005.png)

    a. Habilite **logon Social**.

    b. Abrir o certificado de codificação Base64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo à caixa de texto **OpenSSL certificado** .

    c. Na caixa de texto SAM SSO URL, digite`https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
    
    d. Na caixa de texto URL de SSO SAM, substitua `<your subdomain>` com seu subdomínio.

    e. Na caixa de texto URL de SSO SAM, substitua `<saml sso url>` com SAML SSO URL que você copiou do Azure portal clássico.

    f. Clique em **Salvar configurações**.

9. No portal do clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.
    
    ![Azure AD Single Sign-On][10]

10. Na página **confirmação de logon única** , clique em **Concluir**.  
    
    ![Azure AD Single Sign-On][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico chamado Britta Simon.

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **Portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_09.png)

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_04.png)

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_05.png)

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_07.png)

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-a-deputy-test-user"></a>Criar um usuário de teste de representante

Para permitir que usuários do Azure AD efetuar login no representante, ele devem ser provisionados para o representante. No caso de substituto, provisionamento é uma tarefa manual.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Para configurar uma conta de usuário, execute as seguintes etapas:

1.  Faça logon em seu site de empresa representante como administrador.

2.  No painel de navegação superior, clique em **pessoas**.

    ![Pessoas] (./media/active-directory-saas-deputy-tutorial/tutorial_deputy_001.png "Pessoas")

3.  Clique no botão **Adicionar pessoas** e clique em **Adicionar uma única pessoa**.

    ![Adicionar pessoas] (./media/active-directory-saas-deputy-tutorial/tutorial_deputy_002.png "Adicionar pessoas")

4.  Execute as seguintes etapas e clique em **Salvar e convidar**.

    ![Novo usuário] (./media/active-directory-saas-deputy-tutorial/tutorial_deputy_003.png "Novo usuário")

    a. Na caixa de texto **nome** , digite **Britta** e **Simon**.  

    b. Na caixa de texto **Email** , digite o endereço de email de uma conta do Azure AD que deseja provisionar.

    c. Na caixa de texto **trabalho em** , digite o nome de bussniess.

    d. Clique em botão **Salvar & convidar** .

    >[AZURE.NOTE]O proprietário de conta AAD será recebe um email e siga um link para confirmar sua conta antes que ele fique ativo. Você pode usar as ferramentas de criação de outros representante usuário conta ou APIs fornecida pelo representante para provisionar AAD contas de usuário.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso ao representante.
    
![Atribuir usuário][200]

**Para atribuir Britta Simon ao representante, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.
    
    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **representante**.
    
    ![Configurar o logon único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_50.png)

3. No menu na parte superior, clique em **usuários**.
    
    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.
    
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.
 
Quando você clica no bloco do representante no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de representante.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_205.png
