<properties
 pageTitle="Entidades, termos e conceitos Agendador | Microsoft Azure"
 description="Hierarquia de entidades, incluindo trabalhos e conjuntos de trabalho, terminologia e conceitos de Agendador Azure.  Mostra um exemplo abrangente de uma tarefa agendada."
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>Conceitos do Agendador, terminologia + hierarquia entidade

## <a name="scheduler-entity-hierarchy"></a>Hierarquia de entidade do Agendador

A tabela a seguir descreve os recursos principais exposto ou usado por API do Agendador:

|Recurso | Descrição |
|---|---|
|**Conjunto de trabalho**|Uma coleção de trabalho contém um grupo de trabalhos e mantém as configurações, cotas e limitações que são compartilhadas por trabalhos dentro do conjunto. Um conjunto de trabalho é criado por um proprietário de assinatura e trabalhos em conjunto com base em limites de uso ou aplicativo de grupos. É restringido a uma região. Isso também permite a imposição de cotas para restringir o uso de todos os trabalhos nessa coleção. As cotas incluem MAXTRABALHOS e MaxRecurrence.|
|**Trabalho**|Um trabalho define uma única ação recorrente, com estratégias simples ou complexas para execução. Ações podem incluir HTTP, fila do armazenamento, fila de barramento de serviço ou solicitações de tópico de barramento de serviço.|
|**Histórico de trabalho**|Um histórico de trabalho representa detalhes de uma execução de um trabalho. Ele contém sucesso versus falha, bem como quaisquer detalhes de resposta.|

## <a name="scheduler-entity-management"></a>Gerenciamento de entidade do Agendador

Em um alto nível, o Agendador e a API de gerenciamento do serviço expõem as seguintes operações os recursos:

|Recurso|Endereço URI e descrição|
|---|---|
|**Gerenciamento de conjunto de trabalho**|OBTER, colocar e exclua o suporte para criar e modificar os trabalhos contidos e conjuntos de trabalho. Uma coleção de trabalho é um contêiner para trabalhos e mapas para cotas e configurações compartilhadas. Exemplos de cotas, descritos posteriormente, são o número máximo de trabalhos e menor intervalo de recorrência. <p>COLOCAR e exclua:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>Obter:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p>
|**Gerenciamento de trabalho**|OBTER, colocar, postar, PATCH e suporte para criar e modificar trabalhos excluído. Todos os trabalhos devem pertencer a uma coleção de trabalho que já existe, então não há nenhuma criação implícita. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p>|
|**Gerenciamento de histórico de trabalho**|Obtenha suporte para buscar 60 dias do histórico de execução de trabalho, como o tempo decorrido trabalho e resultados da execução de trabalho. Adiciona o suporte de parâmetro de cadeia de caracteres de consulta para filtragem com base no estado e status. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p>|

## <a name="job-types"></a>Tipos de trabalho

Há vários tipos de trabalhos: trabalhos HTTP (incluindo trabalhos HTTPS que oferecem suporte ao SSL), trabalhos de fila do armazenamento, trabalhos de fila de barramento de serviço e trabalhos de tópico de barramento de serviço. Trabalhos HTTP são ideais se você tiver um ponto de extremidade de uma carga de trabalho ou serviço existente. Você pode usar trabalhos de fila do armazenamento para postar mensagens em filas de armazenamento, para que os trabalhos são ideais para cargas de trabalho que usa filas de armazenamento. Da mesma forma, trabalhos de barramento de serviço são ideais para cargas de trabalho que usam tópicos e filas do barramento de serviço.

## <a name="the-job-entity-in-detail"></a>A entidade de "trabalho" em detalhes

Em um nível básico, um trabalho agendado tem várias partes:

- A ação a ser executada quando o timer de trabalho for acionado  

- (Opcional) O tempo para executar o trabalho  

- (Opcional) Quando e como com frequência repetir o trabalho  

- (Opcional) Uma ação a ser acionada se a ação primária falhar  

Internamente, um trabalho agendado também contém dados fornecidos pelo sistema como o tempo de execução agendado próximo.

O código a seguir fornece um exemplo abrangente de uma tarefa agendada. Detalhes são fornecidos nas seções subsequentes.

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

Conforme visto no trabalho agendado de exemplo acima, uma definição de trabalho tem várias partes:

- Hora de início ("hora de início")  

- Ação ("ação"), que inclui a ação de erro ("errorAction")

- Recorrência ("recorrência")  

- Estado ("estado")  

- Status ("status")  

- Repetir a política ("retryPolicy")  

Vamos examinar cada um em detalhes:

## <a name="starttime"></a>hora de início

"Hora de início" é a hora de início e permite ao chamador especificar um deslocamento de fuso horário na conexão no [formato ISO 8601](http://en.wikipedia.org/wiki/ISO_8601).

## <a name="action-and-erroraction"></a>ação e errorAction

A "ação" é a ação chamada em cada ocorrência e descreve um tipo de invocação de serviço. A ação é o que será executado na agenda fornecida. Agendador oferece suporte HTTP, fila do armazenamento, tópico de barramento de serviço e ações de fila de barramento de serviço.

A ação no exemplo acima é uma ação de HTTP. Abaixo está um exemplo de uma ação de fila do armazenamento:

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

Abaixo está um exemplo de uma ação de tópico de barramento de serviço.

  "ação": {"tipo": "serviceBusTopic", "serviceBusTopicMessage": {"topicPath": "t1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", / / pode ser netMessaging ou AMQP "autenticação": {"sasKeyName": "QPolicy", "tipo": "sharedAccessKey"}, "mensagem": "Alguns mensagem", "brokeredMessageProperties": {}, "customMessageProperties": {"appname": "FromScheduler"}},}

Abaixo está um exemplo de uma ação de fila de barramento de serviço:


  "ação": {"serviceBusQueueMessage": {"NomeDaFila": "T1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", / / pode ser netMessaging ou AMQP "autenticação": {  
        "sasKeyName": "QPolicy", "tipo": "sharedAccessKey"}, "mensagem": "Alguns mensagem",  
      "brokeredMessageProperties": {}, "customMessageProperties": {"appname": "FromScheduler"}}, "tipo": "serviceBusQueue"}

O "errorAction" é o manipulador de erro, a ação a ser chamada quando a ação principal falha. Você pode usar essa variável para chamar um ponto de extremidade de tratamento de erro ou enviar uma notificação de usuário. Isso pode ser usado para contatar um ponto de extremidade secundário no caso que o principal não está disponível (por exemplo, no caso de um desastre no local do ponto de extremidade) ou pode ser usado para notificar um ponto de extremidade de manipulação de erro. Assim como a ação principal, a ação de erro pode ser simple ou composta lógica com base em outras ações. Para saber como criar um token SAS, consulte [criar](https://msdn.microsoft.com/library/azure/jj721951.aspx)e usar uma assinatura de acesso compartilhado.

## <a name="recurrence"></a>recorrência

Recorrência tem várias partes:

- Frequência: Um dos minuto, hora, dia, semana, mês, ano  

- Intervalo: Intervalo na frequência determinada para a recorrência  

- Cronograma indicada: especificar minutos, horas, dias da semana, meses e monthdays da recorrência  

- Contagem: Contagem de ocorrências  

- Hora de término: nenhum trabalho será executada após a hora de término especificada  

Um trabalho é recorrente se ele tiver um objeto recorrente especificado em sua definição de JSON. Se Cont e a hora de término forem especificados, a regra de conclusão que ocorre primeiro será aplicada.

## <a name="state"></a>estado

O estado do trabalho é um dos quatro valores: ativado, desativado, concluídas ou com defeito. Você pode colocar ou trabalhos de PATCH para atualizá-los para o estado habilitado ou desabilitado. Se uma tarefa foi concluída ou com defeito, que é um estado final que não pode ser atualizado (embora o trabalho ainda pode ser excluído). Um exemplo da propriedade estado é da seguinte maneira:


        "state": "disabled", // enabled, disabled, completed, or faulted
Trabalhos com defeito e concluídos serão excluídos após 60 dias.

## <a name="status"></a>status

Depois que um trabalho do Agendador foi iniciado, serão retornadas informações sobre o status atual do trabalho. Esse objeto não é configurável pelo usuário — está definido pelo sistema. No entanto, ele está incluído no objeto de trabalho (em vez de um recurso de vinculado separado) para que um pode obter o status de um trabalho com facilidade.

Status do trabalho inclui a hora da execução anterior (se houver), o tempo da próxima execução agendada (para trabalhos em andamento) e a contagem de execução do trabalho.

## <a name="retrypolicy"></a>retryPolicy

Se um trabalho do Agendador falhar, é possível especificar uma política de repetição para determinar se e como a ação será repetida. Isso é determinado pelo objeto **retryType** — está definido como **Nenhum** se nenhuma diretiva de repetição, conforme mostrado acima. Configurá-lo para **corrigido** se houver uma política de repetição.

Para definir uma política de repetição, duas configurações adicionais podem ser especificadas: um intervalo de repetição (**retryInterval**) e o número de tentativas (**retryCount**).

O intervalo de repetição, especificado com o objeto **retryInterval** , é o intervalo entre as tentativas. Seu valor padrão é 30 segundos, seu valor configurável mínimo é de 15 segundos e o valor máximo é 18 meses. Trabalhos em conjuntos de trabalho gratuito têm um valor mínimo configurável de 1 hora.  Ele é definido no formato ISO 8601. Da mesma forma, o valor do número de tentativas é especificado no objeto de **retryCount** ; é o número de vezes que uma nova tentativa será feita. Seu valor padrão é 4 e seu valor máximo é 20\. ambos **retryInterval** e **retryCount** são opcionais. Eles recebem seu padrão valores se **retryType** for definido como **fixo** e não são especificados explicitamente.

## <a name="see-also"></a>Consulte também

 [O que é o Agendador?](scheduler-intro.md)

 [Começar a usar o Agendador no portal do Azure](scheduler-get-started-portal.md)

 [Planos e cobranças no Agendador do Azure](scheduler-plans-billing.md)

 [Como criar agendas complexas e recorrência avançada com o Agendador do Azure](scheduler-advanced-complexity.md)

 [Referência de API do Agendador REST Azure](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Agendador Azure](scheduler-powershell-reference.md)

 [Azure Agendador alta disponibilidade e confiabilidade](scheduler-high-availability-reliability.md)

 [Limites de Agendador Azure, padrões e códigos de erro](scheduler-limits-defaults-errors.md)

 [Autenticação de saída Agendador Azure](scheduler-outbound-authentication.md)
