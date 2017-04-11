<properties
    pageTitle="Gatilho de timer do Azure funções | Microsoft Azure"
    description="Compreenda como usar disparadores de timer em funções do Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="funções de Azure, funções, processamento de eventos, computação dinâmico, arquitetura sem servidor"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-timer-trigger"></a>Azure gatilho de timer de funções

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar disparadores de timer em funções do Azure. Timer aciona funções de chamada com base em um cronograma, uma vez ou recorrente.  

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a name="functionjson-for-timer-trigger"></a>Function.JSON para disparador de timer

O arquivo *function.json* fornece uma expressão de cronograma. Por exemplo, a seguinte agenda executa a função de cada minuto:

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O disparador de timer lida com várias instâncias escala-out automaticamente: apenas uma única instância de uma função particular timer será executado em todas as instâncias.

## <a name="format-of-schedule-expression"></a>Formato de expressão de agendamento

A expressão de cronograma é uma [expressão CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) que inclui 6 campos: `{second} {minute} {hour} {day} {month} {day of the week}`. 

Observe que muitas das expressões cron que encontrar online omitem o campo {segundo}, para se você copiar de um desses tê ajustar para o campo extra. 

Aqui está algumas outra cronograma exemplos de expressões:

Disparar uma vez a cada 5 minutos:

```json
"schedule": "0 */5 * * * *"
```

Disparar uma vez na parte superior de cada hora:

```json
"schedule": "0 0 * * * *",
```

Disparar uma vez a cada duas horas:

```json
"schedule": "0 0 */2 * * *",
```

Disparar cada hora de 9H para 5 PM:

```json
"schedule": "0 0 9-17 * * *",
```

Para dispara 9:30 AM diariamente:

```json
"schedule": "0 30 9 * * *",
```

Para dispara 9:30 AM todos os dias úteis:

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="timer-trigger-c-code-example"></a>Exemplo de código do disparador c# de timer

Este exemplo de código c# grava um único log sempre que a função é disparada.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
