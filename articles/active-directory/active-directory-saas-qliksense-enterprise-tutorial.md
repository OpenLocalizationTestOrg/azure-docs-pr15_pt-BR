<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Qlik sentido Enterprise | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e Qlik sentido Enterprise."
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
    ms.date="08/31/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Tutorial: Integração com o Active Directory do Azure com Qlik sentido Enterprise

Neste tutorial, você aprenderá a integrar Qlik sentido Enterprise com o Azure Active Directory (AD Azure).

Integração Qlik sentido Enterprise com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Qlik sentido Enterprise
- Você pode habilitar os usuários para automaticamente obter assinado no Qlik sentido Enterprise (Single Sign-On) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central - clássico portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativo de SaaS com Azure AD, consulte [o que é o acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Qlik sentido Enterprise, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um logon único Qlik sentido Enterprise na assinatura habilitada


> [AZURE.NOTE] Para testar as etapas deste tutorial, não recomendamos usar um ambiente de produção.


Para testar as etapas deste tutorial, você deverá seguir essas recomendações:

- Você não deve usar o seu ambiente de produção, a menos que isso é necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, você pode obter um um mês avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testar Azure AD logon único em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Qlik sentido Enterprise da Galeria
2. Configurando e testando Azure AD logon único


## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Adicionando Qlik sentido Enterprise da Galeria
Para configurar a integração do Qlik sentido Enterprise no Azure AD, você precisa adicionar Qlik sentido Enterprise da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar Qlik sentido Enterprise da galeria, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory][1]
2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Qlik sentido Enterprise**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_01.png)

7. No painel de resultados, selecione **Qlik sentido Enterprise**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurando e testando Azure AD logon único
Nesta seção, configurar e testar logon único Azure AD com Qlik sentido Enterprise com base em um usuário de teste chamado "Simon Britta".

Para logon único trabalhar, Azure AD precisa saber qual é o usuário correspondente no Qlik sentido Enterprise a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Qlik sentido Enterprise deve ser feita.

Essa relação de link é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor de **nome de usuário** no Qlik sentido Enterprise.

Para configurar e testar o Azure AD logon único com Qlik sentido Enterprise, você precisa concluir os blocos de construção a seguir:

1. **[Configurando Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que seus usuários usar este recurso.
2. **[Criando um anúncio Azure testar usuário](#creating-an-azure-ad-test-user)** - testar Azure AD logon único com Britta Simon.
3. **[Criando uma empresa de sentido Qlik testar usuário](#creating-a-qliksense-enterprise-test-user)** - ter um representante de Britta Simon Qlik sentido empresa que esteja vinculado à representação Azure AD dela.
4. **[Atribuindo o Azure AD testar usuário](#assigning-the-azure-ad-test-user)** - habilitar Britta Simon usar logon único Azure AD.
5. **[Teste Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurando o logon único Azure AD

Nesta seção, você habilitar Azure AD logon único no portal do clássico e configurar o logon único em seu aplicativo Qlik sentido Enterprise.


**Para configurar o logon único Azure AD com Qlik sentido Enterprise, execute as seguintes etapas:**

1. No portal do clássico, na página de integração do aplicativo **Qlik sentido Enterprise** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o logon único][6] 

2. Na página **como você gostaria que os usuários para assinar Qlik sentido Enterprise** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_03.png) 

3. Na página de diálogo **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_04.png) 

    a. Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para logon para seu aplicativo de Qlik sentido Enterprise usando o seguinte padrão: **https://\<Qlik sentido totalmente Qualifed Hostname\>: 443 / < Virtual Proxy prefixo\>/samlauthn/**.
    
    > [AZURE.NOTE] Observe a barra invertida no final desse URI.  Ele é necessário.

    b. Clique em **Avançar**
 
4. Na página **Configurar logon único em Qlik sentido Enterprise** , execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_05.png)

    a. Clique em **baixar metadados**e salve o arquivo em seu computador.  Prepare-se para editar o arquivo de metadados antes de carregar no servidor Qlik sentido.

    b. Clique em **Avançar**.

5. Prepare o arquivo XML de metadados de federação para que você pode carregar que servidor Qlik sentido.

    > [AZURE.NOTE] Antes de carregar os metadados IdP no servidor Qlik sentido, o arquivo precisa ser editado para remover informações para garantir a operação correta entre Azure AD e sentido Qlik server.

    ![QlikSense][qs24]

    a. Abra o arquivo FederationMetaData.xml baixado do Azure em um editor de texto.

    b. Procure o valor **RoleDescriptor**.  Haverá quatro entradas (dois pares de marcas de elemento abertura e fechamento).

    c. Exclua todas as informações e as marcas de RoleDescriptor do arquivo.

    d. Salve o arquivo e mantê-lo nas proximidades para usar posteriormente neste documento.

6. Navegue para Console de gerenciamento de Qlik (QMC) o Qlik sentido como um usuário que pode criar configurações de proxy virtual.

7. Na QMC, clique no item de menu Proxy Virtual.

    ![QlikSense][qs6] 

8. Na parte inferior da tela, clique no botão novo de criar.

    ![QlikSense][qs7]

9. A tela de edição de proxy Virtual é exibida.  No lado direito da tela é um menu para tornar as opções de configuração visível.

    ![QlikSense][qs9]

10. Com a opção de menu de identificação marcada, insira as informações de identificação para a configuração de proxy virtual Azure.

    ![QlikSense][qs8]  
    
    a. O campo Descrição é um nome amigável para a configuração de proxy virtual.  Insira um valor para obter uma descrição.
    
    b. O campo prefixo identifica o ponto de extremidade de proxy virtual para conexão com o sentido de Qlik com o Azure AD Single Sign-On.  Insira um nome de prefixo exclusivo para este proxy de virtual.

    c. Tempo limite de inatividade de sessão (minutos) é o tempo limite de conexões através desse proxy virtual.

    d. O nome de cabeçalho de cookie da sessão é o nome do cookie armazenando o identificador de sessão para a sessão de sentido Qlik que um usuário recebe após a autenticação bem-sucedida.  Esse nome deve ser exclusivo.

11. Clique na opção de menu de autenticação para torná-la visível.  Aparece a tela de autenticação.

    ![QlikSense][qs10]

    a. **Modo de acesso anônimo** suspensa determina se usuários anônimos podem acessar Qlik sentido através do proxy virtual.  A opção padrão não é nenhum usuário anônimo.

    b. **Método de autenticação** suspensa determina que o esquema de autenticação do proxy virtual usará.  Selecione SAML na lista suspensa.  Mais opções aparecerão como resultado.

    c. No **campo URI de host SAML**, entrada que serão inseridos pelos usuários hostname para acessar o sentido de Qlik este proxy de virtual SAML.  O nome do host é o uri do servidor Qlik sentido.

    d. A **ID de entidade SAML**, insira o mesmo valor inserido para o campo URI de host SAML.

    e. Os **metadados de SAML IdP** é o arquivo editado anteriormente na seção **Editar metadados de Federação da configuração do Azure AD** .  **Antes de carregar os metadados IdP, o arquivo precisa ser editado** para remover informações para garantir a operação correta entre Azure AD e sentido Qlik server.  **Consulte as instruções acima se o arquivo tem ainda a ser editada.**  Se o arquivo foi editado clique no botão Procurar e selecione o arquivo de metadados editado carregá-la para a configuração de proxy virtual.

    f. Insira o nome do atributo ou referência de esquema para o atributo SAML que representa a **ID de usuário** Azure AD enviará ao servidor Qlik sentido.  Informações de referência de esquema estão disponíveis na configuração de postagem de telas do aplicativo do Azure.  Para usar o atributo de nome, **digite http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.

    g. Insira o valor para o **diretório de usuário** que será anexado aos usuários quando autenticam ao servidor de Qlik sentido por meio do Azure AD.  Valores embutidos devem estar entre **colchetes []**.  Para usar um atributo que enviou a declaração Azure AD SAML, insira o nome do atributo este texto caixa **sem** colchetes.

    h. O **algoritmo de assinatura SAML** define o certificado de provedor (nesse caso, o servidor de Qlik sentido) de serviço de assinatura para a configuração de proxy virtual.  Se o servidor de Qlik sentido usa um certificado confiável gerado usando Microsoft Enhanced RSA e o provedor de criptografia AES, altere o algoritmo de assinatura SAML para **SHA-256**.

    Eu. A seção de mapeamento de atributo SAML permite atributos adicionais, como os grupos para ser enviada para Qlik sentido para uso em regras de segurança.

12. Clique na opção de menu para torná-la visível de balanceamento de carga.  Aparece a tela de balanceamento de carga.

    ![QlikSense][qs11]

13. Clique em novo servidor nó botão Adicionar, nó de mecanismo select ou nós Qlik sentido enviará a sessões para fins de balanceamento de carga e clique no botão Adicionar.

    ![QlikSense][qs12]

14. Clique na opção menu Avançado para torná-la visível. Aparece a tela Avançado.

    ![QlikSense][qs13]

    a. A lista de branco Host identifica nomes de host que são aceitos ao conectar com o servidor de Qlik sentido.  **Insira o nome de host usuários, especifique ao conectar ao servidor de Qlik sentido.** O nome do host é o mesmo valor que o uri de host SAML sem o https://.

15. Clique no botão Aplicar.

    ![QlikSense][qs14]

16. Clique em Okey para aceitar a mensagem de aviso informando proxies vinculados ao proxy virtual serão reiniciados.

    ![QlikSense][qs15]

17. No lado direito da tela, o menu de itens associada é exibida.  Clique na opção de menu de Proxies.

    ![QlikSense][qs16]

18. Aparece a tela de proxy.  Clique no botão Link na parte inferior para vincular um proxy para o proxy virtual.

    ![QlikSense][qs17]

19. Selecione o nó de proxy que esta conexão de proxy virtual de suporte e clique no botão Link.  Depois de vincular, o proxy estará listado em proxies associados.

    ![QlikSense][qs18]
    ![QlikSense][qs19]

20. Após cerca de 5 a 10 segundos, a mensagem de atualização de QMC será exibido.  Clique no botão Atualizar QMC.

    ![QlikSense][qs20]

21. Quando o QMC atualiza, clique no item de menu proxies Virtual. A nova entrada de proxy virtual SAML está listada na tabela na tela.  Clique na entrada proxy virtual.

    ![QlikSense][qs51]

22. Na parte inferior da tela, botão de metadados baixar SP ativará.  Clique no botão de metadados de baixar SP para salvar os metadados para um arquivo.

    ![QlikSense][qs52]

23. Abra o arquivo de metadados de sp.  Observe a entrada de **entityID** e a entrada **AssertionConsumerService** .  Esses valores são equivalentes para o **identificador** e a **entrar em URL** na configuração de aplicativo do Azure AD. Se eles não correspondem, em seguida, substitua-las no Assistente de configuração de aplicativo do Azure AD.

    ![QlikSense][qs53]

24. No portal do clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.
    
    ![Azure AD Single Sign-On][10]

25. Na página **confirmação de logon única** , clique em **Concluir**.  
 
    ![Azure AD Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do Azure AD
Nesta seção, você criar um usuário de teste no portal do clássico chamado Britta Simon.


![Criar usuário do Azure AD][20]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **portal do Azure clássico**, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_09.png) 

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **usuários**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar usuário** , na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:  ![criação de um usuário de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_05.png) 

    a. Como tipo de usuário, selecione novo usuário na sua organização.

    b. Na **caixa de texto**o nome de usuário, digite **BrittaSimon**.

    c. Clique em **Avançar**.

6.  Na página de diálogo de **Perfil de usuário** , execute as seguintes etapas: ![criação de um usuário de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **nome** , digite **Britta**.  

    b. Na caixa **Sobrenome** de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome para exibição** , digite **Britta Simon**.

    d. Na lista de **função** , selecione o **usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **obter senha temporária** , clique em **criar**.

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova senha**.

    b. Clique em **Concluir**.   


### <a name="creating-an-qlik-sense-enterprise-test-user"></a>Criação de um usuário de teste Qlik sentido Enterprise

Nesta seção, você criar um usuário chamado Britta Simon no Qlik sentido Enterprise. Trabalhe com a equipe de suporte de Qlik sentido Enterprise para adicionar os usuários na plataforma Qlik sentido Enterprise.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuindo o usuário de teste do Azure AD

Nesta seção, você habilitar Britta Simon usar logon único Azure concedendo o acesso aos Qlik sentido Enterprise.

![Atribuir usuário][200] 

**Para atribuir Britta Simon a Qlik sentido Enterprise, execute as seguintes etapas:**

1. No portal de clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Qlik sentido Enterprise**.

    ![Configurar o logon único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_50.png) 

3. No menu na parte superior, clique em **usuários**.

    ![Atribuir usuário][203]

4. Na lista de usuários, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir usuário][205]


## <a name="testing-single-sign-on"></a>Teste de logon único

Nesta seção, você testar seu Azure AD único logon configuração usando o painel de acesso.

Quando você clica no bloco de Qlik sentido Enterprise no painel de acesso, que deve receber automaticamente conectado no seu aplicativo Qlik sentido Enterprise.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_205.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs21]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_21.png
[qs22]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_22.png
[qs23]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_23.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs25]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_25.png
[qs26]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_26.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png