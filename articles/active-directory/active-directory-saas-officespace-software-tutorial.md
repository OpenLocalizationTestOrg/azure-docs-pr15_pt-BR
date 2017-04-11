<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Software de OfficeSpace | Microsoft Azure" 
    description="Saiba como usar o Software de OfficeSpace com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Tutorial: Integração com o Active Directory do Azure com Software de OfficeSpace
  
O objetivo deste tutorial é mostrar a integração do Azure e OfficeSpace Software.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Software de OfficeSpace logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que atribuiu ao Software de OfficeSpace poderão único entrar no aplicativo no site da sua empresa OfficeSpace Software (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo de Software de OfficeSpace
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-officespace-software-tutorial/IC777764.png "Cenário")
##<a name="enabling-the-application-integration-for-officespace-software"></a>Habilitar a integração de aplicativo de Software de OfficeSpace
  
O objetivo desta seção é como habilitar a integração de aplicativo de Software de OfficeSpace da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-officespace-software-perform-the-following-steps"></a>Para habilitar a integração de aplicativo de Software de OfficeSpace, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-officespace-software-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-officespace-software-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-officespace-software-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-officespace-software-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **OfficeSpace Software**.

    ![Galeria de aplicativo] (./media/active-directory-saas-officespace-software-tutorial/IC777765.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **OfficeSpace Software**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Software de OfficeSpace] (./media/active-directory-saas-officespace-software-tutorial/IC781007.png "Software de OfficeSpace")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar ao Software de OfficeSpace com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para OfficeSpace Software requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo do **Software de OfficeSpace** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único = em] (./media/active-directory-saas-officespace-software-tutorial/IC777766.png "Configurar o logon único = em")

2.  Na página **como você gostaria que usuários para entrar Software de OfficeSpace** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-officespace-software-tutorial/IC777767.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **OfficeSpace Software entrada na URL** , digite a URL usada pelos usuários para entrar seu aplicativo de Software de OfficeSpace (por exemplo: "*https://company.officespacesoftware.com*") e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-officespace-software-tutorial/IC775556.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único na OfficeSpace Software** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-officespace-software-tutorial/IC793769.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa de Software de OfficeSpace como administrador.

6.  Vá para **administrador \> conectores**.

    ![Administração] (./media/active-directory-saas-officespace-software-tutorial/IC777769.png "Administração")

7.  Clique em **autorização de SAML**.

    ![Conectores] (./media/active-directory-saas-officespace-software-tutorial/IC777770.png "Conectores")

8.  Na seção **Autorização SAML** , execute as seguintes etapas:

    ![Configuração de SAML] (./media/active-directory-saas-officespace-software-tutorial/IC777771.png "Configuração de SAML")

    1.  No portal do clássico Azure, na página da caixa de diálogo **Configurar logon único na OfficeSpace Software** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **url do provedor de Logout** .
    2.  No portal do clássico Azure, na página da caixa de diálogo **Configurar logon único na OfficeSpace Software** , copie o valor de **URL Logout remota** e, em seguida, cole a **url de destino do cliente idp** de caixa de texto.
    3.  Copie o valor de **impressão digital** do certificado exportado e, em seguida, cole a caixa de texto de **impressão digital de certificado de cliente idp** .  

        >[AZURE.TIP]
        Para obter mais detalhes, consulte [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    4.  Clique em **Salvar configurações**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-officespace-software-tutorial/IC777772.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD efetuar login no Software de OfficeSpace, ele devem ser provisionados no Software de OfficeSpace. No caso de Software de OfficeSpace, provisionamento é uma tarefa automatizada.  
Não há nenhum item de ação para você.  
Os usuários são criados automaticamente se for necessário durante a primeira única logon tentativa.

>[AZURE.NOTE]Você pode usar qualquer ferramentas de criação de outro Software de OfficeSpace usuário conta ou APIs fornecidos pelo Software de OfficeSpace para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-officespace-software-perform-the-following-steps"></a>Para atribuir usuários a OfficeSpace Software, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo de **OfficeSpace Software **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-officespace-software-tutorial/IC777773.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-officespace-software-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).