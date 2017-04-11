<properties
    pageTitle="Usando o serviço de gerenciamento de API para gerar solicitações de HTTP"
    description="Aprenda a usar políticas de solicitação e resposta no gerenciamento de API para chamar serviços externos de sua API"
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


# <a name="using-external-services-from-the-azure-api-management-service"></a>Usando serviços externos do serviço de gerenciamento de API do Azure

As políticas disponíveis no serviço de gerenciamento de API do Azure podem fazer uma ampla variedade de trabalho útil baseado puramente na solicitação de entrada, a resposta de saída e informações de configuração básica. No entanto, políticas de ser capaz de interagir com serviços externos do gerenciamento de API abre muitos mais oportunidades.

Observamos anteriormente como podemos podem interagir com o [serviço de Hub de evento do Azure para o registro em log, monitoramento e análise](api-management-log-to-eventhub-sample.md). Neste artigo, demonstraremos serviço baseado em políticas que permitem a interação com qualquer HTTP externo. Essas políticas podem ser usadas para acionar eventos remotos ou para recuperar informações que serão usadas para manipular a solicitação e resposta de alguma forma original.

## <a name="send-one-way-request"></a>Enviar uma-maneira-solicitação
Possivelmente a interação externa mais simples é o estilo disparar e esquecer da solicitação que permite que um serviço externo ser notificado sobre algum tipo de evento importante. Podemos usar a política de fluxo de controle `choose` para detectar qualquer tipo de condição que estamos interessados e, em seguida, se a condição for satisfeita, podemos fazer uma solicitação HTTP externa usando a política de [envio-uni-solicitação de maneira](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) . Isso pode ser uma solicitação para um sistema de mensagens como Hipchat ou margem de atraso ou um email API como SendGrid ou MailChimp ou para ocorrências de suporte crítica algo parecido com PagerDuty. Todos esses sistemas de mensagens têm APIs HTTP simples que podemos facilmente pode invocar.

### <a name="alerting-with-slack"></a>Alertas com margem de atraso
O exemplo a seguir demonstra como enviar uma mensagem para uma sala de bate-papo atraso se o código de status de resposta HTTP é maior que ou igual a 500. Um erro no 500 intervalo indica um problema com nosso back-end API que o cliente da nossa API não consegue solucionar sozinhos. Isso geralmente requer algum tipo de intervenção da nossa parte.  

    <choose>
        <when condition="@(context.Response.StatusCode >= 500)">
          <send-one-way-request mode="new">
            <set-url>https://hooks.slack.com/services/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</set-url>
            <set-method>POST</set-method>
            <set-body>@{
                    return new JObject(
                            new JProperty("username","APIM Alert"),
                            new JProperty("icon_emoji", ":ghost:"),
                            new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                    context.Request.Method,
                                                    context.Request.Url.Path + context.Request.Url.QueryString,
                                                    context.Request.Url.Host,
                                                    context.Response.StatusCode,
                                                    context.Response.StatusReason,
                                                    context.User.Email
                                                    ))
                            ).ToString();
                }</set-body>
          </send-one-way-request>
        </when>
    </choose>

Margem de atraso tem a noção de fixação da web de entrada. Ao configurar um gancho da web de entrada, margem de atraso gera uma URL especial que permite a você para fazer uma solicitação de POSTAGEM simples e passar uma mensagem para o canal de margem de atraso. Corpo JSON que criamos baseia-se em um formato definido pelo margem de atraso.

![Margem de atraso Web gancho](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>É fire e esquecer bom o suficiente?
Há determinadas compensações ao usar um estilo disparar e esquecer da solicitação. Se por algum motivo, a solicitação falhará e a falha não será informada. Nessa situação específica, a complexidade de ter uma falha secundária reporting sistema e o custo de desempenho adicional de esperando a resposta não é garantia. Para situações em que é essencial para verificar a resposta, em seguida, a política de [solicitação de envio](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) é a melhor opção.

## <a name="send-request"></a>Solicitação de envio
O `send-request` política permite usando um serviço externo para executar funções complexo processamento e retornar dados para o gerenciamento de API do serviço que pode ser usadas para processamento de política adicional.

### <a name="authorizing-reference-tokens"></a>Autorizar tokens de referência
Uma função importante do gerenciamento de API está protegendo recursos de back-end. Se o servidor de autorização usado pela sua API cria [tokens de JWT](http://jwt.io/) como parte do seu fluxo de OAuth2, como o [Active Directory do Azure](../active-directory/active-directory-aadconnect.md) faz, então você pode usar o `validate-jwt` política para verificar a validade do token. Entretanto, alguns servidores de autorização criam o que é chamado [tokens de referência](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) que não pode ser verificada sem fazer uma chamada de volta para o servidor de autorização.

### <a name="standardized-introspection"></a>Introspecção padronizada
No passado, houve nenhuma maneira padronizada de verificar um token de referência com um servidor de autorização. No entanto um padrão proposto recentemente [RFC 7662](https://tools.ietf.org/html/rfc7662) foi publicado pela IETF que define como um servidor de recurso pode verificar a validade de um token.

### <a name="extracting-the-token"></a>Extrair o token
A primeira etapa é extrair o token no cabeçalho de autorização. O valor do cabeçalho deve ser formatado com a `Bearer` esquema de autorização, um único espaço e, em seguida, o token de autorização de acordo com a [RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1). Infelizmente, há casos em que o esquema de autorização é omitido. Para justificar isso durante a análise, podemos dividir o valor do cabeçalho em um espaço e selecione a última cadeia de caracteres da matriz retornada de cadeias de caracteres. Isso fornece uma solução alternativa para cabeçalhos de autorização formatada incorretamente.

    <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

### <a name="making-the-validation-request"></a>Fazendo a solicitação de validação
Depois que temos o token de autorização, podemos tornar a solicitação para validar o token. RFC 7662 chama introspecção esse processo e requer que você `POST` um formulário HTML para o recurso de introspecção. O formulário HTML deve conter pelo menos um par chave/valor com a chave `token`. Essa solicitação para o servidor de autorização também deve ser autenticada para garantir que clientes mal-intencionado não podem ir trawling para tokens válidos.

    <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
      <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
      <set-method>POST</set-method>
      <set-header name="Authorization" exists-action="override">
        <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
      </set-header>
      <set-header name="Content-Type" exists-action="override">
        <value>application/x-www-form-urlencoded</value>
      </set-header>
      <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
    </send-request>

### <a name="checking-the-response"></a>A resposta de verificação
O `response-variable-name` atributo é usado para conceder acesso a resposta retornada. O nome definido nesta propriedade pode ser usado como uma chave para o `context.Variables` dicionário para acessar o `IResponse` objeto.

Do objeto resposta podemos recuperar o corpo e RFC 7622 informa que a resposta deve ser um objeto JSON e deve conter pelo menos uma propriedade chamada `active` isto é um valor booliano. Quando `active` for verdadeira, então o token é considerado válido.

### <a name="reporting-failure"></a>Falha de relatório
Usamos uma `<choose>` política para detectar se o token é inválido e em caso afirmativo, retornar uma resposta 401.

    <choose>
      <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
        <return-response response-variable-name="existing response variable">
          <set-status code="401" reason="Unauthorized" />
          <set-header name="WWW-Authenticate" exists-action="override">
            <value>Bearer error="invalid_token"</value>
          </set-header>
        </return-response>
      </when>
    </choose>

Acordo com as [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) que descreve como `bearer` tokens devem ser usados, podemos também retornar um `WWW-Authenticate` cabeçalho com a resposta 401. Autenticar o WWW destina-se para instruir um cliente sobre como construir uma solicitação de devidamente autorizada. Devido à variedade de abordagens possíveis com a estrutura de OAuth2, é difícil para se comunicar todas as informações necessárias. Felizmente existem esforços em andamento para ajudar [clientes descobrir como corretamente autorizar solicitações para um servidor de recursos](http://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Solução final
Juntando todas as partes, temos a diretiva a seguir:

    <inbound>
      <!-- Extract Token from Authorization header parameter -->
      <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

      <!-- Send request to Token Server to validate token (see RFC 7662) -->
      <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
        <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
        <set-method>POST</set-method>
        <set-header name="Authorization" exists-action="override">
          <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
        </set-header>
        <set-header name="Content-Type" exists-action="override">
          <value>application/x-www-form-urlencoded</value>
        </set-header>
        <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
      </send-request>

      <choose>
            <!-- Check active property in response -->
            <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
                <!-- Return 401 Unauthorized with http-problem payload -->
                <return-response response-variable-name="existing response variable">
                    <set-status code="401" reason="Unauthorized" />
                    <set-header name="WWW-Authenticate" exists-action="override">
                        <value>Bearer error="invalid_token"</value>
                    </set-header>
                </return-response>
            </when>
        </choose>
      <base />
    </inbound>

Isso é apenas um dos muitos exemplos de como o `send-request` diretiva pode ser usada para integrar serviços externos útil o processo de solicitações e respostas fluindo através do serviço de gerenciamento de API.

## <a name="response-composition"></a>Composição de resposta
O `send-request` diretiva pode ser usada para melhorar a uma solicitação primária para um sistema de back-end, conforme visto no exemplo anterior, ou pode ser usado como uma substituição completa da chamada back-end. Usando essa técnica, podemos facilmente criar compostos recursos que são agregados de vários sistemas diferentes.

### <a name="building-a-dashboard"></a>Criar um painel   
Às vezes, você deseja ser capaz de expor informações que existe em vários sistemas de back-end, por exemplo, para direcionar um painel. Os KPIs provenientes de todos os diferentes back-ends, mas você preferir não fornecem acesso direto a eles e seria bom se todas as informações puderam ser recuperadas em uma única solicitação. Talvez algumas das informações back-end precisa alguns divisão e repartir e corrigindo um pouco primeiro! Ser capaz de cache esse recurso composto seria útil reduzir a carga de back-end, como você sabe que os usuários têm o hábito de hammering a tecla F5 para ver se suas medidas com baixo desempenho pode ser alterada.    

### <a name="faking-the-resource"></a>Simular o recurso
A primeira etapa para a criação de nosso recurso dashboard é configurar uma nova operação no portal de fornecedor de gerenciamento de API. Este será uma operação de espaço reservado usada para configurar a nossa política de composição para criar nosso recurso dinâmico.

![Operação de painel](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Como fazer as solicitações
Uma vez o `dashboard` operação tiver sido criada podemos configurar uma política especificamente para a operação. 

![Operação de painel](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

A primeira etapa é extrair os parâmetros de consulta de solicitação de entrada, para que nós pode encaminhá-las para nosso back-end. Neste exemplo nosso painel está mostrando informações com base em um período de tempo uma, portanto, tem um `fromDate` e `toDate` parâmetro. Podemos usar o `set-variable` política extrair as informações de URL da solicitação.

    <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
    <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

Depois que temos essas informações poderemos solicitações de todos os sistemas de back-end. Cada solicitação constrói um novo URL com as informações de parâmetro e chama seu respectivo servidor e armazena a resposta em uma variável de contexto.

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

Essas solicitações executará em sequência, que não é ideal. Em uma versão futura apresentaremos uma nova política chamada `wait` que permitirá todas essas solicitações de executar em paralelo.

### <a name="responding"></a>Responder

Para construir a resposta composta podemos usar a política de [resposta de retorno](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) . O `set-body` elemento pode usar uma expressão para construir um novo `JObject` com todas as representações de componente inseridas como propriedades.

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>

A política completa fica assim:

    <policies>
        <inbound>

      <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
      <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

        <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
          <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
          <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <return-response response-variable-name="existing response variable">
          <set-status code="200" reason="OK" />
          <set-header name="Content-Type" exists-action="override">
            <value>application/json</value>
          </set-header>
          <set-body>
            @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                          new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                          new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                          new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                          ).ToString())
          </set-body>
        </return-response>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
    </policies>

Na configuração do espaço reservado operação que podemos configurar o recurso de dashboard a ser armazenados em cache para pelo menos uma hora porque entendemos a natureza dos dados significa que mesmo se estiver em uma hora desatualizada, que ele ainda estará suficientemente eficaz transmitir informações importantes para os usuários.

## <a name="summary"></a>Resumo
Serviço de gerenciamento de API Azure fornece políticas flexíveis que podem ser aplicadas seletivamente para tráfego HTTP e permite composição dos serviços de back-end. Se você deseja aprimorar seu gateway API com alertas funções, verificação, recursos de validação ou criar novos recursos compostos com base em vários serviços de back-end, o `send-request` e políticas relacionadas abrem um mundo de possibilidades.

## <a name="watch-a-video-overview-of-these-policies"></a>Assista a uma visão geral em vídeo dessas diretivas
Para obter mais informações sobre o [envio-uni-solicitação de maneira](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest), [solicitação de envio](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest)e políticas de [retorno-resposta](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) descritas neste artigo, assista a este vídeo.

> [AZURE.VIDEO send-request-and-return-response-policies]
