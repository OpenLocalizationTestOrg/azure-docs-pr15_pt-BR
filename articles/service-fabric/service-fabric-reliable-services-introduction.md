<properties
   pageTitle="Visão geral do modelo de programação de tecidos confiável serviço | Microsoft Azure"
   description="Saiba mais sobre o modelo de programação do serviço tecidos serviço confiável e começar a escrever seus próprios serviços."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="vturecek; mani-ramaswamy"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="masnider;vturecek"/>

# <a name="reliable-services-overview"></a>Visão geral de serviços confiável
Estrutura de serviço Azure simplifica escrita e gerenciamento de serviços confiáveis com e sem monitoração. Este documento será falar sobre:

- O modelo de programação serviços confiáveis dos serviços de com e sem monitoração.
- As opções que você precisará fazer ao escrever um serviço confiável.
- Alguns cenários e exemplos de quando usar serviços confiáveis e como eles são gravados.

Serviços confiáveis é um dos modelos de programação disponíveis em estrutura de serviço. Para obter mais informações sobre o modelo de programação de atores confiável, consulte [Introdução aos atores confiável do serviço tecidos](service-fabric-reliable-actors-introduction.md).

Em estrutura de serviço, um serviço é composto de configuração, código do aplicativo e (opcionalmente) estado.

Serviço tecidos gerencia o ciclo de vida dos serviços, de provisionamento e a implantação por meio de atualização e exclusão, por meio de [gerenciamento de aplicativos de serviço tecidos](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>O que são serviços confiáveis?
Serviços confiáveis fornece um modelo de programação simple, potente de nível superior para ajudá-lo a expressar o que é importante para seu aplicativo. Com os serviços confiável modelo de programação, você obtém:

- Para serviços de estado, o modelo de programação de serviços confiáveis permite maneira consistente e confiável armazenar seu direito de estado dentro do seu serviço usando coleções confiável. Este é um conjunto simples de classes coleção altamente disponível que serão familiares para qualquer pessoa que usou c# conjuntos. Normalmente, serviços necessários sistemas externos para gerenciamento de estado confiável. Com conjuntos de confiável, você pode armazenar seu estado ao lado de sua computação com a mesma alta disponibilidade e confiabilidade que você veio esperar de lojas externas altamente disponíveis e com as melhorias de latência adicional que fornecem conjunta Localizando a computação e o estado.

- Um modelo simples para executar seu próprio código que se parece com modelos que é usados para de programação. Seu código tem um ponto de entrada bem definido e facilmente gerenciado do ciclo de vida.

- Um modelo de comunicação conectável. Use o transporte de sua escolha, como HTTP com [API Web](service-fabric-reliable-services-communication-webapi.md), WebSocket, protocolos TCP personalizados, etc. Serviços confiáveis fornecem alguns ótimos-de-prontos opções que você pode usar, ou você pode fornecer o seu próprio.

## <a name="what-makes-reliable-services-different"></a>O que torna diferentes serviços confiáveis?
Serviços confiáveis no serviço tecidos é diferente de serviços que você pode ter escrito antes. Serviço tecidos fornece confiabilidade, disponibilidade, consistência e escalabilidade.  

- **Confiabilidade**– seu serviço permanecerá o mesmo em ambientes não confiáveis onde suas máquinas podem falhar ou acertar problemas de rede.

- **Disponibilidade**, seu serviço estará acessível e ágil. (Isso não significa que você não pode ter os serviços que não podem ser encontrados ou acessados a partir da fora.)

- **Escalabilidade**-- serviços são desassociados hardware específico e podem aumentar ou reduzir conforme necessário por meio da adição ou remoção de hardware ou recursos virtuais. Serviços facilmente são particionados (especialmente no caso com informações de estado) para garantir que as partes independentes do serviço podem dimensionar e responder a falhas de maneira independente. Por fim, serviço tecidos incentiva serviços a ser leves permitindo milhares de serviços para ser provisionado dentro de um processo simples, em vez de exigir ou dedicar instâncias de sistema operacional todos a uma única instância de uma carga de trabalho específica.

- Garantia de **consistência**– qualquer informação armazenada neste serviço consistente (isso se aplica somente aos serviços de estado - Saiba mais sobre isso posteriormente)

## <a name="service-lifecycle"></a>Ciclo de vida do serviço
Se seu serviço é com estado ou sem estado, serviços confiáveis fornecem um ciclo de vida simple que permite que você conecte-se rapidamente seu código e introdução.  Há apenas um ou dois métodos que você precisa implementar para acessar seu serviço em funcionamento.

- **CreateServiceReplicaListeners/CreateServiceInstanceListeners** - é onde o serviço define a pilha de comunicação que deseja usar. Pilha de comunicação, como [API da Web](service-fabric-reliable-services-communication-webapi.md), é o que define o ponto de extremidade de ouvinte ou pontos de extremidade do serviço (como clientes atingirá-lo). Ele também define como as mensagens que aparecem acabem interaja com o restante do código de serviço.

- **RunAsync** - é onde o seu serviço executa sua lógica comercial. O token de cancelamento é fornecido é um sinal de quando que funcionam deve parar. Por exemplo, se você tiver um serviço que precisa constantemente retirar mensagens fora uma fila confiável e processá-los, isso é onde que funcionam seria acontecer.

### <a name="service-startup"></a>Inicialização do serviço

Os principais eventos no ciclo de vida de um serviço confiável são:

1. O objeto de serviço (a coisa que deriva o serviço sem estado ou estado) é construído.

2. O `CreateServiceReplicaListeners` / `CreateServiceInstanceListeners` método é chamado, fazendo o serviço uma chance de retornar um ou mais listeners de comunicação de sua escolha.
  - Observe que isso é opcional, embora a maioria dos serviços irá expor algumas ponto de extremidade diretamente.

3. Depois que os ouvintes de comunicação são criados, ele é aberto.
  - Ouvintes de comunicação tem um método chamado `OpenAsync()`, que é chamado neste ponto e que retorna o endereço de ouvinte do serviço. Se seu serviço confiável usa um do ICommunicationListeners interno, isso é tratado para você.

4. Depois que o ouvinte de comunicação é aberto, o `RunAsync()` método no serviço principal é chamado.
  - Observe que `RunAsync()` é opcional. Se o serviço faz todo seu trabalho diretamente em resposta ao usuário apenas chamadas, não é necessário para que ele implementar `RunAsync()`.

### <a name="service-shutdown"></a>Desligamento de serviço

Quando o serviço está sendo desligado (a ser excluído, atualizada ou movido) a ordem de chamada é espelhada: primeiro, o token de cancelamento é mantido por `RunAsync()` será cancelado; em seguida, `CloseAsync()` é chamado nos ouvintes de comunicação.

Há algumas informações importantes sobre desligamento para serviços de estado:

- Serviço tecidos não serão promover outra réplica do seu serviço ao status principal até `CloseAsync` e `RunAsync` ter retornado. Se você estiver usando um ouvinte de comunicação interna, o `CloseAsync` método é tratado para você.

- Enquanto não há nenhum limite de tempo no retornando entre esses métodos, você imediatamente perder a capacidade de gravar para conjuntos de confiável e, portanto, não é possível concluir qualquer trabalho real. É recomendável que você retorne mais rápido possível ao receber a solicitação de cancelamento.

## <a name="example-services"></a>Serviços de exemplo
Este modelo de programação se souber, vamos dar uma olhada rápida dois serviços diferentes para ver como essas partes se encaixam.

### <a name="stateless-reliable-services"></a>Sem estado serviços confiáveis
Um serviço de estado é um onde não há literalmente nenhum estado mantido dentro do serviço ou o estado em que está presente é totalmente descartável e não exige sincronização, replicação, persistência ou alta disponibilidade.

Por exemplo, considere uma calculadora que não tem memória e recebe todos os termos e as operações a serem executadas ao mesmo tempo.

Nesse caso, o RunAsync() do serviço pode ser vazio, porque não há nenhum plano de fundo-processamento de tarefas que o serviço precisa fazer. Quando o serviço da calculadora é criado, ele retornará um ICommunicationListener (por exemplo [API da Web](service-fabric-reliable-services-communication-webapi.md)) que abre um ponto de extremidade ouvinte em alguma porta. Neste ponto de extremidade ouvinte irá se conectar para os diferentes métodos (exemplo: "Adicionar (n1, n2)") que definem API pública da Calculadora.

Quando uma chamada é feita de um cliente, o método apropriado é invocado e o serviço da Calculadora executa as operações nos dados fornecidas e retorna o resultado. Ele não armazenar qualquer estado.

Não armazenar qualquer estado interno torna esta Calculadora de exemplo muito simples. Mas a maioria dos serviços não são realmente sem estado. Em vez disso, eles Externalizar seu estado para alguns outro store. (Por exemplo, qualquer aplicativo web que depende de manter o estado de sessão em um armazenamento de backup ou o cache não está completamente sem estado.)

Um exemplo comum de como os serviços sem estado são usados no serviço tecidos é como um front-end que expõe a API público para um aplicativo web. O serviço de front-end, em seguida, conversa com serviços de estado para concluir uma solicitação de usuário. Nesse caso, chamadas de clientes são direcionadas para uma porta conhecida, como 80, onde o serviço de estado é listening. Esse serviço sem estado recebe a chamada e determina se a chamada é de um parceiro confiável, como bem como qual serviço-destinado.  Em seguida, o serviço de estado encaminha a chamada para a partição correta do serviço estado e aguarda uma resposta. Quando o serviço de estado recebe uma resposta, ele responde ao cliente original.

### <a name="stateful-reliable-services"></a>Estado serviços confiáveis
Um serviço de estado é aquele que deve ter uma parte do estado mantido consistentes e apresentar na ordem para o serviço de função. Considere a possibilidade de um serviço que constantemente computa uma média de algum valor com base em atualizações que ele recebe. Para fazer isso, ele deve ter o conjunto atual de solicitações de entrada necessárias para o processo, bem como a média atual. Qualquer serviço que recupera, processa e armazena informações em um repositório externo (como um Azure blob ou tabela store hoje) é com estado. Ele simplesmente mantém seu estado no armazenamento de estado externo.

A maioria dos serviços hoje armazenar seu estado externamente, desde que o armazenamento externo é o que fornece confiabilidade, disponibilidade, escalabilidade e consistência para esse estado. Em estrutura de serviço, serviços de estado não são necessários para armazenar seu estado externamente; Serviço tecidos cuida desses requisitos para o código de serviço e o estado do serviço.

Digamos que queremos escrever um serviço que recebe as solicitações de uma série de conversões que precisam ser realizadas em uma imagem e a imagem que precisa ser convertido.  Esse serviço, ela retornará um ouvinte de comunicação (vamos supor API da Web) que abre uma porta de comunicação e permite que envios por meio de uma API, como `ConvertImage(Image i, IList<Conversion> conversions)`. Nesta API, o serviço pode levar as informações e armazenar a solicitação em uma fila confiável e retorne alguns token do cliente para que ele pode controlar as a solicitação (desde que as solicitações podem demorar algum tempo).

Neste serviço, RunAsync pode ser mais complexo. O serviço pode ter um loop dentro de sua RunAsync que obtém solicitações fora IReliableQueue, executa as conversões listadas e armazena os resultados em IReliableDictionary, para que quando o cliente volta, eles possam acessar suas imagens convertidas. Para garantir que, mesmo se algo falha a imagem não é perdida, esse serviço confiável seria retirar fora fila, executar as conversões e armazenar o resultado em uma transação. Nesse caso, a mensagem é realmente removida apenas da fila e os resultados são armazenados no dicionário resultado quando as conversões são concluídas. Se algo falhar no meio (como a máquina que esta instância do código está em execução), a solicitação permanece na fila aguardando para serem processados novamente.

Observação sobre este serviço é que parece um serviço .NET normal. A única diferença é que as estruturas de dados que está sendo usadas (IReliableQueue e IReliableDictionary) são fornecidas pelo serviço tecidos e, portanto, são feitas altamente confiáveis, disponíveis e consistentes.

## <a name="when-to-use-reliable-services-apis"></a>Quando usar APIs de serviços confiável
Se qualquer um dos seguintes caracterizar suas necessidades de serviço do aplicativo, você deve considerar confiável APIs de serviços:

- Você precisa fornecer o comportamento do aplicativo em várias unidades de estado (por exemplo, pedidos e itens de linha do pedido).

- Estado do seu aplicativo pode ser modelado naturalmente como dicionários confiável e filas.

- Seu estado precisa ser altamente disponível com acesso de baixa latência.

- Seu aplicativo precisa controlar a concorrência ou detalhamento de operações realizadas em um ou mais conjuntos confiável.

- Você deseja gerenciar as comunicações ou controlar o esquema de partição de seu serviço.

- Seu código precisa de um ambiente de tempo de execução de segmentação livre.

- Seu aplicativo precisa criar dinamicamente ou destroy dicionários confiável ou filas em tempo de execução.

- Você precisa programaticamente controlar fornecido tecidos de serviço de backup e restaurar recursos para o estado * seu serviço.

- Seu aplicativo precisa manter histórico de alterações de suas unidades de estado *.

- Você deseja desenvolver ou consumir estado personalizado desenvolvidos de festa de terceiro provedores *.

> [AZURE.NOTE] * Recursos disponíveis em disponibilidade geral do SDK.


## <a name="next-steps"></a>Próximas etapas
+ [Início rápido de serviços confiável](service-fabric-reliable-services-quick-start.md)
+ [Uso avançado de serviços confiáveis](service-fabric-reliable-services-advanced-usage.md)
+ [O modelo de programação de atores confiável](service-fabric-reliable-actors-introduction.md)
