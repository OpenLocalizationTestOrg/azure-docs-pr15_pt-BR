<properties 
    pageTitle="Visão geral do modelo de segurança e autenticação de Hubs de evento | Microsoft Azure"
    description="Evento Hubs autenticação e segurança visão geral do modelo."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/16/2016"
    ms.author="sethm;clemensv" />

# <a name="event-hubs-authentication-and-security-model-overview"></a>Evento Hubs autenticação e segurança visão geral do modelo

O modelo de segurança do evento Hubs atende aos seguintes requisitos:

- Somente dispositivos que apresentam credenciais válidas podem enviar dados a um Hub de evento.
- Um dispositivo não pode representar outro dispositivo.
- Um dispositivo não autorizado pode ser bloqueado enviar dados a um Hub de evento.

## <a name="device-authentication"></a>Autenticação de dispositivo

O modelo de segurança do evento Hubs baseia-se em uma combinação de tokens de [Assinatura de acesso compartilhado (SAS)](../service-bus-messaging/service-bus-shared-access-signature-authentication.md) e *fornecedores de evento*. Um fornecedor de evento define um ponto de extremidade virtual para um Hub de evento. O publisher só pode ser usado para enviar mensagens a um Hub de evento. Não é possível receber mensagens de um fornecedor.

Normalmente, um Hub de evento utiliza um editor por dispositivo. Todas as mensagens enviadas para qualquer um dos editores de um Hub de evento são colocados em que o evento Hub. Fornecedores habilitar o controle de acesso refinadas e a otimização.

Cada dispositivo é atribuído um símbolo exclusivo, que é carregado no dispositivo. Os tokens são produzidos, de forma que cada token exclusivo concede acesso a um fornecedor exclusivo diferente. Um dispositivo que apresenta um token só pode enviar um editor, mas não outro publisher. Se vários dispositivos compartilharem o mesmo token, cada um desses dispositivos compartilha um fornecedor.

Embora não seja recomendado, é possível equipamentos dispositivos com símbolos que conceder acesso direto a um Hub de evento. Qualquer dispositivo que detém isso token pode enviar mensagens diretamente no Hub desse evento. Tal dispositivo não estarão sujeitas a otimização. Além disso, o dispositivo não pode ser na lista negra de enviar para que o evento Hub.

Todos os tokens são assinados com uma chave SAS. Normalmente, todos os tokens são assinados com a mesma chave. Dispositivos não estão cientes da chave; Isso impede que dispositivos tokens de fabricação.

### <a name="create-the-sas-key"></a>Criar a chave SAS

Ao criar um namespace Hubs de evento, Hubs de evento do Azure gera uma chave SAS de 256 bits chamada **RootManageSharedAccessKey**. Esta chave concede envia, ouvir e gerenciar direitos ao namespace. Você pode criar chaves adicionais. É recomendável que você produzir uma chave que concede envia permissões para o Hub de evento específico. Para o restante deste tópico, pressupõe-se que você nomeado esta tecla `EventHubSendKey`.

O exemplo a seguir cria uma chave somente enviar ao criar o Hub de evento:

```
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event Hub with a SAS rule that enables sending to that Event Hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Gerar tokens

Você pode gerar tokens usando a tecla SAS. Você deve produzir apenas um token por dispositivo. Em seguida, podem ser produzidos tokens usando o seguinte método. Todos os tokens são gerados usando a tecla **EventHubSendKey** . Cada token é atribuído um URI exclusivo.

```
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Ao chamar esse método, o URI deve ser especificado como `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Para todos os símbolos, o URI é idêntico, com exceção do `PUBLISHER_NAME`, que devem ser diferentes para cada símbolo. Ideal seria `PUBLISHER_NAME` representa o ID do dispositivo que recebe esse token.

Este método gera um token com a seguinte estrutura:

```
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

O tempo de expiração do token é especificado em segundos desde 1º de janeiro de 1970. Este é um exemplo de um token:

```
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Geralmente, os tokens têm um período de vida que lembra ou excede o tempo de vida do dispositivo. Se o dispositivo tem a capacidade de obter um novo símbolo, tokens com o período de vida mais curto podem ser usados.

### <a name="devices-sending-data"></a>Enviar dados de dispositivos

Quando os tokens tiveram sido criados, cada dispositivo está provisionado com seu próprio token exclusivo.

Quando o dispositivo envia dados a um Hub de evento, o dispositivo marcas seu token com a solicitação de envio. Para impedir que um invasor espionagem e roube o token, a comunicação entre o dispositivo e o Hub de evento deve ocorrer por um canal criptografado.

### <a name="blacklisting-devices"></a>Lista de dispositivos de bloqueio

Se um token for roubado por um invasor, o invasor pode representar o dispositivo cujo token tenha sido roubado. Um dispositivo da lista de bloqueio renderiza o dispositivo inutilizável até que o dispositivo é fornecido um novo token que usa um fornecedor diferente.

## <a name="authentication-of-back-end-applications"></a>Autenticação de aplicativos back-end

Para autenticar aplicativos de back-end que consumir os dados gerados por dispositivos, Hubs de evento utiliza um modelo de segurança que seja parecido com o modelo que é usado para obter tópicos barramento de serviço. Um grupo de consumidor do evento Hubs é equivalente a uma assinatura a um tópico de barramento de serviço. Um cliente pode criar um grupo de consumidor se a solicitação para criar o grupo de consumidor for acompanhada um token que concede gerenciar os privilégios para o Hub de evento, ou para o namespace ao qual pertence o Hub de evento. Um cliente tem permissão para consumir dados de um grupo de consumidor se a solicitação de recebimento é acompanhada por um símbolo que concede direitos de recebimento no grupo consumidor, no Hub do evento ou namespace ao qual pertence o Hub de evento.

A versão atual do barramento de serviço não suporta regras SAS para assinaturas individuais. O mesmo se aplica para os grupos de consumidor Hubs de evento. Suporte a SAS será adicionado para ambos os recursos no futuro.

Na ausência de autenticação de SAS para os grupos de consumidor individual, você pode usar teclas SAS para proteger todos os grupos de consumidor com uma chave comum. Essa abordagem habilita um aplicativo para consumir dados de qualquer um dos grupos consumidor de um Hub de evento.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o evento Hubs, visite os seguintes tópicos:

- [Visão geral de Hubs de evento]
- Um [enfileirados solução mensagens] usando filas de barramento de serviço.
- Um [aplicativo de exemplo que usa o evento Hubs]completo.

[Visão geral de Hubs de evento]: event-hubs-overview.md
[aplicativo de exemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[enfileirados solução de mensagens]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 
