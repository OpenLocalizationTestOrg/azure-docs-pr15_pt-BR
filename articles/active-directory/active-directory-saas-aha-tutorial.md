<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure com Aha! | Microsoft Azure" 
    description="Saiba como usar Aha! com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-aha"></a>Tutorial: Integração do Active Directory do Azure com Aha!

O objetivo deste tutorial é mostrar a integração do Azure e Aha!  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um AH! logon único habilitado para assinatura

Depois de concluir este tutorial, os usuários do Azure AD você atribuiu a Aha! serão capazes de logon único para o aplicativo em seu Aha! site de empresa (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Aha!
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-aha-tutorial/IC798944.png "Cenário")
##<a name="enabling-the-application-integration-for-aha"></a>Habilitar a integração de aplicativo para Aha!

O objetivo desta seção é destacar como habilitar a integração de aplicativo para Aha!.

###<a name="to-enable-the-application-integration-for-aha-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Aha!, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-aha-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-aha-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-aha-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-aha-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Aha!**.

    ![Galeria de aplicativo] (./media/active-directory-saas-aha-tutorial/IC798945.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Aha!**e clique em **concluído** para adicionar o aplicativo.

    ![AH!] (./media/active-directory-saas-aha-tutorial/IC802746.png "AH!")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é destacar como habilitar usuários autenticar Aha! com sua conta no Azure AD usando a federação com base no protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal de clássico do Azure, sobre o **Aha!** integração com o aplicativo da página, clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-aha-tutorial/IC798946.png "Configurar o logon único")

2.  Sobre o **como você gostaria que os usuários para assinar Aha!** página, selecione **Microsoft Azure AD Single Sign-On**e clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-aha-tutorial/IC798947.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na **Aha! Entrar na URL** caixa de texto, digite a URL usada pelos usuários para logon para seu Aha! Aplicativo (por exemplo: "*https://company.aha.io/session/new*") e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-aha-tutorial/IC798948.png "Configurar a URL do aplicativo")

4.  Sobre o **Configurar logon único no Aha!** página, para baixar o arquivo de metadados, clique em **baixar metadados**e salve o arquivo de metadados localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-aha-tutorial/IC798949.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em sua Aha! site de empresa como administrador.

6.  No menu na parte superior, clique em **configurações**.

    ![Configurações] (./media/active-directory-saas-aha-tutorial/IC798950.png "Configurações")

7.  Clique em **conta**.

    ![Perfil] (./media/active-directory-saas-aha-tutorial/IC798951.png "Perfil")

8.  Clique em **segurança e logon único**.

    ![Segurança e logon único] (./media/active-directory-saas-aha-tutorial/IC798952.png "Segurança e logon único")

9.  Na seção **Single Sign-On** , como **Provedor de identidade**, selecione **SAML2.0**.

    ![Segurança e logon único] (./media/active-directory-saas-aha-tutorial/IC798953.png "Segurança e logon único")

10. Na página configuração **Single Sign-On** , execute as seguintes etapas:

    ![Logon único] (./media/active-directory-saas-aha-tutorial/IC798954.png "Logon único")

    1.  Na caixa de texto **nome** , digite um nome para a sua configuração.
    2.  Para **Configurar usando**, selecione o **Arquivo metadados**.
    3.  Para carregar o arquivo de metadados baixado, clique em **Procurar**.
    4.  Clique em **Atualizar**.

11. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-aha-tutorial/IC798955.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que os usuários do Azure AD fazer login Aha!, eles devem ser provisionados em Aha!.  
No caso de Aha!, provisionamento é uma tarefa automatizada.  
Não há nenhum item de ação para você.
  
Os usuários são criados automaticamente se for necessário durante a primeira única logon tentativa.

>[AZURE.NOTE] Você pode usar qualquer outro Aha! ferramentas de criação de conta de usuário ou APIs fornecidas pela Aha! Configurar contas de usuário do AAD.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-aha-perform-the-following-steps"></a>Para atribuir usuários Aha!, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Sobre o **AH!** integração de aplicativo página, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-aha-tutorial/IC798956.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-aha-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
