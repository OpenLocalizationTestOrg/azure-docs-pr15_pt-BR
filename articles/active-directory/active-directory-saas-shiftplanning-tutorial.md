<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com ShiftPlanning | Microsoft Azure" 
    description="Saiba como usar ShiftPlanning com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-shiftplanning"></a>Tutorial: Integração com o Active Directory do Azure com ShiftPlanning
  
O objetivo deste tutorial é mostrar a integração do Azure e ShiftPlanning.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um ShiftPlanning logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a ShiftPlanning poderão único entrar no aplicativo no site da sua empresa ShiftPlanning (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para ShiftPlanning
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-shiftplanning-tutorial/IC786612.png "Cenário")
##<a name="enabling-the-application-integration-for-shiftplanning"></a>Habilitar a integração de aplicativo para ShiftPlanning
  
O objetivo desta seção é como habilitar a integração de aplicativo para ShiftPlanning da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-shiftplanning-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para ShiftPlanning, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-shiftplanning-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-shiftplanning-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-shiftplanning-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-shiftplanning-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **ShiftPlanning**.

    ![Galeria de aplicativo] (./media/active-directory-saas-shiftplanning-tutorial/IC786613.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **ShiftPlanning**e clique em **concluído** para adicionar o aplicativo.

    ![ShiftPlanning] (./media/active-directory-saas-shiftplanning-tutorial/IC786614.png "ShiftPlanning")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar ShiftPlanning com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **ShiftPlanning** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-shiftplanning-tutorial/IC786615.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar ShiftPlanning** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-shiftplanning-tutorial/IC786616.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **ShiftPlanning entrada na URL** , digite sua URL usando o seguinte padrão "*https://company.shiftplanning.com/includes/saml/*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-shiftplanning-tutorial/IC786617.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em ShiftPlanning** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-shiftplanning-tutorial/IC786618.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa **ShiftPlanning** como administrador.

6.  No menu na parte superior, clique em **administrador**.

    ![Administração] (./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Administração")

7.  Em **integração**, clique em **Logon único**.

    ![Logon único] (./media/active-directory-saas-shiftplanning-tutorial/IC786620.png "Logon único")

8.  Na seção **Single Sign-On** , execute as seguintes etapas:

    ![Logon único] (./media/active-directory-saas-shiftplanning-tutorial/IC786905.png "Logon único")

    1.  Selecione **SAML habilitado**.
    2.  Selecione **Permitir logon de senha**
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único em ShiftPlanning** , copie o valor de **URL de Login remoto** e, em seguida, cole a **URL do emissor SAML** de caixa de texto.
    4.  No portal do clássico Azure, na página de diálogo **Configurar logon único em ShiftPlanning** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL Logout remota** .
    5.  Crie um arquivo **codificado na base 64** do seu certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo para a caixa de texto de **Certificado x. 509**
    7.  Clique em **Salvar configurações**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-shiftplanning-tutorial/IC786621.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login ShiftPlanning, ele devem ser provisionados em ShiftPlanning.  
No caso de ShiftPlanning, provisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **ShiftPlanning** como administrador.

2.  Clique em **administrador**.

    ![Administração] (./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Administração")

3.  Clique em **equipe**.

    ![Equipe] (./media/active-directory-saas-shiftplanning-tutorial/IC786623.png "Equipe")

4.  Em **ações**, clique em **Adicionar funcionário**.

    ![Adicionar funcionários] (./media/active-directory-saas-shiftplanning-tutorial/IC786624.png "Adicionar funcionários")

5.  Na seção **Adicionar funcionários** , execute as seguintes etapas:

    ![Salvar os funcionários] (./media/active-directory-saas-shiftplanning-tutorial/IC786625.png "Salvar os funcionários")

    1.  Digite o **nome**, **Sobrenome** e **Email** de uma conta AAD válida que você deseja configurar para as caixas de texto relacionadas.
    2.  Clique em **Salvar funcionários**.

>[AZURE.NOTE]Você pode usar as ferramentas de criação de outros ShiftPlanning usuário conta ou APIs fornecidas pela ShiftPlanning para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-shiftplanning-perform-the-following-steps"></a>Para atribuir usuários a ShiftPlanning, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **ShiftPlanning **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-shiftplanning-tutorial/IC786626.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-shiftplanning-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).