<properties
    pageTitle="Como usar filas de barramento de serviço com Ruby | Microsoft Azure"
    description="Saiba como usar filas de barramento de serviço no Azure. Exemplos de código escritos em Ruby."
    services="service-bus"
    documentationCenter="ruby"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Como usar filas de barramento de serviço

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este guia descreve como usar filas de barramento de serviço. Os exemplos são gravados em Ruby e usam o Azure gem. Os cenários cobertos incluem **Criando filas, enviar e receber mensagens**e **excluindo filas**. Para obter mais informações sobre filas de barramento de serviço, consulte a seção [Próximas etapas](#next-steps) .

## <a name="what-are-service-bus-queues"></a>O que são filas de barramento de serviço?

Filas de barramento de serviço oferecem suporte a um modelo de comunicação *intermediário de mensagens* . Com filas, componentes de um aplicativo distribuído não comunicarem diretamente; em vez disso, eles trocam mensagens por meio de uma fila, que atua como um intermediário. Um produtor de mensagem (remetente) mãos desativar uma mensagem na fila e, em seguida, continua seu processamento.
Forma assíncrona, um consumidor de mensagem (receptor) obtém a mensagem da fila e a processa. O produtor não tem de esperar uma resposta do consumidor para continuar a processar e ainda mais enviar mensagens. Filas oferecem **primeiro em, PEPS (primeiro)** entrega de mensagens para uma ou mais consumidores concorrência. Ou seja, as mensagens são normalmente recebidas e processadas pelos receptores na ordem em que eles foram adicionados à fila, e cada mensagem é recebida e processada pelo consumidor de apenas uma mensagem.

![QueueConcepts](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

Filas de barramento de serviço são uma tecnologia de uso geral que pode ser usada para uma ampla variedade de situações:

-   Comunicação entre funções da web e trabalhador em um [aplicativo do Azure de vários níveis](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md).
-   Comunicação entre aplicativos no local e o Azure hospedado aplicativos em uma [solução híbrida](service-bus-dotnet-hybrid-app-using-service-bus-relay.md).
-   Comunicação entre componentes de um aplicativo distribuído em execução no local em organizações diferentes ou departamentos de uma organização.

Usando filas pode permitem que você dimensionar seus aplicativos melhores e habilitar mais resiliência para sua arquitetura.

## <a name="create-a-namespace"></a>Criar um namespace

Para começar a usar filas de barramento de serviço no Azure, você deve primeiro criar um namespace. Um namespace fornece um contêiner de escopo para endereçar recursos de barramento de serviço dentro de seu aplicativo. Você deve criar o espaço para nome por meio da interface de linha de comando porque o portal do Azure não criar namespace com uma conexão do ACS.

Para criar um namespace:

1. Abra um console do Powershell do Azure.

2. Digite o seguinte comando para criar um namespace barramento de serviço. Forneça seu próprio valor namespace e especifique o mesmo região como seu aplicativo.

    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

    ![Create Namespace](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)
    ```

## <a name="obtain-management-credentials-for-the-namespace"></a>Obter credenciais de gerenciamento do namespace

Para realizar operações de gerenciamento, como criar uma fila no novo namespace, você deve obter as credenciais de gerenciamento do namespace.

O cmdlet do PowerShell que você executou para criar o espaço para nome do serviço Azure barramento exibe a chave que você pode usar para gerenciar namespace. Copie o valor de **DefaultKey** . Você usará esse valor em seu código posteriormente neste tutorial.

![Copiar chave](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

> [AZURE.NOTE] Você também pode encontrar esta tecla se você faça logon no [portal do Azure](https://portal.azure.com/) e navegar até as informações de conexão para seu namespace barramento de serviço.

## <a name="create-a-ruby-application"></a>Criar um aplicativo Ruby

Crie um aplicativo Ruby. Para obter instruções, consulte [criar um aplicativo Ruby no Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar barramento de serviço

Para usar barramento de serviço do Azure, baixar e usar o pacote Ruby Azure, que inclui um conjunto de bibliotecas de conveniência que se comunicar com os serviços de armazenamento restante.

### <a name="use-rubygems-to-obtain-the-package"></a>Use RubyGems para obter o pacote

1. Use uma interface de linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2. Digite "azure de instalação de gem" na janela de comando para instalar o gem e as dependências.

### <a name="import-the-package"></a>Importar o pacote

Usando o editor de texto favorito, adicione o seguinte à parte superior do arquivo Ruby onde você pretende usar o armazenamento:

```
require "azure"
```

## <a name="set-up-an-azure-service-bus-connection"></a>Configurar uma conexão de barramento de serviço do Azure

O módulo Azure lê as variáveis de ambiente **AZURE\_SERVICEBUS\_NAMESPACE** e **AZURE\_SERVICEBUS\_ACCESS_KEY** para informações necessárias para se conectar ao seu namespace barramento de serviço. Se essas variáveis de ambiente não estiverem definidas, você deve especificar as informações de namespace antes de usar **Azure::ServiceBusService** com o seguinte código:

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Defina o valor de espaço para nome para o valor que você criou, em vez de toda a URL. Por exemplo, use **"yourexamplenamespace"**, não "yourexamplenamespace.servicebus.windows.net".

## <a name="how-to-create-a-queue"></a>Como criar uma fila

O objeto **Azure::ServiceBusService** permite que você trabalhe com filas. Para criar uma fila, use o método **create_queue()** . O exemplo a seguir cria uma fila ou imprime todos os erros.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Você também pode passar um objeto de **Azure::ServiceBus::Queue** com opções adicionais, que permite substituir as configurações de fila padrão, como o tempo de live mensagem ou o tamanho máximo da fila. O exemplo a seguir mostra como configurar o tamanho máximo da fila para 5GB e a hora ao vivo para 1 minuto:

```
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Como enviar mensagens para uma fila

Para enviar uma mensagem para uma fila de barramento de serviço, você aplicativo chama o **Enviar\_fila\_message()** método no objeto **Azure::ServiceBusService** . Mensagens enviadas para (e recebidas de) filas de barramento de serviço são **Azure::ServiceBus::BrokeredMessage** objetos e tem um conjunto de propriedades padrão (como o **rótulo** e **tempo\_para\_live**), um dicionário que é usado para armazenar propriedades específicas do aplicativo personalizado e um corpo de dados de aplicativo aleatório. Um aplicativo pode definir o corpo da mensagem, passando um valor de cadeia de caracteres da mensagem e as propriedades padrão obrigatórias serão preenchidas com valores padrão.

O exemplo a seguir demonstra como enviar uma mensagem de teste para a fila denominada "fila de teste" usando **Enviar\_fila\_message()**:

```
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Filas de barramento de serviço oferecem suporte a um tamanho máximo de mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB no [nível Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizados, pode ter um tamanho máximo de 64 KB. Não há nenhum limite o número de mensagens mantido em uma fila, mas há um limite no tamanho total das mensagens apresentadas por uma fila. Este tamanho de fila é definido em tempo de criação, com um limite de 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Como receber mensagens de uma fila

As mensagens são recebidas de um fila usando o **receber\_fila\_message()** método no objeto **Azure::ServiceBusService** . Por padrão, as mensagens são lidos e bloqueadas sem serem excluídas da fila. No entanto, você pode excluir mensagens da fila conforme eles são lidos definindo a **: peek_lock** opção como **false**.

O comportamento padrão torna a leitura e a exclusão de uma operação de dois estágios, que também possibilita dar suporte a aplicativos que não é possível suportar mensagens ausentes. Quando barramento de serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, bloqueia, para evitar que outros consumidores recebê-lo e retorna para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou armazena confiável para processamento futuro), ele conclui o segundo estágio do processo de recebimento chamando **Excluir\_fila\_message()** método e fornecendo a mensagem a ser excluída como um parâmetro. O **Excluir\_fila\_message()** método será marcar a mensagem como sendo consumida e removê-lo da fila.

Se o **: espiada\_lock** parâmetro for definido como **false**, ler e excluir a mensagem se torna o modelo mais simples e funciona melhor para cenários em que um aplicativo pode suportar não processando uma mensagem em caso de uma falha. Para entender isso, considere um cenário em que o consumidor emite a solicitação de recebimento e então trava antes de processá-lo. Porque barramento de serviço será marcou a mensagem como sendo consumida, quando o aplicativo é reiniciado e começa consumindo mensagens novamente, a mensagem que foi consumida antes da falha ter perdido a ele.

O exemplo a seguir demonstra como receber e processar mensagens usando **receber\_fila\_message()**. O exemplo primeiro recebe e exclui uma mensagem usando **: espiada\_lock** definida como **false**, ele recebe outra mensagem e, em seguida, exclui a mensagem usando **Excluir\_fila\_message()**:

```
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como lidar com mensagens ilegíveis e travamentos de aplicativos

Barramento de serviço fornece funcionalidade para ajudá-lo a recuperar normalmente de erros em seu aplicativo ou dificuldades processamento de uma mensagem. Se um aplicativo receptor é possível processar a mensagem por algum motivo, ele pode chamar a **Desbloquear\_fila\_message()** método no objeto **Azure::ServiceBusService** . Isso faz com que o barramento de serviço desbloquear a mensagem dentro da fila e disponibilizá-lo a ser recebida novamente, pelo mesmo aplicativo consumindo ou por outro aplicativo consumindo.

Há também um tempo limite associado a uma mensagem bloqueada dentro da fila e se o aplicativo falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se o aplicativo trava), e em seguida, serviço desbloqueie a mensagem automaticamente e disponibiliza a ser recebida novamente.

Se o aplicativo trava após processar a mensagem mas antes do **Excluir\_fila\_message()** método é chamado, em seguida, a mensagem será entregue novamente para o aplicativo quando ele for reiniciado. Isso normalmente é chamado **Pelo menos uma vez processamento**; ou seja, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser entregue novamente. Se o cenário não pode suportar processamento duplicado, os desenvolvedores de aplicativos devem adicionar lógica adicional para seu aplicativo para controlar a entrega de mensagem duplicada. Isso geralmente é feito usando o **mensagem\_id** propriedade da mensagem, que permanece constante entre tentativas de entrega.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos de filas de barramento de serviço, siga estes links para saber mais.

-   Visão geral de [filas, tópicos e assinaturas](service-bus-queues-topics-subscriptions.md).
-   Visite o repositório de [SDK do Azure para Ruby](https://github.com/Azure/azure-sdk-for-ruby) no GitHub.

Para uma comparação entre as filas de barramento de serviço do Azure discutido neste artigo e filas do Azure discutido no artigo [como usar o armazenamento de fila do Ruby](../storage/storage-ruby-how-to-use-queue-storage.md) , consulte [filas do Azure e filas de barramento de serviço do Azure - comparada e Contrasted](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
 
