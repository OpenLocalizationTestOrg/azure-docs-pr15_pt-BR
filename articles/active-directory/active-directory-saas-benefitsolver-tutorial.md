<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Benefitsolver | Microsoft Azure"
    description="Saiba como usar Benefitsolver com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="10/10/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-benefitsolver"></a>Tutorial: Integração com o Active Directory do Azure com Benefitsolver

O objetivo deste tutorial é mostrar a integração do Azure e Benefitsolver.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Benefitsolver logon único habilitado para assinatura

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Benefitsolver será capazes de logon único para o aplicativo usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Benefitsolver
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "Cenário")
##<a name="enabling-the-application-integration-for-benefitsolver"></a>Habilitar a integração de aplicativo para Benefitsolver

O objetivo desta seção é como habilitar a integração de aplicativo para Benefitsolver da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-benefitsolver-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Benefitsolver, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Benefitsolver**.

    ![Galeria de aplicativo] (./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Benefitsolver**e clique em **concluído** para adicionar o aplicativo.

    ![Benefitssolver] (./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitssolver")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar Benefitsolver com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Seu aplicativo Benefitsolver espera as declarações SAML em um formato específico, o que requer que você adicionar os mapeamentos de atributo personalizado à sua configuração de **atributos de token saml** .  
A captura de tela a seguir mostra um exemplo para isso.

![Atributos] (./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Atributos")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Benefitsolver** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Benefitsolver** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "Configurar o logon único")

3.  Na página **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Definir configurações de aplicativo] (./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "Definir configurações de aplicativo")

    1.  Na caixa de texto de **Entrada na URL** , digite **http://azure.benefitsolver.com**.
    2.  Na caixa de texto **URL de resposta** , digite **https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml**.  


    3.  Clique em **Avançar**.

4.  Na página **Configurar logon único em Benefitsolver** , para baixar os metadados, clique em **baixar metadados**e salve o arquivo de metadados localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "Configurar o logon único")

5.  Envie o arquivo baixado metadados para sua equipe de suporte de Benefitsolver.

    >[AZURE.NOTE] A equipe de suporte Benefitsolver tem que fazer a configuração de SSO real.
Você receberá uma notificação quando o SSO foi ativado para sua assinatura.

6.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "Configurar o logon único")

7.  No menu na parte superior, clique em **atributos** para abrir a caixa de diálogo de **Atributos de Token SAML** .

    ![Atributos] (./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "Atributos")

8.  Para adicionar os mapeamentos de atributo obrigatório, execute as seguintes etapas:

    ![Atributos] (./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Atributos")

  	|Nome do atributo|Valor do atributo|
  	|---|---|
  	|ClientID|Você precisa obter este valor da sua equipe de suporte de Benefitsolver.|
  	|ClientKey|Você precisa obter este valor da sua equipe de suporte de Benefitsolver.|
  	|LogoutURL|Você precisa obter este valor da sua equipe de suporte de Benefitsolver.|
  	|EmployeeID|Você precisa obter este valor da sua equipe de suporte de Benefitsolver.|

    1.  Para cada linha de dados na tabela acima, clique em **Adicionar atributo de usuário**.
    2.  Na caixa de texto **Nome do atributo** , digite o nome do atributo mostrado para aquela linha.
    3.  Na caixa de texto **Valor do atributo** , selecione o valor do atributo mostrado para aquela linha.
    4.  Clique em **Concluir**.

9.  Clique em **Aplicar alterações**.

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login Benefitsolver, ele devem ser provisionados em Benefitsolver.  
No caso de Benefitsolver, dados de funcionários estão em seu aplicativo preenchido por meio de um arquivo de censo de seu sistema de HRIS (normalmente à noite).  

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Benefitsolver usuário conta ou APIs fornecidas pela Benefitsolver para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-benefitsolver-perform-the-following-steps"></a>Para atribuir usuários a Benefitsolver, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Benefitsolver **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
