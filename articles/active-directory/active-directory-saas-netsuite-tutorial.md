<properties
    pageTitle="Tutorial: Integração com o Active Directory do Azure com NetSuite | Microsoft Azure"
    description="Saiba como usar NetSuite com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="05/16/2016"
    ms.author="asmalser-msft"/>

#<a name="tutorial-how-to-integrate-netsuite-with-azure-active-directory"></a>Tutorial: Como integrar NetSuite com o Active Directory do Azure

Este tutorial mostrará como conectar seu ambiente NetSuite ao Azure Active Directory (AD Azure). Você aprenderá como configurar o logon único para NetSuite, como habilitar o provisionamento automatizado de usuário e como atribuir usuários tenham acesso aos NetSuite. 

##<a name="prerequisites"></a>Pré-requisitos

1. Para acessar o Active Directory do Azure por meio do [portal clássico Azure](https://manage.windowsazure.com), primeiro você deve ter uma assinatura válida do Azure.

2. Você deve ter acesso de administrador para uma assinatura de [NetSuite](http://www.netsuite.com/portal/home.shtml) . Você pode usar uma conta de avaliação gratuita para o serviço.

##<a name="step-1-add-netsuite-to-your-directory"></a>Etapa 1: Adicionar NetSuite ao seu diretório

1. No [portal do Azure clássico](https://manage.windowsazure.com), no painel de navegação esquerdo, clique em **Active Directory**.

    ![Selecione o Active Directory do painel de navegação à esquerda.][0]

2. Na lista de **diretório** , selecione o diretório que você gostaria de adicionar NetSuite para.

3. Clique em **aplicativos** no menu superior.

    ![Clique em aplicativos.][1]

4. Clique em **Adicionar** na parte inferior da página.

    ![Clique em Adicionar para adicionar um novo aplicativo.][2]

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Clique em Adicionar um aplicativo da Galeria.][3]

6. Na **caixa de pesquisa**, digite **NetSuite**. Em seguida, selecione **NetSuite** nos resultados e clique em **concluído** para adicionar o aplicativo.

    ![Adicione NetSuite.][4]

7. Agora, você verá a página de início rápido para NetSuite:

    ![Página de início rápido do NetSuite no Azure AD][5]

##<a name="step-2-enable-single-sign-on"></a>Etapa 2: Habilitar logon único

1. No Azure AD, na página de início rápido para NetSuite, clique no botão de **logon único configurar** .

    ![O único logon botão Configurar][6]

2. Uma caixa de diálogo será aberta e você verá uma tela que pergunta "Como você gostaria usuários para entrar NetSuite?" Selecione **Azure AD Single Sign-On**e clique em **Avançar**.

    ![Selecione Azure AD Single Sign-On][7]

    > [AZURE.NOTE] Para aprender mais sobre as diferentes única logon opções, [clique aqui](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

3. Na página **Definir configurações de aplicativo** , para o campo de **URL de resposta** , digite sua URL de locatário NetSuite usando um dos seguintes formatos:
    - `https://<tenant-name>.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.netsuite.com/saml2/acs`
    - `https://<tenant-name>.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    ![Digite sua URL locatário][8]

4. Na página **Configurar logon único em NetSuite** , clique em **baixar metadados**e salve o arquivo de certificado localmente em seu computador.

    ![Baixe os metadados.][9]

5. Abrir uma nova guia em seu navegador e entre no site da sua empresa Netsuite como administrador.

6. Na barra de ferramentas na parte superior da página, clique em **configuração**, clique em **Gerenciador de configuração**.

    ![Vá para o Gerenciador de configuração][10]

7. Na lista de **Tarefas de configuração** , selecione **integração**.

    ![Acesse a integração][11]

8. Na seção **Autenticação de gerenciar** , clique em **SAML Single Sign-on**.

    ![Vá para SAML Single Sign-on][12]

9. Na página **Configuração de SAML** , execute as seguintes etapas:

    - No Active Directory do Azure, copie o valor de **URL de Login remoto** e cole-o no campo **Página de Login do provedor de identidade** em NetSuite.

        ![A página de configuração de SAML.][13]

    - Na NetSuite, selecione **O método de autenticação primária**.

    - Para o campo rotulado **SAMLV2 metadados de provedor de identidade**, selecione o **Arquivo de metadados de IDP carregar**. Clique em **Procurar** para carregar o arquivo de metadados que você baixou na etapa 4 #.

        ![Carregar os metadados][16]

    - Clique em **Enviar**.

9. No Azure AD, marque a caixa de seleção de confirmação de configuração de logon único para habilitar o certificado que você carregou em NetSuite. Clique em **Avançar**.

    ![Marque a caixa de seleção de confirmação][14]

10. Na página final da caixa de diálogo, digite um endereço de email se você gostaria de receber notificações de email para erros e avisos relacionados à manutenção dessa configuração de logon único. 

    ![Digite seu endereço de email.][15]

11. Clique em **Concluir** para fechar a caixa de diálogo. Em seguida, clique em **atributos** na parte superior da página.

    ![Clique em atributos.][17]

12. Clique em **Adicionar atributo de usuário**.

    ![Clique em Adicionar atributo de usuário.][18]

13. Para o campo **Nome do atributo** , digite na `account`. Para o campo de **Valor do atributo** , digite sua ID de conta NetSuite. Instruções sobre como localizar sua ID de conta estão incluídas abaixo:

    ![Adicionar sua ID de conta NetSuite.][19]

    - No NetSuite, clique em **configuração** no menu de navegação superior.
    - Clique em abaixo da seção de **Tarefas de configuração** do menu de navegação à esquerda, selecione a seção **integração** e clique em **Preferências de serviços da Web**.
    - Copie seu ID de conta NetSuite e cole-o no campo de **Valor do atributo** no Azure AD.

        ![Obter sua ID de conta][20]

14. No Azure AD, clique em **Concluir** para concluir a adição de atributo SAML. Em seguida, clique em **Aplicar alterações** no menu inferior.

    ![Salve as alterações.][21]

15. Antes que os usuários podem realizar logon único em NetSuite, elas devem primeiro ser atribuídas as permissões apropriadas no NetSuite. Siga as instruções abaixo para atribuir essas permissões.

    - No menu de navegação superior, clique em **configuração**, clique em **Gerenciador de configuração**.

        ![Vá para o Gerenciador de configuração][10]

    - No menu de navegação à esquerda, selecione **Usuários/funções**, clique em **Gerenciar funções**.

        ![Ir para gerenciar funções][22]

    - Clique em **nova função**.

    - Digite um **nome** para a nova função e selecione a caixa de seleção **Single Sign-On somente** .

        ![Nomeie a nova função.][23]

    - Clique em **Salvar**.

    - No menu na parte superior, clique em **permissões**. Clique em **Configurar**.

        ![Acesse permissões][24]

    - Selecione **definir o SAM logon único**e clique em **Adicionar**.

    - Clique em **Salvar**.

    - No menu de navegação superior, clique em **configuração**, clique em **Gerenciador de configuração**.

        ![Vá para o Gerenciador de configuração][10]

    - No menu de navegação à esquerda, selecione **Usuários/funções**, clique em **Gerenciar usuários**.

        ![Vá para gerenciar usuários][25]

    - Selecione um usuário de teste. Clique em **Editar**.

        ![Vá para gerenciar usuários][26]

    - Na caixa de diálogo funções, selecione a função que você criou e clique em **Adicionar**.

        ![Vá para gerenciar usuários][27]

    - Clique em **Salvar**.

19. Para testar sua configuração, consulte a seção abaixo, intitulada [Atribuir aos usuários NetSuite](#step-4-assign-users-to-netsuite).

##<a name="step-3-enable-automated-user-provisioning"></a>Etapa 3: Ative provisionamento automatizado de usuário

> [AZURE.NOTE] Por padrão, os usuários provisionados serão adicionados à subsidiária raiz do seu ambiente NetSuite.

1. No Active Directory do Azure, na página de início rápido para NetSuite, clique no **provisionamento de usuário de configurar**.

    ![Configurar provisionamento do usuário][28]

2. Na caixa de diálogo que é aberta, digite suas credenciais de administrador para NetSuite, clique em **Avançar**.

    ![Digite suas credenciais de administrador NetSuite.][29]

3. Na página confirmação, digite seu endereço de email para receber notificações de provisionamento falhas.

    ![Confirme.][30]

4. Clique em **Concluir** para fechar a caixa de diálogo.

##<a name="step-4-assign-users-to-netsuite"></a>Etapa 4: Atribuir usuários a NetSuite

1. Para testar sua configuração, começar a criar uma nova conta de teste no diretório.

2. Na página de início rápido do NetSuite, clique no botão **Atribuir usuários** .

    ![Clique em atribuir usuários][31]

3. Selecione o usuário de teste e clique no botão **atribuir** na parte inferior da tela:

 - Se ainda não habilite provisionamento automatizado de usuário, você verá a seguinte solicitação para confirmar:

        ![Confirm the assignment.][32]

 - Se você tiver habilitado o provisionamento automatizado do usuário, você verá um aviso para definir o tipo de função, o usuário deve ter no NetSuite. Usuários recentemente provisionados devem aparecer no seu ambiente de NetSuite após alguns minutos.

4. Para testar suas configurações de logon única, abra o painel de acesso em [https://myapps.microsoft.com](https://myapps.microsoft.com/), entre na conta do teste e clique em **NetSuite**.

##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png
