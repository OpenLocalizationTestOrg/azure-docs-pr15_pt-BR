<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com TOPdesk - público | Microsoft Azure" 
    description="Saiba como usar TOPdesk - público com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-directory-integration-with-topdesk---public"></a>Tutorial: Integração de diretório Azure com TOPdesk - público

O objetivo deste tutorial é mostrar a integração do Azure e TOPdesk - público.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um TOPdesk - logon único público habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você tiver atribuído a TOPdesk - público serão capaz de logon único para o aplicativo em seu TOPdesk - site de empresa pública (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para TOPdesk - público
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-topdesk-public-tutorial/IC790613.png "Cenário")

##<a name="enabling-the-application-integration-for-topdesk---public"></a>Habilitar a integração de aplicativo para TOPdesk - público
  
O objetivo desta seção é como habilitar a integração de aplicativo para TOPdesk - público da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-topdesk---public-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para TOPdesk - pública, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-topdesk-public-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-topdesk-public-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-topdesk-public-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-topdesk-public-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **TOPdesk - público**.

    ![Galeria de aplicativo] (./media/active-directory-saas-topdesk-public-tutorial/IC790614.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **TOPdesk - público**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![TOPdesk público] (./media/active-directory-saas-topdesk-public-tutorial/IC791317.png "TOPdesk público")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar TOPdesk - público com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Configurando single sign-on para TOPdesk - público requer que você carregar um arquivo de ícone de logotipo. Para obter o arquivo de ícone, contate a equipe de suporte de TOPdesk.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  Entre site da sua empresa **TOPdesk - público** como um administrador.

2.  No menu **TOPdesk** , clique em **configurações**.

    ![Configurações] (./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Configurações")

3.  Clique em **configurações de Login**.

    ![Configurações de logon] (./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Configurações de logon")

4.  Expandir o menu de **Configurações de Login** e clique em **Geral**.

    ![Geral] (./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "Geral")

5.  Na seção **pública** da seção de configuração de **login SAML** , execute as seguintes etapas:

    ![Configurações técnicas] (./media/active-directory-saas-topdesk-public-tutorial/IC790601.png "Configurações técnicas")

    1.  Clique em **Baixar** para baixar o arquivo de metadados públicos e, em seguida, salve-a localmente em seu computador.
    2.  Abra o arquivo de metadados e, em seguida, localize o nó de **AssertionConsumerService** .
        ![AssertionConsumerService] (./media/active-directory-saas-topdesk-public-tutorial/IC790619.png "AssertionConsumerService")
    3.  Copie o valor de **AssertionConsumerService** .  

        >[AZURE.NOTE] Você precisará o valor na seção **Configurar a URL do aplicativo** mais tarde neste tutorial.

6.  Em uma janela de navegador da web diferente, faça logon em seu **portal clássico Azure** como administrador.

7.  Na página de integração do aplicativo **TOPdesk - público** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-topdesk-public-tutorial/IC790620.png "Configurar o logon único")

8.  Na página **como você gostaria que os usuários para assinar TOPdesk - público** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-topdesk-public-tutorial/IC790621.png "Configurar o logon único")

9.  Na página **Configurar a URL do aplicativo** , execute as seguintes etapas:

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-topdesk-public-tutorial/IC790622.png "Configurar a URL do aplicativo")

    1.  Na caixa de texto **TOPdesk - entrada na URL pública** , digite a URL usada pelos usuários para entrar em sua TOPdesk - aplicativo pública (por exemplo: "*https://qssolutions.topdesk.net*").
    2.  Na caixa de texto **TOPdesk – URL pública de resposta** , cole a **TOPdesk - URL AssertionConsumerService pública** (por exemplo: "*https://qssolutions.topdesk.net/tas/public/login/saml*")
    3.  Clique em **Avançar**.

10. Na página **Configurar logon único em TOPdesk - público** , para baixar o arquivo de metadados, clique em **baixar metadados**e salve o arquivo localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-topdesk-public-tutorial/IC790623.png "Configurar o logon único")

11. Para criar um arquivo de certificado, execute as seguintes etapas:

    ![Certificado] (./media/active-directory-saas-topdesk-public-tutorial/IC790606.png "Certificado")

    1.  Abra o arquivo baixado metadados.
    2.  Expanda o nó de **RoleDescriptor** que tem um **tipo xsi** de **tio: ApplicationServiceType**.
    3.  Copie o valor do nó **X509Certificate** .
    4.  Salve o valor de **X509Certificate** copiado localmente em seu computador de um arquivo.

12. Na sua TOPdesk - site de empresa pública, no menu **TOPdesk** , clique em **configurações**.

    ![Configurações] (./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Configurações")

13. Clique em **configurações de Login**.

    ![Configurações de logon] (./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Configurações de logon")

14. Expandir o menu de **Configurações de Login** e clique em **Geral**.

    ![Geral] (./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "Geral")

15. Na seção **público** , clique em **Adicionar**.

    ![SAML Login] (./media/active-directory-saas-topdesk-public-tutorial/IC790625.png "SAML Login")

16. Na página de diálogo do **Assistente de configuração de SAML** , execute as seguintes etapas:

    ![Assistente de configuração de SAML] (./media/active-directory-saas-topdesk-public-tutorial/IC790608.png "Assistente de configuração de SAML")

    1.  Para carregar o arquivo baixado metadados, em **Metadados de Federação**, clique em **Procurar**.
    2.  Para carregar seu arquivo de certificado, em **Certificado (RSA)**, clique em **Procurar**.
    3.  Para carregar o arquivo de logotipo obtido da equipe de suporte do TOPdesk, sob **o ícone de logotipo**, clique em **Procurar**.
    4.  Na caixa de texto do **atributo de nome de usuário** , digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    5.  Na caixa de texto **nome para exibição** , digite um nome para a sua configuração.
    6.  Clique em **Salvar**.

17. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-topdesk-public-tutorial/IC790627.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que os usuários do Azure AD fazer login TOPdesk - pública, ele devem ser provisionados em TOPdesk - público.  
No caso de TOPdesk - pública, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Entre site da sua empresa **TOPdesk - público** como administrador.

2.  No menu na parte superior, clique em **TOPdesk \> novo \> arquivos de suporte \> pessoa**.

    ![Pessoa] (./media/active-directory-saas-topdesk-public-tutorial/IC790628.png "Pessoa")

3.  Na caixa de diálogo nova pessoa, execute as seguintes etapas:

    ![Nova pessoa] (./media/active-directory-saas-topdesk-public-tutorial/IC790629.png "Nova pessoa")

    1.  Clique na guia Geral.
    2.  Na caixa de texto Sobrenome, digite o último nome de uma conta do Active Directory do Azure válida que deseja provisionar.
    3.  Selecione um **Site** para a conta.
    4.  Clique em **Salvar**.

>[AZURE.NOTE] Você pode usar qualquer outro TOPdesk - ferramentas de criação de conta de usuário públicas ou APIs fornecidas pela TOPdesk - público para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-topdesk---public-perform-the-following-steps"></a>Para atribuir usuários TOPdesk - pública, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **TOPdesk - público **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-topdesk-public-tutorial/IC790630.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-topdesk-public-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).