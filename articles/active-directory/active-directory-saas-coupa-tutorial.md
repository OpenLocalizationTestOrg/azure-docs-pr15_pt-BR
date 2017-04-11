<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Coupa | Microsoft Azure" 
    description="Saiba como usar Coupa com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-coupa"></a>Tutorial: Integração com o Active Directory do Azure com Coupa

O objetivo deste tutorial é mostrar a integração do Azure e Coupa.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Coupa logon único habilitado para assinatura

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Coupa será capazes de logon único para o aplicativo usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Coupa
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-coupa-tutorial/IC791897.png "Cenário")
##<a name="enabling-the-application-integration-for-coupa"></a>Habilitar a integração de aplicativo para Coupa

O objetivo desta seção é como habilitar a integração de aplicativo para Coupa da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-coupa-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Coupa, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-coupa-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-coupa-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-coupa-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-coupa-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Coupa**.

    ![Galeria de aplicativo] (./media/active-directory-saas-coupa-tutorial/IC791898.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Coupa**e clique em **concluído** para adicionar o aplicativo.

    ![Coupa] (./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar Coupa com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para Coupa requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  Entre site da sua empresa Coupa como um administrador.

2.  Vá para **configuração \> controle de segurança**.

    ![Controles de segurança] (./media/active-directory-saas-coupa-tutorial/IC791900.png "Controles de segurança")

3.  Para baixar o arquivo de metadados de Coupa no seu computador, clique em **Download e importar SP metadados**.

    ![Metadados Coupa SP] (./media/active-directory-saas-coupa-tutorial/IC791901.png "Metadados Coupa SP")

4.  Em uma janela de navegador diferente, entre portal do Azure clássico.

5.  Na página de integração do aplicativo **Coupa** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-coupa-tutorial/IC791902.png "Configurar o logon único")

6.  Na página **como você gostaria que os usuários para assinar Coupa** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-coupa-tutorial/IC791903.png "Configurar o logon único")

7.  Na página **Configurar a URL do aplicativo** , execute as seguintes etapas:

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-coupa-tutorial/IC791904.png "Configurar a URL do aplicativo")

    1.  Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para entrar seu aplicativo de Coupa (por exemplo: "*http://company.Coupa.com*").
    2.  Abrir o arquivo de metadados Coupa baixado e copie a **AssertionConsumerService índice/URL**.
    3.  Na caixa de texto **URL de resposta Coupa** , cole o valor de **Índice de AssertionConsumerService/URL** .
    4.  Clique em **Avançar**.

8.  Na página **Configurar logon único em Coupa** , para baixar o arquivo de metadados, clique em **baixar metadados**e salve o arquivo localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-coupa-tutorial/IC791905.png "Configurar o logon único")

9.  No site da empresa Coupa, acesse **configuração \> controle de segurança**.

    ![Controles de segurança] (./media/active-directory-saas-coupa-tutorial/IC791900.png "Controles de segurança")

10. Na seção **Faça logon usando credenciais de Coupa** , execute as seguintes etapas:

    ![Faça logon usando credenciais de Coupa] (./media/active-directory-saas-coupa-tutorial/IC791906.png "Faça logon usando credenciais de Coupa")

    1.  Selecione **Faça logon usando SAML**.
    2.  Clique em **Procurar** para carregar o arquivo de metadados do Azure Active baixado.
    3.  Clique em **Salvar**.

11. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-coupa-tutorial/IC791907.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login Coupa, ele devem ser provisionados em Coupa.  
No caso de Coupa, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **Coupa** como administrador.

2.  No menu na parte superior, clique em **configuração**e, em seguida, clique em **usuários**.

    ![Usuários] (./media/active-directory-saas-coupa-tutorial/IC791908.png "Usuários")

3.  Clique em **criar**.

    ![Criar usuários] (./media/active-directory-saas-coupa-tutorial/IC791909.png "Criar usuários")

4.  Na seção **Criar usuário** , execute as seguintes etapas:

    ![Detalhes do usuário] (./media/active-directory-saas-coupa-tutorial/IC791910.png "Detalhes do usuário")

    1.  Digite o **Login**, **nome**, **Sobrenome**, **ID de logon único**, atributos de **Email** de uma conta do Active Directory do Azure válida que deseja provisionar em caixas de texto relacionadas.
    2.  Clique em **criar**.

    >[AZURE.NOTE] O proprietário da conta do Active Directory do Azure receberão um email com um link para confirmar a conta antes que ela se torna ativa.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Coupa usuário conta ou APIs fornecidas pela Coupa para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-coupa-perform-the-following-steps"></a>Para atribuir usuários a Coupa, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Coupa **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-coupa-tutorial/IC791911.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-coupa-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
