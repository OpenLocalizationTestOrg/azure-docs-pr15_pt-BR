<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com Veracode | Microsoft Azure" 
    description="Saiba como usar Veracode com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-veracode"></a>Tutorial: Integração com o Active Directory do Azure com Veracode
  
O objetivo deste tutorial é mostrar a integração do Azure e Veracode. O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Veracode logon único habilitado para assinatura
  
Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Veracode será capazes de logon único para o aplicativo usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Veracode
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-veracode-tutorial/IC802903.png "Cenário")

##<a name="enabling-the-application-integration-for-veracode"></a>Habilitar a integração de aplicativo para Veracode
  
O objetivo desta seção é como habilitar a integração de aplicativo para Veracode da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-veracode-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Veracode, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-veracode-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-veracode-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-veracode-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-veracode-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Veracode**.

    ![Galeria de aplicativo] (./media/active-directory-saas-veracode-tutorial/IC802904.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Veracode**e clique em **concluído** para adicionar o aplicativo.

    ![Veracode] (./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar Veracode com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Seu aplicativo Veracode espera as declarações SAML em um formato específico, o que requer que você adicionar os mapeamentos de atributo personalizado à sua configuração de **atributos de token saml** .  
A captura de tela a seguir mostra um exemplo para isso.

![Atributos] (./media/active-directory-saas-veracode-tutorial/IC802906.png "Atributos")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Veracode** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-veracode-tutorial/IC802907.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Veracode** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-veracode-tutorial/IC802908.png "Configurar o logon único")

3.  Na página **Definir configurações de aplicativo** , clique em **Avançar**.

    ![Definir configurações de aplicativo] (./media/active-directory-saas-veracode-tutorial/IC802909.png "Definir configurações de aplicativo")

4.  Na página **Configurar logon único em Veracode** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-veracode-tutorial/IC802910.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Veracode como administrador.

6.  No menu na parte superior, clique em **configurações**e, em seguida, clique em **administrador**.

    ![Administração] (./media/active-directory-saas-veracode-tutorial/IC802911.png "Administração")

7.  Clique na guia **SAML** .

8.  Na seção **Configurações de SAML da organização** , execute as seguintes etapas:

    ![Administração] (./media/active-directory-saas-veracode-tutorial/IC802912.png "Administração")

    1.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Veracode** , copie o valor de **URL do emissor** e, em seguida, cole a caixa de texto do **emissor**
    2.  Para carregar seu certificado baixado, clique em **Escolher arquivo**.
    3.  Selecione **Ativar o registro automático**.

9.  Na seção **Configurações de registro automático** , execute as seguintes etapas e clique em **Salvar**:

    ![Administração] (./media/active-directory-saas-veracode-tutorial/IC802913.png "Administração")

    1.  Como **Ativação do novo usuário**, selecione **Sem ativação necessária**.
    2.  Como **As atualizações de dados de usuário**, selecione **Dados de preferência de usuário Veracode**.
    3.  Para **Obter detalhes de atributo SAML**, selecione o seguinte:
        -   **Funções de usuário**
        -   **Administrador de políticas**
        -   **Revisor**
        -   **Prazo de entrega de segurança**
        -   **Executivo**
        -   **Emissor**
        -   **Criador**
        -   **Todos os tipos de verificação**
        -   **Membros de equipe**
        -   **Equipe padrão**

10. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-veracode-tutorial/IC802914.png "Configurar o logon único")

11. No menu na parte superior, clique em **atributos** para abrir a caixa de diálogo de **Atributos de Token SAML** .

    ![Atributos] (./media/active-directory-saas-veracode-tutorial/IC795920.png "Atributos")

12. Para adicionar os mapeamentos de atributo obrigatório, execute as seguintes etapas:

    ![Atributos] (./media/active-directory-saas-veracode-tutorial/IC802906.png "Atributos")

  	| Nome do atributo | Valor do atributo |
  	|:---------------|:----------------|
  	| nome      | User.givenName  |
  	| Sobrenome       | User.surname    |
  	| Email          | User.mail       |

    1.  Para cada linha de dados na tabela acima, clique em **Adicionar atributo de usuário**.
    
    2.  Na caixa de texto **Nome do atributo** , digite o nome do atributo mostrado para aquela linha.

    3.  Na caixa de texto **Valor do atributo** , selecione o valor do atributo mostrado para aquela linha.

    4.  Clique em **Concluir**.

13. Clique em **Aplicar alterações**.

##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para permitir que usuários do Azure AD fazer login Veracode, ele devem ser provisionados em Veracode.  
No caso de Veracode, provisionamento é uma tarefa automatizada.  
Não há nenhum item de ação para você...
  
Os usuários são criados automaticamente se for necessário durante a primeira única logon tentativa.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Veracode usuário conta ou APIs fornecidas pela Veracode para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-veracode-perform-the-following-steps"></a>Para atribuir usuários a Veracode, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Veracode **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-veracode-tutorial/IC802915.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-veracode-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).