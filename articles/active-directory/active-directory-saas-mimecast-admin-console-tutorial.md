<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Console de administração de Mimecast | Microsoft Azure" 
    description="Saiba como usar o Console de administração do Mimecast com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Tutorial: Integração com o Active Directory do Azure com Mimecast Console de administração
  
O objetivo deste tutorial é mostrar a integração do Azure e Console de administração do Mimecast.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Console de administração do Mimecast logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Console de administração do Mimecast poderão único entrar no aplicativo no site da sua empresa Console de administração do Mimecast (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo do Console de administração de Mimecast
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795008.png "Cenário")
##<a name="enabling-the-application-integration-for-mimecast-admin-console"></a>Habilitar a integração de aplicativo do Console de administração de Mimecast
  
O objetivo desta seção é como habilitar a integração de aplicativo do Console de administração do Mimecast da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-mimecast-admin-console-perform-the-following-steps"></a>Para habilitar a integração de aplicativo do Console de administração do Mimecast, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Console de administração do Mimecast**.

    ![Galeria de aplicativo] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795009.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Console de administração do Mimecast**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Console de administração de Mimecast] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795010.png "Console de administração de Mimecast")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar console de administração do Mimecast com sua conta no Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo do **Console de administração do Mimecast** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795011.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para entrar console de administração Mimecast** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795012.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Mimecast Admin Console entrada na URL** , digite a URL usada pelos usuários para entrar seu aplicativo de Console de administração do Mimecast (por exemplo: "https://webmail-uk.mimecast.com" ou "https://webmail-us.mimecast.com") e, em seguida, clique em **Avançar**.

    >[AZURE.NOTE] A entrada na URL é específico de região.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795013.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no Console de administração do Mimecast** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795014.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu Console de administração do Mimecast como administrador.

6.  Vá para **serviços \> aplicativo**.

    ![Serviços] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC794998.png "Serviços")

7.  Clique em **perfis de autenticação**.

    ![Perfis de autenticação] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC794999.png "Perfis de autenticação")

8.  Clique em **novo perfil de autenticação**.

    ![Novos perfis de autenticação] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795000.png "Novos perfis de autenticação")

9.  Na seção **Autenticação de perfil** , execute as seguintes etapas:

    ![Perfil de autenticação] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795015.png "Perfil de autenticação")

    1.  Na caixa de texto **Descrição** , digite um nome para a sua configuração.
    2.  Selecione **impor autenticação SAML do Console de administração de Mimecast**.
    3.  Como **provedor**, selecione **Active Directory do Azure**.
    4.  No portal do clássico Azure, na página de diálogo **Configurar logon único no Console de administração do Mimecast** , copie o valor de **URL do emissor** e cole-o na caixa de texto **URL do emissor** .
    5.  No portal do clássico Azure, na página de diálogo **Configurar logon único no Console de administração do Mimecast** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login** .
    6.  No portal do clássico Azure, na página de diálogo **Configurar logon único no Console de administração do Mimecast** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Logout** .  

        >[AZURE.NOTE]O valor de URL de Login e o valor de URL de Logout são do console de administração do Mimecast os mesmos.

    7.  Crie um arquivo **codificado na base 64** do seu certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    8.  Abrir seu certificado codificado de base 64 no bloco de notas, remova a primeira linha ("*--*") e a última linha ("*--*"), copie o conteúdo restante dela para sua área de transferência e colá-lo para a caixa de texto de **Certificado de provedor de identidade (metadados)** .
    9.  Selecione **Permitir que o logon único em**.
    10. Clique em **Salvar**.

10. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795016.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD efetuar login no Console de administração do Mimecast, eles devem ser provisionados no Console de administração do Mimecast.  
No caso de Console de administração do Mimecast, provisionamento é uma tarefa manual.
  
Você precisa registrar um domínio antes de criar usuários.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Conecte-se ao seu **Console de administração do Mimecast** como administrador.

2.  Vá para **diretórios \> interno**.

    ![Diretórios] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795003.png "Diretórios")

3.  Clique em **registrar o novo domínio**.

    ![Registrar o novo domínio] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795004.png "Registrar o novo domínio")

4.  Após o novo domínio tiver sido criado, clique em **Novo endereço**.

    ![Novo endereço] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795005.png "Novo endereço")

5.  Na caixa de diálogo Novo do endereço, execute as seguintes etapas:

    ![Salvar] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795006.png "Salvar")

    1.  Digite os atributos de **Endereço de Email**, **Nome Global**, **senha** e **Confirmar senha** de uma conta AAD válida que deseja provisionar em caixas de texto relacionadas.
    2.  Clique em **Salvar**.

>[AZURE.NOTE]Você pode usar outras ferramentas de criação de conta de usuário do Console de administração do Mimecast ou APIs fornecidas pelo Console de administração do Mimecast para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-mimecast-admin-console-perform-the-following-steps"></a>Para atribuir usuários a Console de administração do Mimecast, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo do **Console de administração do Mimecast **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795017.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).