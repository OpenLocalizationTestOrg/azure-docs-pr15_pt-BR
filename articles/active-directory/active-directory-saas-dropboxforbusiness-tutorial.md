<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Dropbox para empresas | Microsoft Azure" 
    description="Saiba como usar o Dropbox para empresas com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Tutorial: Integração com o Active Directory do Azure com Dropbox para empresas
  
O objetivo deste tutorial é mostrar a integração do Azure e Dropbox para empresas.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário de teste no Dropbox para empresas
  
Depois de concluir este tutorial, os usuários do Azure AD você atribuiu a Dropbox para empresas serão capazes de logon único para o aplicativo em seu Dropbox para empresas empresa site (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo de Dropbox para empresas
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Cenário")



##<a name="enabling-the-application-integration-for-dropbox-for-business"></a>Habilitar a integração de aplicativo de Dropbox para empresas
  
O objetivo desta seção é como habilitar a integração de aplicativo de Dropbox para empresas de estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-dropbox-for-business-perform-the-following-steps"></a>Para habilitar a integração de aplicativo de Dropbox para empresas, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Dropbox para empresas**.

    ![Galeria de aplicativo] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Dropbox para empresas**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![Dropbox para empresas] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox para empresas")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Dropbox para empresas com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.

Como parte deste procedimento, é necessário para carregar um certificado codificado base 64 em seu Dropbox do locatário de negócios. Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo do **Dropbox para empresas** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Dropbox para empresas** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , execute as seguintes etapas:

    a. Logon no seu Dropbox do locatário de negócios. 

    ![Configurar o logon único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Configurar o logon único")

    b. No painel de navegação no lado esquerdo, clique em **Console de administração**. 

    ![Configurar o logon único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Configurar o logon único")

    c. No **Console de administração**, clique em **autenticação** no painel de navegação à esquerda. 

    ![Configurar o logon único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Configurar o logon único")

    d. Na seção de **logon único** , selecione **Habilitar logon único**e, em seguida, clique em **mais** para expandir essa seção.  

    ![Configurar o logon único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Configurar o logon único")

    e. Copie a URL ao lado dos **usuários possam entrar por meio de seu endereço de email ou podem ir diretamente para o**. 

    ![Configurar o logon único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Configurar o logon único")

    f. No portal do Azure clássico, na caixa **DropBox para empresas entrar** URL de texto, cole a URL. 

    ![Configurar o logon único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Configurar o logon único")  



4. Na página **Configurar logon único no Dropbox para empresas** , clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado em seu computador.  

    ![Configurar o logon único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Configurar o logon único")


5. Na sua Dropbox locatário de negócios, na seção de **logon único** da página de **autenticação** , execute as seguintes etapas: 

    ![Configurar o logon único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Configurar o logon único")

    a. Clique em **obrigatório**.

    b. No portal do clássico Azure, na página de diálogo **Configurar logon único no Dropbox para empresas** , copie o valor de **entrada URL da página** e, em seguida, cole a caixa de texto **entrar URL** .


    c. Crie um arquivo **codificado na Base 64** do seu certificado baixado. 

    > [AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).


    d. Clique em botão de **"Escolher certificado"** e navegue até seu **arquivo de certificado codificado na base 64**.


    e. Clique em botão **"Salvar alterações"** para concluir a configuração no seu DropBox do locatário de negócios.


6. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** . 

    ![Configurar o logon único] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Configurar o logon único")



##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
O objetivo desta seção é como habilitar o usuário provisionamento de contas de usuário do Active Directory no Dropbox para empresas de estrutura de tópicos.


### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1. No Portal do clássico Azure, na página de integração de aplicativo do **Dropbox para empresas** , clique em **configurar provisionamento de usuário** para abrir a caixa de diálogo **Configurar provisionamento do usuário** .

2. Na habilitar provisionamento do usuário no DropBox para página de negócios, clique em Habilitar provisionamento do usuário para abrir entrar no Dropbox para vincular com caixa de diálogo do Azure AD.  

    ![Provisionamento do usuário] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "Provisionamento do usuário")

3. Na caixa de diálogo **entrar no Dropbox para vincular com o Azure AD** , entrar no seu Dropbox do locatário de negócios. 

    ![Provisionamento do usuário] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "Provisionamento do usuário")



4. Clique em **Permitir** para conceder Azure AD para acessar Dropbox. 

    ![Provisionamento do usuário] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "Provisionamento do usuário")



5. Para concluir a configuração, clique no botão **concluído** .  

    ![Provisionamento do usuário] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "Provisionamento do usuário")




##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-dropbox-for-business-perform-the-following-steps"></a>Para atribuir usuários a Dropbox para empresas, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicativo do **Dropbox para empresas **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Sim")
  


Agora, você deve aguardar de 10 minutos e verifique se que a conta foi sincronizada no Dropbox para empresas.

Como uma primeira etapa de verificação, você pode verificar o status de provisionamento, clicando em **Painel de controle** na página de integração do **Dropbox para empresas** aplicativo no Portal do clássico Azure.

![Atribuir aos usuários] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Atribuir aos usuários")


Um ciclo de provisionamento de usuário concluído com êxito é indicado por um status relacionado.

![Atribuir aos usuários] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Atribuir aos usuários")


Se você quiser testar suas configurações de logon única, abra o painel de acesso.
Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).




## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)