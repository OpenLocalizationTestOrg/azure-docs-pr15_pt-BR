<properties 
    pageTitle="Visão geral de amostras de retransmissão de barramento de serviço | Microsoft Azure"
    description="Categoriza e descreve exemplos de retransmissão de barramento de serviço com links para cada um."
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

# <a name="service-bus-relay-samples"></a>Exemplos de retransmissão de barramento de serviço

As amostras de retransmissão barramento de serviço demonstram recursos principais do [retransmissão de barramento de serviço](https://azure.microsoft.com/services/service-bus/). Este artigo categoriza e descreve os exemplos disponíveis, com links para cada.

>[AZURE.NOTE] Exemplos de barramento de serviço não são instalados com o SDK. Para obter as amostras, visite a [página de amostras de SDK do Azure](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
>
>Além disso, há um conjunto de amostras de retransmissão de barramento de serviço atualizado [aqui](https://github.com/Azure-Samples/azure-servicebus-relay-samples) (até o momento, eles não são descritos neste artigo).  

Para exemplos de mensagens, consulte [exemplos de mensagens de barramento de serviço](../service-bus-messaging/service-bus-samples.md).

## <a name="service-bus-relay"></a>Retransmissão de barramento de serviço

Os exemplos a seguir ilustram como escrever aplicativos que usam o serviço de retransmissão barramento de serviço.

Observe que as amostras de retransmissão exigem uma cadeia de conexão para acessar seu namespace barramento de serviço.

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

## <a name="service-bus-relay"></a>Retransmissão de barramento de serviço

Exemplos que demonstram o relay barramento de serviço.

### <a name="getting-started"></a>Guia de Introdução

|Nome de amostra|Descrição|Versão mínima SDK|Disponibilidade|
|---|---|---|---|
|[Enviadas mensagens: Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3)|Demonstra como executar um cliente de barramento de serviço e o serviço no Azure. Este exemplo configura o barramento de serviço por programação. Somente informações de segurança e de ambiente são armazenadas nos arquivos de configuração.|1.8|Barramento de serviço do Microsoft Azure|
|[Autenticação de mensagens retransmissão: Segredo compartilhado](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02)|Demonstra como usar um nome de emissor e segredo emissor para autenticar com barramento de serviço.|1.8|Barramento de serviço do Microsoft Azure|
|[Transmitidas autenticação de mensagens: WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831)|Demonstra como expor um serviço HTTP que não exige autenticação de usuário do cliente.|1.8|Barramento de serviço do Microsoft Azure|
|[Transmitidas ligações mensagens: WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0)|Demonstra como usar a ligação **WebHttpRelayBinding** para retornar dados binários usando o modelo de programação Web.|1.8|Barramento de serviço do Microsoft Azure|
|[Ligações retransmissão de mensagens: NetTcp enviada](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692)|Demonstra como usar a ligação **NetTcpRelayBinding** .|1.8|Barramento de serviço do Microsoft Azure|

### <a name="exploring-features"></a>Explorando os recursos

Exemplos que demonstram vários recursos de retransmissão de barramento de serviço.

|Nome de amostra|Descrição|Versão mínima SDK|Disponibilidade|
|---|---|---|---|
|[Autenticação retransmissão de mensagens: WebToken Simple](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392)|Demonstra como usar uma credencial de token de web simples para autenticar com barramento de serviço. O exemplo é semelhante da amostra de eco, com algumas alterações. Especificamente, este exemplo adiciona um comportamento nos aplicativos de ChannelFactory (cliente) e ServiceHost (serviço).|1.8|Barramento de serviço do Microsoft Azure|
|[Retransmissão de mensagens: Balanceamento de carga](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8)|Demonstra como usar o barramento de serviço do Microsoft Azure para rotear mensagens para vários destinatários. Mostra várias instâncias de um serviço simples se comunicar com um cliente via a ligação **NetTcpRelayBinding**|1.8|Barramento de serviço do Microsoft Azure|
|[Ligações retransmissão de mensagens: Evento líquido](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977)|Demonstra usando a ligação **NetEventRelayBinding** no barramento de serviço do Microsoft Azure.|1.8|Barramento de serviço do Microsoft Azure|
|[Ligações retransmissão de mensagens: Sessão de WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb)|Demonstra usando a ligação **WS2007HttpRelayBinding** com sessões confiáveis habilitadas. Ele também mostra como especificar credenciais de barramento de serviço no arquivo de configuração em vez de por programação.|1.8|Barramento de serviço do Microsoft Azure|
|[Ligações retransmissão de mensagens: MsgSecCertificate de WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5)|Demonstra como usar a ligação de **WS2007HttpRelayBinding** com segurança de mensagem para proteger mensagens de ponta a ponta exigindo ainda clientes autenticar com barramento de serviço.|1.8|Barramento de serviço do Microsoft Azure|
|[Retransmissão de mensagens: Troca de metadados](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e)|Demonstra como expor um ponto de extremidade de metadados que usa a vinculação de retransmissão. **MetadataExchange** é compatível com as seguintes ligações de retransmissão: **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding**e **WS2007HttpRelayBinding**.|1.8|Barramento de serviço do Microsoft Azure|
|[Ligações retransmissão de mensagens: Direta de NetTcp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161)|Demonstra como configurar a ligação **NetTcpRelayBinding** para o modo de conexão **híbrido** que primeiro estabelece uma conexão de retransmissão e se possível, mudará automaticamente para uma conexão direta entre um cliente e um serviço de suporte.|1.8|Barramento de serviço do Microsoft Azure|
|[Ligações retransmissão de mensagens: Nome de usuário de MsgSec de NetTcp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392)|Demonstra como usar a ligação **NetTcpRelayBinding** com segurança de mensagem.|1.8|Barramento de serviço do Microsoft Azure|
|[Ligações retransmissão de mensagens: Unidirecional líquido](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a)|Demonstra como expor e consumir um ponto de extremidade do serviço usando a ligação **NetOnewayRelayBinding** .|1.8|Barramento de serviço do Microsoft Azure|
|[Ligações retransmissão de mensagens: Simples de WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a)|Demonstra usando a ligação **WS2007HttpRelayBinding** . Ele demonstra um serviço simples que usa sem opções de segurança e não exigir que os clientes se autenticar.|1.8|Barramento de serviço do Microsoft Azure|

## <a name="next-steps"></a>Próximas etapas

Consulte os tópicos a seguir para uma visão geral conceitual do barramento de serviço.

- [Visão geral de retransmissão de barramento de serviço](service-bus-relay-overview.md)
- [Arquitetura de barramento de serviço](../service-bus-messaging/service-bus-architecture.md)
- [Fundamentos de barramento de serviço](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)