<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com AnswerHub | Microsoft Azure" 
    description="Saiba como usar AnswerHub com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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
    ms.date="10/10/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Tutorial: Integração com o Active Directory do Azure com AnswerHub

O objetivo deste tutorial é mostrar a integração do Azure e [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software).  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software) logon único habilitado para assinatura

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a AnswerHub poderão único entrar no aplicativo no site da sua empresa AnswerHub (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para AnswerHub
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-answerhub-tutorial/IC785165.png "Cenário")

##<a name="enabling-the-application-integration-for-answerhub"></a>Habilitar a integração de aplicativo para AnswerHub

O objetivo desta seção é como habilitar a integração de aplicativo para AnswerHub da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-answerhub-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para AnswerHub, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-answerhub-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-answerhub-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-answerhub-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-answerhub-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **AnswerHub**.

    ![Galeria de aplicativo] (./media/active-directory-saas-answerhub-tutorial/IC785166.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **AnswerHub**e clique em **concluído** para adicionar o aplicativo.

    ![AnswerHub] (./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")

##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar AnswerHub com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **AnswerHub** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-answerhub-tutorial/IC785168.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar AnswerHub** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-answerhub-tutorial/IC785169.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **AnswerHub URL de entrada** , digite sua URL usando o seguinte padrão "*https://company.answerhub.com*" e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em AnswerHub** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-answerhub-tutorial/IC785171.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa AnswerHub como administrador.
    >[AZURE.NOTE] Se precisar de ajuda para configurar AnswerHub, contate [a equipe de suporte do AnswerHub](mailto:success@answerhub.com. ).








6.  Vá para **Administração**.

7.  Clique na guia do **usuário e grupo** .

8.  No painel de navegação no lado esquerdo, na seção **Configurações Social** , clique em **Configuração de SAML**.

9.  Clique em guia **IDP Config** .

10. Na guia **IDP Config** , execute as seguintes etapas:

    ![Configuração SAML] (./media/active-directory-saas-answerhub-tutorial/IC785172.png "Configuração SAML")

    1.  No portal do clássico Azure, na página de diálogo **Configurar logon único em AnswerHub** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de logon IDP** .
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único em AnswerHub** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **IDP Logout URL** .
    3.  No portal do clássico Azure, na página de diálogo **Configurar logon único em AnswerHub** , copie o valor de **Formato de nome de identificador** e cole-o na caixa **Formato do IDP nome identificador** de texto.
    4.  Clique em **chaves e certificados**.

11. Na guia certificados e chaves, execute as seguintes etapas:

    ![Chaves e certificados] (./media/active-directory-saas-answerhub-tutorial/IC785173.png "Chaves e certificados")

    1.  Crie um arquivo **codificado na base 64** do seu certificado baixado.  

        >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    2.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo à caixa de texto **IDP chave pública (x 509 formato)** .
    3.  Clique em **Salvar**.

12. Na guia **IDP Config** , clique em **Salvar**.

13. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-answerhub-tutorial/IC785174.png "Configurar o logon único")

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login AnswerHub, ele devem ser provisionados em AnswerHub.  
No caso de AnswerHub, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **AnswerHub** como administrador.

2.  Vá para **Administração**.

3.  Clique na guia **usuários e grupos** .

4.  No painel de navegação no lado esquerdo, na seção **Gerenciar usuários** , clique em **criar ou importar usuários**.

    ![Usuários e grupos] (./media/active-directory-saas-answerhub-tutorial/IC785175.png "Usuários e grupos")

5.  Digite o **endereço de Email**, o **nome de usuário** e a **senha** de uma conta do Active Directory do Azure válida que deseja provisionar em caixas de texto relacionadas e clique em **Salvar**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros AnswerHub usuário conta ou APIs fornecidas pela AnswerHub para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-answerhub-perform-the-following-steps"></a>Para atribuir usuários a AnswerHub, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **AnswerHub **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-answerhub-tutorial/IC785176.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-answerhub-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
