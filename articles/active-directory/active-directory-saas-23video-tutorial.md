<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com vídeo 23 | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e 23 de vídeo."
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
    ms.date="10/24/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-23-video"></a>Tutorial: Integração com o Active Directory do Azure com vídeo 23

O objetivo deste tutorial é mostram como integrar 23 de vídeo com o Azure Active Directory (AD Azure).  
Integração 23 vídeo com o Azure AD fornece os seguintes benefícios: 

- Você pode controlar no Azure AD quem tem acesso ao vídeo 23 
- Você pode habilitar os usuários para automaticamente obter entrou no vídeo 23 (Single Sign-On) com suas contas do Azure AD

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos 

Para configurar a integração do Azure AD com vídeo 23, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um 23 vídeo logon único na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar vídeo 23 da Galeria 
2. Configurando e testando Azure AD logon único


## <a name="adding-23-video-from-the-gallery"></a>Adicionar vídeo 23 da Galeria
Para configurar a integração de vídeo 23 no Azure AD, você precisa adicionar vídeo 23 da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar vídeo 23 da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **23 de vídeo**.

    ![Aplicativos][5]

7. No painel de resultados, selecione **vídeo 23**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Aplicativos][25]

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar logon único Azure AD com vídeo 23 com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário de um correspondente para o vídeo 23 para um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em 23 vídeo precisa ser estabelecida.  
Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no vídeo 23.
 
Para configurar e testar o Azure AD logon único com vídeo 23, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um vídeo 23 testar usuário](#creating-a-23-video-test-user)** - a com um representante de Britta Simon 23 vídeo que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando Azure AD Single Sign-On

O objetivo desta seção é habilitar Azure AD logon único no portal de clássico do Azure e configurar o logon único em seu aplicativo de vídeo 23.

**Para configurar o logon único Azure AD com vídeo 23, execute as seguintes etapas:**

1. No portal do clássico Azure, na página de integração de aplicativo de **23 de vídeo** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][6] 

2. Na página **como você gostaria que usuários para entrar no vídeo 23** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Azure AD Single Sign-On][7] 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Azure AD Single Sign-On][8] 
 
     a. Na caixa de texto **URL de resposta** , digite a URL usada pelos usuários para logon ao seu site de vídeo 23 (por exemplo: *https://britta-simon.23Video.com/saml/login*).

     > [AZURE.NOTE] Integração do Active Directory usando SAML 2.0 está disponível para todos os usuários de vídeo 23. Entre em contato com o suporte ao [support@23company.com](mailto:support@23company.com) se você precisar os metadados relacionados.

     b. Clique em **Avançar**.
 
4. Na página **Configurar logon único vídeo 23** , execute as seguintes etapas:

    ![Azure AD Single Sign-On][9] 

    a. Clique em certificado de Download e salve o arquivo em seu computador.

    b. Entre em contato com sua equipe de suporte de vídeo 23 via [support@23company.com](mailto:support@23company.com), forneça a ele o certificado baixado, a **URL do emissor**, **Single Sign-On URL do serviço**, a **Única Sign-Out URL**e, em seguida, peça para configurar o SSO para seu aplicativo de vídeo 23. 

    c. Clique em **Avançar**.


6. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**. 

    ![Azure AD Single Sign-On][10]

7. Na página **confirmação de logon única** , clique em **Concluir**.  
  
    ![Azure AD Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico Azure chamado Britta Simon.

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_09.png)  

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_03.png) 
 
4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**. 

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas: 

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_05.png)  

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas: 

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_06.png) 
 
    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.
    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_07.png) 
 
8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_08.png) 
  
    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   

  
 
### <a name="creating-a-23-video-test-user"></a>Criar um usuário de teste de vídeo 23

O objetivo desta seção é criar um usuário chamado Britta Simon 23 de vídeo.

**Para criar um usuário chamado Britta Simon 23 de vídeo, execute as seguintes etapas:**

1. Entrar seu site de empresa de vídeo 23 como administrador.

1. Vá para **configurações**.


2. Na seção de **usuários** , clique em **Configurar**.

    ![Atribuir usuário][400]

3. Clique em **Adicionar um novo usuário**. 

    ![Atribuir usuário][401]

4. Na seção **Convidar alguém para ingressar neste site** , execute as seguintes etapas:

    ![Atribuir usuário][402]

    a. Na caixa de texto **endereços de email** , digite o endereço de email de Britta Simon no Azure AD.

    b. Clique em **Adicionar usuário**.   


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso a 23 de vídeo.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a 23 de vídeo, execute as seguintes etapas:**

1. No portal do Azure clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **vídeo 23**.

    ![Atribuir usuário][202] 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]



### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.  
Quando você clica no bloco 23 de vídeo no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de vídeo 23.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-23video-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-23video-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-23video-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-23video-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_01.png
[25]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_02.png

[6]: ./media/active-directory-saas-23video-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_03.png
[8]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_04.png
[9]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_05.png
[10]: ./media/active-directory-saas-23video-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-23video-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-23video-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-23video-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-23video-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_07.png
[203]: ./media/active-directory-saas-23video-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-23video-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-23video-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_10.png
[401]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_11.png
[402]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_12.png




