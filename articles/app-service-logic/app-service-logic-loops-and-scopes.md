<properties
   pageTitle="Aplicativos de lógica Loops, escopos e Debatching | Microsoft Azure"
   description="Loop de aplicativo de lógica, escopo e debatching conceitos"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# <a name="logic-apps-loops-scopes-and-debatching"></a>Aplicativos de lógica Loops, escopos e Debatching
  
>[AZURE.NOTE] Esta versão do artigo se aplica ao esquema de lógica 2016 de aplicativos-04-01-preview e posterior.  Conceitos são semelhantes para esquemas mais antigos, mas escopos só estão disponíveis para este esquema e posterior.
  
## <a name="foreach-loop-and-arrays"></a>Loop ForEach e matrizes
  
Lógica de aplicativos permite que você faça um loop sobre um conjunto de dados e executar uma ação para cada item.  Isso é possível por meio do `foreach` ação.  No designer, você pode especificar para adicionar um para cada loop.  Depois de selecionar a matriz que você deseja itera, você pode começar a adicionar ações.  No momento você está limitado a apenas uma ação por loop foreach, mas essa restrição será elevada nas próximas semanas.  Uma vez dentro do loop você pode começar especificar o que deve ocorrer a cada valor da matriz.

Se usando o modo de exibição de código, você pode especificar um para cada loop como abaixo.  Este é um exemplo de um loop for each que envia um email para cada endereço de email que contém 'microsoft.com':

```
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  A `foreach` ação pode iteramos sobre matrizes até 5.000 linhas.  Cada iteração pode executar em paralelo, portanto, talvez seja necessário adicionar mensagens para uma fila se controle de fluxo é necessária.
  
## <a name="until-loop"></a>Até Loop
  
  Você pode executar uma ação ou série de ações até que uma condição seja atendida.  O cenário mais comum para isso é chamar um ponto de extremidade até chegar a resposta que você está procurando.  No designer, você pode especificar para adicionar um até loop.  Depois de adicionar ações dentro do loop, você pode definir a condição de saída, bem como o loop limites.  Há um atraso de um minuto entre ciclos de loop.
  
  Se usando o modo de exibição de código, você pode especificar um até loop, como abaixo.  Este é um exemplo de chamar um ponto de extremidade HTTP até que o corpo da resposta tem o valor 'Concluído'.  Ele concluirá quando qualquer um dos 
  
  * Resposta HTTP tem o status 'Concluído'
  * Ele tentou para 1 hora
  * Ela tem loop 100 vezes
  
  ```
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed'),
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn e Debatching

Às vezes, um disparador pode receber uma matriz de itens que você deseja debatch e iniciar um fluxo de trabalho por item.  Isso pode ser feito por meio do `spliton` comando.  Por padrão, se sua swagger disparador Especifica uma carga que é uma matriz, uma `spliton` será adicionada e iniciar uma execução por item.  SplitOn só pode ser adicionado a um disparador.  Isso pode ser manualmente configurado ou substituído em definição modo de exibição de código.  Atualmente SplitOn pode debatch matrizes até 5.000 itens.  Você não pode ter um `spliton` e também implementar o padrão de resposta síncrono.  Qualquer fluxo de trabalho chamado que possui um `response` ação além `spliton` será executado asyncronously e enviar imediata `202 Accepted` resposta.  

SplitOn pode ser especificado no modo de exibição de código, como o exemplo a seguir.  Este recebe uma matriz de itens e debatches em cada linha.

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequency": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>Escopos

É possível agrupar uma série de ações em conjunto usando um escopo.  Isso é especialmente útil para implementar tratamento de exceção.  No designer, você pode adicionar um novo escopo e começar a adicionar quaisquer ações dentro dela.  Você pode definir escopos na visualização de código semelhante ao seguinte:


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
