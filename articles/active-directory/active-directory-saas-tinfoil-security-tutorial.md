<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com segurança Tinfoil | Microsoft Azure"
    description="Saiba como usar Tinfoil segurança com o Active Directory do Azure para habilitar o logon único, automatizado de provisionamento e muito mais!." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Tutorial: Integração com o Active Directory do Azure com segurança Tinfoil
  
O objetivo deste tutorial é mostrar a integração do Azure e segurança Tinfoil.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Tinfoil segurança logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a segurança Tinfoil poderão único entrar no aplicativo no site da sua empresa Tinfoil segurança (sinal de identidade provedor iniciada em), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo de segurança de Tinfoil
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Configurar o logon único] (./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "Configurar o logon único")

##<a name="enabling-the-application-integration-for-tinfoil-security"></a>Habilitar a integração de aplicativo de segurança de Tinfoil
  
O objetivo desta seção é como habilitar a integração de aplicativo para Tinfoil segurança da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-tinfoil-security-perform-the-following-steps"></a>Para habilitar a integração de aplicativo de segurança de Tinfoil, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Tinfoil segurança**.

    ![Galeria de aplicativo] (./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Tinfoil segurança**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Segurança de tinfoil] (./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Segurança de tinfoil")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Tinfoil segurança com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar o logon único para segurança Tinfoil requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Tinfoil segurança** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "Configurar o logon único")

2.  Na página **como você gostaria que usuários para entrar segurança de Tinfoil** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **URL de resposta de segurança Tinfoil** , digite sua URL Tinfoil segurança declaração consumidor ACS (serviço) (por exemplo: "*https://www.tinfoilsecurity.com/saml/consume*" e, em seguida, clique em **Avançar**.

    >[AZURE.NOTE] Você deve ser capaz de obter a URL do ACS de metadados de segurança Tinfoil (https://www.tinfoilsecurity.com/saml/metadata).

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em segurança de Tinfoil** , para baixar o certificado, clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado localmente como **c:\\Tinfoil Security.cer**.

    ![Configurar o logon único] (./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa de segurança Tinfoil como administrador.

6.  Na barra de ferramentas na parte superior, clique em **Minha conta**.

    ![Painel de controle] (./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "Painel de controle")

7.  Clique em **segurança**.

    ![Segurança] (./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "Segurança")

8.  Na página configuração **Single Sign-On** , execute as seguintes etapas:

    ![Logon único] (./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "Logon único")

    1.  Selecione **Habilitar SAML**.
    2.  Clique em **Configuração Manual**.
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único em segurança de Tinfoil** , copie o valor de **SAML SSO URL** e cole-o na caixa de texto **URL de postagem SAML** .
    4.  Copie o valor de **impressão digital** do certificado exportado e, em seguida, cole a caixa de texto de **Impressão digital de certificado SAML** .  

        >[AZURE.TIP] Para obter mais detalhes, consulte [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    5.  Copie **sua ID de conta**.
    6.  Clique em **Salvar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "Configurar o logon único")

10. No menu na parte superior, clique em **atributos** para abrir a caixa de diálogo de **Atributos de Token SAML** .

    ![Atributos] (./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "Atributos")

11. Para adicionar os mapeamentos de atributo obrigatório, execute as seguintes etapas:

    ![Atributos] (./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "Atributos")

    1.  Clique em **Adicionar atributo de usuário**.
    2.  Na caixa de texto **Nome do atributo** , digite **accountid**.
    3.  Na caixa de texto **Valor do atributo** , cole o valor de ID de conta que você copiou na seção anterior.
    4.  Clique em **Concluir**.

12. Clique em **Aplicar alterações**.

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Tinfoil segurança, ele devem ser provisionados em segurança de Tinfoil.  
No caso de segurança Tinfoil, provisionamento é uma tarefa manual.

###<a name="to-get-a-user-provisioned-perform-the-following-steps"></a>Para obter um usuário provisionado, execute as seguintes etapas:

1.  Se o usuário for parte de uma conta da empresa, você precise contatar a equipe de suporte de segurança de Tinfoil para acessar a conta de usuário criada.

2.  Se o usuário for um usuário Tinfoil segurança SaaS normal, o usuário pode adicionar um colaborador para qualquer um dos sites do usuário. Isso dispara um processo para enviar um convite para o email especificado para criar uma nova conta de usuário de segurança de Tinfoil.

>[AZURE.NOTE] Você pode usar qualquer outras Tinfoil usuário conta criação ferramentas de segurança ou APIs fornecidas pela Tinfoil segurança para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-tinfoil-security-perform-the-following-steps"></a>Para atribuir usuários a segurança de Tinfoil, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo de **Segurança de Tinfoil **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).