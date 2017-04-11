<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com plataforma de nuvem do SAP HANA | Microsoft Azure" 
    description="Saiba como usar plataforma de nuvem do SAP HANA com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform"></a>Tutorial: Integração com o Active Directory do Azure com plataforma de nuvem do SAP HANA
  
O objetivo deste tutorial é mostrar a integração do Azure e plataforma de nuvem do SAP HANA.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma conta de plataforma de nuvem do SAP HANA
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a plataforma de nuvem do SAP HANA poderão logon único para o aplicativo usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

>[AZURE.IMPORTANT]Você precisa implantar seu próprio aplicativo ou assinar um aplicativo em sua conta de plataforma de nuvem do SAP HANA para testar logon único. Neste tutorial, um aplicativo é implantado na conta.
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para plataforma de nuvem do SAP HANA
2.  Configurando o logon único
3.  Atribuir uma função a um usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "Cenário")
##<a name="enabling-the-application-integration-for-sap-hana-cloud-platform"></a>Habilitar a integração de aplicativo para plataforma de nuvem do SAP HANA
  
O objetivo desta seção é como habilitar a integração de aplicativo para plataforma de nuvem do SAP HANA da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-sap-hana-cloud-platform-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para plataforma de nuvem do SAP HANA, execute as seguintes etapas:

1.  No Portal de gerenciamento do Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Plataforma de nuvem do SAP HANA**.

    ![Galeria de aplicativo] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "Galeria de aplicativo")

7.  No painel de resultados, selecione a **Plataforma de nuvem do SAP HANA**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Hana SAP] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "Hana SAP")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar a plataforma de nuvem do SAP HANA com sua conta no Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário para carregar um certificado codificado base 64 em seu locatário de plataforma de nuvem do SAP HANA.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo de **Plataforma de nuvem do HANA SAP** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "Configurar o logon único")

2.  Na página **como você gostaria que usuários para entrar plataforma de nuvem do SAP HANA** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Configurar o logon único")

3.  Em uma janela de navegador da web diferente, logon ao SAP HANA nuvem plataforma Cockpit em https://account. \<host paisagem\>.ondemand.com/cockpit (por exemplo: *https://account.hanatrial.ondemand.com/cockpit*).

4.  Clique na guia **Confiar** .

    ![Confiar] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "Confiar")

5.  Na seção de gerenciamento de confiança, execute as seguintes etapas:

    ![Obter metadados] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "Obter metadados")

    1.  Clique na guia de **Provedor de serviços de Local** .
    2.  Para baixar o arquivo de metadados de plataforma de nuvem do HANA SAP, clique em **Obter metadados**.

6.  No portal do clássico Azure Active, na página **Configurar a URL do aplicativo** , execute as seguintes etapas e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "Configurar a URL do aplicativo")

    1.  Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para entrar em seu aplicativo de **Plataforma de nuvem do SAP HANA** . Esta é a URL específicas de conta de um recurso em seu aplicativo de plataforma de nuvem do SAP HANA protegido. A URL baseia-se no seguinte padrão: https:// *\<applicationName\>\<accountName\>.\< host de paisagem\>.ondemand.com/\<caminho\_para\_protegido\_recurso\> * (por exemplo: *https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*)

        >[AZURE.NOTE]Esta é a URL em seu aplicativo de plataforma de nuvem do SAP HANA que requer que o usuário se autenticar.

    2.  Abra o arquivo de metadados de plataforma de nuvem do SAP HANA baixado e localize a marca de **ns3:AssertionConsumerService** .
    3.  Copiar o valor do atributo **local** e, em seguida, cole a caixa de texto **URL de resposta de plataforma do SAP HANA nuvem** .

7.  Na página **Configurar logon único na plataforma de nuvem do SAP HANA** , para baixar os metadados, clique em **baixar metadados**e salve o arquivo em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Configurar o logon único")

8.  No Cockpit SAP HANA nuvem plataforma, na seção **Service Provider Local** , execute as seguintes etapas:

    ![Gerenciamento de confiança] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "Gerenciamento de confiança")

    1.  Clique em **Editar**.
    2.  Como **O tipo de configuração**, selecione **personalizado**.
    3.  Como **O nome de provedor Local**, deixe o valor padrão.
    4.  Para gerar uma **Chave de assinatura** e um par de chaves de **Certificado de autenticação** , clique em **Gerar par de chaves**.
    5.  Como **Propagação Principal**, selecione **desabilitado**.
    6.  Como **Forçar autenticação**, selecione **desabilitado**.
    7.  Clique em **Salvar**.

9.  Clique na guia de **Provedor de identidade confiável** e clique em **Adicionar provedor de identidade confiável**.

    ![Gerenciamento de confiança] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "Gerenciamento de confiança")

    >[AZURE.NOTE]Para gerenciar a lista de provedores de identidade confiável, você precisa escolheu o tipo de configuração personalizada na seção Service Provider Local. Para tipo de configuração padrão, você tem uma relação de confiança implícita e não editáveis para o serviço de ID do SAP. Para nenhum, você não tem quaisquer configurações de confiança.

10. Clique na guia **Geral** e clique em **Procurar** para carregar o arquivo baixado metadados.

    ![Gerenciamento de confiança] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "Gerenciamento de confiança")

    >[AZURE.NOTE] Depois de carregar o arquivo de metadados, os valores para a **URL de logon único**, **Única URL Logout** e **Certificado de autenticação** são preenchidos automaticamente.

11. Clique na guia **atributos** .

12. Na guia **atributos** , execute as seguintes etapas:

    ![Atributos] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "Atributos")

    1.  Clicando em **Add Assertion-Based atributo**, adicione os seguintes atributos baseado em declaração:

        |Atributo de declaração| Atributo de capital|
        |-------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/givenName|   nome|--------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/surname|        Sobrenome|-----------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/EmailAddress|Email|

    >[AZURE.NOTE]A configuração dos atributos depende de como os aplicativos na HCP são desenvolvidos, ou seja, quais atributos esperam na resposta SAML e em qual nome (atributo de capital) acessam esse atributo no código.
    >  
    >a.  O **Atributo padrão** na captura de tela é apenas para fins de ilustração. Ele não é necessário para fazer com que o cenário de funcionar.  
    >
    >b.  Os nomes e os valores de **Atributo de capital** mostrados na captura de tela dependem de como o aplicativo é desenvolvido. É possível que o seu aplicativo exige mapeamentos diferentes.

13. No portal do clássico Azure, na página da caixa de diálogo **Configurar logon único na plataforma de nuvem do SAP HANA** , selecione a confirmação de configuração de logon único e, em seguida, clique em **Concluir**.

    ![Configurar o logon único] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Configurar o logon único")
  
Como uma etapa opcional, você pode configurar grupos baseados em declaração para seu provedor de identidade do Azure Active Directory

>[AZURE.NOTE]Usando grupos na plataforma de nuvem do SAP HANA permite que você atribuir dinamicamente um ou mais usuários a uma ou mais funções em seus aplicativos de plataforma de nuvem do SAP HANA, determinadas por valores de atributos na declaração SAML 2.0. Por exemplo, se a declaração contém o atributo "*contrato = temporário*", você pode querer afetados todos os usuários a serem adicionados ao grupo "*TEMPORÁRIOS*". O grupo "*TEMPORÁRIOS*" pode conter uma ou mais funções de um ou mais aplicativos implantados em sua conta de plataforma de nuvem do SAP HANA.
>  
>Use grupos baseados em declaração se você quiser atribuir muitos usuários para uma ou mais funções de aplicativos na sua conta de plataforma de nuvem do SAP HANA em massa. Se você quiser atribuir um único ou pequeno número de usuários a (a) funções específicas recomendamos atribuí-las diretamente na guia "**autorizações**" do cockpit plataforma de nuvem do SAP HANA.

##<a name="assigning-a-role-to-a-user"></a>Atribuir uma função a um usuário
  
Para permitir que usuários do Azure AD de login plataforma de nuvem do HANA SAP, você deve atribuir funções na plataforma de nuvem do SAP HANA a elas.

###<a name="to-assign-a-role-to-a-user-perform-the-following-steps"></a>Para atribuir uma função a um usuário, execute as seguintes etapas:

1.  Faça logon em sua **Plataforma de nuvem do SAP HANA** cockpit.

2.  Execute as seguintes etapas:

    ![Autorizações] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "Autorizações")

    1.  Clique em **autorização**.
    2.  Clique na guia **usuários** .
    3.  Na caixa de texto do **usuário** , digite o endereço de email do usuário.
    4.  Clique em **atribuir** para atribuir o usuário a uma função.
    5.  Clique em **Salvar**.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-sap-hana-cloud-platform-perform-the-following-steps"></a>Para atribuir usuários a plataforma de nuvem do SAP HANA, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo de **Plataforma de nuvem do HANA SAP** , clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).