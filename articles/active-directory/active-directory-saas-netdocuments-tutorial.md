<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com NetDocuments | Microsoft Azure" 
    description="Saiba como usar NetDocuments com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Tutorial: Integração com o Active Directory do Azure com NetDocuments
  
O objetivo deste tutorial é mostrar a integração do Azure e NetDocuments.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário NetDocuments
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a NetDocuments poderão único entrar no aplicativo no site da sua empresa NetDocuments (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para NetDocuments
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-netdocuments-tutorial/IC795040.png "Cenário")
##<a name="enabling-the-application-integration-for-netdocuments"></a>Habilitar a integração de aplicativo para NetDocuments
  
O objetivo desta seção é como habilitar a integração de aplicativo para NetDocuments da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-netdocuments-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para NetDocuments, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-netdocuments-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-netdocuments-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-netdocuments-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-netdocuments-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **NetDocuments**.

    ![Galeria de aplicativo] (./media/active-directory-saas-netdocuments-tutorial/IC795041.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **NetDocuments**e clique em **concluído** para adicionar o aplicativo.

    ![NetDocuments] (./media/active-directory-saas-netdocuments-tutorial/IC795042.png "NetDocuments")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar NetDocuments com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para NetDocuments requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **NetDocuments** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-netdocuments-tutorial/IC795043.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar NetDocuments** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-netdocuments-tutorial/IC795044.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , execute as seguintes etapas:

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-netdocuments-tutorial/IC795045.png "Configurar a URL do aplicativo")

    1.  Na caixa de texto de **Entrada na URL** , digite sua URL usada pelos usuários para entrar seu aplicativo de NetDocuments (por exemplo: "*https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=CA-JI1BG3H1*").
    2.  Na caixa de texto **URL de resposta NetDocuments** , digite o mesmo valor que você digitou para a caixa de texto **Entrada na URL** .  

        >[AZURE.NOTE]Você pode encontrar o valor correto no final da caixa de diálogo **Identidade federado** (consulte a captura de tela para a etapa 9).

    3.  Clique em **Avançar**

4.  Na página **Configurar logon único em NetDocuments** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-netdocuments-tutorial/IC795046.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa NetDocuments como administrador.

6.  Vá para **administrador**.

7.  Clique em **Adicionar e remover usuários e grupos**.

    ![Repositório] (./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Repositório")

8.  Clique em **Configurar avançadas opções de autenticação**.

    ![Configurar opções de autenticação de avançadas] (./media/active-directory-saas-netdocuments-tutorial/IC795048.png "Configurar opções de autenticação de avançadas")

9.  Na caixa de diálogo **A identidade federado** , execute as seguintes etapas:

    ![Federado Identitty] (./media/active-directory-saas-netdocuments-tutorial/IC795049.png "Federado Identitty")

    1.  Como o **tipo de servidor de identidade federados**, selecione **Os serviços de Federação do Active Directory**.
    2.  Clique em **Escolher arquivo**para carregar o arquivo baixado metadados.
    3.  Clique em **Okey**.

10. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-netdocuments-tutorial/IC795050.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login NetDocuments, ele devem ser provisionados em NetDocuments.  
No caso de NetDocuments, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Tocar o site da sua empresa de **NetDocuments** -lo como administrador.

2.  No menu na parte superior, clique em **administrador**.

    ![Administração] (./media/active-directory-saas-netdocuments-tutorial/IC795051.png "Administração")

3.  Clique em **Adicionar e remover usuários e grupos**.

    ![Repositório] (./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Repositório")

4.  Na caixa de texto **Endereço de Email** , digite o endereço de email de uma conta do Active Directory do Azure válida você deseja provisionar e, em seguida, clique em **Adicionar usuário**.

    ![Endereço de email] (./media/active-directory-saas-netdocuments-tutorial/IC795053.png "Endereço de email")

    >[AZURE.NOTE]O proprietário da conta do Active Directory do Azure receberão um email que inclui um link para confirmar a conta antes que ela se torna ativa.

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros NetDocuments usuário conta ou APIs fornecidas pela NetDocuments para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-netdocuments-perform-the-following-steps"></a>Para atribuir usuários a NetDocuments, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **NetDocuments **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-netdocuments-tutorial/IC795054.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-netdocuments-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).