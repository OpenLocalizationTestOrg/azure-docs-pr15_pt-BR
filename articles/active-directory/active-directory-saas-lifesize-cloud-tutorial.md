<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Lifesize nuvem | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e nuvem de Lifesize."
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
    ms.date="10/04/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Tutorial: Integração com o Active Directory do Azure com Lifesize nuvem

Neste tutorial, você aprenderá a integrar nuvem Lifesize com o Azure Active Directory (AD Azure).

Integração Lifesize nuvem com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso à nuvem Lifesize
- Você pode habilitar os usuários para automaticamente obter conectado em nuvem Lifesize (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Lifesize nuvem, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único Lifesize nuvem na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testar Azure AD logon único em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Lifesize nuvem da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-lifesize-cloud-from-the-gallery"></a>Adicionando Lifesize nuvem da Galeria
Para configurar a integração de nuvem Lifesize no Azure AD, você precisa adicionar nuvem Lifesize da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar Lifesize nuvem da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory][1]
2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Lifesize nuvem**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_01.png)

7. No painel de resultados, selecione **Lifesize nuvem**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
Nesta seção, configure e teste Azure AD SSO com Lifesize nuvem com base em um usuário de teste chamado "Britta Simon".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente na nuvem Lifesize a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na nuvem Lifesize precisa ser estabelecida.

Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** na nuvem Lifesize.

Para configurar e testar o Azure AD logon único com Lifesize nuvem, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
3. **[Criando uma nuvem Lifesize testar usuário](#creating-a-lifesize-cloud-test-user)** - ter um representante de Britta Simon em nuvem de Lifesize que esteja vinculado à representação Azure AD dela.
4. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD

Nesta seção, você habilitar Azure AD logon único no portal do clássico e configurar o logon único em seu aplicativo de nuvem Lifesize.


**Para configurar o logon único Azure AD com Lifesize nuvem, execute as seguintes etapas:**

1. No portal do clássico, na página de integração de aplicativo de **Nuvem Lifesize** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o logon único][6] 

2. Na página **como você gostaria que usuários para entrar nuvem Lifesize** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_03.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_04.png) 

    a. Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para logon para seu aplicativo de nuvem Lifesize usando o seguinte padrão: **https://login.lifesizecloud.com/ls/?acs**.
    
    b. Clique em **Avançar**
 
4. Na página **Configurar logon único em nuvem Lifesize** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_05.png)

    a. Clique em **baixar o certificado**e, em seguida, salve o arquivo em seu computador.

    b. Clique em **Avançar**.


5. Para obter o SSO configurado para seu aplicativo, faça logon no aplicativo Lifesize nuvem com privilégios de administrador.

6. No canto superior direito clique no seu nome e clique nas **Configurações de avanço**

    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

7. Em configurações de Advance Agora clique no link de **Configuração de SSO** . Isso abrirá a página de configuração de SSO para sua instância.

    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

8. Agora, configure os seguintes valores na configuração SSO interface do usuário.    

    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    • Copie o valor da URL do emissor do Azure AD e cole na caixa de texto do **Emissor do provedor de identidade** .

    • Copie o valor de URL de Login remoto do Azure AD e cole na caixa de texto **URL de Login** .

    • Abra o certificado baixado no bloco de notas e copie o conteúdo do certificado, excluindo as linhas começar Certificate e End Certificate, cole isso na caixa de texto **Certificado x. 509** .

    • No mapeamento de atributo SAML para a caixa de texto **nome** digite o valor como **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**

    • No mapeamento de atributo SAML para a caixa de texto **Sobrenome** insira o valor como **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**

    • No mapeamento de atributo SAML para a caixa de texto de **Email** digite o valor como **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

9. Para verificar a configuração, você pode clicar no botão de **teste** .

    > [AZURE.NOTE] Para testar bem-sucedido, você precisa concluir o Assistente de configuração do Azure AD e também fornecem acesso a usuários ou grupos que podem realizar o teste.
    
10. Habilite o SSO marcando no botão **Habilitar SSO** .

11. Agora, clique no botão de **atualização** para que todas as configurações sejam salvas. Isso irá gerar o valor de RelayState. Copie o valor de RelayState que é gerado na caixa de texto. Precisaremos esse valor nas próximas etapas.

12. No portal do clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.
    
    ![Azure AD Single Sign-On][10]

13. Na página **confirmação de logon única** , clique em **Concluir**.  
 
    ![Azure AD Single Sign-On][11]

14. Agora login para o Portal de gerenciamento do Azure **https://portal.azure.com** usando as credenciais de administrador

15. Clique no link **Mais serviços** no painel de navegação à esquerda
    
    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_09.png)

16. Pesquisar Active Directory do Azure e clique no Link **Active Directory do Azure**
    
    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_10.png)

17. Você encontrará todos os seus aplicativos SaaS sob o botão de **Aplicativos corporativos** .

    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_11.png)

18. Agora, clique no link de **Todos os aplicativos** na próxima lâmina
    
    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_12.png)

19. Procurar Lifesize aplicativo do qual você deseja configurar o RelayState. 
    
    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_13.png)

20. Agora, clique em link de **logon único** na lâmina

    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_14.png)

21. Você verá a caixa de seleção **Mostrar URL configurações avançadas** . Clique na caixa de seleção.
    
    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_15.png)
    
22. Agora, configure o RelayState para o aplicativo, o que você vê na página de configuração do Lifesize aplicativo SSO. 

    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_16.png)

23. Salve as configurações.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
Nesta seção, você criar um usuário de teste no portal do clássico chamado Britta Simon.


![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:  ![criação de um usuário de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas: ![criação de um usuário de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-an-lifesize-cloud-test-user"></a>Criação de um usuário de teste de nuvem Lifesize

Nesta seção, você criar um usuário chamado Britta Simon na nuvem Lifesize. Nuvem de Lifesize suporta provisionamento automático de usuário. Após a autenticação bem-sucedida no Azure AD, o usuário será configurado no aplicativo automaticamente. 


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

Nesta seção, você habilitar Britta Simon usar logon único Azure concedendo o acesso Lifesize nuvem.

![Atribuir usuário][200] 

**Para atribuir Britta Simon Lifesize nuvem, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Lifesize nuvem**.

    ![Configurar o logon único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_50.png) 

3. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]


### <a name="testing-single-sign-on"></a>Teste de logon único

Nesta seção, você testar seu Azure AD único logon configuração usando o painel de acesso.

Quando você clica no bloco de nuvem Lifesize no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de nuvem Lifesize.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_205.png
