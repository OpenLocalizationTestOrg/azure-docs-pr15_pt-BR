<properties 
    pageTitle="Exceções de retransmissão | Microsoft Azure"
    description="Lista de exceções de retransmissão e ações sugeridas."
    services="service-bus"
    documentationCenter="na"
    authors="jtaubensee"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="jotaub" />

# <a name="relay-exceptions"></a>Exceções de retransmissão

Este artigo lista algumas exceções geradas pelas APIs de retransmissão do Microsoft Azure. Esta referência está sujeita a alterações, portanto, verifique sempre se há atualizações.

## <a name="exception-categories"></a>Categorias de exceção

As APIs de retransmissão gerar exceções que podem se enquadram nas categorias a seguir, juntamente com a ação associada que você pode realizar para tentar corrigi-los.

1.  Usuário codificação erro ([System. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Ação geral: tentar corrigir o código antes de continuar.

2.  Erro de instalação/configuração ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx), [System. UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ação geral: revisar sua configuração e altere se necessário.

3.  Exceções temporárias ([Microsoft.ServiceBus.Messaging.MessagingException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx), [Microsoft.ServiceBus.Messaging.ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx)). Ação geral: Repetir a operação ou notificar os usuários.

4.  Outras exceções ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System. TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx), [Microsoft.ServiceBus.Messaging.SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx)). Ação geral: específicos para o tipo de exceção; Consulte a tabela na seção a seguir. 

## <a name="exception-types"></a>Tipos de exceção

A tabela a seguir lista mensagens tipos de exceção e suas causas e ação sugerida de anotações que você pode realizar.

| **Tipo de exceção**                                                                                                                                                                                                                                                                                | **Descrição/causa/exemplos**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | **Ação sugerida**                                                                                                                                                                                                                                                                                                                                                                                                          | **Observação sobre a repetição automática/imediata**                                                                                             |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)                                                                                                                                                                                                           | O servidor não respondeu para a operação solicitada dentro do tempo especificado, que é controlada pelo [OperationTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx). O servidor pode ter concluído a operação solicitada. Isso pode acontecer devido a rede ou outros atrasos de infraestrutura.                                                                                                                                                                                                                                                                   | Verificar o estado de sistema para consistência e tente novamente se necessário. Consulte [exceções de tempo limite](#timeoutexception).                                                                                                                                                                                                                                                                                                                                                           | Repetir pode ajudar em alguns casos; Adicione lógica de repetição ao código.                                                                      |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx)                                                                                                                                                                                         | A operação de usuário solicitado não é permitida no servidor ou serviço. Consulte a mensagem de exceção para obter detalhes. Por exemplo, [concluída](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) gerará essa exceção se a mensagem foi recebida no modo de **ReceiveAndDelete** .                                                                                                                                                                                                                                                                                                     | Verifique o código e a documentação. Certifica-se de que a operação solicitada é válida.                                                                                                                                                                                                                                                                                                                                         | Repetir não ajudará.                                                                                                          |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx)                                                                                                                                                                                       | Uma tentativa é feita para chamar uma operação em um objeto que já foi fechado, anulada ou descartado. Em casos raros, a transação ambiente já foi descartada.                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Verifique o código e verifique se que ele não invoca operações em um objeto descartado.                                                                                                                                                                                                                                                                                                                                          | Repetir não ajudará.                                                                                                          |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx)                                                                                                                                                                                     | O objeto [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) não foi possível adquirir um token, o token é inválido ou o token não contém as declarações necessárias para executar a operação.                                                                                                                                                                                                                                                                                                                                                                                                  | Verifique se que o provedor de token é criado com os valores corretos. Verifique a configuração do serviço do controle de acesso.                                                                                                                                                                                                                                                                                                   | Repetir pode ajudar em alguns casos; Adicione lógica de repetição ao código.                                                                      |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Um ou mais argumentos fornecidos ao método são inválidos.<br /> O URI fornecido ao [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) ou [criar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx) contém segment(s) de caminho.<br /> O esquema URI fornecido ao [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) ou [criar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx) é inválido. <br />O valor da propriedade é maior que 32KB. | Verifique o código de chamada e verifique se que os argumentos estão corretos.                                                                                                                                                                                                                                                                                                                                                           | Repetir não ajudará.                                                                                                          |
| [ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx)                                                                                                                                                                       | Serviço não é possível processar a solicitação neste momento.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Cliente pode esperar um período de tempo, e repita a operação.                                                                                                                                                                                                                                                                                                                                                           | Cliente pode tentar novamente após determinado intervalo. Se uma nova tentativa resulta em uma exceção diferente, marque comportamento repetir essa exceção. |
| [QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx)                                                                                                                                                                 | A entidade de mensagens atingiu seu tamanho máximo permitido.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Crie o espaço na entidade ao receber mensagens da entidade ou suas filas sub. Consulte [QuotaExceededException](#quotaexceededexception).                                                                                                                                                                                                                                                                                                                                      | Repetir pode ajudá-lo se mensagens foram removidas enquanto isso.                                                               |
| [MessageSizeExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesizeexceededexception.aspx)                                                                                                                                                     | Uma carga de mensagem excede o limite de 256K. Observe que o limite de k 256 é o tamanho total da mensagem, que pode incluir propriedades do sistema e qualquer sobrecarga de .NET.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Reduzir o tamanho da carga da mensagem e, em seguida, repita a operação.                                                                                                                                                                                                                                                                                                                                                         | Repetir não ajudará.                                                                                                          |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) indica que uma cota para uma entidade específica foi excedida.

Para retransmissão, essa exceção disposto a [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), indicando que o número máximo de ouvintes foi excedido para este ponto de extremidade. Isso é indicado no valor **MaximumListenersPerEndpoint** da mensagem de exceção.

## <a name="timeoutexception"></a>TimeoutException 

Uma [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação de iniciado pelo usuário está demorando mais do que o tempo limite da operação. 

Você deve verificar o valor da propriedade [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) , como atingir esse limite pode também causa uma [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Para retransmissão, você poderá receber exceções de tempo limite ao primeiro abrir uma conexão de remetente de retransmissão. Existem duas causas comuns para essa exceção:

1. O valor de [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) pode ser pequeno demais (mesmo uma fração de um segundo).
2. O listener(s) de retransmissão local pode não estar respondendo (ou pode encontrar problemas de regras de firewall proibindo os ouvintes do aceite novas conexões de cliente) e o valor de [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) é menor que cerca de 20 segundos.

Por exemplo:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10. The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Causas comuns

Existem duas causas comuns para este erro: um erro de serviço temporárias ou configuração incorreta.

1. **Configuração incorreta** 
    o tempo limite da operação pode ser pequeno demais para a condição operacional. O valor padrão para o tempo limite da operação no cliente do SDK é 60 segundos. Verifique para ver se seu código tem o valor definido como algo muito pequeno. Observe que a condição da rede e o uso da CPU pode afetar o tempo de uma determinada operação ser concluído, para que o tempo limite da operação não deve ser definido para um valor muito pequeno.

2. **Erro de serviço temporárias** 
    retransmissão às vezes pode sofrer atrasos processamento solicitações; Por exemplo, durante períodos de tráfego alto. Nesses casos, você pode repetir a operação após um atraso, até que a operação for bem-sucedida. Se a mesma operação ainda falhar após várias tentativas, visite o [site de status do serviço Azure](https://azure.microsoft.com/status/) para ver se há qualquer interrupções de serviço conhecido.

## <a name="next-steps"></a>Próximas etapas:

- [Retransmissão perguntas Frequentes](relay-faq.md)
- [Criar um namespace](relay-create-namespace-portal.md)
- [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Introdução ao nó](relay-hybrid-connections-node-get-started.md)