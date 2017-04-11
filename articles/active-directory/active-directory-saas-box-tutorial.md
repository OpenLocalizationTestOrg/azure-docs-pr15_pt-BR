<properties 
    pageTitle="Tutorial: Active Directory do Azure caixa integração com | Microsoft Azure" 
    description="Saiba como usar a caixa com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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




#<a name="tutorial-azure-active-directory-integration-with-box"></a>Tutorial: Active Directory do Azure caixa integração com


  
O objetivo deste tutorial é mostrar a integração do Azure e caixa.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário de teste na caixa
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a caixa poderão único entrar no aplicativo no site da sua empresa caixa (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo de caixa
2.  Configurando o logon único
3.  Configuração de usuário e grupo de provisionamento
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-box-tutorial/IC769537.png "Cenário")



##<a name="enabling-the-application-integration-for-box"></a>Habilitar a integração de aplicativo de caixa
  
O objetivo desta seção é como habilitar a integração de aplicativo para caixa de estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-box-perform-the-following-steps"></a>Para habilitar a integração de aplicativo caixa, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-box-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-box-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-box-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-box-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **caixa**.

    ![Galeria de aplicativo] (./media/active-directory-saas-box-tutorial/IC701023.png "Galeria de aplicativo")

7.  No painel de resultados, selecione a **caixa**e clique em **concluído** para adicionar o aplicativo.

    ![Caixa] (./media/active-directory-saas-box-tutorial/IC701024.png "Caixa")



##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar a caixa com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos. Como parte deste procedimento, você precisa carregar metadados para Box.com.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração de aplicativo de **caixa** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-box-tutorial/IC769538.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para entrar caixa** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-box-tutorial/IC769539.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa **URL do locatário de caixa** de texto, digite a URL de locatário de caixa (por exemplo: https://<mydomainname>. box.com) e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-box-tutorial/IC669826.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único caixa** , para baixar os metadados, clique em **baixar metadados**e, em seguida, o arquivo de dados localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-box-tutorial/IC669824.png "Configurar o logon único")

5.  Encaminhar desse arquivo de metadados para caixa de equipe de suporte. As necessidades de equipe de suporte configura single sign-on para você.

6.  Selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-box-tutorial/IC769540.png "Configurar o logon único")
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
O objetivo desta seção é como habilitar o provisionamento de contas de usuário do Active Directory para caixa de estrutura de tópicos.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1. No portal do clássico Azure, na página de integração de aplicativo de **caixa** , clique em **configurar provisionamento de usuário** para abrir a caixa de diálogo **Configurar provisionamento do usuário** . 

    ![Habilitar o provisionamento automático de usuário] (./media/active-directory-saas-box-tutorial/IC769541.png "Habilitar o provisionamento automático de usuário")

2. Na página **Habilitar usuário provisionamento a caixa de** diálogo, clique em **Habilitar provisionamento do usuário**. 

    ![Habilitar o provisionamento automático de usuário] (./media/active-directory-saas-box-tutorial/IC769544.png "Habilitar o provisionamento automático de usuário")

3. Na página **login para conceder acesso a caixa** , forneça as credenciais necessárias e clique em **Autorizar**. 

    ![Habilitar o provisionamento automático de usuário] (./media/active-directory-saas-box-tutorial/IC769546.png "Habilitar o provisionamento automático de usuário")


4. Clique em **conceder acesso a caixa** para autorizar essa operação e retornar para o portal de clássico Azure. 

    ![Habilitar o provisionamento automático de usuário] (./media/active-directory-saas-box-tutorial/IC769549.png "Habilitar o provisionamento automático de usuário")


5. Na página **Opções de provisionamento** , as caixas de seleção de **tipos de objeto para provisionar** permitem que você selecione se deseja ou não agrupar objetos são provisionados a caixa além dos objetos de usuário.  Consulte "Atribuir usuários e grupos seção" abaixo para obter mais informações.


6. Para concluir a configuração, clique no botão concluído. 

    ![Habilitar o provisionamento automático de usuário] (./media/active-directory-saas-box-tutorial/IC769551.png "Habilitar o provisionamento automático de usuário")



##<a name="assigning-a-test-user"></a>Atribuir um usuário de teste
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-box-perform-the-following-steps"></a>Para atribuir usuários a caixa, execute as seguintes etapas:

1. No portal do Azure clássico, crie uma conta de teste.

2. Na página de integração de aplicativo de **caixa **, clique em **atribuir aos usuários**. 

    ![Atribuir aos usuários] (./media/active-directory-saas-box-tutorial/IC769552.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição. 

    ![Sim] (./media/active-directory-saas-box-tutorial/IC767830.png "Sim")
  
Agora, você deve aguardar de 10 minutos e confirmar que a conta foi sincronizada a caixa.

Como uma primeira etapa de verificação, você pode verificar o status de provisionamento, clicando em Dashboard na D na página de integração de aplicativo de caixa no portal de clássico do Azure.

![Painel de controle] (./media/active-directory-saas-box-tutorial/IC769553.png "Painel de controle")

Um ciclo de provisionamento de usuário concluído com êxito é indicado por um status de relacionados:

![Status da integração] (./media/active-directory-saas-box-tutorial/IC769555.png "Status da integração")


No seu locatário de caixa, os usuários sincronizados estão listados em **Usuários gerenciado** no **Console de administração**.

![Status da integração] (./media/active-directory-saas-box-tutorial/IC769556.png "Status da integração")


##<a name="assigning-users-and-groups"></a>Atribuir usuários e grupos

O **caixa > usuários e grupos** guia no portal de clássico do Azure permite que você especifique quais usuários e grupos devem ter acesso à caixa. Atribuição de um usuário ou grupo faz com que as seguintes ações para ocorrer:

* Azure AD permite que o usuário atribuído (seja por atribuição direta ou associações de grupo) para autenticar a caixa. Se um usuário não é atribuído, Azure AD não permitirá-los para entrar caixa e retornará um erro na página de entrada do Azure AD.

* Um bloco de aplicativo para caixa é adicionado ao do usuário [inicializador de aplicativo](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

* Se provisionamento automática estiver habilitada, então a usuários atribuídos e/ou grupos são adicionados à fila de provisionamento para ser provisionados automaticamente.

    * Se apenas objetos de usuário foram configurados para ser provisionado, em seguida, todos os usuários diretamente atribuído são colocados na fila de provisionamento, e todos os usuários que são membros de grupos atribuídos serão colocados na fila de provisionamento. 
    
    * Se agrupar objetos foram configurados para ser provisionado, todos os objetos de grupo atribuído são provisionados à caixa, bem como todos os usuários que são membros desses grupos. Os membros do grupo e usuário são preservados durante sendo gravado a caixa.
    
Você pode usar o **atributos > Single Sign-On** guia para configurar quais atributos do usuário (ou declarações) são apresentadas à caixa durante a autenticação baseada em SAML e o **atributos > provisionamento** guia para configurar como atributos de usuário e grupo fluem do Azure AD para caixa durante operações de provisionamento. Consulte os recursos abaixo para obter mais informações.


## <a name="additional-resources"></a>Recursos adicionais

* [Personalizando declarações emitidas no token SAML](active-directory-saml-claims-customization.md)
* [Provisionamento: Personalizar os mapeamentos de atributo](active-directory-saas-customizing-attribute-mappings.md)
* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
