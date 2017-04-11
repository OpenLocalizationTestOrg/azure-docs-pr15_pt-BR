<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com DocuSign | Microsoft Azure"
    description="Saiba como configurar o logon único entre o Active Directory do Azure e DocuSign."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Tutorial: Integração com o Active Directory do Azure com DocuSign

O objetivo deste tutorial é mostrar a integração do Azure e DocuSign.
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

- Uma assinatura válida do Azure
- Um locatário em DocuSign



O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1. [Habilitar a integração de aplicativo para DocuSign](#enabling-the-application-integration-for-docusign) 


2. [Configurando o logon único](#configuring-single-sign-on) 


3. [Configurando provisionamento de contas](#configuring-account-provisioning) 


4. [Atribuir usuários](#assigning-users) 

    ![Configurando o logon único][0]
 

## <a name="enabling-the-application-integration-for-docusign"></a>Habilitar a integração de aplicativo para DocuSign

O objetivo desta seção é como habilitar a integração de aplicativo para DocuSign da estrutura de tópicos.

### <a name="to-enable-the-application-integration-for-docusign-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para DocuSign, execute as seguintes etapas:

1. No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Configurando o logon único][1]

2. Na lista de diretório, selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Configurando o logon único][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. No que você deseja fazer a caixa de diálogo, clique em **Adicionar um aplicativo da Galeria**.

    ![Configurando o logon único][4]


6. Na caixa de pesquisa, digite **DocuSign**.

    ![Configurando o logon único][5]

7. No painel de resultados, selecione **DocuSign**e clique em **concluído** para adicionar o aplicativo.

    ![Configurando o logon único][6]


## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar DocuSign com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.


### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1. No portal do clássico Azure, na página de **integração de aplicativos de DocuSign** , clique **logon único configurar** para abrir a caixa de diálogo Configurar o logon único.

    ![Configurando o logon único][7]

2. Na página **como você gostaria que os usuários para assinar DocuSign** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em Avançar.

    ![Configurando o logon único][8]

3. Na página **Definir configurações de aplicativo** , execute as seguintes etapas:

    ![Configurando o logon único][61]

    a. Na caixa de texto **entrar em URL** , digite `https://account.docusign.com/*`.  

    b. Na caixa de texto **identificador** , digite `https://account.docusign.com/*`.  
   
    c. Clique em **Avançar**. 


    > [AZURE.TIP] A entrada na URL e os valores de identificador são somente espaços reservados. Instruções sobre como recuperar os valores reais para seu ambiente é abordados neste tópico.
 

4. Na página **Configurar logon único em DocuSign** , clique em **baixar o certificado**e, em seguida, salve o arquivo de certificado localmente em seu computador.

    ![Configurando o logon único][10]


5. Em uma janela de navegador da web diferente, faça logon em seu **portal de administração de DocuSign** como administrador.


6. No menu de navegação à esquerda, clique em **domínios**.

    ![Configurando o logon único][51]

7. No painel direito, clique em **Domínio de declaração**.

    ![Configurando o logon único][52]

8. Na caixa de diálogo **declarar um domínio** , na caixa de texto **Nome de domínio** , digite seu domínio de empresa e clique em **declarações**. Certifique-se de que você verificar o domínio e o status for ativo.

    ![Configurando o logon único][53]

9. No menu à esquerda, clique em **Provedores de identidade**  

    ![Configurando o logon único][54]

10. No painel direito, clique em **Adicionar provedor de identidade**. 
    
    ![Configurando o logon único][55]

11. Na página **Configurações de provedor de identidade** , execute as seguintes etapas:

    ![Configurando o logon único][56]


    a. Na caixa de texto **nome** , digite um nome exclusivo para sua configuração. Não use espaços.

    b. No portal do Azure clássico, copie a URL do emissor e, em seguida, cole a caixa de texto do **Emissor do provedor de identidade** .

    c. No portal do Azure clássico, copie a **URL de Login remoto**e cole-o na caixa de texto **URL de Login do provedor de identidade** .

    d. No portal do Azure clássico, copie a **URL de Logout remota**e cole-o na caixa de texto **URL de Logout do provedor de identidade** .

    e. Selecione **Solicitação de AuthN de entrada**.

    f. Como **Enviar AuthN solicitação por**, selecione **postar**.

    g. Como **Enviar solicitação de logout por**, selecione **postar**. 


12. Na seção de **Mapeamento de atributo personalizada** , escolha o campo que você deseja mapear com Azure AD declaração. Neste exemplo, a declaração de **emailaddress** é mapeada com o valor de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Este é o nome de declaração padrão do Azure AD para declaração de email. 

    > [AZURE.NOTE] Use o **identificador de usuário** de apropriado para mapear o usuário do Azure AD para mapeamento de usuários Docusign. Selecione o campo adequado e insira o valor apropriado com base nas configurações de sua organização.

    ![Configurando o logon único][57]

13. Na seção de **Certificado de provedor de identidade** , clique em **Adicionar certificado**e carregue o certificado que você baixou do portal clássico do Azure AD.   

    ![Configurando o logon único][58]

14. Clique em **Salvar**.

15. Na seção **Provedores de identidade** , clique em **ações**e clique em **pontos de extremidade**.   

    ![Configurando o logon único][59]



10. No portal do Azure clássico, volte para a página **Definir configurações de aplicativo** . 

16. No **portal de administração de DocuSign**, na seção **Pontos de extremidade do modo de exibição SAML 2.0** execute, as seguintes etapas:

    ![Configurando o logon único][60]

    a. Copie a **URL de emissor do provedor de serviço**e, em seguida, cole a caixa de texto do **identificador** no portal de clássico do Azure.

    b. Copie a **URL de Login do provedor de serviço**e, em seguida, cole a caixa de texto de **Entrada na URL** no portal de clássico do Azure.

    c.  Clique em **Fechar**  


10. No portal do Azure clássico, clique em **Avançar**. 


15. No portal do Azure clássico, selecione a **confirmação de configuração de logon único**e clique em **Avançar**.

    ![Aplicativos][14]

10. Na página **confirmação de logon única** , clique em **Concluir**.

    ![Aplicativos][15]
 

## <a name="configuring-account-provisioning"></a>Configurando provisionamento de contas

O objetivo desta seção é como habilitar o usuário provisionamento de contas de usuário do Active Directory para DocuSign da estrutura de tópicos.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1. No **portal clássico Azure**, na página de **integração de aplicativos DocuSign** , clique em **configurar provisionamento de contas** para abrir a caixa de diálogo Configurar provisionamento do usuário.

    ![Configurando provisionamento de contas][30]

2. Na página **configurações e credenciais de administrador** , para habilitar automática de provisionamento de usuário, forneça as credenciais de uma conta de DocuSign com direitos suficientes e clique em **Avançar**. 

    ![Configurando provisionamento de contas][31]

3. Na caixa de diálogo **Testar conexão** , clique em **Iniciar teste**e após um teste com êxito, clique em **Avançar**.

    ![Configurando provisionamento de contas][32]

3. Na página **confirmação** , clique em **Concluir**.

    ![Configurando provisionamento de contas][33]
 

## <a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

### <a name="to-assign-users-to-docusign-perform-the-following-steps"></a>Para atribuir usuários a DocuSign, execute as seguintes etapas:

1. No **portal de clássico Azure**, crie uma conta de teste.

2. Na página de **integração de aplicativos de DocuSign** , clique em **atribuir aos usuários**.

    ![Atribuir usuários][40]
 

3. Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Atribuir usuários][41]


Agora, você deve aguardar de 10 minutos e confirmar que a conta foi sincronizada para DocuSign.

Como uma primeira etapa de verificação, você pode verificar o status de provisionamento, clicando em Dashboard na D na página de integração de aplicativo de DocuSign no portal de clássico do Azure.

![Atribuir usuários][42]

Um ciclo de provisionamento de usuário concluído com êxito é indicado por um status de relacionados:

![Atribuir usuários][43]


Se você quiser testar suas configurações de logon única, abra o painel de acesso.

Para obter mais detalhes sobre o painel de acesso, consulte Introdução ao painel de acesso.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicativos com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png