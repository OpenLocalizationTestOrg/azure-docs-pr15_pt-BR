<properties 
    pageTitle="Tutorial: Integração com o Active Directory do Azure com DIATRABALHO | Microsoft Azure" 
    description="Saiba como usar DIATRABALHO com o Azure Active Directory para habilitar o logon único, automatizado de provisionamento e muito mais!." 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-workday"></a>Tutorial: Integração com o Active Directory do Azure com DIATRABALHO
  
O objetivo deste tutorial é mostrar a integração do Azure e DIATRABALHO. O cenário descrito neste tutorial supõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário em DIATRABALHO
  
O cenário descrito neste tutorial consiste nos blocos de construção a seguir:

1.  Habilitar a integração de aplicativo para o dia de trabalho
2.  Configurando o logon único
3.  Configuração de provisionamento do usuário
4.  Configuração de provisionamento do usuário

![Cenário] (./media/active-directory-saas-workday-tutorial/IC782919.png "Cenário")

##<a name="enabling-the-application-integration-for-workday"></a>Habilitar a integração de aplicativo para o dia de trabalho
  
O objetivo desta seção é como habilitar a integração de aplicativo para a equipe de vendas da estrutura de tópicos.

###<a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>Para habilitar a integração de aplicativo para o dia de trabalho, execute as seguintes etapas:

1.  No portal do clássico Azure, no painel de navegação esquerdo, clique em **Active Directory**.

    ![Do Active Directory] (./media/active-directory-saas-workday-tutorial/IC700993.png "Do Active Directory")

2.  Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3.  Para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **aplicativos** no menu superior.

    ![Aplicativos] (./media/active-directory-saas-workday-tutorial/IC700994.png "Aplicativos")

4.  Para abrir a **Galeria de aplicativo**, clique em **Adicionar um aplicativo**e clique em **Adicionar um aplicativo para minha organização usar**.

    ![o que você deseja fazer?] (./media/active-directory-saas-workday-tutorial/IC700995.png "o que você deseja fazer?")

5.  Na **caixa de pesquisa**, digite **DIATRABALHO**.

    ![DIATRABALHO] (./media/active-directory-saas-workday-tutorial/IC701021.png "DIATRABALHO")

6.  No painel de resultados, selecione **o dia de trabalho**e, em seguida, clique em **concluído** para adicionar o aplicativo.

    ![DIATRABALHO] (./media/active-directory-saas-workday-tutorial/IC701022.png "DIATRABALHO")

##<a name="configuring-single-sign-on"></a>Configurando o logon único
  
O objetivo desta seção é como habilitar usuários autenticar DIATRABALHO com sua conta do Azure AD usando a federação com base no protocolo SAML da estrutura de tópicos.  
Como parte deste procedimento, é necessário criar um certificado codificado de base 64.  
Se você não estiver familiarizado com esse procedimento, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:

1.  Na página de integração de aplicativo **DIATRABALHO** , clique **logon único configurar** para abrir a caixa de diálogo **Configurar o logon único** .

    ![Configurar o logon único] (./media/active-directory-saas-workday-tutorial/IC782920.png "Configurar o logon único")

2.  Na página **como você gostaria que os usuários para assinar DIATRABALHO** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **Avançar**.

    ![Configurar o logon único] (./media/active-directory-saas-workday-tutorial/IC782921.png "Configurar o logon único")

3.  Na página **Configurar a URL do aplicativo** , execute as seguintes etapas e clique em **Avançar**.

    ![Configurar a URL do aplicativo] (./media/active-directory-saas-workday-tutorial/IC782957.png "Configurar a URL do aplicativo")

    a. Na caixa de texto de **Entrada na URL** , digite a URL usada pelos usuários para entrar no dia de trabalho usando o seguinte padrão:`https://impl.workday.com/<tenant>/login-saml2.htmld`

    b.  Na caixa de texto **DIATRABALHO resposta URL** , digite a URL de resposta de DIATRABALHO usando o seguinte padrão:`https://impl.workday.com/<tenant>/login-saml.htmld`

    >[AZURE.NOTE]A URL de resposta deve ter um subdomínio (por exemplo: www, wd2, wd3, wd3-implementação, wd5, wd5-implementação). 
    >Usar algo parecido com "*http://www.myworkday.com*" funciona, mas não "*http://myworkday.com*". 
 
4.  Na página **Configurar logon único em DIATRABALHO** , para baixar o certificado, clique em **baixar o certificado**e salve o arquivo de certificado em seu computador.

    ![Configurar o logon único] (./media/active-directory-saas-workday-tutorial/IC782922.png "Configurar o logon único")

5.  Em uma janela de navegador da web diferente, faça logon em seu site de empresa DIATRABALHO como administrador.

6.  Vá para **Menu \> ambiente de trabalho**.

    ![Ambiente de trabalho] (./media/active-directory-saas-workday-tutorial/IC782923.png "Ambiente de trabalho")

7.  Vá para **Administração de conta**.

    ![Administração de conta] (./media/active-directory-saas-workday-tutorial/IC782924.png "Administração de conta")

8.  Vá para **Editar a configuração de locatário – segurança**.

    ![Editar segurança de locatário] (./media/active-directory-saas-workday-tutorial/IC782925.png "Editar segurança de locatário")

9.  Na seção **URLs de redirecionamento** , execute as seguintes etapas:

    ![URLs de redirecionamento] (./media/active-directory-saas-workday-tutorial/IC7829581.png "URLs de redirecionamento")

    a. Clique em **Adicionar linha**.

    b. Na caixa de texto **URL de redirecionamento de Login** e a caixa de texto **URL de redirecionamento de celular** , digite a **URL de locatário DIATRABALHO** você inseriu na página **Configurar a URL do aplicativo** do portal de clássico do Azure.
    
    c. No portal do clássico Azure, na página de diálogo **Configurar logon único em DIATRABALHO** , copie a **URL do serviço Sign-Out único**e cole-o na caixa de texto **URL de redirecionamento de Logout** .

    d.  No **ambiente** de texto, digite o nome do ambiente.  


    >[AZURE.NOTE] O valor do atributo ambiente está vinculado ao valor da URL do locatário:
    >
    >-   Se o nome de domínio do locatário DIATRABALHO URL começa com implementação (por exemplo: *https://impl.workday.com/\<locatário\>/login-saml2.htmld*), o atributo de **ambiente** deve ser definido como implementação.
    >-   Se o nome de domínio começa com algo mais, você precise contatar DIATRABALHO para obter o valor de **ambiente** correspondente.

10. Na seção **Configuração de SAML** , execute as seguintes etapas:

    ![Configuração SAML] (./media/active-directory-saas-workday-tutorial/IC782926.png "Configuração SAML")

    a.  Selecione **Habilitar a autenticação de SAML**.

    b.  Clique em **Adicionar linha**.

11. Na seção provedores de identidade SAML, execute as seguintes etapas:

    ![Provedores de identidade SAML] (./media/active-directory-saas-workday-tutorial/IC7829271.png "Provedores de identidade SAML")

    a. Na caixa de texto Nome do provedor de identidade, digite um nome de provedor (por exemplo: *SPInitiatedSSO*).

    b. No portal do clássico Azure, na página de diálogo **Configurar logon único em DIATRABALHO** , copie o valor de **ID do provedor de identidade** e, em seguida, cole a caixa de texto do **emissor** .

    c. Selecione **Habilitar DIATRABALHO Initialted Logout**.

    d. No portal do clássico Azure, na página de diálogo **Configurar logon único em DIATRABALHO** , copie o valor de **URL do serviço Sign-Out único** e cole-o na caixa de texto **URL de solicitação de Logout** .


    e. Clique em **Certificado de chave pública provedor de identidade**e clique em **criar**. 

    ![Criar] (./media/active-directory-saas-workday-tutorial/IC782928.png "Criar")

    f. Clique em **criar x509 chave pública**. 
        
    ![Criar] (./media/active-directory-saas-workday-tutorial/IC782929.png "Criar")


1. Na seção **Exibir x509 chave pública** , execute as seguintes etapas: 

    ![Exibir x509 chave pública] (./media/active-directory-saas-workday-tutorial/IC782930.png "Exibir x509 chave pública") 

    a. Na caixa de texto **nome** , digite um nome para seu certificado (por exemplo: *PPE\_SP*).
        
    b. Na caixa de texto **Válido de** , digite o valor do atributo do seu certificado válido.
    
    c.  Na caixa de texto **Válido até** , digite o valor do atributo do seu certificado válido.
        
    >[AZURE.NOTE] Você pode acessar o válida de data e a válido até a data do certificado baixado clicando duas vezes nele. As datas são listadas sob a guia **detalhes** .

    d. Crie um arquivo **codificado na Base 64** do seu certificado baixado.  

    >[AZURE.TIP] Para obter mais detalhes, consulte [como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    e.  Abra seu certificado codificado de base 64 no bloco de notas e copie o conteúdo dela.
    
    f.  Na caixa de texto do **certificado** , cole o conteúdo da área de transferência.
    
    g.  Clique em **Okey**.

12.  Execute as seguintes etapas: 

    ![Configuração de SSO] (./media/active-directory-saas-workday-tutorial/IC7829351111.png "Configuração de SSO")

    a.  Habilitar o **x509 par de chaves particular**.

    b.  Na caixa de texto **Identificação do provedor de serviço** , digite **http://www.workday.com**.

    c.  Selecione **Habilitar SP iniciada SAML autenticação**.

    d.  No portal do clássico Azure, na página de diálogo **Configurar logon único em DIATRABALHO** , copie o valor de **Single Sign-On URL do serviço** e cole-o na caixa de texto **URL do serviço de SSO IdP** .
     
    e. Selecione **não Deflate a solicitação de autenticação iniciado pelo SP**.

    f. Como o **Método de assinatura de solicitação de autenticação**, selecione **SHA256**. 
        
    ![Método de assinatura de solicitação de autenticação] (./media/active-directory-saas-workday-tutorial/IC782932.png "Método de assinatura de solicitação de autenticação") 
 
    g. Clique em **Okey**. 
        
    ![OK] (./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

12. No portal do clássico Azure, na página **Configurar logon único no dia de trabalho** , clique em **Avançar**. 

    ![Configurar o logon único] (./media/active-directory-saas-workday-tutorial/IC782934.png "Configurar o logon único")

13. Na página **confirmação de logon única** , clique em **Concluir**. 

    ![Configurar o logon único] (./media/active-directory-saas-workday-tutorial/IC782935111.png "Configurar o logon único")



##<a name="configuring-user-provisioning"></a>Configuração de provisionamento do usuário
  
Para obter um usuário de teste provisionado no dia de trabalho, você precise contatar a equipe de suporte do dia de trabalho.  
A equipe de suporte DIATRABALHO criará o usuário para você.

##<a name="assigning-users"></a>Atribuir usuários
  
Para testar sua configuração, você precisa conceder os usuários do Azure AD que deseja permitir usando a atribuí-los de seu aplicativo o acesso a ele.

###<a name="to-assign-users-to-workday-perform-the-following-steps"></a>Para atribuir usuários a DIATRABALHO, execute as seguintes etapas:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do aplicativo **DIATRABALHO **, clique em **atribuir aos usuários**.

    ![Atribuir aos usuários] (./media/active-directory-saas-workday-tutorial/IC782935.png "Atribuir aos usuários")

3.  Selecione o usuário de teste, clique em **atribuir**e clique em **Sim** para confirmar sua atribuição.

    ![Sim] (./media/active-directory-saas-workday-tutorial/IC767830.png "Sim")
  
Se você quiser testar suas configurações de logon única, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).