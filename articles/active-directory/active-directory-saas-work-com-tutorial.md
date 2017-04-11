<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Work.com | Microsoft Azure" 
    description="Saiba como usar Work.com com o Azure Active Directory para habilitar o logon único, automatizado de provisionamento e muito mais!." 
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

#<a name="tutorial-azure-active-directory-integration-with-workcom"></a>Tutorial: Integração com o Active Directory do Azure com Work.com
  
O objetivo deste tutorial é mostrar a integração do Azure e Work.com.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Work.com logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários AAD aos quais você tem atribuem Work.com acesso será capaz de única entrada para o aplicativo no site da sua empresa Work.com (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Work.com
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-work-com-tutorial/IC794105.png "Cenário")

##<a name="enabling-the-application-integration-for-workcom"></a>Habilitar a integração de aplicativo para Work.com
  
O objetivo desta seção é como habilitar a integração de aplicativo para Work.com da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-workcom-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Work.com, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-work-com-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-work-com-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-work-com-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-work-com-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Work.com**.

    ![Galeria de aplicativo] (./media/active-directory-saas-work-com-tutorial/IC794106.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Work.com**e clique em **concluído** para adicionar o aplicativo.

    ![Work.com] (./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Work.com com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, você é necessárias para carregar um certificado no Work.com.com.

>[AZURE.NOTE] Para configurar o logon único, você precisa configurar um nome de domínio personalizado Work.com ainda. Você precisa definir pelo menos um nome de domínio, testar seu nome de domínio e implantá-lo em toda a sua organização.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  Faça logon em seu locatário Work.com como administrador.

2.  Vá para **a configuração**.

    ![Configuração] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Configuração")

3.  No painel de navegação à esquerda, na seção **administrador** , clique em **Gerenciamento de domínios** para expandir a seção relacionada e, em seguida, clique em **Meu domínio** para abrir a página de **Meu domínio** . 

    ![Meu domínio] (./media/active-directory-saas-work-com-tutorial/IC767825.png "Meu domínio")

4.  Para verificar que seu domínio foi configurado corretamente, certifique-se de que ele está em "**Etapa 4 implantado para usuários**" e examine seu "**Minhas configurações de domínio**".

    ![Domínio implantados em usuário] (./media/active-directory-saas-work-com-tutorial/IC784377.png "Domínio implantados em usuário")

5.  Em uma janela de navegador da web diferente, faça logon no seu portal clássico Azure.

6.  Na página de integração do aplicativo **Work.com **, clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-work-com-tutorial/IC794109.png "Configurar o logon único")

7.  Na página **como você gostaria que os usuários para assinar Work.com** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-work-com-tutorial/IC794110.png "Configurar o logon único")

8.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Work.com entrada na URL** , digite a URL usada pelos usuários para entrar seu aplicativo de Work.com (por exemplo: " *http://company.my.salesforce.com*") e, em seguida, clique em **Avançar**: 

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-work-com-tutorial/IC794111.png "Configurar a URL do aplicativo")

9.  Na página **Configurar logon único em Work.com** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-work-com-tutorial/IC794112.png "Configurar o logon único")

10. Faça logon em seu locatário Work.com.

11. Vá para **a configuração**.

    ![Configuração] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Configuração")

12. Expandir o menu de **Controles de segurança** e, em seguida, clique em **Configurações de logon único**.

    ![Configurações de logon único] (./media/active-directory-saas-work-com-tutorial/IC794113.png "Configurações de logon único")

13. Na página de diálogo de **Configurações de logon único** , execute as seguintes etapas:

    ![SAML habilitado] (./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML habilitado")

    1.  Selecione **SAML habilitado**.
    2.  Clique em **novo**.

14. Na seção **Configurações de logon único SAML** , execute as seguintes etapas:

    ![Configuração de logon única SAML] (./media/active-directory-saas-work-com-tutorial/IC794114.png "Configuração de logon única SAML")

    1.  Na caixa de texto **nome** , digite um nome para a sua configuração.  

        >[AZURE.NOTE] Fornecer um valor para **nome** preencher automaticamente a caixa de texto **Nome da API** .

    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Work.com** , copie o valor de **URL do emissor** e, em seguida, cole a caixa de texto do **emissor** .
    3.  Para carregar o certificado baixado, clique em **Procurar**.
    4.  Na caixa de texto **Id da entidade** , digite **https://salesforce-work.com**.
    5.  Como **O tipo de identidade SAML**, selecione a **declaração contém a ID de Federação do objeto do usuário**.
    6.  Como **Local de identidade SAML**, selecione a **identidade é no elemento NameIdentfier da política de assunto**.
    7.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Work.com** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login do provedor de identidade** .
    8.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Work.com** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL de Logout do provedor de identidade** .
    9.  Como o **Serviço provedor iniciada solicitar encadernação**, selecione **HTTP Post**.
    10. Clique em **Salvar**.

15. Em seu portal clássico Work.com, no painel de navegação esquerdo, clique em **Gerenciamento de domínios** para expandir a seção relacionada e clique em **Meu domínio** para abrir a página de **Meu domínio** . 

    ![Meu domínio] (./media/active-directory-saas-work-com-tutorial/IC794115.png "Meu domínio")

16. Na página de **Meu domínio** , na seção **Página de Login identidade visual** , clique em **Editar**.

    ![Página de login de identidade visual] (./media/active-directory-saas-work-com-tutorial/IC767826.png "Página de login de identidade visual")

17. Na página de **Marca de página de logon** , na seção **Serviço de autenticação** , o nome das suas **Configurações de SSO SAML** é exibido. Selecione-o e clique em **Salvar**.

    ![Página de login de identidade visual] (./media/active-directory-saas-work-com-tutorial/IC784366.png "Página de login de identidade visual")

18. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-work-com-tutorial/IC794116.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para usuários do Active Directory do Azure conseguir entrar, ele devem ser provisionados para Work.com.  
No caso de Work.com, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Entre site da sua empresa Work.com como um administrador.

2.  Vá para **a configuração**.

    ![Configuração] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Configuração")

3.  Vá para **Gerenciar usuários \> usuários**.

    ![Gerenciar usuários] (./media/active-directory-saas-work-com-tutorial/IC784369.png "Gerenciar usuários")

4.  Clique em **novo usuário**.

    ![Todos os usuários] (./media/active-directory-saas-work-com-tutorial/IC794117.png "Todos os usuários")

5.  Na seção usuário editar, execute as seguintes etapas:

    ![O usuário editar] (./media/active-directory-saas-work-com-tutorial/IC794118.png "O usuário editar")

    1.  Digite o **Sobrenome**, **Alias**, **Email**, **nome de usuário** e atributos de **Apelido** de uma conta do Active Directory do Azure válida que deseja provisionar em caixas de texto relacionadas.
    2.  Selecione a **função**, **licença de usuário** e **perfil**.
    3.  Clique em **Salvar**.  

        >[AZURE.NOTE] O proprietário da conta do Active Directory do Azure receberão um email incluindo um link para confirmar a conta antes que ele fique ativo.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Work.com usuário conta ou APIs fornecidas pela Work.com para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-workcom-perform-the-following-steps"></a>Para atribuir usuários a Work.com, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo Work.com, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-work-com-tutorial/IC794119.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-work-com-tutorial/IC767830.png "Sim")
  
Agora, você deve aguardar de 10 minutos e confirmar que a conta foi sincronizada para Work.com.com.
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).