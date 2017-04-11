<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com o serviço de Web da Amazon (AWS) | Microsoft Azure"
    description="Saiba como usar Amazon Web Services (AWS) com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-amazon-web-service-aws"></a>Tutorial: Integração com o Active Directory do Azure com o serviço de Web da Amazon (AWS)

O objetivo deste tutorial é mostram como integrar o serviço de Web da Amazon (AWS) com o Azure Active Directory (AD Azure).  
Integração Amazon Web serviço (AWS) com o Azure AD fornece os seguintes benefícios: 

- Você pode controlar no Azure AD quem tem acesso ao serviço de Web da Amazon (AWS) 
- Você pode habilitar os usuários para automaticamente obter assinados-on para Amazon Web serviço (AWS) (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos 

Para configurar a integração do Azure AD com o serviço de Web da Amazon (AWS), você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um serviço de Web da Amazon (AWS) single sign-na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é para que você possa testar logon único Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em três blocos de construção principais:

1. Adicionando o serviço de Web da Amazon (AWS) da Galeria 
2. Configurando e testando Azure AD logon único


## <a name="adding-amazon-web-service-aws-from-the-gallery"></a>Adicionando o serviço de Web da Amazon (AWS) da Galeria
Para configurar a integração dos serviços de Web da Amazon (AWS) no Azure AD, você precisa adicionar Amazon Web serviço (AWS) da galeria à sua lista de aplicativos de SaaS gerenciados.

### <a name="to-add-amazon-web-service-aws-from-the-gallery-perform-the-following-steps"></a>Para adicionar o serviço de Web da Amazon (AWS) da galeria, execute as seguintes etapas:

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**. 

    ![Do Active Directory][1] 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior. 
   
    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página. 
   
    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**. 
   
    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Amazon Web serviço (AWS)**.
   
    ![Aplicativos][5]

7. No painel de resultados, selecione o **Serviço de Web da Amazon (AWS)**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Aplicativos][6]



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
O objetivo desta seção é mostrar como configurar e testar logon único Azure AD com Amazon Web serviço (AWS) com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no serviço de Web da Amazon (AWS) para um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no serviço de Web da Amazon (AWS) deve ser feita.  
Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no serviço de Web da Amazon (AWS).
 
Para configurar e testar o Azure AD logon único com o serviço de Web da Amazon (AWS), você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD único Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
4. **[Criando um serviço de Web da Amazon (AWS) teste usuário](#creating-a-halogen-software-test-user)** - ter um representante de Britta Simon no serviço de Web da Amazon (AWS) que esteja vinculado à representação Azure AD dela.
5. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configurando Azure AD Single Sign-On único

O objetivo desta seção é habilitar Azure AD logon único no portal de clássico do Azure e configurar o logon único em seu aplicativo de serviço de Web da Amazon (AWS).  
Seu aplicativo de serviço de Web da Amazon (AWS) espera as declarações SAML em um formato específico, o que requer que você adicionar os mapeamentos de atributo personalizado à sua configuração de **atributos de token saml** . A captura de tela a seguir mostra um exemplo para isso.


![Configurar o logon único][27]

**Para configurar o logon único Azure AD com o serviço de Web da Amazon (AWS), execute as seguintes etapas:**

1. No portal do clássico Azure, na página de integração do aplicativo de **Serviço de Web da Amazon (AWS)** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o logon único][7]

2. Na página **como você gostaria que os usuários fazer logon ao serviço de Web da Amazon (AWS)** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único][8]

3. Na página de diálogo **Definir configurações de aplicativo** , clique em Avançar. 

    ![Definir configurações de aplicativo][9]
 
4. Na página **Configurar logon único no serviço de Web da Amazon (AWS)** , clique em **baixar metadados**e salve o arquivo de metadados localmente em seu computador.

    ![Configurar o logon único][10]

5. Em uma janela de navegador diferente, logon para o site da sua empresa de serviço de Web da Amazon (AWS) como administrador.

6. Clique em **Página inicial do Console**.

    ![Configurar o logon único][11]

7. Clique em **gerenciamento de identidades e acesso**. 

    ![Configurar o logon único][12]

8. Clique em **Provedores de identidade**e clique em **Criar provedor**. 

    ![Configurar o logon único][13]

9. Na página de diálogo **Configurar provedor** , execute as seguintes etapas: 

    ![Configurar o logon único][14]

     a. Como **O tipo de provedor**, selecione **SAML**.

     b. Na caixa de texto **Nome do provedor** , digite um nome de provedor (por exemplo: *WAAD*).

     c. Para carregar o arquivo de metadados baixado, clique em **Escolher arquivo**.

     d. Clique em **próxima etapa**.


10. Na página de diálogo **Informações do provedor de confirmar** , clique em **criar**. 

    ![Configurar o logon único][15]

11. Clique em **funções**e, em seguida, clique em **Criar nova função**. 

    ![Configurar o logon único][16]

12. Na caixa de diálogo **Definir nome de função** , execute as seguintes etapas: 

    ![Configurar o logon único][17]

    a. Na caixa de texto **Nome da função** , digite um nome de função (por exemplo: *TestUser*).

    b. Clique em **próxima etapa**.

13. Na caixa de diálogo **Selecionar tipo de função** , execute as seguintes etapas: 

    ![Configurar o logon único][18]

    a. Selecione **a função para acesso de provedor de identidade**.

    b. Na seção **conceder Web Single Sign-On (WebSSO) acesso aos provedores SAML** , clique em **Selecionar**.


14. Na caixa de diálogo **Estabelecer confiança** , execute as seguintes etapas:  

    ![Configurar o logon único][19]
     
     a. Como provedor de SAML, selecione o provedor SAML que você criou previousley (por exemplo: *WAAD*) 

     b. Clique em **próxima etapa**.


15. Na caixa de diálogo **Verificar confiabilidade de função** , clique em **Próxima etapa**. 

    ![Configurar o logon único][32]


16. Na caixa de diálogo **Anexar política** , clique em **Próxima etapa**.  

    ![Configurar o logon único][33]


17. Na caixa de diálogo **revisão** , execute as seguintes etapas:   

    ![Configurar o logon único][34]

     a. Copie o valor da **Função ARN** .

     b. Copie o valor ARN **Entidades confiáveis** .

     c. Clique em **Criar função**. 

18. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.

    ![O que é Azure AD Connect][20]

19. Na página **confirmação de logon única** , clique em **Concluir** para fechar a caixa de diálogo **logon único configurar** .

    ![O que é Azure AD Connect][22]


20. No menu na parte superior, clique em **atributos** para abrir a caixa de diálogo de **Atributos de Token SAML** . 

    ![Configurar o logon único][21]

21. Clique em **Adicionar atributo de usuário**. 

    ![Configurar o logon único][23]

22. Na caixa de diálogo Adicionar atributo de usuário, execute as seguintes etapas. 

    ![Configurar o logon único][24] 

    a. Na caixa de texto **Nome do atributo** , digite **https://aws.amazon.com/SAML/Attributes/Role**.

    b. Na caixa de texto **Valor do atributo** , digite **[o valor da função ARN], [o valor de ARN de entidade confiável]**.

    >[AZURE.TIP] Estes são os valores que você copiou na caixa de diálogo revisão quando você criou sua função. 

    c. Clique em **Concluir** para fechar a caixa de diálogo **Adicionar atributo de usuário** .

23. Clique em **Adicionar atributo de usuário**. 

    ![Configurar o logon único][23]


24. Na caixa de diálogo Adicionar atributo de usuário, execute as seguintes etapas. 

    ![Configurar o logon único][25]


     a. Na caixa de texto **Nome do atributo** , digite **https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     b. Na caixa de texto **Valor do atributo** , digite ou selecione **user.userprincipalname** na lista suspensa.
     
    ![Configurar o logon único][35]
    

     c. Clique em **Concluir** para fechar a caixa de diálogo **Adicionar atributo de usuário** .


25. Clique em **Aplicar alterações**. 

    ![Configurar o logon único][26]





### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no portal do clássico Azure chamado Britta Simon.

![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png) 
 
4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**. 

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas: 

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png) 

  1. Como tipo de usuário, selecione novo usuário na sua organização.
  2. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.
  3. Clique em Avançar.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas: 

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. No **Sobrenome** txtbox, tipo, **Simon**.
  
    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.
  
    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png) 
 
8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.
  
    b. Clique em **Concluir**.   
  
 
### <a name="creating-a-amazon-web-service-aws-test-user"></a>Criar um usuário de teste do serviço de Web da Amazon (AWS)

O objetivo desta seção é criar um usuário chamado Britta Simon no serviço de Web da Amazon (AWS).

### <a name="to-create-a-user-called-britta-simon-in-amazon-web-service-aws-perform-the-following-steps"></a>Para criar um usuário chamado Britta Simon no serviço de Web da Amazon (AWS), execute as seguintes etapas:

1. Faça logon site da sua empresa de **Serviço de Web da Amazon (AWS)** como administrador.

2. Clique no ícone de **Página inicial do Console** . 

    ![Configurar o logon único][11]

3. Clique em identidade e gerenciamento de acesso. 

    ![Configurar o logon único][28]

4. No painel de controle, clique em usuários e clique em criar novos usuários. 

    ![Configurar o logon único][29]

5. Na caixa de diálogo Criar usuário, execute as seguintes etapas: 

    ![Configurar o logon único][30]

     a. Nas caixas de texto **Inserir nomes de usuário** , digite o nome de usuário de Brita Simon (userprincipalname) no Azure AD.

     b. Clique em **criar**.




### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

O objetivo desta seção é ativando Britta Simon usar logon único Azure concedendo o acesso ao serviço de Web da Amazon (AWS).

![Atribuir usuário][31]

**Para atribuir Britta Simon a CloudPassage, execute as seguintes etapas:**

1. No portal do Azure clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][26]

2. Na lista de aplicativos, selecione o **Serviço de Web da Amazon (AWS)**.

    ![Atribuir usuário][27]

1. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][25]

1. Na lista de usuários, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][29]

### <a name="testing-single-sign-on"></a>Teste de logon único

O objetivo desta seção é testar a Azure AD único logon configuração usando o painel de acesso.  
Quando você clica no bloco do serviço de Web da Amazon (AWS) no painel de acesso, você deve obter automaticamente assinado no seu aplicativo de serviço de Web da Amazon (AWS).


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service/user_attributes_01.png






















