<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com o pacote de BPM Questetra | Microsoft Aure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e o pacote de BPM Questetra."
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
    ms.date="10/28/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Tutorial: Integração com o Active Directory do Azure com o pacote de BPM Questetra

O objetivo deste tutorial é mostram como integrar o conjunto de BPM Questetra com o Azure Active Directory (AD Azure).  
Integração de pacote de BPM Questetra com Azure AD fornece os seguintes benefícios: 

- Você pode controlar no Azure AD quem tem acesso ao pacote de BPM Questetra 
- Você pode habilitar os usuários para automaticamente obter assinado no pacote de BPM Questetra (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos 

Para configurar a integração do Azure AD com Questetra BPM Suite, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único [Pacote de BPM Questetra](https://senbon-imadegawa-988.questetra.net/) na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em três blocos de construção principais:

1. Adicionando um pacote de BPM Questetra da Galeria 
2. Configurando e testando Azure AD logon único


## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Adicionando um pacote de BPM Questetra da Galeria
Para configurar a integração do pacote de BPM Questetra no Azure AD, é necessário adicionar o pacote de BPM Questetra da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o pacote de BPM Questetra da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Questetra BPM Suite**.

    ![Aplicativos][5]

7. No painel de resultados, selecione o **Pacote de BPM Questetra**e, em seguida, clique em **concluído** para adicionar o aplicativo.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar logon único Azure AD com o pacote de BPM Questetra com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no pacote de BPM Questetra um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no pacote de BPM Questetra precisa ser estabelecida.  
Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no pacote de BPM Questetra.
 
Para configurar e testar o Azure AD logon único com Questetra BPM Suite, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um pacote de BPM Questetra testar usuário](#creating-a-questetra-bpm-suite-test-user)** - ter um representante de Britta Simon no pacote de BPM Questetra que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando Azure AD Single Sign-On

O objetivo desta seção é habilitar Azure AD logon único no portal de clássico do Azure e configurar o logon único em seu aplicativo de pacote de BPM Questetra.

**Para configurar o logon único Azure AD com Questetra BPM Suite, execute as seguintes etapas:**

1. No portal do clássico Azure, na página de integração de aplicativo do **Pacote de BPM Questetra** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][8]

2. Na página **como você gostaria que os usuários para assinar o pacote de BPM Questetra** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Azure AD Single Sign-On][9]


3. Em uma janela de navegador da web diferente, faça logon em seu site de empresa de **Pacote de BPM Questetra** como administrador.

4. No menu na parte superior, clique em **Configurações do sistema**. 

    ![Azure AD Single Sign-On][10]

5. Para abrir a página de **SingleSignOnSAML** , clique em **SSO SAML ()**. 

    ![Azure AD Single Sign-On][11]


6. No portal do Azure clássico, na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas: 

    ![Definir configurações de aplicativo][13]
 
    a. Em você site de empresa de **Pacote de BPM Questetra** , na seção informações de SP, copie a **URL do ACS**e, em seguida, cole a caixa de texto de **Entrada na URL** .

    b. Em você site de empresa de **Pacote de BPM Questetra** , na seção informações de SP, copie a **Identificação de entidade**e cole-o na caixa de texto **URL do emissor** .

    c. Em você site de empresa de **Pacote de BPM Questetra** , na seção informações de SP, copie a **URL do ACS**e cole-o na caixa de texto **URL de resposta** .

    d. Clique em **Avançar**.

 
7. Na página **Configurar logon único no pacote de BPM Questetra** , clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único][14]


8. No seu site de empresa de **Pacote de BPM Questetra** , execute as seguintes etapas: 

    ![Configurar o logon único][15]

    a. Selecione **Habilitar logon único**.
     
    b. No portal do Azure clássico, copie o valor de **URL do emissor** e, em seguida, cole a caixa de texto de **ID de entidade** .

    c. No portal do Azure clássico, copie o valor de **Single Sign-On URL do serviço** e, em seguida, cole o **URL da página de entrada** de texto.

    d. No portal do Azure clássico, copie o valor de **URL do serviço Sign-Out único** e, em seguida, cole a **URL da página de saída** de caixa de texto.

    e. Na caixa de texto **formato NameID** , digite **urn: oasis: nomes: tc: SAML:1.1:nameid-formato: emailAddress**.


    f. Crie um arquivo de codificada de base 64 do seu certificado baixado. 

    >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    g. Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e cole-o na caixa de texto **certificado de validação** . 

    h. Clique em **Salvar**.


9. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**. 

    ![O que é Azure AD Connect][17]


10. Na página **confirmação de logon única** , clique em **Concluir**.  

    ![O que é Azure AD Connect][18]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do clássico Azure chamado Britta Simon.

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criar usuário de teste do Azure AD][100] 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criar usuário de teste do Azure AD][101] 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**. 

    ![Criar usuário de teste do Azure AD][102] 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:

    ![Criar usuário de teste do Azure AD][103]
 
    a. Como **Tipo de usuário**, selecione **novo usuário na sua organização**.
  
    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em Avançar.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas: 

    ![Criar usuário de teste do Azure AD][104] 
  
    a. Na caixa de texto **nome** , digite **Britta**. 
 
    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criar usuário de teste do Azure AD][105]  

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criar usuário de teste do Azure AD][106]   
  
    a. Anote o valor da **Nova senha**.
  
    b. Clique em **Concluir**.   
  
 
### <a name="creating-a-questetra-bpm-suite-test-user"></a>Criar um usuário de teste do pacote de BPM Questetra

O objetivo desta seção é criar um usuário chamado Britta Simon no pacote de BPM Questetra.

**Para criar um usuário chamado Britta Simon no pacote de BPM Questetra, execute as seguintes etapas:**

1.  Logon para o site da sua empresa de pacote de BPM Questetra como administrador.
2.  Vá para **configurações do sistema > lista de usuário > novo usuário**. 
3.  Na caixa de diálogo Novo usuário, execute as seguintes etapas: 

    ![Criar usuário de teste][300] 

    a. Na caixa de texto **nome** , digite o nome de usuário de Britta no Azure AD.

    b. Na caixa de texto **Email** , digite o nome de usuário de Britta no Azure AD.

    c. Na caixa de texto **senha** , digite uma senha.

4.  Clique em **Adicionar novo usuário**.



### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso a pacote de BPM Questetra.

![O que é Azure AD Connect][200]

**Para atribuir Britta Simon Questetra BPM Suite, execute as seguintes etapas:**

1. No portal do Azure clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![O que é Azure AD Connect][201]

2. Na lista de aplicativos, selecione o **Pacote de BPM Questetra**.

    ![O que é Azure AD Connect][205]

1. No menu na parte superior, clique em **usuários**.

    ![O que é Azure AD Connect][202]

1. Na lista de usuários, selecione **Britta Simon**.

    ![O que é Azure AD Connect][203]

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![O que é Azure AD Connect][204]



### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.  
Quando você clica no bloco do pacote de BPM Questetra no painel de acesso, que deve receber automaticamente conectado no seu aplicativo de pacote de BPM Questetra.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png 
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png 
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png 
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png 
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png 
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png 
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png 


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png 
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png 
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png 