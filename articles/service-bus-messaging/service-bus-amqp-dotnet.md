<properties 
    pageTitle="Serviço barramento com .NET e AMQP 1.0 | Microsoft Azure"
    description="Usando o barramento de serviço do .NET com AMQP"
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
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="using-service-bus-from-net-with-amqp-10"></a>Usando o barramento de serviço do .NET com AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## <a name="downloading-the-service-bus-sdk"></a>Baixar o SDK do barramento de serviço

Suporte de AMQP 1.0 está disponível no serviço barramento SDK versão 2.1 ou posterior. Você pode garantir que você tem a versão mais recente baixando os bits de barramento de serviço do [NuGet][].

## <a name="configuring-net-applications-to-use-amqp-10"></a>Configurando aplicativos do .NET para usar AMQP 1.0

Por padrão, a biblioteca de cliente .NET de barramento de serviço se comunica com o serviço de barramento de serviço usando um protocolo de baseado em SOAP dedicado. Para usar AMQP 1.0 em vez de padrão protocolo requer configuração explícita na cadeia de conexão do barramento de serviço, conforme descrito na próxima seção. Diferente dessa alteração, o código do aplicativo permanece inalterado quando usando AMQP 1.0.

Na versão atual, há alguns recursos de API que não têm suporte ao usar AMQP. Esses recursos sem suporte são listados posteriormente na seção de [recursos sem suporte, restrições e diferenças de comportamento](#unsupported-features-restrictions-and-behavioral-differences). Algumas das definições de configuração avançada também têm um significado diferente ao usar AMQP.

### <a name="configuration-using-appconfig"></a>Configuração usando App

É recomendável para aplicativos para usar o arquivo de configuração de App para armazenar configurações. Para aplicativos de barramento de serviço, você pode usar o App para armazenar as configurações para o valor de barramento de serviço **ConnectionString** . Um exemplo de arquivo de App é da seguinte maneira:

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
        </appSettings>
    </configuration>

O valor da `Microsoft.ServiceBus.ConnectionString` configuração é a cadeia de conexão do barramento de serviço é usada para configurar a conexão para barramento de serviço. O formato é da seguinte maneira:

    Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

Onde `[namespace]` e `SharedAccessKey` são obtidos a partir do [portal do Azure][]. Para obter mais informações, consulte [Introdução ao filas de barramento de serviço][].

Ao usar AMQP, acrescentar a cadeia de conexão com `;TransportType=Amqp`. Esta notação informa a biblioteca de cliente para fazer sua conexão em barramento de serviço usando AMQP 1.0.

## <a name="message-serialization"></a>Serialização de mensagem

Ao usar o protocolo padrão, o comportamento de serialização padrão da biblioteca cliente .NET é usar o tipo de [DataContractSerializer][] para serializar uma instância de [BrokeredMessage][] para transporte entre a biblioteca de cliente e o serviço de barramento de serviço. Ao usar o modo de transporte AMQP, a biblioteca de cliente usa o sistema de tipo AMQP para serialização da [mensagem de comunicação][BrokeredMessage] em uma mensagem AMQP. Este serialização habilita a mensagem a ser recebida e interpretada por um aplicativo receptor potencialmente executando em uma plataforma diferente, por exemplo, um aplicativo de Java que usa a API JMS para acessar barramento de serviço.

Quando você construir uma instância de [BrokeredMessage][] , você pode fornecer um objeto .NET como um parâmetro para o construtor para servir como o corpo da mensagem. Para objetos que podem ser mapeados para tipos primitivos AMQP, corpo é serializado em tipos de dados AMQP. Se o objeto não pode ser mapeado diretamente em um tipo primitivo AMQP; ou seja, um tipo personalizado definido pelo aplicativo, e em seguida, o objeto é serializado usando o [DataContractSerializer][]e os bytes serializados são enviadas em uma mensagem de dados AMQP.

Para facilitar a interoperabilidade com clientes não .NET, use apenas os tipos de .NET que podem ser serializados diretamente em tipos AMQP para o corpo da mensagem. A tabela a seguir detalha esses tipos e o mapeamento correspondente para o sistema de tipo AMQP.

| Tipo de objeto do .NET corpo          | Tipo AMQP mapeadas                          | Tipo de seção de corpo AMQP                                                                                                                                    |
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| bool                           | booliano                                   | Valor AMQP                                                                                                                                                |
| byte                           | ubyte                                     | Valor AMQP                                                                                                                                                |
| UShort                         | UShort                                    | Valor AMQP                                                                                                                                                |
| uint                           | uint                                      | Valor AMQP                                                                                                                                                |
| ULong                          | ULong                                     | Valor AMQP                                                                                                                                                |
| SByte                          | byte                                      | Valor AMQP                                                                                                                                                |
| curto                          | curto                                     | Valor AMQP                                                                                                                                                |
| int                            | int                                       | Valor AMQP                                                                                                                                                |
| Longas                           | Longas                                      | Valor AMQP                                                                                                                                                |
| flutuação                          | flutuação                                     | Valor AMQP                                                                                                                                                |
| duplo                         | duplo                                    | Valor AMQP                                                                                                                                                |
| decimal                        | decimal128                                | Valor AMQP                                                                                                                                                |
| CARACT                           | CARACT                                      | Valor AMQP                                                                                                                                                |
| DateTime                       | carimbo de hora                                 | Valor AMQP                                                                                                                                                |
| GUID                           | UUID                                      | Valor AMQP                                                                                                                                                |
| byte]                         | binário                                    | Valor AMQP                                                                                                                                                |
| cadeia de caracteres                         | cadeia de caracteres                                    | Valor AMQP                                                                                                                                                |
| System.Collections.IList       | lista                                      | Valor AMQP: itens contidos na coleção só podem ser aqueles que são definidos nesta tabela.                                                             |
| Array                   | matriz                                     | Valor AMQP: itens contidos na coleção só podem ser aqueles que são definidos nesta tabela.                                                             |
| System.Collections.IDictionary | mapa                                       | Valor AMQP: itens contidos na coleção só podem ser aqueles que são definidos nesta tabela. Observação: somente as chaves de cadeia de caracteres são suportadas.                        |
| URI                            | Descrito a cadeia de caracteres (consulte a tabela a seguir) | Valor AMQP                                                                                                                                                |
| DateTimeOffset                 | Descrito longo (consulte a tabela a seguir)   | Valor AMQP                                                                                                                                                |
| Período de tempo                       | Descrito por tempo (veja a seguir)         | Valor AMQP                                                                                                                                                |
| Fluxo                         | binário                                    | Dados de AMQP (pode ser vários). As seções de dados contêm os bytes brutos lidos do objeto fluxo.                                                           |
| Outro objeto                   | binário                                    | Dados de AMQP (pode ser vários). Contém o binário serializado do objeto que usa o DataContractSerializer ou um serializador fornecido pelo aplicativo. |

| Tipo .NET      | AMQP mapeada descrito tipo                                                                                              | Anotações                   |
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| URI            | `<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>`                       | Uri.AbsoluteUri         |
| DateTimeOffset | `<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` | DateTimeOffset.UtcTicks |
| Período de tempo       | `<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> `              | TimeSpan.Ticks          |

## <a name="unsupported-features-restrictions-and-behavioral-differences"></a>Recursos sem suporte, restrições e diferenças de comportamento

Os seguintes recursos da API do serviço barramento .NET não são suportados no momento ao usar AMQP:

-   Transações

-   Enviar por destino de transferência

Também existem algumas pequenas diferenças no comportamento da API do serviço barramento .NET ao usar AMQP, em comparação com o protocolo padrão:

-   A propriedade [OperationTimeout][] será ignorada.

-   `MessageReceiver.Receive(TimeSpan.Zero)`é implementado como `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.

-   Concluir mensagens por tokens de bloqueio só pode ser feito pelos receptores de mensagem que inicialmente receberam as mensagens.

## <a name="controlling-amqp-protocol-settings"></a>Controlar as configurações de protocolo AMQP

As APIs .NET expõem várias configurações para controlar o comportamento do protocolo AMQP:

-   **MessageReceiver.PrefetchCount**: controla o crédito inicial aplicado a um link. O padrão é 0.

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**: controles o tamanho máximo de quadro AMQP oferecido durante a negociação em conexão abrir tempo. O padrão é 65.536 bytes.

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**: se transferências batchable, esse valor determina o atraso máximo para o envio de descartes. Herdadas por remetentes/receptores por padrão. Remetente/receptor individual pode substituir o padrão, que é 20 milissegundos.

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**: controla se conexões AMQP estão estabelecidas sobre uma conexão SSL. O padrão é **verdadeiro**.

## <a name="next-steps"></a>Próximas etapas

Pronto para saber mais? Visite os links a seguir:

- [Visão geral do serviço barramento AMQP]
- [Suporte a AMQP 1.0 tópicos e filas do barramento de serviço particionado]
- [AMQP no carregador de serviço para o Windows Server]

  [Introdução ao filas de barramento de serviço]: service-bus-dotnet-get-started-with-queues.md
  [DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[Portal do Azure]: https://portal.azure.com
[Visão geral do serviço barramento AMQP]: service-bus-amqp-overview.md
[Suporte a AMQP 1.0 tópicos e filas do barramento de serviço particionado]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP no carregador de serviço para o Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
