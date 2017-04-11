<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Freshdesk | Microsoft Azure" 
    description="Saiba como usar Freshdesk com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: Integração com o Active Directory do Azure com Freshdesk
  
O objetivo deste tutorial é mostrar a integração do Azure e Freshdesk.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário Freshdesk
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Freshdesk poderão único entrar no aplicativo no site da sua empresa Freshdesk (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Freshdesk
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-freshdesk-tutorial/IC776761.png "Cenário")
##<a name="enabling-the-application-integration-for-freshdesk"></a>Habilitar a integração de aplicativo para Freshdesk
  
O objetivo desta seção é como habilitar a integração de aplicativo para Freshdesk da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-freshdesk-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Freshdesk, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-freshdesk-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-freshdesk-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-freshdesk-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-freshdesk-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Freshdesk**.

    ![Galeria de aplicativo] (./media/active-directory-saas-freshdesk-tutorial/IC776762.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Freshdesk**e clique em **concluído** para adicionar o aplicativo.

    ![Freshdesk] (./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Freshdesk com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para Freshdesk requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Freshdesk** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Freshdesk** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Freshdesk URL de entrada** , digite sua URL usando o seguinte padrão "https://*\<-nome do locatário\>. Freshdesk.com*"e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-freshdesk-tutorial/IC776766.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Freshdesk** , para baixar o certificado, clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado localmente como **c:\\Freshdesk.cer**.

    ![Configurar o logon único] (./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Freshdesk como administrador.

6.  No menu na parte superior, clique em **administrador**.

    ![Administração] (./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Administração")

7.  Na guia **Configurações gerais** , clique em **segurança**.

    ![Segurança] (./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Segurança")

8.  Na seção **segurança** , execute as seguintes etapas:

    ![Logon único] (./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Logon único")

    1.  **Para **Single Sign On (SSO)**, selecione.**
    2.  Selecione **SAML SSO**.
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Freshdesk** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de logon SAML** .
    4.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Freshdesk** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL de Logout** .
    5.  Copie o valor de **impressão digital** do certificado exportado e, em seguida, cole a caixa de texto de **Impressão digital de certificado de segurança** .  

        >[AZURE.TIP]Para obter mais detalhes, consulte [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    6.  Clique em **Salvar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Freshdesk, ele devem ser provisionados em Freshdesk.  
No caso de Freshdesk, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon em seu locatário **Freshdesk** .

2.  No menu na parte superior, clique em **administrador**.

    ![Administração] (./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Administração")

3.  Na guia **Configurações gerais** , clique em **agentes**.

    ![Agentes] (./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agentes")

4.  Clique em **novo agente**.

    ![Novo agente] (./media/active-directory-saas-freshdesk-tutorial/IC776774.png "Novo agente")

5.  Na caixa de diálogo informações do agente, execute as seguintes etapas:

    ![Informações do agente] (./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Informações do agente")

    1.  Na caixa de texto **Nome completo** , digite o nome da conta do Azure AD que deseja provisionar.
    2.  Na caixa de texto **Email** , digite o endereço de email do Azure AD da conta do Azure AD que deseja provisionar.
    3.  Na caixa de texto **título** , digite o título da conta do Azure AD que deseja provisionar.
    4.  Selecione a **função de agentes**e, em seguida, clique em **atribuir**.
    5.  Clique em **Salvar**.
    
        >[AZURE.NOTE] O proprietário da conta do Azure AD receberão um email que inclui um link para confirmar a conta antes que ele está ativado.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Freshdesk usuário conta ou APIs fornecidas pela Freshdesk para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-freshdesk-perform-the-following-steps"></a>Para atribuir usuários a Freshdesk, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Freshdesk **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-freshdesk-tutorial/IC776776.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).