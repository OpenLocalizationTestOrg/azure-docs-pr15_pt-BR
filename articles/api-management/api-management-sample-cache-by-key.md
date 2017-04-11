<properties
    pageTitle="Cache personalizado no gerenciamento de API do Azure"
    description="Saiba como armazenar em cache itens pela chave Gerenciamento de API do Azure"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>

# <a name="custom-caching-in-azure-api-management"></a>Cache personalizado no gerenciamento de API do Azure
Serviço de gerenciamento de API Azure possui suporte interno para o [cache de resposta HTTP](api-management-howto-cache.md) usando a URL do recurso como a chave. A chave pode ser modificada por cabeçalhos de solicitação usando o `vary-by` propriedades. Isso é útil para armazenamento em cache respostas HTTP inteiras (também conhecidos como representações), mas, às vezes, é útil para cache apenas uma parte de uma representação. As novas diretivas de [valor de pesquisa de cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) e o [valor de armazenamento de cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) fornecem a capacidade para armazenar e recuperar aleatório partes de dados de dentro das definições de política. Essa capacidade também adiciona o valor à política anteriormente introduziu [a solicitação de envio](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) porque você agora pode armazenar em cache respostas de serviços externos.

## <a name="architecture"></a>Arquitetura  
Serviço de gerenciamento de API usa um cache de dados compartilhado por locatário para que, ao dimensionar até várias unidades, você ainda terá acesso à mesma dados armazenados em cache. No entanto, ao trabalhar com uma implantação de várias regiões há caches independentes dentro de cada uma das regiões. Devido a isso, é importante não trate o cache como um armazenamento de dados, onde ele é a única fonte de alguns informação. Se você fez e mais tarde decidiu aproveitar a implantação de várias regiões, clientes com usuários que viajam podem perder o acesso aos dados armazenados em cache.

## <a name="fragment-caching"></a>Cache de fragmento
Há alguns casos onde respostas sendo retornadas contêm parte dos dados que é caro para determinar e ainda permanecem clara por um período de tempo razoável. Como exemplo, considere a possibilidade de um serviço criado por uma companhia aérea que fornece informações relacionadas reservas de voo, status de voos, etc. Se o usuário for um membro do programa de pontos de companhias aéreas, eles também teriam informações relacionadas a seus status atual e quilometragem acumulados. Essas informações relacionadas ao usuário podem estar armazenadas em um sistema diferente, mas pode ser desejável para incluí-lo em respostas sobre reservas e status de voos retornadas. Isso pode ser feito usando um processo chamado cache de fragmento. A representação principal pode ser retornada do servidor de origem usando algum tipo de token para indicar onde as informações relacionadas ao usuário são a ser inserido. 

Considere a seguinte resposta JSON de um API de back-end.


    {
      "airline" : "Air Canada",
      "flightno" : "871",
      "status" : "ontime",
      "gate" : "B40",
      "terminal" : "2A",
      "userprofile" : "$userprofile$"
    }  

E recursos secundários em `/userprofile/{userid}` semelhante,

     { "username" : "Bob Smith", "Status" : "Gold" }

Para determinar as informações de usuário apropriadas para incluir, precisamos identificar quem é o usuário final. Esse mecanismo é implementação dependente. Como exemplo, estou usando o `Subject` reivindicar de um `JWT` token. 

    <set-variable
      name="enduserid"
      value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

Podemos armazená-la `enduserid` valor em uma variável de contexto para uso posterior. A próxima etapa é determinar se uma solicitação anterior já recuperou as informações do usuário e armazenou no cache. Para isso, podemos usar a `cache-lookup-value` política.

      <cache-lookup-value
      key="@("userprofile-" + context.Variables["enduserid"])"
      variable-name="userprofile" />

Se não houver nenhuma entrada no cache que corresponde ao valor da chave, então, não `userprofile` variável de contexto será criado. Vamos verificar o sucesso da pesquisa usando o `choose` política de fluxo de controle.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("userprofile"))">
            <!— If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
        </when>
    </choose>


Se o `userprofile` variável de contexto não existir, em seguida, vamos terá de tornar uma solicitação HTTP para recuperá-lo.

    <send-request
      mode="new"
      response-variable-name="userprofileresponse"
      timeout="10"
      ignore-error="true">

      <!-- Build a URL that points to the profile for the current end-user -->
      <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
          (string)context.Variables["enduserid"]).AbsoluteUri)
      </set-url>
      <set-method>GET</set-method>
    </send-request>

Usamos o `enduserid` para construir a URL para o recurso de perfil de usuário. Depois que temos a resposta, podemos puxe o corpo de texto fora da resposta e armazená-la novamente em uma variável de contexto.

    <set-variable
        name="userprofile"
        value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

Para evitar que tenhamos faça essa solicitação HTTP novamente, quando o mesmo usuário fizer outra solicitação, podemos armazenar o perfil de usuário no cache.

    <cache-store-value
        key="@("userprofile-" + context.Variables["enduserid"])"
        value="@((string)context.Variables["userprofile"])" duration="100000" />

Podemos armazenar o valor em cache usando a mesma chave exata que estamos originalmente tentou recuperá-la com. A duração que podemos optar por armazenar o valor deve ser baseada em como muitas vezes as alterações de informações e como tolerância a usuários são informações para desatualizada. 

É importante perceber que recuperar do cache ainda é um fora de processo, a solicitação de rede e potencialmente ainda poderá adicionar dezenas de milissegundos à solicitação. Os benefícios vêm ao determinar que as informações de perfil de usuário leva significativamente maiores que devido a necessidade de banco de dados consultas ou agregar informações de vários back-ends.

A etapa final no processo é atualizar a resposta retornada com nossas informações de perfil do usuário.

    <!—Update response body with user profile-->
    <find-and-replace
        from='"$userprofile$"'
        to="@((string)context.Variables["userprofile"])" />

Eu optar por incluir as aspas como parte do token para que mesmo quando substituir não ocorre, a resposta foi JSON ainda válida. Isso era principalmente facilitar a depuração.

Depois que você combina todas essas etapas juntas, o resultado final é uma política que semelhante ao seguinte.

     <policies>
        <inbound>
            <!-- How you determine user identity is application dependent -->
            <set-variable
              name="enduserid"
              value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

            <!--Look for userprofile for this user in the cache -->
            <cache-lookup-value
              key="@("userprofile-" + context.Variables["enduserid"])"
              variable-name="userprofile" />

            <!-- If we don’t find it in the cache, make a request for it and store it -->
            <choose>
                <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                    <!—Make HTTP request to get user profile -->
                    <send-request
                      mode="new"
                      response-variable-name="userprofileresponse"
                      timeout="10"
                      ignore-error="true">

                       <!-- Build a URL that points to the profile for the current end-user -->
                        <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                        <set-method>GET</set-method>
                    </send-request>

                    <!—Store response body in context variable -->
                    <set-variable
                      name="userprofile"
                      value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                    <!—Store result in cache -->
                    <cache-store-value
                      key="@("userprofile-" + context.Variables["enduserid"])"
                      value="@((string)context.Variables["userprofile"])"
                      duration="100000" />
                </when>
            </choose>
            <base />
        </inbound>
        <outbound>
            <!—Update response body with user profile-->
            <find-and-replace
                  from='"$userprofile$"'
                  to="@((string)context.Variables["userprofile"])" />
            <base />
        </outbound>
     </policies>

Essa abordagem cache é usada principalmente em sites da web onde HTML é composta no lado do servidor para que ela pode ser processada como uma única página. No entanto, também pode ser útil em APIs onde os clientes não podem fazer cliente lado HTTP cache ou é desejável não colocar essa responsabilidade no cliente.

Esse tipo de cache de fragmento também pode ser feito nos servidores web back-end usando um servidor de cache relacionada, no entanto, usando o serviço de gerenciamento de API para realizar esse trabalho é útil quando os fragmentos em cache são provenientes de back-ends diferentes que as respostas principais.

## <a name="transparent-versioning"></a>Controle de versão transparente
É prática comum para várias versões de implementação diferente de uma API suporte em qualquer momento. Essa é talvez para dar suporte a ambientes diferentes, como o desenvolvimento, teste, produção, etc., ou pode ser dar suporte a versões mais antigas da API para dar tempo para consumidores de API migrar para versões mais recentes. 

Uma abordagem para lidar com isso em vez de exigir que os desenvolvedores de cliente alterar as URLs de `/v1/customers` para `/v2/customers` é armazenar dados de perfil do consumidor qual versão da API atualmente desejam usar e chamar a URL de back-end apropriado. Para determinar a URL de back-end correto para ligar para um determinado cliente, é necessário para alguns dados de configuração da consulta. Armazenando esses dados de configuração, podemos pode minimizar a perda de desempenho de fazer esta pesquisa.

A primeira etapa é determinar o identificador usado para configurar a versão desejada. Neste exemplo, eu escolhi associar a versão para a chave de assinatura do produto. 

        <set-variable name="clientid" value="@(context.Subscription.Key)" />

Em seguida, fazemos uma pesquisa de cache para ver se podemos já tiver recuperado a versão de cliente desejado.

        <cache-lookup-value
        key="@("clientversion-" + context.Variables["clientid"])"
        variable-name="clientversion" />

Em seguida, vamos verificar se não encontramos-lo no cache.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">

Se nós não podemos ir e recuperá-la.

    <send-request
        mode="new"
        response-variable-name="clientconfiguresponse"
        timeout="10"
        ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
    </send-request>

Extrai o texto de corpo de resposta da resposta.

    <set-variable
          name="clientversion"
          value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />

Armazená-lo novamente em cache para uso futuro.

    <cache-store-value
          key="@("clientversion-" + context.Variables["clientid"])"
          value="@((string)context.Variables["clientversion"])"
          duration="100000" />

E finalmente atualizar a URL de back-end para selecionar a versão do serviço desejado pelo cliente.

    <set-backend-service
          base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />

A política completamente é da seguinte maneira.

     <inbound>
        <base />
        <set-variable name="clientid" value="@(context.Subscription.Key)" />
        <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

        <!-- If we don’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("clientversion"))">
                <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                    <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>
                <!-- Store response body in context variable -->
                <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
                <!-- Store result in cache -->
                <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
            </when>
        </choose>
        <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
    </inbound>


Permitindo que os consumidores de API transparente controlar qual versão de back-end está sendo acessado por clientes sem precisar atualizar e reimplantar clientes é uma solução elegante que atende muitos preocupações de controle de versão de API.

## <a name="tenant-isolation"></a>Isolamento de locatário

Em implantações maiores, vários locatários algumas empresas criar grupos separados de locatários em implantações distintas de hardware de back-end. Isso minimiza o número de clientes que são afetados por um problema de hardware no back-end. Ele também permite novas versões de software ser implementadas em estágios. Ideal essa arquitetura de back-end deve ser transparente para consumidores de API. Isso pode ser feito de maneira semelhante ao controle de versão transparente porque ele é baseado na mesma técnica de manipular a URL de back-end usando o estado de configuração por chave API.  

Em vez de retornar uma versão preferencial da API para cada chave de assinatura, você poderia retornar um identificador que se relacionam um locatário para o grupo de hardware atribuído. Identificador pode ser usado para construir a URL de back-end apropriado.

## <a name="summary"></a>Resumo
A liberdade de usar o cache de gerenciamento de API do Azure para armazenar qualquer tipo de dados permite acesso eficiente aos dados de configuração que podem afetar a maneira como uma solicitação de entrada é processada. Ele também pode ser usado para armazenar fragmentos de dados que podem aumentar respostas, retornadas de um API de back-end.

## <a name="next-steps"></a>Próximas etapas
Envie-nos seus comentários no thread Disqus para este tópico se houver outros cenários que essas políticas tem habilitado para você, ou se houver cenários que você gostaria de obter mas considere não são possíveis no momento.
