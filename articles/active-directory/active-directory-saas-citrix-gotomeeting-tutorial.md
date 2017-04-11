<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Citrix GoToMeeting | Microsoft Azure" 
    description="Saiba como usar o Citrix GoToMeeting com o Azure Active Directory para habilitar o logon único, automatizado de provisionamento e muito mais!." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-citrix-gotomeeting"></a>Tutorial: Integração com o Active Directory do Azure com Citrix GoToMeeting  
Aplica-se a: Azure

>[AZURE.TIP]Para enviar comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=522412).

O objetivo deste tutorial é mostrar a integração do Azure e Citrix GoToMeeting. O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário em Citrix GoToMeeting

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Citrix GoToMeeting
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Configuração] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuração")



##<a name="enabling-the-application-integration-for-citrix-gotomeeting"></a>Habilitar a integração de aplicativo para Citrix GoToMeeting

O objetivo desta seção é como habilitar a integração de aplicativo para Citrix GoToMeeting da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-citrix-gotomeeting-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Citrix GoToMeeting, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de galeria] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Adicionar um aplicativo de galeria")

6.  Na **caixa de pesquisa**, digite **Citrix GoToMeeting**.

    ![Citrix GoToMeeting] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  No painel de resultados, selecione **Citrix GoToMeeting**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Citrix GoToMeeting] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar Citrix GoToMeeting com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário para carregar um certificado codificado base 64 em seu locatário Citrix GoToMeeting.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  Na página de integração de aplicativo do **Citrix GoToMeeting** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar Diante de logon único** .

    ![Habilitar o logon único] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Habilitar o logon único")

2.  Na página **como você gostaria que usuários para entrar Citrix GoToMeeting** , selecione **Microsoft Azure AD Single Sign-On**.

    ![Configurar o logon único] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configurar o logon único")


3. Na página **Definir configurações de aplicativo** , clique em **Avançar**. 

    ![Habilitar o logon único] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Habilitar o logon único")

4.  Na página **Configurar logon único em Citrix GoToMeeting** , clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configurar o logon único")

5.  Em uma janela de navegador diferente, faça logon em sua Central de organização de Citrix ([https://account.citrixonline.com/organization/administration/](https://account.citrixonline.com/organization/administration/)).

6. Clique na guia de **Provedor de identidade** e, em seguida, execute as seguintes etapas:  

    ![Configuração SAML] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "Configuração SAML")

    a. Selecione **Manual**

    
    b. No portal do clássico Azure, na página de diálogo **Configurar logon único em Citrix GoToMeeting** , copie o valor de **Entrada URL da página** e, em seguida, cole o **URL da página de entrada** de texto. 

    
    c. No portal do clássico Azure, na página de diálogo **Configurar logon único em Citrix GoToMeeting** , copie o valor de **URL da página de Sign-Out** e, em seguida, cole a **URL da página de saída** de caixa de texto.

    
    d. No portal clássico do Azure, na página de diálogo **Configurar logon único em Citrix GoToMeeting** , copie o valor de **Identificação de entidade** e, em seguida, cole a caixa de texto de **ID de entidade de provedor de identidade** .

   
    e. Para carregar seu certificado baixado, clique em **Carregar certificado**.

    
    f. Clique em **Salvar**.

6.  No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configurar o logon único")


7. Na página **confirmação de logon única** , clique em **Concluir**.

    ![Configuração SAML] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "Configuração SAML")





##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

O objetivo desta seção é como habilitar o provisionamento de contas de usuário do Active Directory para Citrix GoToMeeting da estrutura de tópicos.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo do **Citrix GoToMeeting** , clique em **configurar provisionamento de usuário** para abrir a caixa de diálogo **Configurar provisionamento do usuário** .

    ![Configurar provisionamento de usuário] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configurar provisionamento de usuário")

2.  Na página **configurações e credenciais de administrador** , execute as seguintes etapas:

    ![Configurar provisionamento de usuário] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configurar provisionamento de usuário")

    a. Na caixa de texto **Nome de usuário de administrador do Citrix GoToMeeting** , digite o nome de usuário de um administrador.

    
    b. Na caixa de texto **Senha de administrador do Citrix GoToMeeting** , a senha do administrador.

    
    c. Clique em **Avançar**.

3.  Na página **confirmação** , clique na marca de seleção para salvar sua configuração.

4.  Clique no botão **Validar** para verificar a configuração.


##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-citrix-gotomeeting-perform-the-following-steps"></a>Para atribuir usuários a Citrix GoToMeeting, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo do **Citrix GoToMeeting** , clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Sim")

Agora, você deve aguardar de 10 minutos e verifique se que a conta foi sincronizada no Dropbox para empresas.

Como uma primeira etapa de verificação, você pode verificar o status de provisionamento, clicando em Dashboard na D na página de integração de aplicativo do **Citrix GoToMeeting** no portal de clássico do Azure.

![Painel de controle] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Painel de controle")

Um ciclo de provisionamento de usuário concluído com êxito é indicado por um status de relacionados:

![Status da integração] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Status da integração")

Se você quiser testar suas configurações de logon única, abra o painel de acesso.

Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](https://msdn.microsoft.com/library/dn308586).
