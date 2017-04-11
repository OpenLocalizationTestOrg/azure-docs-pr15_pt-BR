<properties
    pageTitle="Introdução ao Azure Active Directory proteção de identidade e Microsoft Graph | Microsoft Azure"
    description="Fornece uma introdução ao Microsoft Graph de consulta para uma lista de eventos de risco e informações associadas do Azure Active Directory."
    services="active-directory"
    keywords="proteção de identidade do active directory do Azure, evento de risco, vulnerabilidade, política de segurança, Microsoft Graph"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi"/>

# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introdução ao Azure Active Directory proteção de identidade e Microsoft Graph

Microsoft Graph é o ponto de extremidade de API de unificado da Microsoft e a página inicial do [Azure Active Directory identidade proteção](active-directory-identityprotection.md) APIs. Nossa primeira API, **identityRiskEvents**, permite a consulta Microsoft Graph para uma lista de [eventos de risco](active-directory-identityprotection-risk-events-types.md) e informações associadas. Este artigo apresenta consultando essa API. Para uma profundidade em Introdução, documentação completa e acesso ao Gerenciador de gráfico, consulte o [site do Microsoft Graph](https://graph.microsoft.io/).

Há três etapas para acessar os dados de proteção de identidade por meio do Microsoft Graph:

1. Adicione um aplicativo com um segredo de cliente. 

2. Use esse segredo e algumas outras partes de informação para autenticar Microsoft Graph, onde você recebe um token de autenticação. 

3. Use esse token para fazer as solicitações para o ponto de extremidade de API e obter dados de proteção de identidade.

Antes de começar, você precisará:

- Privilégios de administrador para criar o aplicativo no Azure AD
- O nome de domínio do seu locatário (por exemplo, contoso.onmicrosoft.com)



## <a name="add-an-application-with-a-client-secret"></a>Adicionar um aplicativo com um segredo de cliente


1. [Entrar no](https://manage.windowsazure.com) seu portal clássico Azure como administrador. 

1. No painel de navegação esquerdo, clique em **Active Directory**. 

    ![Criando um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)

2. Na lista de **diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretório.

3. No menu na parte superior, clique em **aplicativos**.

    ![Criando um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)

4. Clique em **Adicionar** na parte inferior da página.

    ![Criando um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)

5. Na caixa de diálogo **o que você deseja fazer** , clique em **Adicionar um aplicativo minha organização está desenvolvendo**.

    ![Criando um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)


5. Na caixa de diálogo **Conte-nos sobre seu aplicativo** , execute as seguintes etapas:

    ![Criando um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)

    a. Na caixa de texto **nome** , digite um nome para seu aplicativo (por exemplo: aplicativo de API de evento de risco AADIP).

    b. Como **tipo**, selecione **Web Application e/ou Web API**.

    c. Clique em **Avançar**.


5. Na caixa de diálogo **Propriedades do aplicativo** , execute as seguintes etapas:

    ![Criando um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)

    a. Na caixa de texto **URL de logon** , digite `http://localhost`.

    b. Na caixa de texto de **URI de ID do aplicativo** , digite `http://localhost`.

    c. Clique em **Concluir**.


Agora, você pode configurar o seu aplicativo.

![Criando um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)



## <a name="grant-your-application-permission-to-use-the-api"></a>Conceder permissão ao seu aplicativo para usar a API


1. Na página do seu aplicativo, no menu na parte superior, clique em **Configurar**. 

    ![Criando um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)

2. Na seção **permissões para outros aplicativos** , clique em **Adicionar aplicativo**.

    ![Criando um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)


2. Na caixa de diálogo **permissões para outros aplicativos** , execute as seguintes etapas:

    ![Criando um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)

    a. Selecione **Microsoft Graph**.

    b. Clique em **Concluir**.


1. Clique em **permissões de aplicativo: 0**e selecione **Ler todas as informações de evento de risco de identidade**.

    ![Criando um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)

1. Clique em **Salvar** na parte inferior da página.

    ![Criando um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)


## <a name="get-an-access-key"></a>Obtenha uma tecla de acesso

1. Na página do seu aplicativo, na seção de **chaves** , selecione 1 ano como duração.

    ![Criando um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)

1. Clique em **Salvar** na parte inferior da página.

    ![Criando um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

1. na seção teclas, copie o valor da sua chave recém-criado e, em seguida, colá-lo em um local seguro.

    ![Criando um aplicativo](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)

    > [AZURE.NOTE] Se você perder a chave, você terá que voltar para esta seção e criar uma nova chave. Manter esta tecla um segredo: qualquer pessoa que tenha ele pode acessar seus dados.


1. Na seção **Propriedades** , copie a **ID do cliente**e, em seguida, colá-lo em um local seguro. 


## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Autenticar para o Microsoft Graph e consultar a API de eventos de risco de identidade

Neste ponto, você deve ter:

- A ID do cliente que você copiou acima

- A chave que você copiou acima

- O nome de domínio do seu locatário


Para autenticar, envie uma solicitação de postagem para `https://login.microsoft.com` com os seguintes parâmetros no corpo:

- grant_type: "**client_credentials**"

- recurso: "**https://graph.microsoft.com**"

- client_id:<your client ID>

- client_secret:<your key>


> [AZURE.NOTE] Você precisa fornecer valores para o **client_id** e o parâmetro **client_secret** .

Se for bem-sucedido, isso retorna um token de autenticação.  
Para chamar a API, crie um cabeçalho com o parâmetro a seguir:

    `Authorization`=”<token_type> <access_token>"


Durante a autenticação, você pode encontrar o tipo de token e o token de acesso no token retornado.

Envie esse cabeçalho como uma solicitação para a seguinte URL de API:`https://graph.microsoft.com/beta/identityRiskEvents`

A resposta, se tiver êxito, é uma coleção de eventos de risco de identidade e dados associados no formato OData JSON, que pode ser analisado e tratado como ajustá-la.

Aqui está o código de amostra para autenticar e chamar a API usando o Powershell.  
Basta adicionar seu ID do cliente, tecla e domínio de locatários.

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>Próximas etapas

Parabéns, você acabou de criar sua primeira chamada para o Microsoft Graph!  
Agora você pode consultar eventos de risco de identidade e usar os dados, porém ajustá-la.

Para saber mais sobre o Microsoft Graph e como criar aplicativos usando a API do gráfico, verifique a [documentação](https://graph.microsoft.io/docs) e muito mais no [site do Microsoft Graph](https://graph.microsoft.io/). Além disso, certifique-se marcar a página de [API de proteção de identidade do Azure AD](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) que lista todas as APIs de proteção de identidade disponíveis no gráfico. Como adicionamos novas maneiras de trabalhar com proteção de identidade por meio da API, você verá na página.


## <a name="additional-resources"></a>Recursos adicionais

- [Proteção de identidade do Active Directory do Azure](active-directory-identityprotection.md)

- [Tipos de eventos de risco detectados pelo Azure Active Directory proteção de identidade](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Visão geral do Microsoft Graph](https://graph.microsoft.io/docs)

- [Raiz de serviço de proteção de identidade do Azure AD](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)
