<properties
   pageTitle="Manipulação de exceção de aplicativos lógica | Microsoft Azure"
   description="Saiba padrões de erro e tratamento de exceções com aplicativos de lógica do Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-error-and-exception-handling"></a>Erro de aplicativos de lógica e manipulação de exceção

Lógica de aplicativos fornece um conjunto sofisticado de ferramentas e padrões para ajudar a garantir que suas integrações são robustos e flexíveis contra falhas.  Um dos desafios com qualquer arquitetura de integração é garantir o tempo de inatividade ou problemas de sistemas dependentes são tratados adequadamente.  Lógica de aplicativos torna tratamento de erros uma experiência de primeira classe, dando a você as ferramentas que necessárias para agir em exceções e erros dentro de seus fluxos de trabalho.

## <a name="retry-policies"></a>Repetir políticas

O tipo mais básico de exceção e manipulação de erro é uma política de repetição.  Essa política define se a ação deve repetir se solicitação inicial expirou ou falha (qualquer solicitação que resultou em uma 429 ou resposta 5xx).  Por padrão, todas as ações repetir 4 vezes adicionais em intervalos de 20 segundos.  Portanto, se a primeira solicitação recebeu uma `500 Internal Server Error` resposta, o mecanismo de fluxo de trabalho pausa por 20 segundos e tente a solicitação novamente.  Se após todas as repetições a resposta ainda uma exceção ou falha, o fluxo de trabalho continuará e marcar o status de ação como `Failed`.

Você pode configurar políticas de repetir as **entradas** de uma determinada ação.  Uma política de repetição pode ser configurada para testar intervalos de até 4 vezes mais de 1 hora.  Detalhes completos sobre as propriedades de entrada podem ser [encontrada no MSDN][retryPolicyMSDN].

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

Se quiser que sua ação de HTTP repetir 4 horas e 10 minutos entre cada tentativa de esperar você teria a seguinte definição:

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

Para obter mais detalhes sobre sintaxe compatível, exibir a [seção de repetição-política no MSDN][retryPolicyMSDN].

## <a name="runafter-property-to-catch-failures"></a>Propriedade RunAfter para capturar falhas

Cada ação de aplicativo lógica declara quais ações são necessárias antes da ação será iniciado.  Você pode considerar isso como a ordem das etapas no seu fluxo de trabalho.  Essa ordem é conhecido como o `runAfter` propriedade na definição de ação.  É um objeto que descreve quais ações e status de ação seriam executar a ação.  Por padrão, todas as ações adicionadas por meio do designer são definidas como `runAfter` a etapa anterior, se a etapa anterior era `Succeeded`.  No entanto, você pode personalizar esse valor para disparar ações quando ações anteriores são `Failed`, `Skipped`, ou um conjunto de possível desses valores.  Se você quiser adicionar um item a um tópico de barramento de serviço designado após uma ação específica `Insert_Row` falhar, você deverá usar o seguinte `runAfter` configuração:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

Observe o `runAfter` propriedade estiver definida como acionada se o `Insert_Row` ação é `Failed`.  Para executar a ação se o status da ação for `Succeeded`, `Failed`, ou `Skipped` a sintaxe seria:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

>[AZURE.TIP] Ações que executar após uma ação anterior tiver falhado e concluído com êxito, serão marcadas como `Succeeded`.  Esse comportamento significa que se você com êxito variável todas as falhas de fluxo de trabalho a execução propriamente dito está marcada como `Succeeded`.

## <a name="scopes-and-results-to-evaluate-actions"></a>Escopos e resultados para avaliar ações

Semelhante a como você pode executar após ações individuais, você também pode agrupar ações juntas dentro de um [escopo](app-service-logic-loops-and-scopes.md) - que funcionam como um agrupamento lógico de ações.  Escopos são úteis para organizar suas ações de aplicativo lógica, tanto para realizar avaliações agregadas sobre o status de um escopo.  O escopo próprio receberá um status após concluir todas as ações em um escopo.  O status de escopo é determinado com os mesmos critérios como uma execução - se a ação final em uma ramificação de execução for `Failed` ou `Aborted` o status for `Failed`.

Você pode `runAfter` um escopo foi marcado `Failed` para acionar ações específicas quaisquer falhas ocorridas dentro do escopo.  Executando após falha de um escopo permite que você crie uma única ação para capturar falhas se *quaisquer* ações dentro do escopo falharem.

### <a name="getting-the-context-of-failures-with-results"></a>Obtendo o contexto de falhas com os resultados

Captura de falhas de um escopo é muito útil, mas você também pode querer o contexto para compreender exatamente quais ações de falha e quaisquer erros ou códigos de status que foram retornados.  O `@result()` função de fluxo de trabalho fornece contexto para o resultado de todas as ações em um escopo.

`@result()`leva um único parâmetro, o nome de escopo e retorna uma matriz de todos os resultados de ação de dentro desse escopo.  Esses objetos de ação incluem os mesmos atributos a `@actions()` saídas de objeto, incluindo a hora de início de ação, hora de término de ação, status da ação, entradas de ação, IDs de correlação de ação e ação.  Você pode facilmente emparelhar um `@result()` funcionar com um `runAfter` para enviar o contexto de quaisquer ações que falhou em um escopo.

Se você quiser executar uma ação *para cada* ação em um escopo que `Failed`, você pode emparelhar `@result()` com uma ação de **[Matriz de filtro](../connectors/connectors-native-query.md)** e um loop **[ForEach](app-service-logic-loops-and-scopes.md)** .  Isso permite filtrar a matriz de resultados para ações que falhou.  Você pode executar a matriz de resultado filtrado e execute uma ação para cada falha usando o loop **ForEach** .  Aqui está um exemplo abaixo, seguido por uma explicação detalhada.  Este exemplo envia uma solicitação de HTTP POST com o corpo de resposta de quaisquer ações que falhou no escopo `My_Scope`.

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Aqui está um passo a passo detalhada do que está acontecendo:

1. Ação de **Matriz de filtro** para filtrar a `@result('My_Scope')` para obter o resultado de todas as ações dentro`My_Scope`
1. Condição da **Matriz de filtro** é qualquer `@result()` item com o status igual a `Failed`.  Isso filtre a matriz de todos os resultados de ação de `My_Scope` para apenas uma matriz de resultados de ação que falhou.
1. Execute uma ação **para cada** em saídas **Matriz filtrada** .  Isso executará um ação *para cada* falhou resultado ação podemos filtrados acima.
    - Se houver uma única ação no escopo que falhou, as ações no `foreach` será executado somente uma vez.  Várias ações falhas faria com que uma ação por falha.
1. Enviar um HTTP POST na `foreach` item corpo de resposta, ou `@item()['outputs']['body']`.  O `@result()` forma item é a mesma que a `@actions()` de forma e pode ser analisado da mesma maneira.
1. Também incluído dois cabeçalhos personalizados com o nome da ação que falhou `@item()['name']` e executa a falha cliente ID de rastreamento `@item()['clientTrackingId']`.

Para referência, aqui está um exemplo de um único `@result()` item.  Você pode ver o `name`, `body`, e `clientTrackingId` propriedades analisados no exemplo acima.  Deve-se observar que fora de um `foreach`, `@result()` retorna uma matriz desses objetos.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/foo/bar does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

Você pode usar as expressões acima para executar diferentes padrões de manipulação de exceção.  Você pode optar por executar uma única exceção manipulando ação fora do escopo que aceita toda a matriz filtrada de falhas e remover o `foreach`.  Você também pode incluir outras propriedades úteis do `@result()` resposta mostrada acima.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnóstico do Azure e telemetria

Os padrões acima são excelentes maneira de lidar com erros e exceções dentro de uma execução, mas você também pode identificar e responder a erros independentes da execução em si.  [Diagnóstico do Azure](app-service-logic-monitor-your-logic-apps.md) fornece uma maneira simple de enviar todos os eventos de fluxo de trabalho (incluindo todos os status de executar e ação) a uma conta de armazenamento do Azure ou um Hub de evento do Azure.  Você pode monitorar os logs e métricas ou publicá-los em qualquer ferramenta de monitoramento que preferir, para avaliar o status de execução.  Uma opção de possível é transmitir todos os eventos por meio do Hub de evento do Azure para [A análise de fluxo](https://azure.microsoft.com/services/stream-analytics/).  Em análise de fluxo, você pode escrever consultas vivo de quaisquer problemas, médias ou falhas de logs de diagnóstico.  Análise de fluxo pode facilmente saída para outras fontes de dados como filas tópicos, SQL, DocumentDB e Power BI.

## <a name="next-steps"></a>Próximas etapas
- [Veja como um cliente integrado robustos tratamento de erros com aplicativos de lógica](app-service-logic-scenario-error-and-exception-handling.md)
- [Localizar mais exemplos de aplicativos de lógica e cenários](app-service-logic-examples-and-scenarios.md)
- [Aprenda a criar implantações automatizadas dos aplicativos de lógica](app-service-logic-create-deploy-template.md)
- [Criar e implantar aplicativos de lógica do Visual Studio](app-service-logic-deploy-from-vs.md)


<!-- References -->
[retryPolicyMSDN]: https://msdn.microsoft.com/library/azure/mt643939.aspx#Anchor_9