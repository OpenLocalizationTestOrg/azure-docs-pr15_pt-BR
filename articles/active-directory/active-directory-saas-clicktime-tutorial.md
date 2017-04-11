<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com ClickTime | Microsoft Azure" 
    description="Saiba como usar ClickTime com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"
    documentationCenter="na" 
    manager="femila" />
<tags
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Tutorial: Integração com o Active Directory do Azure com ClickTime

Neste tutorial, você aprenderá a integrar ClickTime com o Azure Active Directory (AD Azure).

Integração ClickTime com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao ClickTime
- Você pode habilitar os usuários para automaticamente obter assinado no ClickTime (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ClickTime, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único ClickTime na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testar Azure AD logon único em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando ClickTime da Galeria
2. Configurando e testando Azure AD logon único

##<a name="adding-clicktime-from-the-gallery"></a>Adicionando ClickTime da Galeria

O objetivo desta seção é como habilitar a integração de aplicativo para ClickTime da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-clicktime-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para ClickTime, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-clicktime-tutorial/tic700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-clicktime-tutorial/tic700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-clicktime-tutorial/tic749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-clicktime-tutorial/tic749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **ClickTime**.

    ![Galeria de aplicativo] (./media/active-directory-saas-clicktime-tutorial/tic777275.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **ClickTime**e clique em **concluído** para adicionar o aplicativo.

    ![ClickTime] (./media/active-directory-saas-clicktime-tutorial/tic777276.png "ClickTime")

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
Nesta seção, configurar e testar logon único Azure AD com ClickTime com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no ClickTime a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em ClickTime precisa ser estabelecida.

Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no ClickTime.

Para configurar e testar o Azure AD logon único com ClickTime, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
3. **[Criando um ClickTime testar usuário](#creating-a-clicktime-test-user)** - ter um representante de Britta Simon em ClickTime que esteja vinculado à representação Azure AD dela.
4. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.


### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD

O objetivo desta seção é como habilitar usuários autenticar ClickTime com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  


>[AZURE.IMPORTANT] Para poder configurar o logon único em seu locatário ClickTime, é necessário primeiro contatar o suporte técnico ClickTime para acessar esse recurso ativado.

**Para configurar o logon único Azure AD com ClickTime, execute as seguintes etapas:**

1.  No portal do clássico Azure, na página de integração do aplicativo **ClickTime** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Habilitar o logon único] (./media/active-directory-saas-clicktime-tutorial/tic777277.png "Habilitar o logon único")

2.  Na página **como você gostaria que os usuários para assinar ClickTime** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-clicktime-tutorial/tic777278.png "Configurar o logon único")

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-clicktime-tutorial/tic777286.png) 

    a. Na caixa de texto **IdentifierL** , digite a URL usando o seguinte padrão: **https://app.clicktime.com/sp/**
    
    b. Na caixa de texto **URL de resposta** , digite a URL usando o seguinte padrão: **https://app.clicktime.com/Login/**

    c. Clique em **Avançar**

4.  Na página **Configurar logon único em ClickTime** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-clicktime-tutorial/tic777279.png "Configurar o logon único")

4.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa ClickTime como administrador.

5.  Na barra de ferramentas na parte superior, clique em **Preferências**e, em seguida, clique em **Configurações de segurança**.

6.  Na seção de configuração de **Preferências de logon único** , execute as seguintes etapas:

    ![Configurações de segurança] (./media/active-directory-saas-clicktime-tutorial/tic777280.png "Configurações de segurança")

    a.  Selecione **Permitir** entrar usando Single Sign-On (SSO) com o **Azure AD**.
    
    b.  No portal do clássico Azure, na página de diálogo **Configurar logon único em ClickTime** , copie o valor de **Single Sign-On URL do serviço** e, em seguida, cole a caixa de texto de **Ponto de extremidade de provedor de identidade** .

    c.  Abra o certificado de codificada de base 64 no **Bloco de notas**, copie o conteúdo e, em seguida, cole a caixa de texto de **Certificado x. 509** .
    
    d.  Clique em **Salvar**.

7.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-clicktime-tutorial/tic777281.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login ClickTime, ele devem ser provisionados em ClickTime.  
No caso de ClickTime, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **ClickTime** .

2.  Na barra de ferramentas na parte superior, clique em **empresa**e, em seguida, clique em **pessoas**.

    ![Pessoas] (./media/active-directory-saas-clicktime-tutorial/tic777282.png "Pessoas")

3.  Clique em **Adicionar pessoa**.

    ![Adicionar pessoa] (./media/active-directory-saas-clicktime-tutorial/tic777283.png "Adicionar pessoa")

4.  Na seção nova pessoa, execute as seguintes etapas:

    ![Pessoas] (./media/active-directory-saas-clicktime-tutorial/tic777284.png "Pessoas")

    a.  Na caixa de texto **endereço de email** , digite o endereço de email da sua conta do Azure AD.
    
    b.  Na caixa de texto **nome completo** , digite o nome da sua conta do Azure AD.  

    >[AZURE.NOTE] Se desejar, você pode definir propriedades adicionais do novo objeto pessoa.

    c.  Clique em **Salvar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros ClickTime usuário conta ou APIs fornecidas pela ClickTime para provisionar contas de usuário do Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

Nesta seção, você habilitar Britta Simon usar logon único Azure concedendo o acesso aos ClickTime.

![Atribuir usuário][200]

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

**Para atribuir Britta Simon a ClickTime, execute as seguintes etapas**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **ClickTime**.

    ![Configurar o logon único](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_50.png) 

3. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]

## <a name="testing-single-sign-on"></a>Teste de logon único
Nesta seção, você testar seu Azure AD único logon configuração usando o painel de acesso.

Quando você clica no bloco de ClickTime no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de ClickTime.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_205.png