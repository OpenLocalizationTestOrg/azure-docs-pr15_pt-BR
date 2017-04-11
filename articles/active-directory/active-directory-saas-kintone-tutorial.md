<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Kintone | Microsoft Azure" 
    description="Saiba como usar Kintone com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="09/01/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-kintone"></a>Tutorial: Integração com o Active Directory do Azure com Kintone
  
O objetivo deste tutorial é mostrar a integração do Azure e Kintone.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Kintone logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Kintone poderão único entrar no aplicativo no site da sua empresa Kintone (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Kintone
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-kintone-tutorial/IC785859.png "Cenário")
##<a name="enabling-the-application-integration-for-kintone"></a>Habilitar a integração de aplicativo para Kintone
  
O objetivo desta seção é como habilitar a integração de aplicativo para Kintone da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-kintone-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Kintone, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-kintone-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-kintone-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-kintone-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-kintone-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Kintone**.

    ![Galeria de aplicativo] (./media/active-directory-saas-kintone-tutorial/IC785867.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Kintone**e clique em **concluído** para adicionar o aplicativo.

    ![Kintone] (./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Kintone com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Kintone** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-kintone-tutorial/IC785872.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Kintone** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-kintone-tutorial/IC785873.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Kintone entrada na URL** , digite sua URL usando o seguinte padrão "*https://company.kintone.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-kintone-tutorial/IC785875.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Kintone** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-kintone-tutorial/IC785878.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa **Kintone** como administrador.

6.  Clique em **configurações**.

    ![Configurações] (./media/active-directory-saas-kintone-tutorial/IC785879.png "Configurações")

7.  Clique em **usuários e administração do sistema**.

    ![Usuários e administração do sistema] (./media/active-directory-saas-kintone-tutorial/IC785880.png "Usuários e administração do sistema")

8.  Em **Administração do sistema \> segurança** clique em **Login**.

    ![Fazer login] (./media/active-directory-saas-kintone-tutorial/IC785881.png "Fazer login")

9.  Clique em **Habilitar SAML autenticação**.

    ![Autenticação de SAML] (./media/active-directory-saas-kintone-tutorial/IC785882.png "Autenticação de SAML")

10. Na seção autenticação de SAML, execute as seguintes etapas:

    ![Autenticação de SAML] (./media/active-directory-saas-kintone-tutorial/IC785883.png "Autenticação de SAML")

    1.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Kintone** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login** .
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Kintone** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL de Logout** .
    3.  Clique em **Procurar** para carregar seu certificado baixado.
    4.  Clique em **Salvar**.

11. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-kintone-tutorial/IC785884.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Kintone, ele devem ser provisionados em Kintone.  
No caso de Kintone, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **Kintone** como administrador.

2.  Clique em **configuração**.

    ![Configurações] (./media/active-directory-saas-kintone-tutorial/IC785879.png "Configurações")

3.  Clique em **usuários e administração do sistema**.

    ![Administração de sistema & usuário] (./media/active-directory-saas-kintone-tutorial/IC785880.png "Administração de sistema & usuário")

4.  Em **Administração do usuário**, clique em **departamentos e usuários**.

    ![Departamento & usuários] (./media/active-directory-saas-kintone-tutorial/IC785888.png "Departamento & usuários")

5.  Clique em **novo usuário**.

    ![Novos usuários] (./media/active-directory-saas-kintone-tutorial/IC785889.png "Novos usuários")

6.  Na seção **Novo usuário** , execute as seguintes etapas:

    ![Novos usuários] (./media/active-directory-saas-kintone-tutorial/IC785890.png "Novos usuários")

    1.  Digite um **Nome para exibição**, **Nome de Login**, **Nova senha**, **Confirme a senha**, **Endereço de email** e outros detalhes de uma conta AAD válida que deseja provisionar para o texboxes relacionados.
    2.  Clique em **Salvar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Kintone usuário conta ou APIs fornecidas pela Kintone para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-kintone-perform-the-following-steps"></a>Para atribuir usuários a Kintone, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Kintone **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-kintone-tutorial/IC785891.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-kintone-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).