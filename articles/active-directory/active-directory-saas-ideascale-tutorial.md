<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com IdeaScale | Microsoft Azure" 
    description="Saiba como usar IdeaScale com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Tutorial: Integração com o Active Directory do Azure com IdeaScale
  
O objetivo deste tutorial é mostrar a integração do Azure e IdeaScale.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um IdeaScale logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a IdeaScale será capazes de logon único para o aplicativo usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para IdeaScale
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-ideascale-tutorial/IC790838.png "Cenário")
##<a name="enabling-the-application-integration-for-ideascale"></a>Habilitar a integração de aplicativo para IdeaScale
  
O objetivo desta seção é como habilitar a integração de aplicativo para IdeaScale da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-ideascale-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para IdeaScale, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-ideascale-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-ideascale-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-ideascale-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-ideascale-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **IdeaScale**.

    ![Galeria de aplicativo] (./media/active-directory-saas-ideascale-tutorial/IC790841.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **IdeaScale**e clique em **concluído** para adicionar o aplicativo.

    ![IdeaScale] (./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar IdeaScale com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para IdeaScale requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **IdeaScale** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar IdeaScale** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **IdeaScale entrada na URL** , digite a URL usada pelos usuários para entrar seu aplicativo de IdeaScale (por exemplo: "*https://company.IdeaScale.com*") e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-ideascale-tutorial/IC790845.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em IdeaScale** , para baixar os metadados, clique em **baixar metadados**e salve o arquivo de metadados localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa IdeaScale como administrador.

6.  Vá para **configurações da comunidade**.

    ![Configurações da comunidade] (./media/active-directory-saas-ideascale-tutorial/IC790847.png "Configurações da comunidade")

7.  Vá para **segurança \> simples em configurações de sessão**.

    ![Configurações de sessão única] (./media/active-directory-saas-ideascale-tutorial/IC790848.png "Configurações de sessão única")

8.  Como **Entrar único tipo**, selecione **SAML 2.0**.

    ![Tipo de sessão única] (./media/active-directory-saas-ideascale-tutorial/IC790849.png "Tipo de sessão única")

9.  Na caixa de diálogo **Configurações de sessão única** , execute as seguintes etapas:

    ![Configurações de sessão única] (./media/active-directory-saas-ideascale-tutorial/IC790850.png "Configurações de sessão única")

    1.  No portal do clássico Azure, na página de diálogo **Configurar logon único em IdeaScale** , copie o valor de **ID de entidade** e, em seguida, cole a caixa de texto de **ID de entidade IdP SAML** .
    2.  Copie o conteúdo do seu arquivo de metadados baixado e cole-o na caixa de texto **SAML IdP metadados** .
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único em IdeaScale** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL de sucesso Logout** .
    4.  Clique em **Salvar alterações**.

10. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login IdeaScale, ele devem ser provisionados em IdeaScale.  
No caso de IdeaScale, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **IdeaScale** como administrador.

2.  Vá para **configurações da comunidade**.

    ![Configurações da comunidade] (./media/active-directory-saas-ideascale-tutorial/IC790847.png "Configurações da comunidade")

3.  Vá para **configurações básicas de \> gerenciamento de membros**.

4.  Clique em **Adicionar membro**.

    ![Gerenciamento de membros] (./media/active-directory-saas-ideascale-tutorial/IC790852.png "Gerenciamento de membros")

5.  Na seção Adicionar novo membro, execute as seguintes etapas:

    ![Adicionar novo membro] (./media/active-directory-saas-ideascale-tutorial/IC790853.png "Adicionar novo membro")

    1.  Na caixa de texto **Endereços de Email** , digite o endereço de email de uma conta AAD válida que deseja provisionar.
    2.  Clique em **Salvar alterações**.

    >[AZURE.NOTE] O proprietário da conta do Active Directory do Azure receberão um email com um link para confirmar a conta antes que ela se torna ativa.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros IdeaScale usuário conta ou APIs fornecidas pela IdeaScale para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-ideascale-perform-the-following-steps"></a>Para atribuir usuários a IdeaScale, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **IdeaScale **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-ideascale-tutorial/IC790854.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-ideascale-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).