<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Lynda.com | Microsoft Azure" 
    description="Saiba como usar Lynda.com com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-lyndacom"></a>Tutorial: Integração com o Active Directory do Azure com Lynda.com
  
O objetivo deste tutorial é mostrar a integração do Azure e Lynda.com.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário Lynda.com
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Lynda.com poderão único entrar no aplicativo no site da sua empresa Lynda.com (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Lynda.com
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-lynda-tutorial/IC781046.png "Cenário")
##<a name="enabling-the-application-integration-for-lyndacom"></a>Habilitar a integração de aplicativo para Lynda.com
  
O objetivo desta seção é como habilitar a integração de aplicativo para Lynda.com da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-lyndacom-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Lynda.com, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-lynda-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-lynda-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-lynda-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-lynda-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Lynda.com**.

    ![Galeria de aplicativo] (./media/active-directory-saas-lynda-tutorial/IC777524.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Lynda.com**e clique em **concluído** para adicionar o aplicativo.

    ![Lynda.com] (./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Lynda.com com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

>[AZURE.IMPORTANT]Para poder configurar o logon único em seu locatário Lynda.com, é necessário primeiro contatar o suporte técnico Lynda.com para acessar esse recurso ativado.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Lynda.com** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-lynda-tutorial/IC777526.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Lynda.com** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-lynda-tutorial/IC777527.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Lynda.com URL de entrada** , digite a URL de locatário Lynda.com (por exemplo: *https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target= https://shib.lynda.com/InCommon*) e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-lynda-tutorial/IC781047.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Lynda.com** , para baixar os metadados, clique em **baixar metadados**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-lynda-tutorial/IC777529.png "Configurar o logon único")

5.  Envie o arquivo baixado metadados para a equipe de suporte de Lynda.com. A equipe de suporte Lynda.com faz a configuração de logon único para você.

6.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-lynda-tutorial/IC777530.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Não há nenhum item de ação para configurar a Lynda.com de provisionamento do usuário.  
Quando um usuário atribuído tenta faça logon em Lynda.com usando o painel de acesso, Lynda.com verifica se o usuário existe.  
Se houver nenhuma conta de usuário ainda disponível, ele será criado automaticamente pelo Lynda.com.

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros Lynda.com usuário conta ou APIs fornecidas pela Lynda.com para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-lyndacom-perform-the-following-steps"></a>Para atribuir usuários a Lynda.com, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Lynda.com **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-lynda-tutorial/IC777531.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-lynda-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).