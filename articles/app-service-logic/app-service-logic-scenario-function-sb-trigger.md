<properties
   pageTitle="Cenário de aplicativo de lógica: criar um gatilho de barramento de serviço de funções do Azure | Microsoft Azure"
   description="Usar funções do Azure para criar um disparador barramento de serviço para um aplicativo de lógica"
   services="logic-apps,functions"
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
   ms.date="05/23/2016"
   ms.author="jehollan"/>

# <a name="logic-app-scenario-create-an-azure-service-bus-trigger-by-using-azure-functions"></a>Cenário de aplicativo de lógica: criar um gatilho de barramento de serviço do Azure usando funções do Azure

Você pode usar funções do Azure para criar um disparador para um aplicativo de lógica quando você precisa implantar um ouvinte ou uma tarefa demorada. Por exemplo, você pode criar uma função que será ouça de uma fila e, em seguida, acionado imediatamente um aplicativo de lógica como um disparador de envio.

## <a name="build-the-logic-app"></a>Criar o aplicativo de lógica

Neste exemplo, você tem uma função em execução de cada aplicativo lógica que precisa ser disparadas. Primeiro, crie um aplicativo de lógica que tem um gatilho de solicitação HTTP. A função chama o ponto de extremidade sempre que uma mensagem de fila for recebida.  

1. Criar um novo aplicativo de lógica; Selecione o disparador **Manual - quando uma solicitação HTTP for recebida** .  
   Opcionalmente, você pode especificar um esquema JSON para usar com a mensagem de fila usando uma ferramenta como [jsonschema.net](http://jsonschema.net). Cole o esquema no disparador. Isso ajuda o designer de compreender a forma de dados e muito mais facilmente fluem propriedades no fluxo de trabalho.
1. Adicione quaisquer etapas adicionais que você deseja ocorrer após uma mensagem de fila for recebida. Por exemplo, envie um email por meio do Office 365.  
1. Salve o aplicativo de lógica para gerar a URL de retorno de chamada para o disparador para este aplicativo de lógica. A URL é exibida no cartão de disparador.

![O retorno de chamada URL aparece no cartão de disparadores][1]

## <a name="build-the-function"></a>Criar a função.

Em seguida, você precisa criar uma função que irá atuar como o disparador e ouvir a fila.

1. No [portal de funções do Azure](https://functions.azure.com/signin), selecione a **Nova função**e selecione o modelo de **ServiceBusQueueTrigger - c#** .

    ![Azure portal de funções][2]

2. Configurar a conexão para a fila de barramento de serviço (que usa o SDK de barramento de serviço do Azure `OnMessageReceive()` ouvinte).
3. Escreva uma função simple para chamar o ponto de extremidade de aplicativo de lógica (criado anteriormente) usando a mensagem fila como um disparador. Aqui está um exemplo completo de uma função. O exemplo usa um `application/json` tipo de conteúdo de mensagem, mas você pode alterar isso se necessário.

   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";

   public static void Run(string myQueueItem, TraceWriter log)
   {

       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

Para testar, adicione uma mensagem de fila por meio de uma ferramenta como o [Explorador de barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer). Ver o aplicativo de lógica fire imediatamente após a função recebe a mensagem.

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG
