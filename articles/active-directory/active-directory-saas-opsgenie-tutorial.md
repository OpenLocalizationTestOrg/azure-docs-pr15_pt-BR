<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com OpsGenie | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e OpsGenie."
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
    ms.date="10/07/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Tutorial: Integração com o Active Directory do Azure com OpsGenie

O objetivo deste tutorial é mostram como integrar OpsGenie com o Azure Active Directory (AD Azure).

Integração OpsGenie com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao OpsGenie
- Você pode habilitar os usuários para automaticamente obter assinado no OpsGenie (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com OpsGenie, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um OpsGenie logon único habilitado para assinatura


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando OpsGenie da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-opsgenie-from-the-gallery"></a>Adicionando OpsGenie da Galeria
Para configurar a integração do OpsGenie no Azure AD, você precisa adicionar OpsGenie da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar OpsGenie da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **OpsGenie**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_01.png)

7. No painel de resultados, selecione **OpsGenie**e clique em **concluído** para adicionar o aplicativo.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar logon único Azure AD com OpsGenie com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber o usuário correspondente no OpsGenie a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em OpsGenie precisa ser estabelecida.

Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no OpsGenie.

Para configurar e testar o Azure AD logon único com OpsGenie, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um OpsGenie testar usuário](#creating-a-opsgenie-test-user)** - ter um representante de Britta Simon em OpsGenie que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD

O objetivo desta seção é habilitar Azure AD logon único no portal do Azure clássico e configurar o logon único em seu aplicativo de OpsGenie.



**Para configurar o logon único Azure AD com OpsGenie, execute as seguintes etapas:**

1. No portal do clássico Azure, na página de integração do aplicativo **OpsGenie** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para assinar OpsGenie** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_03.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_04.png) 


    a. Na caixa de texto de entrada na URL, digite a URL usada pelos usuários para logon para seu aplicativo de OpsGenie usando o seguinte padrão: **"https://app.opsgenie.com/auth/login"**.

    > [AZURE.NOTE] Entre em contato com sua [equipe de suporte de OpsGenie](mailto:support@opsgenie.com) se precisar de sua entrada na URL.

    b. Clique em **Avançar**.


4. Na página **Configurar logon único em OpsGenie** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_05.png) 

    a. Clique em **Baixar o certificado**e, em seguida, salve o arquivo em seu computador. Precisaremos este certificado e URLs de metadados (ID de entidade, SSO URL de entrada e sinal de Check-Out URL) para configurar o SSO do lado OpsGenie.

    b. Clique em **Avançar**.


5. Abrir outra instância do navegador e, em seguida, faça login OpsGenie como administrador.

6. Clique em **configurações**e, em seguida, clique na guia **Logon** .
 
    ![OpsGenie Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_06.png) 

7. Para habilitar SSO, selecione **habilitado**.

    ![Configurações de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_07.png) 
   
8. Na seção de **provedor** , clique na guia do **Active Directory do Azure** .

    ![Configurações de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_08.png) 
    
9. Na página de diálogo do Azure Active Directory, execute as seguintes etapas:
 
    ![Configurações de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_09.png) 

    a. No portal do clássico Azure, na página de diálogo **Configurar logon único em OpsGenie** , copie o valor de **Entrada única na URL de serviço** e, em seguida, cole a caixa de texto de **Ponto de extremidade do SAML 2.0** .

    b. Crie um arquivo de codificada de base 64 do seu certificado baixado.      
    
    > [AZURE.NOTE] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](https://www.youtube.com/watch?v=PlgrzUZ-Y1o&feature=youtu.be).

    c. Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e cole-o na caixa de texto **X. 500 certificado** .

    d. Clique em **Salvar alterações**.


6. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.

    ![Azure AD Single Sign-On][10]

7. Na página **confirmação de logon única** , clique em **Concluir**.  

    ![Azure AD Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico Azure chamado Britta Simon.



![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **Portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-a-opsgenie-test-user"></a>Criar um usuário de teste OpsGenie

O objetivo desta seção é criar um usuário chamado Britta Simon no OpsGenie. 

1.  Na janela do navegador da web, faça logon em seu locatário OpsGenie como administrador.

2.  Navegue até a lista de usuários clicando em **usuário** no painel esquerdo.
   
    ![Configurações de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_10.png) 

3.  Clique em "**Adicionar usuário**".

3.  Na caixa de diálogo **Adicionar usuário** , execute as seguintes etapas:

    ![Configurações de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_11.png) 

    a. Na caixa de texto **Email** , digite o endereço de email de Britta no Active Directory do Azure.

    b. Na caixa de texto **Nome completo** , digite **Britta Simon**.

    c. Clique em **Salvar**. 

Britta receberão um email com instruções para configurar o seu perfil.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso aos OpsGenie.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a OpsGenie, execute as seguintes etapas:**

1. No portal do Azure clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.
 
    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **OpsGenie**.

    ![Configurar o logon único](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_50.png) 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]



### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.

Quando você clica no bloco de OpsGenie no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de OpsGenie.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_205.png
