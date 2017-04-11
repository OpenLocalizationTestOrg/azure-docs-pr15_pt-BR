<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Jobscience | Microsoft Azure" 
    description="Saiba como usar Jobscience com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Tutorial: Integração com o Active Directory do Azure com Jobscience
  
O objetivo deste tutorial é mostrar a integração do Azure e Jobscience.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura Jobscience logon único habilitado
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Jobscience poderão único entrar no aplicativo no site da sua empresa Jobscience (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Jobscience
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-jobscience-tutorial/IC784341.png "Cenário")
##<a name="enabling-the-application-integration-for-jobscience"></a>Habilitar a integração de aplicativo para Jobscience
  
O objetivo desta seção é como habilitar a integração de aplicativo para Jobscience da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-jobscience-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Jobscience, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-jobscience-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-jobscience-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-jobscience-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-jobscience-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **jobscience**.

    ![Galeria de aplicativo] (./media/active-directory-saas-jobscience-tutorial/IC784342.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Jobscience**e clique em **concluído** para adicionar o aplicativo.

    ![Jobscience] (./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Jobscience com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurar single sign-on para Jobscience requer que você recuperar um valor de impressão digital de um certificado.  
Se você não estiver familiarizado com esse procedimento, veja [como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  Faça logon site da sua empresa Jobscience como administrador.

2.  Vá para **a configuração**.

    ![Configuração] (./media/active-directory-saas-jobscience-tutorial/IC784358.png "Configuração")

3.  No painel de navegação à esquerda, na seção **administrador** , clique em **Gerenciamento de domínios** para expandir a seção relacionada e, em seguida, clique em **Meu domínio** para abrir a página de **Meu domínio** . 

    ![Meu domínio] (./media/active-directory-saas-jobscience-tutorial/IC767825.png "Meu domínio")

4.  Para verificar que seu domínio foi configurado corretamente, certifique-se de que ele está em "**Etapa 4 implantado para usuários**" e examine seu "**Minhas configurações de domínio**".

    ![Domínio implantados em usuário] (./media/active-directory-saas-jobscience-tutorial/IC784377.png "Domínio implantados em usuário")

5.  Em uma janela de navegador da web diferente, faça logon no seu portal clássico Azure.

6.  Na página de integração do aplicativo **Jobscience** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-jobscience-tutorial/IC784360.png "Configurar o logon único")

7.  Na página **como você gostaria que os usuários para assinar Jobscience** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-jobscience-tutorial/IC784361.png "Configurar o logon único")

8.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Jobscience URL de entrada** , digite sua URL usando o seguinte padrão "*http://company.my.salesforce.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-jobscience-tutorial/IC784362.png "Configurar a URL do aplicativo")

9.  Na página **Configurar logon único em Jobscience** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-jobscience-tutorial/IC784363.png "Configurar o logon único")

10. No site da empresa Jobscience, clique em **Controles de segurança**e, em seguida, clique em **Configurações de logon único**.

    ![Controles de segurança] (./media/active-directory-saas-jobscience-tutorial/IC784364.png "Controles de segurança")

11. Na seção **Configurações de logon único** , execute as seguintes etapas:

    ![Configurações de logon único] (./media/active-directory-saas-jobscience-tutorial/IC781026.png "Configurações de logon único")

    1.  Selecione **SAML habilitado**.
    2.  Clique em **novo**.

12. Na caixa de diálogo **SAML Single Sign-On configuração Editar** , execute as seguintes etapas:

    ![Configuração de logon única SAML] (./media/active-directory-saas-jobscience-tutorial/IC784365.png "Configuração de logon única SAML")

    1.  Na caixa de texto **nome** , digite um nome para a sua configuração.
    2.  No portal do clássico Azure, na página da caixa de diálogo **Configurar logon único em Jobscience** , copie o valor de **URL do emissor** e, em seguida, cole a caixa de texto do **emissor**
    3.  Na caixa de texto **Id da entidade** , digite **https://salesforce-jobscience.com**
    4.  Clique em **Procurar** para carregar seu certificado Azure AD.
    5.  Como **O tipo de identidade SAML**, selecione a **declaração contém a ID de Federação do objeto do usuário**.
    6.  Como **Local de identidade SAML**, selecione a **identidade é no elemento NameIdentfier da política de assunto**.
    7.  No portal do clássico Azure, na página da caixa de diálogo **Configurar logon único em Jobscience** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login do provedor de identidade**
    8.  No portal do clássico Azure, na página da caixa de diálogo **Configurar logon único em Jobscience** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL de Logout do provedor de identidade**
    9.  Clique em **Salvar**.

13. No painel de navegação à esquerda, na seção **administrador** , clique em **Gerenciamento de domínios** para expandir a seção relacionada e, em seguida, clique em **Meu domínio** para abrir a página de **Meu domínio** . 

    ![Meu domínio] (./media/active-directory-saas-jobscience-tutorial/IC767825.png "Meu domínio")

14. Na página de **Meu domínio** , na seção **Página de Login identidade visual** , clique em **Editar**.

    ![Página de login de identidade visual] (./media/active-directory-saas-jobscience-tutorial/IC767826.png "Página de login de identidade visual")

15. Na página de **Marca de página de logon** , na seção **Serviço de autenticação** , o nome das suas **Configurações de SSO SAML** é exibido. Selecione-o e clique em **Salvar**.

    ![Página de login de identidade visual] (./media/active-directory-saas-jobscience-tutorial/IC784366.png "Página de login de identidade visual")

16. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-jobscience-tutorial/IC784367.png "Configurar o logon único")
  
Para obter o SP iniciadas logon único Login URL clique nas **configurações de logon único** na seção de menu **Controles de segurança** .

![Controles de segurança] (./media/active-directory-saas-jobscience-tutorial/IC784368.png "Controles de segurança")
  
Clique no perfil SSO que você criou na etapa acima.  
Esta página mostra o logon único na URL para a sua empresa (por exemplo, *https://companyname.my.salesforce.com?so=companyid*).
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Jobscience, ele devem ser provisionados em Jobscience.  
No caso de Jobscience, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **Jobscience** como administrador.

2.  Vá para configuração

    ![Configuração] (./media/active-directory-saas-jobscience-tutorial/IC784358.png "Configuração")

3.  Vá para **Gerenciar usuários \> usuários**.

    ![Usuários] (./media/active-directory-saas-jobscience-tutorial/IC784369.png "Usuários")

4.  Clique em **novo usuário**.

    ![Todos os usuários] (./media/active-directory-saas-jobscience-tutorial/IC784370.png "Todos os usuários")

5.  Na caixa de diálogo **Editar usuário** , execute as seguintes etapas:

    ![O usuário editar] (./media/active-directory-saas-jobscience-tutorial/IC784371.png "O usuário editar")

    1.  Digite o nome, sobrenome, alias, email, propriedades de nome e apelido de usuário do usuário Azure AD que deseja provisionar em caixas de texto relacionadas.
    2.  Clique em **Salvar**.

    >[AZURE.NOTE] O proprietário da conta do Azure AD receberão um email que inclui um link para confirmar a conta antes que ele está ativado.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Jobscience usuário conta ou APIs fornecidas pela Jobscience para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-jobscience-perform-the-following-steps"></a>Para atribuir usuários a Jobscience, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Jobscience **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-jobscience-tutorial/IC784372.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-jobscience-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).