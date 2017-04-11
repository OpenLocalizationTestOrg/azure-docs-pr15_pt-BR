<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Software de halogênio"
    description="Saiba como configurar o logon único entre Azure Active Directory e o Software de alogênio."
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


# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Tutorial: Integração com o Active Directory do Azure com Software de halogênio

O objetivo deste tutorial é mostram como integrar o Software de halogênio com o Azure Active Directory (AD Azure).

Integrar o Software de halogênio a Azure AD fornece os seguintes benefícios: 

- Você pode controlar no Azure AD quem tem acesso ao Software de halogênio 
- Você pode habilitar os usuários para automaticamente obter assinado no Software de halogênio (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos 

Para configurar a integração do Azure AD com Software de halogênio, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um Software de halogênio single sign-na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Software de halogênio da Galeria 
2. Configurando e testando Azure AD logon único


## <a name="adding-halogen-software-from-the-gallery"></a>Adicionando Software de halogênio da Galeria
Para configurar a integração do Software de halogênio no Azure AD, você precisa adicionar halogênio Software da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Software de halogênio da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página. 

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **software de alogênio**.

    ![Aplicativos][5]

7. No painel de resultados, selecione **Halogênio Software**e, em seguida, clique em **concluído** para adicionar o aplicativo.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar logon único Azure AD com Software de halogênio com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no Software de alogênio para um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Software de halogênio precisa ser estabelecida.

Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no Software de alogênio.
 
Para configurar e testar o Azure AD logon único com Software de halogênio, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD único Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um Software de halogênio testar usuário](#creating-a-halogen-software-test-user)** - ter um representante de Britta Simon no Software de halogênio que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configurando Azure AD Single Sign-On único

O objetivo desta seção é habilitar Azure AD logon único no portal de clássico do Azure e configurar o logon único em seu aplicativo de Software de alogênio.


**Para configurar o logon único Azure AD com Software de halogênio, execute as seguintes etapas:**

1. No portal do clássico Azure, na página de integração do aplicativo **Halogênio Software** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][8]

2. Na página **como você gostaria que usuários para entrar Software de halogênio** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Azure AD Single Sign-On][9]

3. Na página de diálogo **Definir configurações de aplicativo** , execute as etapas a seguir:  ![definir configurações de aplicativo][10]
 
     a. Na caixa de texto de **Entrada na URL** , digite sua URL usada pelos usuários para entrar seu aplicativo de Software de halogênio usando o seguinte padrão: *https://global.hgncloud.com/fabrikam/welcome.jsp*

     b. Clique em **Avançar**.
 
4. Na página **Configurar logon único halogênio software** , clique em **baixar metadados**e salve o arquivo de metadados localmente em seu computador.
    
    ![O que é Azure AD Connect][11]

5. Em uma janela de navegador diferente, logon para seu aplicativo de **Software de halogênio** como administrador.

6. Clique na guia **Opções** . 

    ![O que é Azure AD Connect][12]


7. No painel de navegação esquerdo, clique em **Configuração de SAML**. 

    ![O que é Azure AD Connect][13]

8. Na página **Configuração de SAML** , execute as seguintes etapas:  ![o que é Azure AD Connect][14]

    a. Como **Identificador exclusivo**, selecione **NameID**.

    b. Como **Exclusivo mapas de identificador para**, selecione **nome de usuário**.

    c. Para carregar o arquivo de metadados baixado, clique em **Procurar** para selecionar o arquivo e, em seguida, **Carregar arquivo**.

    d. Para testar a configuração, clique em **Executar testar**. 

    > [AZURE.NOTE] Você precisa esperar para a mensagem "*o SAML teste está concluída. Feche esta janela*". Em seguida, feche a janela de navegador aberta. A caixa de seleção **Habilitar SAML** é ativada somente se o teste foi concluído.

    e. Selecione **Habilitar SAML**.
    
    f. Clique em **Salvar alterações**. 


9. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** . 

    ![O que é Azure AD Connect][15]

10. Na página **confirmação de logon única** , clique em **Concluir**.  

    ![O que é Azure AD Connect][16]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico Azure chamado Britta Simon.

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![O que é Azure AD Connect][100] 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![O que é Azure AD Connect][101] 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**. 

    ![O que é Azure AD Connect][102] 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:

    ![O que é Azure AD Connect][103] 
 
    a. Como **Tipo de usuário**, selecione **novo usuário na sua organização**.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em Avançar.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas: 

    ![O que é Azure AD Connect][104] 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. No **Sobrenome** txtbox, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![O que é Azure AD Connect][105]  

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![O que é Azure AD Connect][106]   

    a. Anote o valor da **Nova senha**.
    b. Clique em **Concluir**.   
  
 
### <a name="creating-a-halogen-software-test-user"></a>Criar um usuário de teste de Software de halogênio

O objetivo desta seção é criar um usuário chamado Britta Simon no Software de alogênio.

**Para criar um usuário chamado Britta Simon no Software de halogênio, execute as seguintes etapas:**

1. Entrar seu aplicativo de **Software de halogênio** como administrador.

2. Clique na guia **Central de usuário** e, em seguida, clique em **Criar usuário**.

    ![O que é Azure AD Connect][300]  

3. Na página de diálogo **Novo usuário** , execute as seguintes etapas:

    ![O que é Azure AD Connect][301]

    a. Na caixa de texto **nome** , digite **Britta**. 
  
    b. Na caixa de texto **Sobrenome** , digite **Simon**.
  
    c. Na caixa de texto **nome de usuário** , digite o **nome do Brita Simon no portal de clássico do Azure**.
  
    d. Na caixa de texto **senha** , digite uma senha para Britta.
  
    e. Clique em **Salvar**.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso ao Software de alogênio.

![O que é Azure AD Connect][200]

**Para atribuir Britta Simon ao Software de halogênio, execute as seguintes etapas:**

1. No portal do Azure clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![O que é Azure AD Connect][201]

2. Na lista de aplicativos, selecione o **Software de alogênio**.

    ![O que é Azure AD Connect][202]

1. No menu na parte superior, clique em **usuários**.

    ![O que é Azure AD Connect][203]

1. Na lista de usuários, selecione **Britta Simon**.

    ![O que é Azure AD Connect][204]

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![O que é Azure AD Connect][205]



### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.

Quando você clica no bloco do Software de halogênio no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de Software de alogênio.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png 
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png 
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png 
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png 
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png 
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png 
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png 
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png 
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png 
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png