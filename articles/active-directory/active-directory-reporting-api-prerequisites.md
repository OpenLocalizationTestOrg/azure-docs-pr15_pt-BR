<properties
    pageTitle="Pré-requisitos para acessar o Azure AD API de relatório. | Microsoft Azure"
    description="Saiba mais sobre os pré-requisitos para acessar o Azure AD API de relatório"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Pré-requisitos para acessar o Azure AD API de relatório 

O [Azure AD reporting APIs](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) fornecer acesso através de programação aos dados por meio de um conjunto de APIs baseado em REST. Você pode chamar essas APIs de uma variedade de ferramentas e linguagens de programação.

A API do relatório usa [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar o acesso à web APIs. 

Para preparar seu acesso à API do relatório, faça o seguinte:

1. Criar um aplicativo em seu locatário do Azure AD 

2. Conceder as permissões apropriadas do aplicativo para acessar os dados do Azure AD

3. Reúna as configurações do seu diretório

Para dúvidas, problemas ou comentários, entre em contato com o [AAD relatórios ajudam](mailto:aadreportinghelp@microsoft.com).


## <a name="create-an-azure-ad-application"></a>Criar um aplicativo do Azure AD

Para configurar o diretório para acessar o Azure AD API de relatório, você deve entrar no portal do Azure clássico com uma conta de administrador de assinatura Azure que também é um membro da função de diretório do Administrador Global no seu locatário do Azure AD.

> [AZURE.IMPORTANT] Aplicativos executados com credenciais com privilégios de "administrador" assim podem ser muito poderosos, portanto Lembre-se de manter credenciais de identificação/segredo do aplicativo seguro.


1. No [portal do Azure clássico](https://manage.windowsazure.com), no painel de navegação esquerdo, clique em **Active Directory**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Na lista **do active directory** , selecione seu diretório.

3. No menu na parte superior, clique em **aplicativos**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Na barra de ferramentas inferior, clique em **Adicionar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/03.png) 

5. Sobre o **o que você deseja fazer?** caixa de diálogo, clique em **Adicionar um aplicativo minha organização está desenvolvendo**. 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/04.png) 


6. Na caixa de diálogo **Conte-nos sobre seu aplicativo** , execute as seguintes etapas: 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/05.png) 

    a. Na caixa de texto **nome** , digite um nome (por exemplo: aplicativo de API de relatório).

    b. Selecione o **aplicativo da Web e/ou API da web**.

    c. Clique em **Avançar**.


7. Na caixa de diálogo **Propriedades do aplicativo** , execute as seguintes etapas: 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/06.png) 

    a. Na caixa de texto **URL de logon** , digite `https://localhost`.

    b. Na caixa de texto de **URI de ID do aplicativo** , digite ```https://localhost/ReportingApiApp```.

    c. Clique em **Concluir**.



## <a name="grant-your-application-permission-to-use-the-api"></a>Conceder permissão ao seu aplicativo para usar a API

1. No [portal do Azure clássico](https://manage.windowsazure.com/), no painel de navegação esquerdo, clique em **Active Directory**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Na lista **do active directory** , selecione seu diretório.

3. No menu na parte superior, clique em **aplicativos**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/02.png)


3. Na lista de aplicativos, selecione seu aplicativo recém-criado.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/07.png)

4. No menu na parte superior, clique em **Configurar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/08.png)


5. Na seção **permissões para outros aplicativos** , para o recurso do **Active Directory do Azure** , clique na lista suspensa de **Permissões de aplicativo** e selecione **os dados de diretório de leitura**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/09.png)


5. Na barra de ferramentas inferior, clique em **Salvar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/10.png)


## <a name="gather-configuration-settings-from-your-directory"></a>Reúna as configurações do seu diretório

Esta seção mostra como obter as seguintes configurações do seu diretório:

- Nome de domínio
- ID do cliente
- Secreta do cliente

Você precisa esses valores ao configurar chamadas para a API de relatórios. 


### <a name="get-your-domain-name"></a>Obtenha o seu nome de domínio

1. No [portal do Azure clássico](https://manage.windowsazure.com), no painel de navegação esquerdo, clique em **Active Directory**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Na lista **do active directory** , selecione seu diretório.

3. No menu na parte superior, clique em **domínios**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/11.png) 

4. Na coluna **Nome do domínio** , copie o seu nome de domínio.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/12.png) 


### <a name="get-the-applications-client-id"></a>Obter ID do cliente do aplicativo

1. No [portal do Azure clássico](https://manage.windowsazure.com), no painel de navegação esquerdo, clique em **Active Directory**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Na lista **do active directory** , selecione seu diretório.

3. No menu na parte superior, clique em **aplicativos**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Na lista de aplicativos, selecione seu aplicativo recém-criado.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/07.png)

4. No menu na parte superior, clique em **Configurar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/08.png)

4. Copie sua **ID do cliente**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/13.png)


### <a name="get-the-applications-client-secret"></a>Obter segredo de cliente do aplicativo

Para obter segredo de cliente do seu aplicativo, você precisa criar uma nova chave e salvar seu valor ao salvar a nova chave porque não é possível recuperar esse valor posteriormente mais.

1. No [portal do Azure clássico](https://manage.windowsazure.com), no painel de navegação esquerdo, clique em **Active Directory**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Na lista **do active directory** , selecione seu diretório.

3. No menu na parte superior, clique em **aplicativos**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Na lista de aplicativos, selecione seu aplicativo recém-criado.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/07.png)

4. No menu na parte superior, clique em **Configurar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/08.png)

5. Na seção de **chaves** , execute as seguintes etapas: 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/14.png)

    a. Na lista de duração, selecione uma duração

    b. Na barra de ferramentas inferior, clique em **Salvar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/10.png)

    c. Copie o valor da chave.

## <a name="next-steps"></a>Próximas etapas

- Você gostaria de acessar os dados do Azure anúncio API de relatório de maneira programação? Consulte [Introdução ao Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md).

- Se você gostaria de saber mais sobre relatórios do Azure Active Directory, consulte o [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).  
