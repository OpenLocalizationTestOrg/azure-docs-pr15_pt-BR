<properties 
    pageTitle="Tutorial: Integração de integração Active Directory do Azure com Druva | Microsoft Azure" 
    description="Saiba como usar Druva com o Azure Active Directory para habilitar o logon único, provisionamento automatizado e muito mais!" 
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

#<a name="tutorial-azure-active-directory-integration-integration-with-druva"></a>Tutorial: Integração de integração Active Directory do Azure com Druva

O objetivo deste tutorial é mostrar a integração do Azure e Druva.  
O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um Druva logon único habilitado para assinatura

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu a Druva poderão único entrar no aplicativo no site da sua empresa Druva (sinal do serviço provedor iniciada no), ou usando a [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para Druva
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Atribuir usuários

![Cenário] (./media/active-directory-saas-druva-tutorial/IC795084.png "Cenário")
##<a name="enabling-the-application-integration-for-druva"></a>Habilitar a integração de aplicativo para Druva

O objetivo desta seção é como habilitar a integração de aplicativo para Druva da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-druva-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para Druva, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-druva-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-druva-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo] (./media/active-directory-saas-druva-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo da Galeria**.

    ![Adicionar um aplicativo de gallerry] (./media/active-directory-saas-druva-tutorial/IC749322.png "Adicionar um aplicativo de gallerry")

6.  Na **caixa de pesquisa**, digite **Druva**.

    ![Galeria de aplicativo] (./media/active-directory-saas-druva-tutorial/IC795085.png "Galeria de aplicativo")

7.  No painel de resultados, selecione **Druva**e clique em **concluído** para adicionar o aplicativo.

    ![Druva] (./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
##<a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é como habilitar usuários autenticar Druva com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um arquivo de certificado codificado base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

Seu aplicativo Druva espera as declarações SAML em um formato específico, o que requer que você adicionar os mapeamentos de atributo personalizado à sua configuração de **atributos de token saml** .  
A captura de tela a seguir mostra um exemplo para isso.

![Atributos de Token de SAML] (./media/active-directory-saas-druva-tutorial/IC795087.png "Atributos de Token de SAML")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  No portal do clássico Azure, na página de integração do aplicativo **Druva** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-druva-tutorial/IC795027.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar Druva** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-druva-tutorial/IC795088.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , na caixa de texto **Druva entrada na URL** , digite a URL usada pelos usuários para entrar seu aplicativo de Druva (por exemplo: "*https://cloud.druva.com/home/*") e, em seguida, clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-druva-tutorial/IC795089.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único em Druva** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado localmente em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-druva-tutorial/IC795090.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa Druva como administrador.

6.  Vá para **Gerenciar \> configurações**.

    ![Configurações] (./media/active-directory-saas-druva-tutorial/IC795091.png "Configurações")

7.  Na caixa de diálogo Configurações de logon único, execute as seguintes etapas:

    ![Elemento Únic logon configurações] (./media/active-directory-saas-druva-tutorial/IC795092.png "Elemento Únic logon configurações")

    1.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Druva** , copie o valor de **URL de Login remoto** e cole-o na caixa de texto **URL de Login do provedor de ID** .
    2.  No portal do clássico Azure, na página de diálogo **Configurar logon único em Druva** , copie o valor de **URL Logout remota** e cole-o na caixa de texto **URL de Logout do ID do provedor** .
    3.  Crie um arquivo **codificado na base 64** do seu certificado baixado.  

        >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abrir o certificado de codificada de base 64 no bloco de notas, copie o conteúdo dela para sua área de transferência e colá-lo à caixa de texto **Identificação do provedor de certificado**
    5.  Para abrir a página de **configurações** , clique em **Salvar**.

8.  Na página **configurações** , clique em **Gerar o Token de SSO**.

    ![Configurações] (./media/active-directory-saas-druva-tutorial/IC795093.png "Configurações")

9.  Na caixa de diálogo **Single Sign-on autenticação de Token** , execute as seguintes etapas:

    ![Símbolo SSO] (./media/active-directory-saas-druva-tutorial/IC795094.png "Símbolo SSO")

    1.  Clique em **Copiar**.
    2.  Clique em **Fechar**.

10. No portal do Azure clássico, selecione a confirmação de configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-druva-tutorial/IC795095.png "Configurar o logon único")

11. No menu na parte superior, clique em **atributos** para abrir a caixa de diálogo de **Atributos de Token SAML** .

    ![Atributos] (./media/active-directory-saas-druva-tutorial/IC795096.png "Atributos")

12. Para adicionar os mapeamentos de atributo obrigatório, execute as seguintes etapas:

  	|Nome do atributo|Valor do atributo|
  	|---|---|
  	|sincronia\_auth\_token|<*valor de área de transferência*>|

    1.  Para cada linha de dados na tabela acima, clique em **Adicionar atributo de usuário**.
    2.  Na caixa de texto **Nome do atributo** , digite o nome do atributo mostrado para aquela linha.
    3.  Na caixa de texto **Valor do atributo** , digite o valor do atributo mostrado para aquela linha.
    4.  Clique em **Concluir**.

13. Clique em **Aplicar alterações**.
##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário

Para permitir que usuários do Azure AD fazer login Druva, ele devem ser provisionados em Druva.  
No caso de Druva, provisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar provisionamento do usuário, execute as seguintes etapas:

1.  Faça logon site da sua empresa **Druva** como administrador.

2.  Vá para **Gerenciar \> usuários**.

    ![Gerenciar usuários] (./media/active-directory-saas-druva-tutorial/IC795097.png "Gerenciar usuários")

3.  Clique em **Criar novo**.

    ![Gerenciar usuários] (./media/active-directory-saas-druva-tutorial/IC795098.png "Gerenciar usuários")

4.  Na caixa de diálogo Criar novo usuário, execute as seguintes etapas:

    ![Criar Novo_usuário] (./media/active-directory-saas-druva-tutorial/IC795099.png "Criar Novo_usuário")

    1.  Digite o endereço de email e o nome de uma conta de usuário do Active Directory do Azure válida que deseja provisionar em caixas de texto relacionadas.
    2.  Clique em **Criar usuário**.

>[AZURE.NOTE] Você pode usar as ferramentas de criação de outros Druva usuário conta ou APIs fornecidas pela Druva para provisionar AAD contas de usuário.

##<a name="assigning-users"></a>Atribuir usuários

Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-druva-perform-the-following-steps"></a>Para atribuir usuários a Druva, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **Druva **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-druva-tutorial/IC795100.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-druva-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).
