<properties
   pageTitle="Início rápido para o gráfico de AD Azure API | Microsoft Aure"
   description="A API do Azure Active Directory Graph fornece acesso através de programação ao Azure AD através de pontos de extremidade da API REST de OData. Aplicativos podem usar a API do gráfico para executar criar, ler, atualizar e excluir operações (CRUD) nos dados de diretório e objetos."
   services="active-directory"
   documentationCenter="n/a"
   authors="PatAltimore"
   manager="mbaldwin"
   editor=""
   tags=""/>


   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="09/16/2016"
      ms.author="patricka"/>

# <a name="quickstart-for-the-azure-ad-graph-api"></a>Início rápido para o gráfico de AD API do Azure

A API de gráfico do Azure Active Directory (AD) fornece acesso por programação ao Azure AD através de pontos de extremidade da API REST de OData. Aplicativos podem usar a API do gráfico para executar criar, ler, atualizar e excluir operações (CRUD) nos dados de diretório e objetos. Por exemplo, você pode usar a API do gráfico para criar um novo usuário, exibir ou atualizar as propriedades do usuário, alterar senha do usuário, verificar a associação a um grupo de acesso baseado em função, desabilitar ou excluir o usuário. Para obter mais informações sobre os recursos de gráfico API e cenários de aplicativos, consulte [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) e [os pré-requisitos de API do Azure AD Graph](https://msdn.microsoft.com/library/hh974476.aspx). 

> [AZURE.IMPORTANT] Funcionalidade do Azure AD Graph API também está disponível por meio do [Microsoft Graph](https://graph.microsoft.io/), uma API unificada que inclui APIs de outros serviços da Microsoft como Outlook, OneDrive, o OneNote, planejador e Office Graph, acessível por meio de uma única empresa e com um token de acesso único.

## <a name="how-to-construct-a-graph-api-url"></a>Como construir uma URL de API do gráfico

Na API de gráfico, para acessar os dados de diretório e objetos (em outras palavras, recursos ou entidades) em relação à qual você deseja executar operações CRUD, você pode usar URLs com base no protocolo de dados aberta (OData). As URLs usadas no gráfico API consistem de quatro partes principais: raiz, identificador de locatário, caminho do recurso e opções de cadeia de caracteres de consulta de serviço: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Veja o exemplo de URL a seguir: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

- **Raiz de serviço**: no Azure AD API do gráfico, a raiz do serviço é sempre https://graph.windows.net.
- **Identificador do locatário**: isso pode ser um nome de domínio (registrado) verificado, no exemplo acima, contoso.com. Ele também pode ser um ID de objeto do locatário ou "myorganiztion" ou "eu" alias. Para obter mais informações, consulte [endereçamento entidades e operações da API do gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)).
- **Caminho do recurso**: Esta seção de uma URL identifica o recurso para interagir com (usuários, grupos, um determinado usuário, ou um grupo específico, etc.) No exemplo acima, é os primeiro nível "grupos" de endereçamento que do conjunto de recursos. Você também pode abordar uma entidade específica, por exemplo "usuários / {ID do objeto}" ou "usuários/userPrincipalName".
- **Parâmetros de consulta**:? separa a seção de caminho de recurso da seção de parâmetros de consulta. O parâmetro de consulta de "versão da api" é necessário em todas as solicitações da API do gráfico. A API de gráfico também suporta as seguintes opções de consulta de OData: **$filter**, **$orderby**, **$expand**, **$top**e **$format**. As seguintes opções de consulta não são suportadas: **$count**, **$inlinecount**e **$skip**. Para obter mais informações, consulte [suporte para consultas, filtros e opções de paginação na API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>As versões de API do gráfico

Você especifica a versão de uma solicitação de API de gráfico do parâmetro de consulta de "versão da api". Para versão 1.5 e posterior, você use um valor numérico versão; versão da API = 1,6. Para versões anteriores, você usa uma cadeia de caracteres de data que segue o formato AAAA-MM-DD; Por exemplo, versão de api = 2013-11-08. Recursos de visualização, use a cadeia de caracteres "beta;" Por exemplo, versão de api = beta. Para obter mais informações sobre as diferenças entre as versões de API do gráfico, consulte [o controle de versão do Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Metadados Graph API

Para retornar o arquivo de metadados de API do gráfico, adicione o segmento "$metadata" após o identificador de locatários no exemplo de URL, a URL a seguir retorna metadados para a empresa de demonstração usada pelo Gerenciador de gráfico: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Você pode inserir esta URL na barra de endereços de um navegador da web para ver os metadados. O documento de metadados CSDL retornado descreve as entidades e tipos complexos, suas propriedades e as funções e ações expostas pela versão do Graph API solicitada. Omitindo o parâmetro da versão da api retornará metadados para a versão mais recente.

## <a name="common-queries"></a>Consultas comuns

[Consultas comuns do Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) lista consultas comuns que podem ser usadas com o Azure AD Graph, incluindo consultas que podem ser usadas para acessar recursos de nível superior no diretório e consultas para executar operações no diretório.

Por exemplo, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` retorna informações para contoso.com diretório da empresa.

Ou `https://graph.windows.net/contoso.com/users?api-version=1.6` lista todos os objetos de usuário em contoso.com o diretório.

## <a name="using-the-graph-explorer"></a>Usando o Explorador de gráfico

Você pode usar o Explorador de gráfico de API do Azure AD Graph para consultar os dados de diretório, conforme você cria seu aplicativo.

> [AZURE.IMPORTANT] O Explorador de gráfico não suporta escrita ou excluir os dados de um diretório. Você somente pode executar operações de leitura no seu diretório Azure AD com o Explorador de gráfico.

A seguir está a saída você ver se você tivesse que navegue até o Explorador de gráfico, selecione usar demonstração empresa e insira `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` para exibir todos os usuários no diretório de demonstração:

![Explorador de api do Azure AD graph](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Carregar o Explorador de gráfico**: para carregar a ferramenta, navegue até [https://graphexplorer.cloudapp.net/](https://graphexplorer.cloudapp.net/). Clique **Empresa de demonstração de uso** para executar o Explorador de gráfico com dados de um locatário de amostra. Você não precisa de credenciais para usar a empresa de demonstração. Como alternativa, você pode clique em **entrar** e entre com suas credenciais de conta do Azure AD para executar o Explorador de gráfico em seu locatário. Se você executar o Explorador de gráfico em relação a seu próprio locatário, você ou o administrador precisará consentimento durante entrar. Se você tiver uma assinatura do Office 365, você tem automaticamente um locatário do Azure AD. As credenciais que você usa para entrar no Office 365 são, na verdade, contas do Azure AD, e você pode usar essas credenciais com o Explorador de gráfico.

**Executar uma consulta**: para executar uma consulta, digite a sua consulta na caixa de texto da solicitação e clique em **obter** ou clique em **Inserir** chave. Os resultados são exibidos na caixa de resposta. Por exemplo, `https://graph.windows.net/graphdir1.onmicrosoft.com /groups?api-version=1.6` listará todos os objetos de grupo no diretório de demonstração.

Observe os seguintes recursos e limitações do Explorer gráfico:
- Recurso de preenchimento automático em conjuntos de recursos. Para ver isso, clique em **Usar demonstração empresa** e clique na caixa de texto da solicitação (onde a URL da empresa aparece). Você pode selecionar um recurso definido na lista suspensa.

- Suporta o "eu" e "MinhaOrganização" endereçamento aliases. Por exemplo, você pode usar `https://graph.windows.net/me?api-version=1.6` para retornar o objeto de usuário do usuário conectado ou `https://graph.windows.net/myorganization/users?api-version=1.6` para retornar todos os usuários na pasta atual. Observe que usar "eu" alias retornará um erro para a empresa de demonstração porque não há nenhum usuário conectado fazendo a solicitação.

- Uma seção de cabeçalhos de resposta. Isso pode ser usado para ajudar a solucionar problemas que ocorrem durante a execução de consultas.

- Um visualizador JSON para a resposta com recursos de expandir e recolher.

- Não há suporte para exibir uma foto em miniatura.

## <a name="using-fiddler-to-write-to-the-directory"></a>Usando Fiddler para gravar no diretório

Para os fins deste guia de início rápido, você pode usar o depurador de Web Fiddler para praticar a execução de operações 'gravação' contra você directory do Azure AD. Para obter mais informações e instalar o Fiddler, consulte [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler).

No exemplo abaixo, você vai usar Fiddler Web depurador para criar um novo grupo de segurança 'MyTestGroup' no diretório Azure AD.

**Obter um token de acesso**: para acessar o Azure AD Graph, os clientes são necessários para autenticar com êxito ao Azure AD primeiro. Para obter mais informações, consulte [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md).

**Redigir e executar uma consulta**: concluir as etapas a seguir.

1. Abra o Fiddler Web depurador e alternar para a guia **compositor** .
2. Como você deseja criar um novo grupo de segurança, selecione **Post** como o método HTTP no menu suspenso. Para obter mais informações sobre permissões e operações em um objeto de grupo, consulte [grupo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity) dentro a [referência da API REST Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. No campo ao lado da **postagem**, digite o seguinte como a URL de solicitação: `https://graph.windows.net/mytenantdomain/groups?api-version=1.6`.

    > [AZURE.NOTE] Você deve substituir mytenantdomain com o nome de domínio do seu próprio diretório Azure AD.

4. No campo diretamente abaixo suspenso postagem, digite o seguinte:

    ```
Host: graph.windows.net
Authorization: your access token
Content-Type: application/json
```

    > [AZURE.NOTE] Substituir sua &lt;seu token de acesso&gt; com o token de acesso para seu diretório Azure AD.

5. No campo de **corpo de solicitação** , digite o seguinte:

    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
```

    Para obter mais informações sobre como criar grupos, consulte [Criar grupo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Para obter mais informações sobre tipos que estão expostos pela Graph e entidades Azure AD e informações sobre as operações que podem ser executadas neles com o gráfico, consulte [referência da API REST Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
- Saiba mais sobre [permissão de API do Azure AD Graph escopos](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
