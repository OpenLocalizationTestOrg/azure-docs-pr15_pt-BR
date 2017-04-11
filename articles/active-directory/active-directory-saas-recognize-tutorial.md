<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com reconhecer | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e reconhecer."
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
    ms.date="10/27/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Tutorial: Integração com o Active Directory do Azure com reconhecer

O objetivo deste tutorial é mostram como integrar reconhecer com o Azure Active Directory (AD Azure).

Integrar reconhecer o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao reconhecer
- Você pode habilitar os usuários para automaticamente obter conectado em reconhecer (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com reconhecer, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único reconhecer na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando reconhecer da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-recognize-from-the-gallery"></a>Adicionando reconhecer da Galeria
Para configurar a integração de reconhecer no Azure AD, você precisa adicionar reconhecer da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar reconhecer da galeria, execute as seguintes etapas:**

1. No **Portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.
    
    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.
    
    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **reconhecer**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_01.png)

7. No painel de resultados, selecione **reconhecer**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Selecionando o aplicativo na Galeria](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar logon único Azure AD com reconhecer com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente em reconhecer um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em reconhecer precisa ser estabelecida.

Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** em reconhecer.

Para configurar e testar o Azure AD logon único com reconhecer, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
3. **[Criando um reconhecer testar usuário](#creating-a-recognize-test-user)** - ter um representante de Britta Simon em reconhecer que esteja vinculado à representação Azure AD dela.
4. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD

Nesta seção, você habilitar Azure AD logon único no portal do clássico e configurar o logon único em seu aplicativo de reconhecer.

**Para configurar o logon único Azure AD com reconhecer, execute as seguintes etapas:**

1. No portal do clássico, na página de integração de aplicativo de **reconhecer** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para assinar reconhecer** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.
    
    ![Configurar o logon único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_03.png)

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas e clique em **Avançar**:

    ![Configurar o logon único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_04.png)

    a. Na caixa de texto de **Entrada na URL** , digite uma URL usando o seguinte padrão: `https://recognizeapp.com/<your-domain>/saml/sso`.

    b. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://recognizeapp.com/<your-domain>/saml/metadata`.

    c. Clique em **Avançar**

    > [AZURE.NOTE] Se você não sabe sobre esses URLs, digite as URLs de amostra com padrão de exemplo. Para acessar esses valores, você pode se referir a etapa 9 para obter mais detalhes ou entre em contato com a equipe de suporte de reconhecer via <mailto:support@recognizeapp.com>.

4. Na página **Configurar logon único em reconhecer** , clique em **baixar o certificado** e, em seguida, salve o arquivo em seu computador:

    ![Configurar o logon único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_05.png)

5. Em uma janela de navegador da web diferente, logon em seu locatário reconhecer como um administrador.

6. No canto superior direito, clique no **Menu**. Vá para **administrador da empresa**.

    ![Configure lado Single Sign-On no aplicativo](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)

7. No painel de navegação esquerdo, clique em **configurações**.

    ![Configure lado Single Sign-On no aplicativo](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)

8. Execute as seguintes etapas na seção **Configurações de SSO** .

    ![Configure lado Single Sign-On no aplicativo](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)

    a. Como **Habilitar o SSO**, selecione **Ativar**.

    b. A **ID de entidade IDP** texto coloca o valor da **URL do emissor** do Assistente de configuração de aplicativo do Azure AD.

    c. Na **url de destino de Sso** texto coloca o valor de **Single Sign-On URL do serviço** do Assistente de configuração de aplicativo do Azure AD.

    d. Na **url de destino de Slo** texto coloca o valor de **URL do serviço Sign-Out único** do Assistente de configuração de aplicativo do Azure AD.

    e. Abra o arquivo de certificado baixado no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo para a caixa de texto de **certificado** . 

    f. Clique no botão **Salvar configurações** . 

9. Ao lado da seção **Configurações de SSO** , copie a URL em **url de metadados de provedor de serviço**.
    
    ![Configure lado Single Sign-On no aplicativo](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)

10. Abra o **link de URL de metadados** em um navegador em branco para baixar o documento de metadados. Use o valor de EntityDescriptor que reconhecer fornecido para **identificador** na caixa de diálogo **Definir configurações de aplicativo** .

    ![Configure lado Single Sign-On no aplicativo](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)

11. No portal do clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.
    
    ![Azure AD Single Sign-On][10]

12. Na página **confirmação de logon única** , clique em **Concluir**.  
    
    ![Azure AD Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico chamado Britta Simon.

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **Portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_09.png)

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png)

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_05.png)

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa de texto **Sobrenome** , digite **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_07.png)

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-a-recognize-test-user"></a>Criar um usuário de teste reconhecer

Para permitir que usuários do Azure AD fazer login reconhecer, ele devem ser provisionados para reconhecer. No caso de reconhecer, provisionamento é uma tarefa manual.

Este aplicativo não dá suporte SCIM de provisionamento, mas tem uma sincronização de usuário alternativas que provisiona usuários. 

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Para configurar uma conta de usuário, execute as seguintes etapas:

1.  Faça logon em seu site de empresa reconhecer como administrador.

2.  No canto superior direito, clique no **Menu**. Vá para **administrador da empresa**.

3.  No painel de navegação esquerdo, clique em **configurações**.

4.  Execute as seguintes etapas na seção de **Sincronização do usuário** .
    
    ![Novo usuário] (./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "Novo usuário")

    a. Como **Sincronizar habilitado**, selecione **ativado**.

    b. Como **provedor de sincronização de escolha**, selecione **Microsoft / Office 365**.

    c. Clique em **executar a sincronização de usuário**

### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso a reconhecer.
    
![Atribuir usuário][200]

**Para atribuir Britta Simon a reconhecer, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.
    
    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **reconhecer**.
    
    ![Configurar o logon único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_50.png)

3. No menu na parte superior, clique em **usuários**.
    
    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.
    
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.
 
Quando você clica no bloco de reconhecer no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de reconhecer.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_205.png
