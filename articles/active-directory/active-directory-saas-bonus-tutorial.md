<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Bonus.ly | Microsoft Azure" 
    description="Saiba como usar Bonus.ly com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: Integração com o Active Directory do Azure com Bonus.ly

O objetivo deste tutorial é mostrar a integração do Azure e Bonus.ly. O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário de teste em Bonus.ly

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Bonus.ly
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-bonus-tutorial/IC773679.png "Cenário")
##<a name="enabling-the-application-integration-for-bonusly"></a>Habilitar a integração de aplicativo para Bonus.ly

O objetivo desta seção é como habilitar a integração de aplicativo para Bonus.ly da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-bonusly-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Bonus.ly, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Habilitar o logon único] (./media/active-directory-saas-bonus-tutorial/IC773680.png "Habilitar o logon único")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-bonus-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-bonus-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-bonus-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Bonus.ly**.

    ![Galeria de aplicativo] (./media/active-directory-saas-bonus-tutorial/IC773681.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Bonus.ly**e clique em **concluído** para adicionar o aplicativo.

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar Bonus.ly com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para Bonus.ly requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Bonus.ly** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-bonus-tutorial/IC749323.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Bonus.ly** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-bonus-tutorial/IC773683.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Bonus.ly locatário URL** , digite sua URL usando o seguinte padrão "https://*\<-nome do locatário\>. Bonus.ly*"e, em seguida, clique em **Avançar**: 

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-bonus-tutorial/IC773684.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Bonus.ly** , clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado localmente como **c:\\Bonusly.cer**.

    ![Configurar o logon único] (./media/active-directory-saas-bonus-tutorial/IC773685.png "Configurar o logon único")

5.  Em uma janela de navegador diferente, faça logon em seu locatário **Bonus.ly** .

6.  Na barra de ferramentas na parte superior, clique em **configurações**e selecione **integrações e aplicativos**.

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")

7.  Em **Single Sign-On**, selecione **SAML**.

8.  Na página de diálogo **SAML** , execute as seguintes etapas:

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")

    1.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Bonus.ly** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de destino IdP SSO** .
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Bonus.ly** , copie o valor de **Identificação do emissor** e cole-o na caixa de texto **IdP emissor** .
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Bonus.ly** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de logon IdP** .
    4.  Copie o valor de **impressão digital** do certificado exportado e, em seguida, cole a caixa de texto de **Impressão digital de certificado** .

        >[AZURE.TIP] Para obter mais detalhes, consulte [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

9.  Clique em **Salvar**.

10. No portal do Microsoft Azure clássico, selecione a confirmação de configuração e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-bonus-tutorial/IC773689.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login Bonus.ly, ele devem ser provisionados em Bonus.ly.  
No caso de Bonus.ly, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Na janela do navegador da web, faça logon em seu locatário Bonus.ly.

2.  Clique em **configurações**

    ![Configurações] (./media/active-directory-saas-bonus-tutorial/IC781041.png "Configurações")

3.  Clique na guia **usuários e bônus** .

    ![Usuários e bônus] (./media/active-directory-saas-bonus-tutorial/IC781042.png "Usuários e bônus")

4.  Clique em **Gerenciar usuários**.

    ![Gerenciar usuários] (./media/active-directory-saas-bonus-tutorial/IC781043.png "Gerenciar usuários")

5.  Clique em **Adicionar usuário**.

    ![Adicionar usuário] (./media/active-directory-saas-bonus-tutorial/IC781044.png "Adicionar usuário")

6.  Na caixa de diálogo **Adicionar usuário** , execute as seguintes etapas:

    ![Adicionar usuário] (./media/active-directory-saas-bonus-tutorial/IC781045.png "Adicionar usuário")

    1.  Digite o "**Email**, **nome**, **Sobrenome**" de uma conta AAD válida que deseja provisionar em caixas de texto relacionadas.
    2.  Clique em **Salvar**.

    >[AZURE.NOTE] O proprietário de conta AAD receberão um email que inclui um link para confirmar a conta antes que ela se torna ativa.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Bonus.ly usuário conta ou APIs fornecidas pela Bonus.ly para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-bonusly-perform-the-following-steps"></a>Para atribuir usuários a Bonus.ly, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo Bonus.ly, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-bonus-tutorial/IC773690.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-bonus-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
