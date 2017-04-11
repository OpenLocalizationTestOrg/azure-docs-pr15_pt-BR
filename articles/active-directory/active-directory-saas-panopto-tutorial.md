<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Panopto | Microsoft Azure" 
    description="Saiba como usar Panopto com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-panopto"></a>Tutorial: Integração com o Active Directory do Azure com Panopto
  
O objetivo deste tutorial é mostrar a integração do Azure e Panopto.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário Panopto
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Panopto poderão único entrar no aplicativo no site da sua empresa Panopto (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Panopto
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-panopto-tutorial/IC777665.png "Cenário")
##<a name="enabling-the-application-integration-for-panopto"></a>Habilitar a integração de aplicativo para Panopto
  
O objetivo desta seção é como habilitar a integração de aplicativo para Panopto da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-panopto-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Panopto, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-panopto-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-panopto-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-panopto-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-panopto-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Panopto**.

    ![Galeria de Appkication] (./media/active-directory-saas-panopto-tutorial/IC777666.png "Galeria de Appkication")

7.  No painel de resultados, selecione **Panopto**e clique em **concluído** para adicionar o aplicativo.

    ![Panopto] (./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Panopto com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Panopto** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-panopto-tutorial/IC777667.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Panopto** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-panopto-tutorial/IC777668.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Panopto URL de entrada** , digite sua URL usando o seguinte padrão "https://*\<-nome do locatário\>. Panopto.com*"e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-panopto-tutorial/IC777528.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Panopto** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-panopto-tutorial/IC777669.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Panopto como administrador.

6.  Na barra de ferramentas à esquerda, clique em **sistema**e clique em **Provedores de identidade**.

    ![Sistema] (./media/active-directory-saas-panopto-tutorial/IC777670.png "Sistema")

7.  Clique em **Adicionar provedor**.

    ![Provedores de identidade] (./media/active-directory-saas-panopto-tutorial/IC777671.png "Provedores de identidade")

8.  Na seção de provedor SAML, execute as seguintes etapas:

    ![Configuração de SaaS] (./media/active-directory-saas-panopto-tutorial/IC777672.png "Configuração de SaaS")

    1.  Na lista **Tipo de provedor** , selecione **SAML20**
    2.  Na caixa de texto **Nome da instância** , digite um nome para a instância.
    3.  Na caixa de texto **Descrição amigável** , digite uma descrição amigável.
    4.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Panopto** , copie o valor de **URL do emissor** e, em seguida, cole a caixa de texto do **emissor** .
    5.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Panopto** , copie o valor de **SAML SSO URL** e cole-o na caixa de texto **Pular Url da página** .
    6.  Crie um arquivo **codificado na base 64** do seu certificado baixado.  

        >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    7.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo para a caixa de texto de **Chave pública**
    8.  Clique em **Salvar**.
        ![Salvar] (./media/active-directory-saas-panopto-tutorial/IC777673.png "Salvar")

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-panopto-tutorial/IC777674.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Não há nenhum item de ação para configurar a Panopto de provisionamento do usuário.  
Quando um usuário atribuído tenta faça logon em Panopto usando o painel de acesso, Panopto verifica se o usuário existe.  
Se houver nenhuma conta de usuário ainda disponível, ele será criado automaticamente pelo Panopto.

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros Panopto usuário conta ou APIs fornecidas pela Panopto para provisionar contas de usuário do Azure AD.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-panopto-perform-the-following-steps"></a>Para atribuir usuários a Panopto, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Panopto **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-panopto-tutorial/IC777675.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-panopto-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).