<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com FreshService | Microsoft Azure" 
    description="Saiba como usar FreshService com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Tutorial: Integração com o Active Directory do Azure com FreshService
  
O objetivo deste tutorial é mostrar a integração do Azure e FreshService.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um FreshService logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a FreshService será capazes de logon único para o aplicativo usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para FreshService
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-freshservice-tutorial/IC790807.png "Cenário")
##<a name="enabling-the-application-integration-for-freshservice"></a>Habilitar a integração de aplicativo para FreshService
  
O objetivo desta seção é como habilitar a integração de aplicativo para FreshService da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-freshservice-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para FreshService, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-freshservice-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-freshservice-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-freshservice-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-freshservice-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **FreshService**.

    ![Galeria de aplicativo] (./media/active-directory-saas-freshservice-tutorial/IC790808.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **FreshService**e clique em **concluído** para adicionar o aplicativo.

    ![Freshservice] (./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar FreshService com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para FreshService requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **FreshService** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-freshservice-tutorial/IC790810.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar FreshService** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-freshservice-tutorial/IC790811.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **FreshService entrada na URL** , digite a URL usada pelos usuários para entrar seu aplicativo Freshdesk (por exemplo: "*http://democompany.freshservice.com/*") e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-freshservice-tutorial/IC790812.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em FreshService** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-freshservice-tutorial/IC790813.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa FreshService como administrador.

6.  No menu na parte superior, clique em **administrador**.

    ![Administração] (./media/active-directory-saas-freshservice-tutorial/IC790814.png "Administração")

7.  No **Portal do cliente**, clique em **segurança**.

    ![Segurança] (./media/active-directory-saas-freshservice-tutorial/IC790815.png "Segurança")

8.  Na seção **segurança** , execute as seguintes etapas:

    ![Logon único] (./media/active-directory-saas-freshservice-tutorial/IC790816.png "Logon único")

    1.  Alternar **OnON de logon único**.
    2.  Selecione **SAML SSO**.
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único em FreshService** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de logon SAML** .
    4.  No portal do clássico Azure, na página de diálogo **Configurar logon único em FreshService** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL de Logout** .
    5.  Copie o valor de **impressão digital** do certificado exportado e, em seguida, cole a caixa de texto de **Impressão digital de certificado de segurança** .
    
        >[AZURE.TIP]Para obter mais detalhes, consulte [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-freshservice-tutorial/IC790817.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login FreshService, ele devem ser provisionados em FreshService.  
No caso de FreshService, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **FreshService** como administrador.

2.  No menu na parte superior, clique em **administrador**.

    ![Administração] (./media/active-directory-saas-freshservice-tutorial/IC790814.png "Administração")

3.  Na seção **Gerenciamento de usuários** , clique em **solicitantes**.

    ![Solicitantes] (./media/active-directory-saas-freshservice-tutorial/IC790818.png "Solicitantes")

4.  Clique em **novo solicitante**.

    ![Novo solicitantes] (./media/active-directory-saas-freshservice-tutorial/IC790819.png "Novo solicitantes")

5.  Na seção **Nova solicitante** , execute as seguintes etapas:

    ![Novo solicitante] (./media/active-directory-saas-freshservice-tutorial/IC790820.png "Novo solicitante")

    1.  Insira os atributos de **nome** e **Email** de uma conta do Active Directory do Azure válida que desejar provisionar as caixas de texto relacionadas.
    2.  Clique em **Salvar**.

    >[AZURE.NOTE] O proprietário da conta do Active Directory do Azure receberão um email incluindo um link para confirmar a conta antes que ele fique ativo

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros FreshService usuário conta ou APIs fornecidas pela FreshService para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-freshservice-perform-the-following-steps"></a>Para atribuir usuários a FreshService, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **FreshService **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-freshservice-tutorial/IC790821.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-freshservice-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).