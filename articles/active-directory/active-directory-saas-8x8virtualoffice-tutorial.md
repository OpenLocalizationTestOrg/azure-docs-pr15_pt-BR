<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com 8 x 8 Virtual Office | Microsoft Azure"
    description="Saiba como configurar o logon único entre Azure Active diretório e 8 x 8 Virtual do Office."
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
    ms.date="10/10/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Tutorial: Integração com o Active Directory do Azure com o Office de Virtual de 8 x 8

O objetivo deste tutorial é mostram como integrar 8 x 8 Virtual Office com o Azure Active Directory (AD Azure).

Integração de 8 x 8 Virtual Office com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Office de Virtual de 8 x 8
- Você pode habilitar os usuários para automaticamente obter conectado em 8 x 8 Virtual Office (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com 8 x 8 Virtual Office, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um 8 x 8 Office Virtual logon único na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar Microsoft Azure AD Single Sign-On em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando 8 x 8 Virtual Office da Galeria
2. Configurando e testando Microsoft Azure AD Single Sign-On


## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Adicionando 8 x 8 Virtual Office da Galeria
Para configurar a integração do Office de Virtual de 8 x 8 no Azure AD, você precisa adicionar 8 x 8 Virtual Office da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar 8 x 8 Virtual Office da galeria, execute as seguintes etapas:**

1. No **Portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.
    
    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **8 x 8 Virtual Office**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_01.png)
7. No painel de resultados, selecione **8 x 8 Virtual Office**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Selecionando o aplicativo na Galeria](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_0001.png)


##  <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>Configurando e testando Microsoft Azure AD Single Sign-On
O objetivo desta seção é mostram como configurar e testar o Microsoft Azure AD Single Sign-On com 8 x 8 que virtual do Office com base em um usuário de teste chamado "Britta Simon".

Para logon único trabalhar, Azure AD precisa saber que o usuário de um correspondente para o Office Virtual para um usuário no Azure AD é 8 x 8. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em 8 x 8 Virtual do Office precisa ser estabelecida.

Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no Office de Virtual de 8 x 8.

Para configurar e testar o Microsoft Azure AD Single Sign-On com 8 x 8 Virtual Office, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Microsoft Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Microsoft Azure AD Single Sign-On com Britta Simon.
3. **[Criar um usuário de teste do Office Virtual de 8 x 8](#creating-a-8x8-virtual-office-test-user)** - ter um representante de Britta Simon em 8 x 8 Virtual Office que esteja vinculado à representação Azure AD dela.
4. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar Microsoft Azure AD Single Sign-On.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>Configurando Microsoft Azure AD Single Sign-On

Nesta seção, você habilitar o Microsoft Azure AD Single Sign-On no portal do clássico e configurar o logon único no aplicativo do Office Virtual de 8 x 8.

**Para configurar o Microsoft Azure AD Single Sign-On com 8 x 8 Virtual Office, execute as seguintes etapas:**

1. No portal do clássico, na página de integração de aplicativo de **8 x 8 Virtual Office** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o logon único][6] 

2. Na página **como deseja usuários para entrar para 8 x 8 Virtual Office** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_03.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas e clique em **Avançar**:

    ![Configurar o logon único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_04.png)

    a. Na caixa de texto **URL de resposta** , digite:`https://sso.8x8.com/saml2`

    b. Clique em **Avançar**

4. Na página **Configurar o logon único em 8 x 8 Virtual Office** , execute as seguintes etapas e clique em **Avançar**:

    ![Configurar o logon único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_05.png)

    a. Clique em **baixar o certificado**e, em seguida, salve o arquivo em seu computador.

    b. Clique em **Avançar**.

5. Logon em seu locatário do Office Virtual 8 x 8 como administrador.
6. Selecione o **gerente de conta do Office Virtual** no painel de aplicativo.

    ![Configurar no lado do aplicativo](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

7. Selecione conta de **negócios** para gerenciar e clique no botão **Entrar** .

    ![Configurar no lado do aplicativo](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

8. Clique em Guia de **contas** na lista do menu.

    ![Configurar no lado do aplicativo](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

9. Clique em **Logon único** na lista de contas.

    ![Configurar no lado do aplicativo](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

10. Selecione **Signle logon** em método de autenticação e clique **SAML**.

    ![Configurar no lado do aplicativo](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

11. Copiar **SAML SSO**, **Single brilhar Check-Out de URL do serviço** **URLs** e do emissor do Azure AD para **entrar no URL**, **saia de URL** e **emissor URL** no Office Virtual 8 x 8. 

    ![Configurar no lado do aplicativo](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    ![Configurar no lado do aplicativo](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_007.png)

12. Clique no botão **navegador** para carregar o certificado que você baixou do Azure AD.

13. Clique no botão **Salvar** .

14. No portal do clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.

    ![Azure AD Single Sign-On][10]

15. Na página **confirmação de logon única** , clique em **Concluir**.  

    ![Azure AD Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico chamado Britta Simon.
    
![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **Portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_09.png)

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_04.png)

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_05.png)

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_07.png)

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-a-8x8-virtual-office-test-user"></a>Criar um usuário de teste do Office Virtual de 8 x 8

O objetivo desta seção é criar um usuário chamado Britta Simon no 8 x 8 Virtual Office. 8 x 8 Virtual Office oferece suporte em vez de provisionamento, que é por padrão ativado.

Não há nenhum item de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar 8 x 8 Virtual Office se ele ainda não existe. 

> [AZURE.NOTE] Se você precisar criar um usuário manualmente, você precise contatar a equipe de suporte do Office Virtual de 8 x 8.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso 8 x 8 Virtual Office.
    
![Atribuir usuário][200]

**Para atribuir Britta Simon 8 x 8 Virtual Office, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **8 x 8 Virtual Office**.

    ![Configurar o logon único](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_50.png)

3. No menu na parte superior, clique em **usuários**.
    
    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]



### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Microsoft Azure AD Single Sign-On configuração usando o painel de acesso.

Quando você clica no bloco do Office Virtual de 8 x 8 no painel de acesso, você deve obter automaticamente assinado no seu aplicativo do Office Virtual 8 x 8.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_205.png
