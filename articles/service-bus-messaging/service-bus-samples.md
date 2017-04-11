<properties 
    pageTitle="Visão geral de exemplos de barramento de serviço de mensagens | Microsoft Azure"
    description="Categoriza e descreve barramento de serviço exemplos com links para cada de mensagens."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/07/2016"
    ms.author="sethm" />

# <a name="service-bus-messaging-samples"></a>Exemplos de mensagens de barramento de serviço

Os exemplos de mensagens de barramento de serviço demonstram recursos principais do [barramento de serviço de mensagens](https://azure.microsoft.com/services/service-bus/) (serviço de nuvem) e [Barramento de serviço para o Windows Server](https://msdn.microsoft.com/library/dn282144.aspx). Este artigo categoriza e descreve os exemplos disponíveis, com links para cada.

>[AZURE.NOTE] Exemplos de barramento de serviço não são instalados com o SDK. Para obter as amostras, visite a [página de amostras de SDK do Azure](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
>
>Além disso, há um conjunto atualizado de barramento de serviço amostras de mensagens [aqui](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) (até o momento, eles não são descritos neste artigo).  

Para obter exemplos de retransmissão, consulte [exemplos de retransmissão de barramento de serviço](../service-bus-relay/service-bus-relay-samples.md).

## <a name="service-bus-messaging"></a>Barramento de serviço de mensagens

Os exemplos a seguir ilustram como escrever aplicativos que usam barramento de serviço de mensagens.

Observe que as amostras de mensagens exigem uma cadeia de conexão para acessar seu namespace barramento de serviço.

### <a name="to-obtain-a-connection-string-for-azure-service-bus"></a>Para obter uma cadeia de conexão para barramento de serviço do Azure

1. Faça logon no [portal do Azure](http://portal.azure.com).

1. Na coluna à esquerda, clique em **Barramento de serviço**.

1. Clique no nome do seu espaço para nome na lista.

3. Na lâmina namespace, clique em **políticas de acesso compartilhado**.

4. Na lâmina **as políticas de acesso compartilhado** , clique em **RootManageSharedAccessKey**.

6. Copie a cadeia de conexão para a área de transferência.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Para obter uma cadeia de conexão para barramento de serviço para o Windows Server

1. Execute o seguinte cmdlet do PowerShell:

    ```
    get-sbClientConfiguration
    ```

2. Cole a cadeia de conexão no arquivo App para o exemplo.

### <a name="getting-started-samples"></a>Obtendo amostras de Introdução

Esses exemplos descrevem funcionalidade básica de mensagens.

|Nome de amostra|Descrição|Versão mínima SDK|Disponibilidade|
|---|---|---|---|
|[Introdução: Mensagens com filas](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3)|Demonstra como usar barramento de serviço do Microsoft Azure para enviar e receber mensagens de uma fila.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Introdução: Mensagens com tópicos](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5)|Demonstra como usar barramento de serviço do Microsoft Azure para enviar e receber mensagens de um tópico com várias assinaturas.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|

### <a name="exploring-features"></a>Explorando os recursos

Os exemplos a seguir demonstram diversos recursos do barramento de serviço.

|Nome de amostra|Descrição|Versão mínima SDK|Disponibilidade|
|---|---|---|---|
|[Provedores de Token de HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5)|Demonstra as diferentes maneiras de autenticação de um cliente HTTP/REST com barramento de serviço.|2.1|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Cliente do serviço barramento HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a)|Demonstra como enviar mensagens para e receber mensagens de barramento de serviço por meio de HTTP/restante.|2.3|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Encaminhamento automático de barramento de serviço](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b)|Demonstra como encaminhar automaticamente mensagens de uma fila, assinatura ou fila de mensagens mortas em outra fila ou tópico. Ele também demonstra como enviar uma mensagem para uma fila ou tópico por meio de uma fila de transferência.|2.3|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Intermediário de mensagens: Exemplo de sessão de canal WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451)|Demonstra como usar o barramento de serviço do Microsoft Azure usando canais de Windows Communication Foundation (WCF). O exemplo mostra o uso de canais do WCF para enviar e receber mensagens por meio de uma fila de barramento de serviço. O exemplo mostra sessão e comunicação de sessão não sobre o barramento de serviço.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Orientado a mensagens: transações](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e)|Demonstra como usar o barramento de serviço do Microsoft Azure recursos dentro de um escopo de transação de mensagens para garantir a lotes de operações de mensagens são confirmadas atomicamente.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Comunicação de mensagens: Operações de gerenciamento usando REST](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88)|Demonstra como realizar operações de gerenciamento no barramento de serviço usando o restante.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Provedor de recursos APIs REST](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203)|Demonstra como usar as novas APIs de restante de RDFE de barramento de serviço para gerenciar namespaces e entidades mensagens.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Mensagens intermediário: Exemplo de sessão de serviço WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2)|Demonstra como usar o barramento de serviço do Microsoft Azure usando o modelo do serviço WCF. O exemplo mostra o uso do modelo de serviço WCF para realizar comunicação baseada em sessão por meio de uma fila de barramento de serviço.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Comunicação de mensagens: Resposta de solicitação](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8)|Demonstra como usar o barramento de serviço do Microsoft Azure e a funcionalidade de solicitação/resposta. O exemplo mostra clientes simples e servidores comunicar-se por meio de uma fila de barramento de serviço.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Intermediário de mensagens: fila de inatividade](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8)|Demonstra como usar barramento de serviço do Microsoft Azure e a funcionalidade de "fila de inatividade" mensagens. O exemplo mostra um remetente simple e receptor comunicar-se por meio de uma fila de barramento de serviço.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Mensagens intermediário: Adiada mensagens](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879)|Demonstra como usar o recurso de adiamento de mensagem do barramento de serviço do Microsoft Azure. O exemplo mostra um remetente simple e receptor comunicar-se por meio de uma fila de barramento de serviço.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Comunicação de mensagens: Mensagens de sessão](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4)|Demonstra como usar barramento de serviço do Microsoft Azure e a funcionalidade de sessão de mensagens. O exemplo mostra simples remetentes e receptores comunicar-se por meio de uma fila de barramento de serviço.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Comunicação de mensagens: Tópico de resposta de solicitação](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e)|Demonstra como implementar o padrão de solicitação/resposta usando assinaturas e tópicos de barramento de serviço do Microsoft Azure. O exemplo mostra clientes simples e servidores comunicar-se por meio de um tópico de barramento de serviço.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Comunicação de mensagens: Fila de resposta de solicitação](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf)|Demonstra como usar barramento de serviço do Microsoft Azure e a funcionalidade de solicitação/resposta. O exemplo mostra clientes simples e servidores comunicação via duas filas de barramento de serviço.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Intermediário de mensagens: Detecção de duplicidades](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25)|Demonstra como usar a detecção de mensagem duplicada barramento de serviço do Microsoft Azure com filas. Ele cria duas filas, uma com detecção de duplicidades habilitada e outros outra sem detecção de duplicidades.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Intermediário de mensagens: Mensagens de assíncrono](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74)|Demonstra como usar barramento de serviço do Microsoft Azure para enviar e receber mensagens de forma assíncrona de uma fila. Fila fornece desacoplada, assíncrona comunicação entre um remetente e qualquer número de receptores (aqui, um destinatário únicos).|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Mensagens intermediário: Filtros avançados](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)|Demonstra como usar o barramento de serviço do Microsoft Azure publicar/assinar filtros avançados. Ela cria um tópico e 3 assinaturas com definições de filtro diferente, envia mensagens para o tópico e recebe todas as mensagens de assinaturas.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Comunicação de mensagens: Busca de mensagens](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d)|Demonstra como usar o recurso de busca de mensagens de barramento de serviço do Microsoft Azure. Ele demonstra como usar o recurso de busca de mensagens após receber.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|

## <a name="service-bus-reference-tools"></a>Ferramentas de referência de barramento de serviço

Os exemplos a seguir demonstram vários outros recursos do serviço.

|Nome de amostra|Descrição|Versão mínima SDK|Disponibilidade|
|---|---|---|---|
|[Explorador de barramento de serviço](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)|O Explorador de barramento de serviço permite que os usuários se conectem a um namespace de serviço de barramento de serviço e gerencie entidades de mensagens de uma maneira fácil. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação e a capacidade de testar entidades mensagens e serviços de retransmissão.|1.8|Barramento de serviço do Microsoft Azure; Barramento de serviço para o Windows Server|
|[Autorização: SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93)|Este exemplo demonstra como criar e gerenciar identidades de serviço no Microsoft Azure Active Directory controle de acesso (também conhecido como o serviço de controle de acesso ou ACS) para uso com barramento de serviço.|N/D|Barramento de serviço do Microsoft Azure|

## <a name="next-steps"></a>Próximas etapas

Consulte os tópicos a seguir para uma visão geral conceitual do barramento de serviço.

- [Visão geral de mensagens do barramento de serviço](service-bus-messaging-overview.md)
- [Arquitetura de barramento de serviço](service-bus-architecture.md)
- [Fundamentos de barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
