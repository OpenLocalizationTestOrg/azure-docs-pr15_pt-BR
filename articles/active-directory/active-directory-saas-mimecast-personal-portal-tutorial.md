<properties 
    pageTitle="Tutorial: Active Directory do Azure integração com o Portal de Mimecast pessoal | Microsoft Azure" 
    description="Saiba como usar o Portal pessoal Mimecast com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Tutorial: Active Directory do Azure integração com o Portal de Mimecast pessoal
  
O objetivo deste tutorial é mostrar a integração do Azure e Portal pessoal Mimecast.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Portal pessoal Mimecast logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Portal pessoal Mimecast poderão único entrar no aplicativo no site da sua empresa Portal pessoal Mimecast (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo de Portal pessoal Mimecast
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794991.png "Cenário")
##<a name="enabling-the-application-integration-for-mimecast-personal-portal"></a>Habilitar a integração de aplicativo de Portal pessoal Mimecast
  
O objetivo desta seção é como habilitar a integração de aplicativo de Portal pessoal Mimecast da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-mimecast-personal-portal-perform-the-following-steps"></a>Para habilitar a integração de aplicativo de Portal pessoal Mimecast, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Portal pessoal Mimecast**.

    ![Galeria de aplicativo] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794992.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Portal pessoal Mimecast**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Portal de Mimecast pessoal] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794993.png "Portal de Mimecast pessoal")
##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Portal pessoal Mimecast com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo de **Portal pessoal Mimecast** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794994.png "Configurar o logon único")

2.  Na página **como você gostaria que usuários para entrar Portal de Mimecast pessoal** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794995.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Mimecast pessoal Portal entrada na URL** , digite a URL usada pelos usuários para entrar seu aplicativo de Portal pessoal Mimecast (por exemplo: "https://webmail-uk.mimecast.com" ou "https://webmail-us.mimecast.com") e, em seguida, clique em **Avançar**.

    >[AZURE.NOTE] A entrada na URL é específico de região.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794996.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no Portal pessoal Mimecast** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794997.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu Portal pessoal Mimecast como administrador.

6.  Vá para **serviços \> aplicativo**.

    ![Aplicativos] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794998.png "Aplicativos")

7.  Clique em **perfis de autenticação**.

    ![Perfis de autenticação] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794999.png "Perfis de autenticação")

8.  Clique em **novo perfil de autenticação**.

    ![Nova autenticação Profil] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795000.png "Nova autenticação Profil")

9.  Na seção **Autenticação de perfil** , execute as seguintes etapas:

    ![Autenticação Profil] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795001.png "Autenticação Profil")

    1.  Na caixa de texto **Descrição** , digite um nome para a sua configuração.
    2.  Selecione **impor autenticação SAML para o Portal de Mimecast pessoal**.
    3.  Como **provedor**, selecione **Active Directory do Azure**.
    4.  No portal do clássico Azure, na página de diálogo **Configurar logon único no Portal pessoal Mimecast** , copie o valor de **URL do emissor** e cole-o na caixa de texto **URL do emissor** .
    5.  No portal do clássico Azure, na página de diálogo **Configurar logon único no Portal pessoal Mimecast** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login** .
    6.  No portal do clássico Azure, na página de diálogo **Configurar logon único no Portal pessoal Mimecast** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Logout** .  

        >[AZURE.NOTE] O valor de URL de Login e o valor de URL de Logout destinam-no Portal pessoal Mimecast a mesma.

    7.  Crie um arquivo **codificado na base 64** do seu certificado baixado.  

        >[AZURE.TIP]Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    8.  Abrir seu certificado codificado de base 64 no bloco de notas, remova a primeira linha ("*--*") e a última linha ("*--*"), copie o conteúdo restante dela para sua área de transferência e colá-lo para a caixa de texto de **Certificado de provedor de identidade (metadados)** .
    9.  Selecione **Permitir que o logon único em**.
    10. Clique em **Salvar**.

10. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795002.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD a fazer logon no Portal de Mimecast pessoal, eles devem ser provisionados no Portal de Mimecast pessoal.  
No caso de Mimecast Portal pessoal, provisionamento é uma tarefa manual.
  
Você precisa registrar um domínio antes de criar usuários.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Entrar seu **Portal pessoal Mimecast** como administrador.

2.  Vá para **diretórios \> interno**.

    ![Diretórios] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795003.png "Diretórios")

3.  Clique em **registrar o novo domínio**.

    ![Registrar o novo domínio] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795004.png "Registrar o novo domínio")

4.  Após o novo domínio tiver sido criado, clique em **Novo endereço**.

    ![Novo endereço] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795005.png "Novo endereço")

5.  Na caixa de diálogo Novo do endereço, execute as seguintes etapas:

    ![Salvar] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795006.png "Salvar")

    1.  Digite os atributos de **Endereço de Email**, **Nome Global**, **senha** e **Confirmar senha** de uma conta AAD válida que deseja provisionar em caixas de texto relacionadas.
    2.  Clique em **Salvar**.

>[AZURE.NOTE]Você pode usar outras ferramentas de criação de conta de usuário de Portal pessoal Mimecast ou APIs fornecido pelo Portal pessoal Mimecast para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-mimecast-personal-portal-perform-the-following-steps"></a>Para atribuir usuários a Portal pessoal Mimecast, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo de **Portal pessoal Mimecast **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795007.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).