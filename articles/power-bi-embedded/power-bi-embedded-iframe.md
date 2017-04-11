<properties
   pageTitle="Como usar o Power BI incorporado com restante | Microsoft Azure"
   description="Saiba como usar o Power BI incorporado com restante "
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="how-to-use-power-bi-embedded-with-rest"></a>Como usar o Power BI incorporado com restante


## <a name="power-bi-embedded-what-it-is-and-what-its-for"></a>Power BI inseridos: O que é e para que serve
Uma visão geral do Power BI inserida é descrita no [site Power BI incorporado](https://azure.microsoft.com/services/power-bi-embedded/)oficial, mas vamos dar uma olhada rápida antes de entrar nos detalhes sobre como usá-lo com restante.

Ele é bem simple, realmente. Um ISV geralmente quer usar as visualizações de dados dinâmicos do [Power BI](https://powerbi.microsoft.com) em seu próprio aplicativo como blocos de construção de interface do usuário.

Mas, você sabe, incorporando relatórios do Power BI ou ladrilhos em sua página da web já está possível sem o serviço do Azure inserida do Power BI, usando a **API do Power BI**. Quando você quiser compartilhar seus relatórios na mesma organização, você pode inserir os relatórios com a autenticação do Azure AD. O usuário que exibe os relatórios deve faça logon usando suas próprias contas do Azure AD. Quando você quiser compartilhar seus relatórios para todos os usuários (incluindo os usuários externos), você pode simplesmente incorporar com acesso anônimo.

Mas vir, este simples incorporar solução bastante não atender às necessidades de um aplicativo de ISV.
A maioria dos aplicativos de ISV precisa entregar os dados para seus próprios clientes, não necessariamente usuários em sua própria organização. Por exemplo, se você estiver entregando algum serviço para empresa A e empresa B, os usuários na empresa A só verá dados para sua empresa A. Isto é, a multilocação é necessária para a entrega.

O aplicativo ISV também pode ser oferecendo seus próprios métodos de autenticação como autenticação de formulários, autenticação básica, etc... Em seguida, a solução incorporação deve colaborar com segurança com este método de autenticação existente. Também é necessário para os usuários a ser capaz de usar esses aplicativos ISV sem compra extra ou licenciamento de uma assinatura do Power BI.

 **Power BI incorporado** destina precisamente esses tipos de cenários de ISV. Portanto agora que temos que rápida introdução fora do caminho, vamos alguns detalhes

Você pode usar o .NET \(c#) ou SDK Node, para criar facilmente seu aplicativo com o Power BI inserida. Mas, neste artigo, explicaremos sobre fluxo HTTP \(incluindo AuthN) do Power BI sem SDKs. Noções básicas sobre esse fluxo, você pode criar seu aplicativo **com qualquer linguagem de programação**, e você pode compreender profundamente a essência do Power BI inserida.

## <a name="create-power-bi-workspace-collection-and-get-access-key-provisioning"></a>Conjunto de espaço de trabalho de criar Power BI e obter acesso chave \(provisionamento)
Power BI incorporado é um dos serviços do Azure. Somente o ISV que usa Portal Azure é cobrado taxas de uso \(por sessão de usuário por hora), e o usuário que o relatório de modos de exibição não é cobrado ou até mesmo exigir uma assinatura do Azure.
Antes de iniciar o desenvolvimento de aplicativos, devemos criar a **coleção de espaço de trabalho do Power BI** usando o Portal do Azure.

Cada espaço de trabalho do Power BI inserida é o espaço de trabalho para cada cliente (Locatário) e podemos adicionar vários espaços de trabalho em cada coleção de espaço de trabalho. A mesma chave de acesso é usada em cada conjunto de espaço de trabalho. No efeito, a coleção de espaço de trabalho é o limite de segurança para o Power BI inserida.

![](media\power-bi-embedded-iframe\create-workspace.png)

Quando concluir a criação do conjunto de espaço de trabalho, copie a tecla de acesso do Portal do Azure.

![](media\power-bi-embedded-iframe\copy-access-key.png)

> [AZURE.NOTE] Podemos também provisionar a coleção de espaço de trabalho e obter a chave de acesso via API REST. Para saber mais, consulte [APIs de provedor de recursos do Power BI](https://msdn.microsoft.com/library/azure/mt712306.aspx).

## <a name="create-pbix-file-with-power-bi-desktop"></a>Criar arquivo de .pbix com Power BI Desktop
Em seguida, devemos criar a conexão de dados e relatórios sejam inseridos.
Para essa tarefa, não há nenhuma programação ou código. Podemos simplesmente usar a área de trabalho do Power BI.
Neste artigo, podemos não dar uma olhada os detalhes sobre como usar o Power BI Desktop. Se você precisar de ajuda aqui, consulte [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/). Para nosso exemplo, usaremos a [Amostra de análise de varejo](https://powerbi.microsoft.com/documentation/powerbi-sample-datasets/).

![](media\power-bi-embedded-iframe\power-bi-desktop-1.png)

## <a name="create-a-power-bi-workspace"></a>Criar um espaço de trabalho do Power BI

Agora que o provisionamento é feito, então vamos começar criando o espaço de trabalho de um cliente na coleção de espaço de trabalho por meio de APIs REST. As seguintes HTTP POST solicitar (REST) é criar novo espaço de trabalho da nossa coleção de espaço de trabalho existente. Em nosso exemplo, o nome de conjunto de espaço de trabalho é **mypbiapp**.
Podemos basta definir a chave de acesso, que podemos copiou anteriormente, como **AppKey**. É muito simple autenticação!

**Solicitação HTTP**

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces
Authorization: AppKey MpaUgrTv5e...
```

**Resposta HTTP**

```
HTTP/1.1 201 Created
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces
RequestId: 4220d385-2fb3-406b-8901-4ebe11a5f6da

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/$metadata#workspaces/$entity",
  "workspaceId": "32960a09-6366-4208-a8bb-9e0678cdbb9d",
  "workspaceCollectionName": "mypbiapp"
}
```

O retornado **workspaceId** é usado para as seguintes chamadas API subsequentes. Nosso aplicativo deve manter esse valor.

## <a name="import-pbix-file-into-the-workspace"></a>Importar arquivo de .pbix para o espaço de trabalho
Cada espaço de trabalho pode hospedar um único arquivo de área de trabalho do Power BI com um conjunto de dados \(incluindo as configurações de fonte de dados) e relatórios. Podemos importar nosso arquivo .pbix no espaço de trabalho, conforme mostrado no código a seguir. Como você pode ver, nós pode carregar o binário do arquivo de .pbix usando com diversas partes MIME em http.

O fragmento de uri **32960a09-6366-4208-a8bb-9e0678cdbb9d** é o workspaceId e de parâmetro de consulta **datasetDisplayName** é o nome do conjunto de dados para criar. O conjunto de dados criado armazena todos os dados relacionam artefatos no .pbix arquivo como como dados importados, o ponteiro para a fonte de dados, etc...

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports?datasetDisplayName=mydataset01
Authorization: AppKey MpaUgrTv5e...
Content-Type: multipart/form-data; boundary="A300testx"

--A300testx
Content-Disposition: form-data

{the content (binary) of .pbix file}
--A300testx--
```

Esta tarefa de importação pode ficar por um tempo. Ao concluir, o nosso aplicativo pode solicitar o status da tarefa usando o id de importação. Em nosso exemplo, a id de importação é **4eec64dd-533b-47c3-a72c-6508ad854659**.

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659?tenantId=myorg
RequestId: 658bd6b4-b68d-4ec3-8818-2a94266dc220

{"id":"4eec64dd-533b-47c3-a72c-6508ad854659"}
```

A seguir está solicitando status usando essa id de importação:

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659
Authorization: AppKey MpaUgrTv5e...
```

Se a tarefa não estiver concluída, a resposta HTTP seria assim:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: 614a13a5-4de7-43e8-83c9-9cd225535136

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Publishing",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "name": "mydataset01"
}
```

Se a tarefa estiver concluída, a resposta HTTP pode ser mais assim:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: eb2c5a85-4d7d-4cc2-b0aa-0bafee4b1606

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Succeeded",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "reports": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://app.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c"
    }
  ],
  "datasets": [
    {
      "id": "458e0451-7215-4029-80b3-9627bf3417b0",
      "name": "mydataset01",
      "tables": [
      ],
      "webUrl": "https://app.powerbi.com/datasets/458e0451-7215-4029-80b3-9627bf3417b0"
    }
  ],
  "name": "mydataset01"
}
```

## <a name="data-source-connectivity-and-multi-tenancy-of-data"></a>Conectividade de fonte de dados \(e multilocação dos dados)
Enquanto quase todos os artefatos no arquivo de .pbix são importados para nosso espaço de trabalho, as credenciais para fontes de dados não são. Como resultado, ao usar o **modo DirectQuery**, o relatório inserido não pode ser exibido corretamente. Mas, ao usar o **modo de importação**, podemos pode exibir o relatório usando os dados importados existentes. Nesse caso, estamos deverá definir a credencial usando as seguintes etapas via chamadas REST.

Primeiro, vamos Obtenha a fonte de dados do gateway. Nós sabemos que a dataset **id** é a id retornada anteriormente.

**Solicitação HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.GetBoundGatewayDatasources
Authorization: AppKey MpaUgrTv5e...
```

**Resposta HTTP**

```
GET HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: 574b0b18-a6fa-46a6-826c-e65840cf6e15

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#gatewayDatasources",
  "value": [
    {
      "id": "5f7ee2e7-4851-44a1-8b75-3eb01309d0ea",
      "gatewayId": "ca17e77f-1b51-429b-b059-6b3e3e9685d1",
      "datasourceType": "Sql",
      "connectionDetails": "{\"server\":\"testserver.database.windows.net\",\"database\":\"testdb01\"}"
    }
  ]
}
```

Usando a identificação de gateway retornado e a id de datasource \(ver as anterior **gatewayId** e **id** no resultado retornado), podemos alterar a credencial dessa fonte de dados da seguinte maneira:

**Solicitação HTTP**

```
PATCH https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/gateways/ca17e77f-1b51-429b-b059-6b3e3e9685d1/datasources/5f7ee2e7-4851-44a1-8b75-3eb01309d0ea
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "credentialType": "Basic",
  "basicCredentials": {
    "username": "demouser",
    "password": "P@ssw0rd"
  }
}
```

**Resposta HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
RequestId: 0e533c13-266a-4a9d-8718-fdad90391099
```

Em produção, nós também pode definir a cadeia de conexão diferentes para cada espaço de trabalho usando a API REST. \(ou seja, podemos pode separar o banco de dados para cada clientes.)

A seguir está mudando a cadeia de conexão da fonte de dados via restante.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.SetAllConnections
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "connectionString": "data source=testserver02.database.windows.net;initial catalog=testdb02;persist security info=True;encrypt=True;trustservercertificate=False"
}
```

Ou, podemos usar segurança em nível de linha no Power BI incorporado e podemos pode separar os dados para cada usuários em um relatório. As a Result, podemos podem provisionar cada relatório de cliente com o mesmo .pbix \(UI, etc.) e diferentes fontes de dados.

> [AZURE.NOTE] Se você estiver usando o **modo de importação** em vez de **modo DirectQuery**, não há nenhuma maneira de atualizar modelos por meio da API. E fontes de dados locais por meio do gateway de Power BI ainda não tem suporte no Power BI inserida. No entanto, você realmente deseja fique atento o [blog do Power BI](https://powerbi.microsoft.com/blog/) para o que há de novo e lançamentos de quais são as novidades no futuro.

## <a name="authentication-and-hosting-embedding-reports-in-our-web-page"></a>Autenticação e hospeda relatórios (incorporação) em nossa página da web

Na API REST anterior, podemos usar a tecla de acesso **AppKey** próprio como o cabeçalho de autorização. Porque essas chamadas podem ser tratadas no lado do servidor back-end, é seguro.

Mas, quando podemos incorporar o relatório em nossa página da web, esse tipo de informação de segurança poderia ser tratado usando JavaScript \(frontend). Em seguida, o valor de cabeçalho de autorização deve ser protegido. Se nossa tecla de acesso é descoberta por um usuário mal-intencionado ou código mal-intencionado, eles podem chamar quaisquer operações usando essa chave.

Quando podemos incorporar o relatório em nossa página da web, deve usamos o token computado em vez de tecla de acesso **AppKey**. Nosso aplicativo deve criar o Token OAuth Json Web \(JWT) que consiste as declarações e a assinatura digital computada. Conforme ilustrado abaixo, este JWT OAuth é tokens de cadeia de caracteres codificada delimitado por ponto.

![](media\power-bi-embedded-iframe\oauth-jwt.png)

Primeiro, vamos deve preparar o valor de entrada, que é assinado posteriormente. Esse valor é a cadeia de caracteres na Base 64 codificada de url (rfc4648) do json a seguir e essas são delimitadas por ponto de \(.) caractere. Posteriormente, explicaremos como obter a id de relatório.

> [AZURE.NOTE] Se quisermos usar linha nível RLS (segurança) com o Power BI inserida, podemos também deve especificar o **nome de usuário** e **funções** nas declarações.

```
{
  "typ":"JWT",
  "alg":"HS256"
}
```

```
{
  "wid":"{workspace id}",
  "rid":"{report id}",
  "wcn":"{workspace collection name}",
  "iss":"PowerBISDK",
  "ver":"0.2.0",
  "aud":"https://analysis.windows.net/powerbi/api",
  "nbf":{start time of token expiration},
  "exp":{end time of token expiration}
}
```

Em seguida, devemos criar a cadeia de caracteres de codificação base64 de HMAC \(a assinatura) com o algoritmo de SHA256. Este valor de entrada assinado é a cadeia de caracteres anterior.

Por último, podemos deve combinar a cadeia de valor e assinatura entrada usando período \(.) caractere. A cadeia de caracteres concluída é o token de aplicativo para a inserção de relatório. Mesmo se o token de aplicativo é descoberto por um usuário mal-intencionado, não conseguem chegar a tecla de acesso original. Esse token de aplicativo expirará rapidamente.

Aqui está um exemplo PHP para estas etapas:

```
<?php
// 1. power bi access key
$accesskey = "MpaUgrTv5e...";

// 2. construct input value
$token1 = "{" .
  "\"typ\":\"JWT\"," .
  "\"alg\":\"HS256\"" .
  "}";
$token2 = "{" .
  "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
  "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
  "\"wcn\":\"mypbiapp\"," . // workspace collection name
  "\"iss\":\"PowerBISDK\"," .
  "\"ver\":\"0.2.0\"," .
  "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
  "\"nbf\":" . date("U") . "," .
  "\"exp\":" . date("U" , strtotime("+1 hour")) .
  "}";
$inputval = rfc4648_base64_encode($token1) .
  "." .
  rfc4648_base64_encode($token2);

// 3. get encoded signature
$hash = hash_hmac("sha256",
    $inputval,
    $accesskey,
    true);
$sig = rfc4648_base64_encode($hash);

// 4. show result (which is the apptoken)
$apptoken = $inputval . "." . $sig;
echo($apptoken);

// helper functions
function rfc4648_base64_encode($arg) {
  $res = $arg;
  $res = base64_encode($res);
  $res = str_replace("/", "_", $res);
  $res = str_replace("+", "-", $res);
  $res = rtrim($res, "=");
  return $res;
}
?>
```

## <a name="finally-embed-the-report-into-the-web-page"></a>Por fim, incorporar o relatório de página da web

Para inserção nosso relatório, estamos deve obter a url de inserção e **id** usando a seguinte API REST de relatório.

**Solicitação HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/reports
Authorization: AppKey MpaUgrTv5e...
```

**Resposta HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: d4099022-405b-49d3-b3b7-3c60cf675958

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#reports",
  "value": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c",
      "isFromPbix": false
    }
  ]
}
```

Nós pode inserir relatório do nosso aplicativo web usando o token de aplicativo anterior.
Se olharmos para o próximo código de amostra, a primeira parte é a mesma que o exemplo anterior. Na segunda parte, este exemplo mostra o **embedUrl** \(ver o resultado anterior) no iframe e está postando o token de aplicativo para o iframe.

> [AZURE.NOTE] Você precisará alterar o valor de id de relatório a um de seus próprios. Além disso, devido a um erro no nosso sistema de gerenciamento de conteúdo, a marca de iframe no código de exemplo é lido literalmente. Remova o texto limitado da marca se você copia e cola este código de exemplo.

```
    <?php
    // 1. power bi access key
    $accesskey = "MpaUgrTv5e...";

    // 2. construct input value
    $token1 = "{" .
      "\"typ\":\"JWT\"," .
      "\"alg\":\"HS256\"" .
      "}";
    $token2 = "{" .
      "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
      "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
      "\"wcn\":\"mypbiapp\"," . // workspace collection name
      "\"iss\":\"PowerBISDK\"," .
      "\"ver\":\"0.2.0\"," .
      "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
      "\"nbf\":" . date("U") . "," .
      "\"exp\":" . date("U" , strtotime("+1 hour")) .
      "}";
    $inputval = rfc4648_base64_encode($token1) .
      "." .
      rfc4648_base64_encode($token2);

    // 3. get encoded signature value
    $hash = hash_hmac("sha256",
        $inputval,
        $accesskey,
        true);
    $sig = rfc4648_base64_encode($hash);

    // 4. get apptoken
    $apptoken = $inputval . "." . $sig;

    // helper functions
    function rfc4648_base64_encode($arg) {
      $res = $arg;
      $res = base64_encode($res);
      $res = str_replace("/", "_", $res);
      $res = str_replace("+", "-", $res);
      $res = rtrim($res, "=");
      return $res;
    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Test page</title>
      <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
      <button id="btnView">View Report !</button>
      <div id="divView">
        <**REMOVE THIS CAPPED TEXT IF COPIED** iframe id="ifrTile" width="100%" height="400"></iframe>
      </div>
      <script>
        (function () {
          document.getElementById('btnView').onclick = function() {
            var iframe = document.getElementById('ifrTile');
            iframe.src = 'https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c';
            iframe.onload = function() {
              var msgJson = {
                action: "loadReport",
                accessToken: "<?=$apptoken?>",
                height: 500,
                width: 722
              };
              var msgTxt = JSON.stringify(msgJson);
              iframe.contentWindow.postMessage(msgTxt, "*");
            };
          };
        }());
      </script>
    </body>
```

E aqui está nosso resultado:

![](media\power-bi-embedded-iframe\view-report.png)

No momento, Power BI incorporado só mostra o relatório no iframe. Mas, fique atento o [Blog do Power BI](). Futuras melhorias podem usar o novo cliente APIs que será Deixe-nos enviar informações para o iframe, além de obter informações de check-out. Questões interessantes!


## <a name="see-also"></a>Consulte também
- [Autenticação e autorização no Power BI inserida](power-bi-embedded-app-token-flow.md)
