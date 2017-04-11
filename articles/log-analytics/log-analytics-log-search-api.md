<properties
    pageTitle="API REST de pesquisa de log de análise de log | Microsoft Azure"
    description="Este guia fornece um tutorial básico que descreve como você pode usar a pesquisa de análise de Log API REST no pacote de gerenciamento de operações (OMS) e fornece exemplos que mostram como usar os comandos."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="log-analytics-log-search-rest-api"></a>Pesquisa de log de análise de log API REST

Este guia fornece um tutorial básico que descreve como você pode usar a API REST de pesquisa de análise de Log no pacote de gerenciamento de operações (OMS) e fornece exemplos que mostram como usar os comandos. Alguns dos exemplos deste artigo se referir a obtenção de informações operacionais, que é o nome da versão anterior do Log de análise.

## <a name="overview-of-the-log-search-rest-api"></a>Visão geral de pesquisa de Log de API REST

A API REST de pesquisa de análise de Log é RESTful e podem ser acessada por meio da API do Gerenciador de recursos do Azure. Neste documento você encontrará exemplos onde a API é acessada através do [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de fonte aberta que simplifica a chamar a API do Gerenciador de recursos do Azure. O uso de ARMClient e PowerShell é uma das várias opções para acessar a API de pesquisa de análise de Log. Outra opção é usar o módulo do PowerShell do Azure para OperationalInsights que inclui cmdlets para acessar a pesquisa. Com essas ferramentas, você pode utilizar a API do Gerenciador de recursos do Azure RESTful para fazer chamadas para espaços de trabalho do OMS e executar comandos de pesquisa dentro deles. A API produzirá resultados de pesquisa para você no formato JSON, permitindo que você use os resultados da pesquisa de muitas maneiras diferentes programaticamente.

O Gerenciador de recursos do Azure podem ser usado por meio de uma [biblioteca para .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) , bem como uma [API REST](https://msdn.microsoft.com/library/azure/mt163658.aspx). Examine as páginas da web vinculadas para saber mais.

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>Tutorial de API REST do Log Analytics pesquisa básica

### <a name="to-use-the-arm-client"></a>Para usar o cliente de ARM

1. Instale o [Chocolatey](https://chocolatey.org/), que é um Gerenciador de pacote de fonte aberta para Windows. Abra uma janela do prompt de comando como administrador e, em seguida, execute o seguinte comando:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

2. Instale o ARMClient executando o seguinte comando:

    ```
    choco install armclient
    ```

### <a name="to-perform-a-simple-search-using-the-armclient"></a>Para realizar uma pesquisa simples usando o ARMClient

1. Login à sua conta da Microsoft ou OrgID:

    ```
    armclient login
    ```

    Um login bem-sucedido lista todas as assinaturas vinculadas a determinada conta:

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. Obter os espaços de trabalho do pacote de gerenciamento de operações:

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Uma chamada Get bem-sucedida seria saída todos os espaços de trabalho associados à assinatura:

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Crie sua variável de pesquisa:

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Pesquise usando sua nova variável de pesquisa:

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Alguns exemplos de referência de API de REST de pesquisa de análise do log
Os exemplos a seguir mostram como você pode usar a API de pesquisa.

### <a name="search---actionread"></a>Pesquisa - ação/lido

**Exemplo de Url:**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**Solicitação:**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
A tabela a seguir descreve as propriedades que estão disponíveis.

|**Propriedade**|**Descrição**|
|---|---|
|Início|O número máximo de resultados para retornar.|
|Realce|Contém parâmetros pre e post, geralmente usados para realçar campos coincidentes|
|Pre|Prefixos a cadeia de caracteres determinada aos campos correspondentes.|
|Postar|Acrescenta a cadeia de caracteres determinada aos campos correspondentes.|
|consulta|A consulta de pesquisa usada para coletar e retornar resultados.|
|Iniciar|Início da janela de tempo que deseja resultados ser encontradas a partir.|
|final|Fim da janela de tempo que deseja resultados ser encontradas a partir.|


**Resposta:**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>Pesquisar / {ID} - ação/lido

**Solicite o conteúdo de uma pesquisa salva:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

>[AZURE.NOTE] Se a pesquisa retorna com um status 'Pendente', os resultados atualizados de sondagem, em seguida, pode ser feito por meio dessa API. Após 6 min, o resultado da pesquisa será ignorado do cache e HTTP não existe mais será retornado. Se a solicitação de pesquisa inicial retornou um status de 'Bem-sucedido' imediatamente, ele não será adicionado ao cache causando essa API retornar HTTP não existe mais se consultado. O conteúdo de um resultado de HTTP 200 será no mesmo formato que a solicitação de pesquisa inicial apenas com valores atualizados.

### <a name="saved-searches---rest-only"></a>Pesquisas - salvas apenas restante

**Solicite a lista de pesquisas salvas:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Métodos suportados: GET colocar excluir

Métodos de coleção suportados: obter

A tabela a seguir descreve as propriedades que estão disponíveis.

|Propriedade|Descrição|
|---|---|
|ID|O identificador exclusivo.|
|ETag|**Necessário para o Patch**. Atualizado pelo servidor em cada gravação. Valor deve ser igual ao valor armazenado atual ou ' *' Atualizar. 409 retornado para valores de antigas/inválidas.|
|Properties.Query|**Obrigatório**. A consulta de pesquisa.|
|properties.displayName|**Obrigatório**. O nome de exibição definido de usuário da consulta. Se modelados como um recurso Azure, isso seria uma marca.|
|Properties.Category|**Obrigatório**. O usuário definido categoria da consulta. Se modelados como um recurso Azure isso seria uma marca.|

>[AZURE.NOTE] A API de pesquisa de análise de Log atualmente retorna criados pelo usuário o pesquisas quando pesquisou pesquisas salvas em um espaço de trabalho salvas. A API não retornará pesquisas salvas fornecidas pelo soluções neste momento. Esta funcionalidade será adicionada em uma data posterior.

### <a name="create-saved-searches"></a>Criar pesquisas salvas

**Solicitação:**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="delete-saved-searches"></a>Pesquisas de excluir salvado

**Solicitação:**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>Pesquisas de atualização salvada

 **Solicitação:**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>Metadados - JSON somente

Aqui está uma maneira para ver os campos para todos os tipos de log para os dados coletados em seu espaço de trabalho. Por exemplo, se você quiser que você sabe se o tipo de evento tem um campo chamado computador, isso é uma maneira de pesquisar e confirme.

**Campos de solicitação de:**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Resposta:**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

A tabela a seguir descreve as propriedades que estão disponíveis.

|**Propriedade**|**Descrição**|
|---|---|
|nome|Nome do campo.|
|displayName|O nome de exibição do campo.|
|tipo|O tipo do valor do campo.|
|facetable|Combinação de atual 'indexado', ' armazenado ' e 'faceta' Propriedades.|
|Exibir|Propriedade atual 'Exibir'. True se o campo estiver visível na pesquisa.|
|ownerType|Reduzido para somente tipos pertencentes do onboarded IP.|


## <a name="optional-parameters"></a>Parâmetros opcionais
As informações a seguir descrevem os parâmetros opcionais disponíveis.

### <a name="highlighting"></a>Realce

O parâmetro "Realce" é um parâmetro opcional, você pode usar para solicitar o subsistema de pesquisa incluir um conjunto de marcadores na sua resposta.

Esses marcadores indicam o início e término texto realçado que coincida com os termos fornecidos em sua consulta de pesquisa.
Você pode especificar os marcadores de início e término que serão usados pela pesquisa para quebrar o termo realçado.

**Consulta de pesquisa de exemplo**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**Resultado de exemplo:**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

Observe que o resultado acima contém uma mensagem de erro que foi prefixo e acrescentada.

## <a name="computer-groups"></a>Grupos de computador

Os grupos de computador são pesquisas salvas especiais que retornam um conjunto de computadores.  Você pode usar um grupo de computador em outras consultas para limitar os resultados para os computadores no grupo.  Um grupo de computador é implementado como uma pesquisa salva com uma marca de grupo com um valor de computador.

A seguir é uma resposta de amostra para um grupo de computador.

    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
        }],
    "Version": 1
    }

### <a name="retrieving-computer-groups"></a>Recuperando grupos de computador

Use o método obter com a ID do grupo para recuperar um grupo de computador.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>Criar ou atualizar um grupo de computador

Use o método de colocar com um exclusivo salvo ID de pesquisa para criar um novo grupo de computador. Se você usar um ID de grupo existente do computador, depois que um será modificado. Quando você cria um grupo de computador no console de OMS, a ID é criada do nome e o grupo.

A consulta usada para a definição de grupo deve retornar um conjunto de computadores para o grupo funcionar corretamente.  É recomendável que você terminar sua consulta com *| Computador distinta* para garantir que os dados corretos são retornados.

A definição da pesquisa salva deve incluir uma marca chamada grupo com um valor de computador para a pesquisa a serem classificadas como um grupo de computador.

    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson

### <a name="deleting-computer-groups"></a>Excluindo grupos de computador

Use o método Delete com a ID do grupo para excluir um grupo de computador.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [as pesquisas de log](log-analytics-log-searches.md) compilar consultas usando campos personalizados para critérios.
