<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Clever | Microsoft Azure" 
    description="Saiba como usar Clever com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-clever"></a>Tutorial: Integração com o Active Directory do Azure com Clever

O objetivo deste tutorial é mostrar a integração do Azure e Clever. O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário inteligente

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Clever será capazes de logon único para o aplicativo em seu site de empresa inteligentes (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Clever
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-clever-tutorial/IC798977.png "Cenário")
##<a name="enabling-the-application-integration-for-clever"></a>Habilitar a integração de aplicativo para Clever

O objetivo desta seção é como habilitar a integração de aplicativo para Clever da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-clever-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Clever, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-clever-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-clever-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-clever-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-clever-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Clever**.

    ![Galeria de aplicativo] (./media/active-directory-saas-clever-tutorial/IC798978.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Clever**e clique em **concluído** para adicionar o aplicativo.

    ![Inteligentes] (./media/active-directory-saas-clever-tutorial/IC798979.png "Inteligentes")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar Clever com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Seu aplicativo inteligente espera as declarações SAML em um formato específico, o que requer que você adicionar os mapeamentos de atributo personalizado à sua configuração de **atributos de token saml** .  
A captura de tela a seguir mostra um exemplo para isso.

![Atributos] (./media/active-directory-saas-clever-tutorial/IC798980.png "Atributos")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Clever** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-clever-tutorial/IC784682.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Clever** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-clever-tutorial/IC798981.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto de **Entrada inteligente na URL** , digite a URL usada pelos usuários para logon para seu aplicativo inteligente (por exemplo: *https://clever.com/in/azsandbox*) e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-clever-tutorial/IC798982.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Clever** , para baixar os metadados, clique em **baixar metadados**e salve o arquivo de metadados localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-clever-tutorial/IC798983.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa inteligentes como administrador.

6.  Na barra de ferramentas, clique em **Login instantânea**.

    ![Login instantânea] (./media/active-directory-saas-clever-tutorial/IC798984.png "Login instantânea")

7.  Na página **Login instantânea** , execute as seguintes etapas:

    ![Login instantânea] (./media/active-directory-saas-clever-tutorial/IC798985.png "Login instantânea")

    1.  Digite a **URL de Login**.  

        >[AZURE.NOTE] A **URL de Login** é um valor personalizado.
Você pode obter o valor real da sua equipe de suporte inteligente.

    2.  Como o **Sistema de identidade**, selecione **ADFS**.
    3.  Clique em **Salvar**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-clever-tutorial/IC798986.png "Configurar o logon único")

9.  No menu na parte superior, clique em **atributos** para abrir a caixa de diálogo de **Atributos de Token SAML** .

    ![Atributos] (./media/active-directory-saas-clever-tutorial/IC795920.png "Atributos")

10. Para adicionar os mapeamentos de atributo obrigatório, execute as seguintes etapas:

    ![atributos de token de SAML] (./media/active-directory-saas-clever-tutorial/IC795921.png "atributos de token de SAML")

  	|Nome do atributo|Valor do atributo|
  	|---|---|
  	|clever.Student.Credentials.District\_nome de usuário|User.userPrincipalName|

    1.  Para cada linha de dados na tabela acima, clique em **Adicionar atributo de usuário**.
    2.  Na caixa de texto **Nome do atributo** , digite o nome do atributo mostrado para aquela linha.
    3.  Na caixa de texto **Valor do atributo** , selecione o valor do atributo mostrado para aquela linha.
    4.  Clique em **Concluir**.

11. Clique em **Aplicar alterações**.

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login Clever, ele devem ser provisionados em Clever.  
No caso de Clever, provisionamento é uma tarefa manual que precisa ser executada pela equipe de suporte inteligente.

>[AZURE.NOTE] Você pode usar qualquer outras ferramentas de criação do usuário inteligente conta ou APIs fornecidas pela Clever para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-clever-perform-the-following-steps"></a>Para atribuir usuários a Clever, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Clever **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-clever-tutorial/IC798987.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-clever-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
