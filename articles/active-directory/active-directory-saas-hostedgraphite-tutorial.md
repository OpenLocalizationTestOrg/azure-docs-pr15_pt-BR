<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com hospedado grafite | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e grafite hospedado."
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
    ms.date="10/18/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Tutorial: Integração com o Active Directory do Azure com hospedado grafite

O objetivo deste tutorial é mostram como integrar grafite hospedado com o Azure Active Directory (AD Azure).

Integração grafite hospedado com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao hospedado grafite
- Você pode habilitar os usuários para automaticamente obter assinado no hospedado grafite (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com grafite hospedado, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único grafite hospedado na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando hospedado grafite da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-hosted-graphite-from-the-gallery"></a>Adicionando hospedado grafite da Galeria
Para configurar a integração de grafite hospedado no Azure AD, você precisa adicionar hospedado grafite da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar hospedado grafite da galeria, execute as seguintes etapas:**

1. No **Portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.
    
    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.
    
    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Grafite hospedado**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_01.png)

7. No painel de resultados, selecione **Grafite hospedado**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Selecionando o aplicativo na Galeria](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar logon único Azure AD com grafite hospedado com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no grafite hospedado para um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em hospedado grafite precisa ser estabelecida.

Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no grafite hospedado.

Para configurar e testar o Azure AD logon único com grafite hospedado, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
3. **[Criando um grafite hospedado testar usuário](#creating-a-hosted-graphite-test-user)** - ter um representante de Britta Simon em grafite hospedado que esteja vinculado à representação Azure AD dela.
4. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD

Nesta seção, você habilitar Azure AD logon único no portal do clássico e configurar o logon único em seu aplicativo hospedado grafite.

**Para configurar o logon único Azure AD com grafite hospedado, execute as seguintes etapas:**

1. No portal do clássico, na página de integração do aplicativo **Hospedado grafite** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o logon único][6] 

2. Na página **como você gostaria que usuários para entrar grafite hospedado** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.
    
    ![Configurar o logon único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_03.png)

3. Na página de diálogo **Definir configurações de aplicativo** , se desejar configurar o aplicativo no **IDP iniciada modo**, execute as seguintes etapas e clique em **Avançar**:

    ![Configurar o logon único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_04.png)

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:`https://www.hostedgraphite.com/metadata/<user id>`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://www.hostedgraphite.com/complete/saml/<user id>`

    c. Clique em **Avançar**

4. Se você desejar configurar o aplicativo no **SP iniciada modo** na página de diálogo **Definir configurações de aplicativo** , em seguida, clique em **"Mostrar configurações (opcionais) avançadas"** e digite a **Entrada na URL** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_10.png)

    a. Na caixa de texto de **Entrada na URL** , digite uma URL usando o seguinte padrão:`https://www.hostedgraphite.com/login/saml/<user id>/`

    b. Clique em **Avançar**

    > [AZURE.NOTE] Observe que esses não são os valores reais. Você precisa atualizar esses valores com o sinal na URL real, identificador e URL de resposta. Para acessar esses valores, você pode ir para o Access -> Configuração SAML no seu lado do aplicativo ou entre em contato grafite hospedado.

5. Na página **Configurar logon único em grafite hospedado** , execute as seguintes etapas e clique em **Avançar**:

    ![Configurar o logon único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_05.png)

    a. Clique em **baixar o certificado**e, em seguida, salve o arquivo em seu computador.

    b. Clique em **Avançar**.

6. Logon em seu locatário grafite hospedado como um administrador.

7. Vá para a **página de configuração de SAML** na barra lateral (**Access -> Configuração de SAML**).

    ![Configure lado Single Sign-On no aplicativo](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

8. Confirme que essas URls correspondem a configuração na etapa 3.

    ![Configure lado Single Sign-On no aplicativo](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

9. Copiar **SAML SSO URLs** e **emissor** do Azure AD para **entidade ou ID do emissor** e **URL de Login SSO** no grafite hospedado.

    ![Configure lado Single Sign-On no aplicativo](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

    ![Configure lado Single Sign-On no aplicativo](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_003.png)

9. Selecione "**somente leitura**" como **padrão a função de usuário**.

    ![Configure lado Single Sign-On no aplicativo](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

10. Copie o conteúdo do arquivo baixado certificado e, em seguida, cole a caixa de texto de **Certificado x. 509** .

     ![Configure lado Single Sign-On no aplicativo](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

11. Clique em botão **Salvar** .

12. No portal do clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.
    
    ![Azure AD Single Sign-On][10]

13. Na página **confirmação de logon única** , clique em **Concluir**.  
    
    ![Azure AD Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico chamado Britta Simon.

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **Portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_09.png)

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_04.png)

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_05.png)

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_07.png)

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-a-hosted-graphite-test-user"></a>Criar um usuário de teste hospedado grafite

O objetivo desta seção é criar um usuário chamado Britta Simon no grafite hospedado. Grafite hospedado suporta em vez de provisionamento, que é por padrão ativado.

Não há nenhum item de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar hospedado grafite se ele ainda não existe.

> [AZURE.NOTE] Se você precisar criar um usuário manualmente, você precise contatar a equipe de suporte de grafite hospedado via <mailto:help@hostedgraphite.com>.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso aos grafite hospedado.
    
![Atribuir usuário][200]

**Para atribuir Britta Simon a grafite hospedado, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.
    
    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Grafite hospedado**.
    
    ![Configurar o logon único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_50.png)

1. No menu na parte superior, clique em **usuários**.
    
    ![Atribuir usuário][203]

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.
    
    ![Atribuir usuário][205]



### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.
 
Quando você clica no bloco grafite hospedado no painel de acesso, que deve receber automaticamente conectado no seu aplicativo hospedado grafite.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_205.png
