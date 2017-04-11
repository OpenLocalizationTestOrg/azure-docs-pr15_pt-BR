<properties 
    pageTitle="Como fazer logon eventos Hubs de evento do Azure no gerenciamento de API do Azure | Microsoft Azure" 
    description="Saiba como fazer logon eventos Hubs de evento do Azure no gerenciamento de API do Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Como fazer logon eventos Hubs de evento do Azure no gerenciamento de API do Azure

Azure Hubs de evento é um serviço de ingresso de dados altamente escaláveis que pode incluir milhões de eventos por segundo para que você possa processar e analisar grandes quantidades de dados produzidos por seus aplicativos e os dispositivos conectados. Hubs de evento atua como a "porta frontal" para um pipeline de evento e depois que os dados coletados a um hub de evento, que pode ser transformado e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de armazenamento/processamento em lotes. Hubs de evento separa a produção de um fluxo de eventos de consumo desses eventos, para que os consumidores de evento podem acessar os eventos no seu próprio cronograma.

Este artigo é um complemento para o vídeo de [Integrar o gerenciamento de API do Azure com Hubs de evento](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) e descreve como registrar eventos de gerenciamento de API utilizando Hubs de evento do Azure.

## <a name="create-an-azure-event-hub"></a>Criar um Hub de evento Azure

Para criar um novo Hub de evento, entrar no [portal do Azure clássico](https://manage.windowsazure.com) e clique em **novo**->**Os serviços de aplicativo**->**Barramento de serviço**->**Hub de evento**->**Criação rápida**. Insira um nome de Hub de evento, região, selecione uma assinatura e selecione um namespace. Se você ainda não criou anteriormente um namespace, você pode criar um digitando um nome na caixa de texto **Namespace** . Quando todas as propriedades estão configuradas, clique em **criar um novo Hub de evento** para criar o Hub de evento.

![Criar hub de evento][create-event-hub]

Em seguida, navegue até a guia **Configurar** para seu novo Hub de eventos e crie duas **políticas de acesso compartilhado**. Nomeie a primeira **envio** e dar a ele permissões **Enviar** .

![Política de envio][sending-policy]

Nomear o segundo **recebendo**, dar a ele permissões **ouvir** e clique em **Salvar**.

![Política de recebimento][receiving-policy]

Cada política de acesso compartilhado permite que aplicativos enviar e receber eventos para e do Hub do evento. Para acessar as cadeias de caracteres de conexão para essas políticas, navegue até a guia **painel** do Hub evento e clique em **informações de Conexão**.

![Cadeia de Conexão][event-hub-dashboard]

A cadeia de conexão de **envio** é usada quando o log de eventos e a cadeia de conexão **recebendo** é usada quando baixando eventos de Hub do evento.

![Cadeia de Conexão][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>Criar um agente de gerenciamento de API

Agora que você tem um Hub de evento, a próxima etapa é configurar um [agente de log](https://msdn.microsoft.com/library/azure/mt592020.aspx) no seu serviço de gerenciamento de API para que ele pode fazer logon eventos Hub do evento.

Registradores de gerenciamento de API são configurados usando a [API do resto de gerenciamento](http://aka.ms/smapi). Antes de usar a API REST pela primeira vez, examine os [pré-requisitos](https://msdn.microsoft.com/library/azure/dn776326.aspx#Prerequisites) e garantir que você tenha [habilitado o acesso à API REST](https://msdn.microsoft.com/library/azure/dn776326.aspx#EnableRESTAPI).

Para criar um agente de log, faça uma solicitação de HTTP colocar usando o modelo de URL a seguir.

    https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview

-   Substituir `{your service}` com o nome da sua instância de serviço de gerenciamento de API.
-   Substituir `{new logger name}` com o nome desejado para seu novo agente. Você fará referência a esse nome ao configurar a política de [log para eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)

Adicione os seguintes cabeçalhos à solicitação.

-   Tipo de conteúdo: aplicativo/json
-   Autorização: SharedAccessSignature uid = …
    -   Para obter instruções sobre gerando o `SharedAccessSignature` consulte [Autenticação de API do Azure API gerenciamento restante](https://msdn.microsoft.com/library/azure/dn798668.aspx).

Especifique o corpo da solicitação usando o modelo a seguir.

    {
      "type" : "AzureEventHub",
      "description" : "Sample logger description",
      "credentials" : {
        "name" : "Name of the Event Hub from the Azure Classic Portal",
        "connectionString" : "Endpoint=Event Hub Sender connection string"
        }
    }

-   `type`deve ser definida como `AzureEventHub`.
-   `description`Fornece uma descrição opcional do agente de log e pode ser uma cadeia de comprimento zero, se desejar.
-   `credentials`contém o `name` e `connectionString` do seu Hub de evento do Azure.

Quando você faz a solicitação, se o agente de log é criado um código de status `201 Created` será retornado. 

>[AZURE.NOTE] Para outros possíveis códigos de retorno e os motivos, consulte [criar um agente de log](https://msdn.microsoft.com/library/azure/mt592020.aspx#PUT). Para ver como realizar outras operações como lista, atualizar e excluir, consulte a documentação de entidade do [agente de log](https://msdn.microsoft.com/library/azure/mt592020.aspx) .

## <a name="configure-log-to-eventhubs-policies"></a>Configurar políticas de log para eventhubs

Quando o agente de log é configurada no gerenciamento de API, você pode configurar suas políticas de log para eventhubs para registrar os eventos desejados. A política de log para eventhubs pode ser usada na seção diretiva de entrada ou a seção de política de saída.

Para configurar políticas, entrar no [portal clássico Azure](https://manage.windowsazure.com), navegue seu serviço de gerenciamento de API e clique em **portal do publisher** ou **Gerenciar** para acessar o portal do publisher.

![Portal do Publisher][publisher-portal]

Clique em **políticas** no menu de gerenciamento de API à esquerda, selecione a API e o produto desejado e clique em **Adicionar política**. Neste exemplo estamos adicionando uma política à **API de eco** no produto **ilimitado** .

![Adicionar política][add-policy]

Posicione o cursor no `inbound` política de seção e clique na política de **Log de EventHub** para inserir o `log-to-eventhub` modelo de política de instrução.

![Editor de política][event-hub-policy]

    <log-to-eventhub logger-id ='logger-id'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
    </log-to-eventhub>

Substituir `logger-id` com o nome do agente de log de gerenciamento de API configurado na etapa anterior.

Você pode usar qualquer expressão que retorna uma cadeia de caracteres como o valor para o `log-to-eventhub` elemento. Neste exemplo, uma cadeia de caracteres que contém a data e hora, nome do serviço, id da solicitação, endereço de ip de solicitação e nome da operação é registrada.

Clique em **Salvar** para salvar a configuração de política atualizado. Assim que ele é salvo a política está ativa e eventos são registrados para o Hub de evento designada.

## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre Hubs de evento do Azure
    -   [Começar com Hubs de evento do Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
    -   [Receber mensagens com EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
    -   [Guia de programação de Hubs de evento](../event-hubs/event-hubs-programming-guide.md)
-   Saiba mais sobre a integração de gerenciamento de API e Hubs de evento
    -   [Referência de entidade do agente](https://msdn.microsoft.com/library/azure/mt592020.aspx)
    -   [referência de política de log para eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
    -   [Monitorar seu APIs com gerenciamento de API Azure, Hubs de evento e Runscope](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>Assista a um vídeo passo a passo

> [AZURE.VIDEO integrate-azure-api-management-with-event-hubs]


[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png






