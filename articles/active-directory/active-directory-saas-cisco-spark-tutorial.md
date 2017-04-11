<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Cisco Spark | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e Cisco Spark."
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
    ms.date="10/20/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Tutorial: Integração com o Active Directory do Azure com Cisco Spark

Neste tutorial, você aprenderá a integrar Spark Cisco com o Azure Active Directory (AD Azure).

Integrar Cisco Spark Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Cisco Spark
- Você pode habilitar os usuários para automaticamente obter assinado no Cisco Spark (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Cisco Spark, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único **Cisco Spark** na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testar Azure AD logon único em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Cisco Spark da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-cisco-spark-from-the-gallery"></a>Adicionando Cisco Spark da Galeria
Para configurar a integração do Cisco Spark no Azure AD, você precisa adicionar Cisco Spark da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar Cisco Spark da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1]

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Cisco Spark**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_01.png)

7. No painel de resultados, selecione **Cisco Spark**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
Nesta seção, configurar e testar logon único Azure AD com Spark Cisco com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no Cisco Spark a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Cisco Spark precisa ser estabelecida.
Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no Cisco Spark. Para configurar e testar o Azure AD logon único com Cisco Spark, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um Spark Cisco testar usuário](#creating-a-cisco-spark-test-user)** - ter um representante de Britta Simon em Spark Cisco que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD

O objetivo desta seção é habilitar Azure AD logon único no portal de clássico do Azure e configurar o logon único em seu aplicativo Cisco Spark.

Aplicativo de Spark Cisco espera as declarações SAML para conter atributos específicos. Configure os seguintes atributos para este aplicativo. Você pode gerenciar os valores desses atributos da guia **"Os atributos do"** do aplicativo. A captura de tela a seguir mostra um exemplo para isso.

![Configurar o logon único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_03.png) 

**Para configurar o logon único Azure AD com Cisco Spark, execute as seguintes etapas:**

1. No portal do clássico Azure, na página de integração do aplicativo **Cisco Spark** , no menu na parte superior, clique em **atributos**.
     
    ![Configurar o logon único][5]


2. Na caixa de diálogo **atributos de token SAML** , execute as seguintes etapas:

    a. Clique em **Adicionar atributo de usuário** para abrir a caixa de diálogo **Adicionar Attribure de usuário** .

    ![Configurar o logon único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_05.png)
    
    b. Na caixa de texto **Nome do atributo** , digite **uid**.
    
    c. A **Valor do atributo** , selecione lista **user.userprincipal**.
    
    d. Clique em **Concluir**. Em seguida, **Aplicar alterações** na parte inferior da página.

3. No menu na parte superior, clique em **Início rápido**.

    ![Configurar o logon único][6]

4. No portal do clássico, na página de integração do aplicativo **Cisco Spark** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][7] 

5. Na página **como você gostaria que usuários para entrar Cisco Spark** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.
    
    ![Configurar o logon único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_06.png)

6. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_07.png)


    a. Na caixa de texto de entrada na URL, digite uma URL usando o seguinte padrão: `https://web.ciscospark.com/#/signin`.

    b. Clique em **Avançar**.


7. Na página **Configurar logon único em Cisco Spark** , clique em **baixar metadados**e, em seguida, salve o arquivo em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_09.png)

8. Entrar no [Gerenciamento de colaboração de nuvem Cisco](https://admin.ciscospark.com/) com suas credenciais de administrador completo.
9. Selecione **configurações** e na seção **autenticação** , clique em **Modificar**.

    ![Configurar o logon único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)

10. Selecione **integrar um provedor de identidade de terceiros 3º. (Avançado)** e vá para a próxima tela.
11. Clique em **Baixar o arquivo de metadados** e salvar o arquivo em seu computador.
12. Na página **Importar Idp metadados** , arraste e solte o arquivo de metadados do Azure AD para a página ou use a opção de navegador de arquivo para localizar e carregar o arquivo de metadados do Azure AD. Em seguida, selecione **exigir certificado assinado por uma autoridade de certificação em metadados (mais segura)** e clique em **Avançar**. 

    ![Configurar o logon único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)

13. Selecione **Teste SSO Conexão**e, quando uma nova guia de navegador é aberta, autenticar com o Azure AD entrando no.
14. Retornar para a guia do navegador de **Gerenciamento de colaboração de nuvem Cisco** . Se o teste foi bem-sucedido, selecione **Este teste foi bem-sucedido. Habilitar a opção Single Sign-On** e clique em **Avançar**.

7. No portal de clássico do Azure AD, selecione a confirmação de configuração de logon único e clique em **Avançar**.
    
    ![Azure AD Single Sign-On][10]

8. Na página **confirmação de logon única** , clique em **Concluir**.  
    
    ![Azure AD Single Sign-On][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
Nesta seção, você criar um usuário de teste no portal do clássico chamado Britta Simon.

![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.
    
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:
 
    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   



### <a name="creating-a-cisco-spark-test-user"></a>Criar um usuário de teste Cisco Spark

Nesta seção, você criar um usuário chamado Britta Simon no Cisco Spark. Nesta seção, você criar um usuário chamado Britta Simon no Cisco Spark.

1. Vá para o [Gerenciamento de colaboração de nuvem Cisco](https://admin.ciscospark.com/) com suas credenciais de administrador completo.
2. Clique em **usuários** e **Gerenciar usuários**.

    ![Configurar o logon único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. Na janela **Gerenciar usuário** , selecione **Adicionar ou modificar usuários manualmente** e clique em **Avançar**.
4. Selecione **nomes e endereços de Email**. Em seguida, preencha a caixa de texto como a seguir:

    ![Configurar o logon único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    a. Na caixa de texto **nome** , digite **Britta**

    b. Na caixa de texto **Sobrenome** , digite **Simon**

    c. Na caixa de texto **endereço de Email** , digite**britta.simon@contoso.com**

5. Clique no sinal de mais para adicionar Britta Simon. Em seguida, clique em **Avançar**.
6. Na janela **Adicionar serviços para os usuários** , clique em **Salvar** e **Concluir**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

Nesta seção, você habilitar Britta Simon usar logon único Azure concedendo o acesso aos Cisco Spark.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a Cisco Spark, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Cisco Spark**.

    ![Configurar o logon único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_14.png) 

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203] 

1. Na lista de todos os usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]


### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.

Quando você clica no bloco do Cisco Spark no painel de acesso, que deve receber automaticamente conectado no seu aplicativo Cisco Spark.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_205.png
