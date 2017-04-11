<properties 
    pageTitle="Azure filas e barramento de serviço filas - comparado e comparados | Microsoft Azure"
    description="Analisa diferenças e semelhanças entre dois tipos de filas oferecidas pelo Azure."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="09/23/2016"
    ms.author="sethm" />

# <a name="azure-queues-and-service-bus-queues---compared-and-contrasted"></a>Azure filas e barramento de serviço filas - comparado e comparados

Este artigo analisa as diferenças e semelhanças entre os dois tipos de filas oferecidos pela Microsoft Azure hoje: filas filas do Azure e barramento de serviço. Usando essas informações, você pode comparar as respectivas tecnologias de contraste e poderá fazer uma melhor decisão sobre qual solução melhor atenda às suas necessidades.

## <a name="introduction"></a>Introdução

Microsoft Azure aceita dois tipos de mecanismos de fila: **Filas do Azure** e **serviço barramento**.

**Filas do azure**, que fazem parte da infraestrutura do [armazenamento do Azure](https://azure.microsoft.com/services/storage/) , apresentam uma interface baseada em REST Get/colocar/espiada simples, fornecendo mensagens confiáveis e persistentes dentro e entre os serviços.

**Filas de barramento de serviço** são parte de uma infraestrutura mais ampla [Azure mensagens](https://azure.microsoft.com/services/service-bus/) que ofereça suporte fila bem como publicar/assinar, remota de serviço Web e padrões de integração. Para obter mais informações sobre filas de barramento de serviço, tópicos/assinaturas e retransmissões, consulte [Visão geral do barramento de serviço de mensagens](service-bus-messaging-overview.md).

Embora ambas as tecnologias filas existam simultaneamente, filas do Azure foram introduzidas primeiro, como um mecanismo de armazenamento de fila dedicado integrado sobre os serviços de armazenamento do Azure. Filas de barramento de serviço são criadas sobre a infraestrutura de "orientado mensagens" mais ampla projetada para integrar aplicativos ou componentes de aplicativo que podem abranger vários protocolos de comunicação, contratos de dados, confiar em domínios e/ou ambientes de rede.

Este artigo compara as tecnologias de dois fila oferecidas pelo Azure discutindo as diferenças no comportamento e na implementação dos recursos fornecidos por cada. O artigo também fornece orientação para escolher quais recursos melhor podem atender às suas necessidades de desenvolvimento do aplicativo.

## <a name="technology-selection-considerations"></a>Considerações de seleção de tecnologia

Filas filas do Azure e barramento de serviço são implementações da mensagem fila serviço atualmente oferecido no Microsoft Azure. Cada tem um conjunto de recursos ligeiramente diferentes, o que significa que você pode escolher um ou outro, ou usar ambas, dependendo as necessidades de sua solução específica ou um problema de negócios/técnicos que está resolvendo.

Ao determinar qual tecnologia fila caiba a finalidade de uma determinada solução, desenvolvedores e arquitetos de solução devem considerar as recomendações abaixo. Para obter mais detalhes, consulte a próxima seção.

Como uma arquitetura de solução/desenvolvedor, **você deve considerar o uso do Azure filas** quando:

- Seu aplicativo deve armazenar mais de 80 GB de mensagens em uma fila, onde as mensagens têm um tempo de vida menos de 7 dias.

- Seu aplicativo deseja controlar o progresso de processamento de uma mensagem dentro de fila. Isso é útil se trava o trabalhador processamento de uma mensagem. Um trabalhador subsequente pode usar essas informações para continuar de onde parou o trabalhador anterior.

- Exigir logs do lado do servidor de todas as transações executadas em seu filas.

Como uma arquitetura de solução/desenvolvedor, **considere usar filas de barramento de serviço** quando:

- Sua solução deve ser capaz de receber mensagens sem precisar poll fila. Com o barramento de serviço, isso pode ser obtido por meio do uso a sondagem longa receber operação usando os protocolos baseados em TCP que suporta barramento de serviço.

- Sua solução requer a fila para fornecer um garantido primeiro-na-primeira-out (FIFO) ordenadas entrega.

- Você quer uma experiência simétrica no Azure e no Windows Server (nuvem privada). Para obter mais informações, consulte [Barramento de serviço para o Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).

- Sua solução deve ser capaz de oferecer suporte a detecção automática de duplicados.

- Você deseja que o aplicativo para processar mensagens como paralelos fluxos de execução longa (mensagens são associadas com um fluxo usando a propriedade [SessionId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) na mensagem). Nesse modelo, cada nó no aplicativo consumindo concorrente para fluxos, em vez de mensagens. Quando é determinado um fluxo de um nó consumindo, o nó pode examinar o estado do estado do fluxo de aplicativo usando transações.

- Sua solução requer o comportamento de transações e atomicidade ao enviar ou receber várias mensagens de uma fila.

- A característica de (TTL) para vida da carga de trabalho específicos do aplicativo pode exceder o período de 7 dias.

- Seu aplicativo trata as mensagens que podem exceder 64 KB, mas limita abordagem provavelmente não 256 KB.

- Você lida com um requisito para fornecer um modelo de acesso baseado em função à filas e direitos/permissões diferentes para remetentes e receptores.

- O tamanho da sua fila não crescerá maior que 80 GB.

- Você deseja usar o protocolo de mensagens baseada em padrões AMQP 1.0. Para saber mais sobre AMQP, consulte [Visão geral do serviço barramento AMQP](./service-bus-amqp-overview.md).

- Você pode imagina uma migração eventual de comunicação ponto a ponto baseada em fila para um padrão de troca de mensagem que permite a integração perfeita de receptores adicionais (assinantes), cada um deles recebe cópias independentes de algumas ou todas as mensagens enviadas para a fila. O último refere-se ao recurso de publicação/assinatura nativamente fornecido pelo barramento de serviço.

- Sua solução de mensagens deve ser capaz de oferecer suporte a garantia de entrega "De maioria-uma vez" sem a necessidade de criar os componentes de infraestrutura adicional.

- Você gostaria de poderá publicar e consumir lotes de mensagens.

- Exigir integração completa com a pilha de comunicação do Windows Communication Foundation (WCF) no .NET Framework.

## <a name="comparing-azure-queues-and-service-bus-queues"></a>Comparando filas filas do Azure e barramento de serviço

As tabelas nas seções a seguir fornecem um agrupamento lógico de recursos de fila e permitem que você compare, num relance, os recursos disponíveis no tanto filas do Azure e barramento de serviço.

## <a name="foundational-capabilities"></a>Recursos básicos

Esta seção compara alguns dos recursos filas fundamentais fornecidos pelo filas filas do Azure e barramento de serviço.

|Critérios de comparação|Filas Azure|Filas do barramento de serviço|
|---|---|---|
|Ordenação garantia|**Não** <br/><br>Para obter mais informações, consulte a Observação primeira na seção "Informações adicionais".</br>|**Sim - primeiro-na-primeira-Out (FIFO)**<br/><br>(por meio do uso de mensagens sessões)|
|Garantia de entrega|**Ao menos uma**|**Ao menos uma**<br/><br/>**De maioria-uma vez**|
|Suporte a operação atômica|**Não**|**Sim**<br/><br/>|
|Comportamento de recebimento|**Sem bloqueios**<br/><br/>(é concluída imediatamente se nenhuma nova mensagem for encontrada)|**Bloqueio com/sem o tempo limite**<br/><br/>(ofertas longo sondagem ou a ["Técnica Comet"](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Sem bloqueios**<br/><br/>(por meio do uso .NET API gerenciada somente)|
|API de estilo de envio|**Não**|**Sim**<br/><br/>[OnMessage](https://msdn.microsoft.com/library/azure/jj908682.aspx) e **OnMessage** sessões API .NET.|
|Modo de recebimento|**Espiada & concessão**|**Espiada & Bloquear**<br/><br/>**Excluir & receber**|
|Modo de acesso exclusivo|**Baseada em concessão**|**Baseada em bloquear**|
|Concessão/bloqueio duração|**30 segundos (padrão)**<br/><br/>**7 dias (máximos)** (Você pode renovar ou liberar uma concessão de mensagem usando o [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) API).|**60 segundos (padrão)**<br/><br/>Você pode renovar um bloqueio de mensagem usando o [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) API.|
|Precisão de concessão/bloqueio|**Nível de mensagem**<br/><br/>(cada mensagem pode ter um valor de tempo limite diferente, que você pode atualizar conforme necessário ao processar a mensagem, usando o [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) API)|**Nível de fila**<br/><br/>(cada fila tem uma precisão de bloqueio aplicada a todas as suas mensagens, mas você pode renovar o bloqueio usando o [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) API.)|
|Em lote receber|**Sim**<br/><br/>(especificando explicitamente contagem da mensagem ao recuperar mensagens, até no máximo 32 mensagens)|**Sim**<br/><br/>(implicitamente habilitando uma propriedade de busca de pré-lançamento ou explicitamente por meio do uso transações)|
|Enviar em lote|**Não**|**Sim**<br/><br/>(por meio do uso transações ou cliente em lotes)|

### <a name="additional-information"></a>Informações adicionais

- Mensagens em filas do Azure são normalmente primeiro-na-primeira-out, mas, às vezes, eles podem ser desordenados; Por exemplo, quando duração do tempo limite de visibilidade da mensagem expira (por exemplo, como resultado de um aplicativo cliente está travando durante o processamento). Quando o tempo limite de visibilidade expira, a mensagem fica visível novamente na fila para outro trabalhador retirá-lo. Neste ponto, a mensagem recentemente visível pode ser colocada na fila (a ser desenfileirado novamente) após uma mensagem que foi originalmente enfileirados depois dela.

- Se você já está usando Blobs de armazenamento do Azure ou tabelas e começar a usar filas, há garantia 99,9% de disponibilidade. Se você usar Blobs ou tabelas com filas de barramento de serviço, você terá menor disponibilidade.

- O padrão de fila garantido em filas de barramento de serviço requer o uso de sessões de mensagens. Se o aplicativo trava durante o processamento de uma mensagem recebida no modo **espiada & Bloquear** , na próxima vez em que um receptor de fila aceita uma sessão de mensagens, ele será iniciado com a mensagem falhou após seu time to live (TTL) período.

- Azure filas foram projetadas para dar suporte a cenários de filas padrão, como tornar componentes de aplicativo para aumentar a escalabilidade e tolerância de falhas, carregar nivelamento e criar fluxos de trabalho do processo.

- Filas de barramento de serviço oferecem suporte a garantia de entrega *Ao menos uma* . Além disso, *De maioria-uma vez* semântico pode ser suportados usando o estado da sessão para armazenar o estado do aplicativo e usando transações atomicamente recebam mensagens e atualize o estado da sessão.

- Filas Azure fornecem um modelo de programação uniforme e consistente em filas, tabelas e BLOBs – para os desenvolvedores e para as equipes de operações.

- Filas de barramento de serviço oferecem suporte para transações locais no contexto de uma única fila.

- O modo de **receber e excluir** suportado pelo barramento de serviço oferece a capacidade de reduzir a contagem de operação de mensagens (e o custo associado) em troca de garantia de entrega reduzido.

- Filas Azure fornecem concessões com a capacidade de estender as concessões de mensagens. Isso permite que os trabalhadores para manter concessões curtas nas mensagens. Portanto, se um trabalhador falhar, a mensagem pode ser rapidamente processada novamente por outro funcionário. Além disso, um trabalhador pode estender a concessão em uma mensagem se precisar processar mais do que o tempo de concessão atual.

- Filas Azure oferecem um limite de visibilidade que você pode definir após a enqueueing ou desenfileiramento de uma mensagem. Além disso, você pode atualizar uma mensagem com valores de concessão diferente em tempo de execução e atualizar valores diferentes em mensagens na fila do mesma. Tempos limite de bloqueio de barramento de serviço é definido nos metadados fila; No entanto, você pode renovar o bloqueio chamando o método [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) .

- O tempo limite máximo de bloqueio de uma operação de recebimento em filas de barramento de serviço é 24 dias. Entretanto, tempos limite baseada em REST têm um valor máximo de 55 segundos.

- No lado do cliente em lotes fornecidos pelo barramento de serviço permite que um cliente fila lote várias mensagens em uma operação de envio único. Processamento em lotes está disponível somente para operações de envio assíncrona.

- Recursos como o teto 200 TB do Azure filas (mais quando sua virtualização contas) e ilimitado fazer a ele uma plataforma ideal para provedores SaaS.

- Filas Azure fornecem um flexível e desempenho delegada mecanismo de controle de acesso.

## <a name="advanced-capabilities"></a>Recursos avançados

Esta seção compara recursos avançados fornecidos pelo filas filas do Azure e barramento de serviço.

|Critérios de comparação|Filas Azure|Filas do barramento de serviço|
|---|---|---|
|Entrega agendada|**Sim**|**Sim**|
|Letras inativa automática|**Não**|**Sim**|
|Aumentar o valor de tempo de vida de fila|**Sim**<br/><br/>(via atualização in-loco de tempo limite de visibilidade)|**Sim**<br/><br/>(fornecido por meio de uma função da API dedicada)|
|Suporte a mensagens de suspeitas|**Sim**|**Sim**|
|Atualização in-loco|**Sim**|**Sim**|
|Log de transação do servidor|**Sim**|**Não**|
|Métricas de armazenamento|**Sim**<br/><br/>**Métricas de minuto**: fornece métricas em tempo real para API de disponibilidade, TPS, chamam contagens, contagens de erros e mais, tudo em tempo real (agregado por minuto e relatado em poucos minutos do que aconteceu acabou em produção. Para obter mais informações, consulte [Sobre métricas de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343258.aspx).|**Sim**<br/><br/>(consultas em massa chamando [GetQueues](https://msdn.microsoft.com/library/azure/hh293128.aspx))|
|Gerenciamento de estado|**Não**|**Sim**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx)|
|Autoencaminhamento de mensagens|**Não**|**Sim**|
|Limpar a função de fila|**Sim**|**Não**|
|Grupos de mensagem|**Não**|**Sim**<br/><br/>(por meio do uso de mensagens sessões)|
|Estado do aplicativo por grupo de mensagem|**Não**|**Sim**|
|Detecção de duplicidades|**Não**|**Sim**<br/><br/>(configurável no lado do remetente)|
|Integração do WCF|**Não**|**Sim**<br/><br/>(oferece ligações do WCF-de-prontos)|
|Integração de WF|**Personalizado**<br/><br/>(exige a criação de uma atividade do WF personalizada)|**Nativo**<br/><br/>(oferece atividades do WF-de-prontos)|
|Grupos de mensagem de navegação|**Não**|**Sim**|
|Buscar sessões de mensagens por ID|**Não**|**Sim**|

### <a name="additional-information"></a>Informações adicionais

- Ambas as tecnologias filas habilitar uma mensagem ser agendado para entrega posteriormente.

- Fila encaminhamento automático permite milhares de filas para encaminhar automaticamente suas mensagens para uma única fila, do qual o aplicativo de recebimento consome a mensagem. Você pode usar esse mecanismo para obter segurança, fluxo de controle e isolar armazenamento entre cada editor de mensagem.

- Filas Azure oferecem suporte para atualizar o conteúdo da mensagem. Você pode usar essa funcionalidade para manter informações de estado e atualizações de andamento incremental na mensagem para que ela pode ser processada da última verificação conhecida, em vez de começar do zero. Com filas de barramento de serviço, você pode habilitar o mesmo cenário por meio do uso sessões de mensagens. Sessões permitem que você salvar e recuperar o estado de processamento de aplicativo (usando [SetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx) e [GetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx)).

- [Letras inativa](service-bus-dead-letter-queues.md), que é suportado apenas por filas de barramento de serviço, pode ser útil para isolar mensagens que não podem ser processadas com êxito pelo aplicativo de recebimento ou quando mensagens não consegue chegar a seu destino devido a uma propriedade de (TTL) expirada time to live. O valor TTL especifica quanto tempo uma mensagem permanece na fila. Com o barramento de serviço, a mensagem será movida para uma fila especial chamada $DeadLetterQueue quando o período TTL expira.

- Para localizar mensagens "suspeitas" em filas do Azure, quando desenfileiramento de uma mensagem o aplicativo examina a propriedade **[DequeueCount](https://msdn.microsoft.com/library/azure/dd179474.aspx)** da mensagem. Se **DequeueCount** estiver acima de um determinado limite, o aplicativo move a mensagem para uma aplicativo definido "" fila de inatividade.

- Azure filas permitem que você obter um log detalhado de todas as transações executadas em fila, como bem como agregadas métricas. Ambas as opções são úteis para depuração e Noções básicas sobre como o aplicativo usa filas do Azure. Eles também são úteis para seu aplicativo de ajuste de desempenho e reduzindo os custos de usando filas.

- O conceito de "sessões de mensagens" suportados pelo serviço barramento permite que mensagens que fazem parte de um determinado grupo lógico a ser associado um determinado receptor, que por sua vez cria uma afinidade de sessão semelhante entre mensagens e seus respectivos receptores. Você pode habilitar essa funcionalidade avançada no serviço carregador definindo a propriedade de [identificação de sessão](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) em uma mensagem. Receptores podem ouvir em uma ID de sessão específica e receber mensagens que compartilham o identificador de sessão especificada.

- A funcionalidade de detecção de duplicação suportada por filas de barramento de serviço automaticamente remove duplicadas mensagens enviadas para uma fila ou tópico, com base no valor da propriedade [MessageID](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) .

## <a name="capacity-and-quotas"></a>Capacidade e cotas

Esta seção compara filas filas do Azure e barramento de serviço sob a perspectiva de [capacidade e cotas](service-bus-quotas.md) que podem se aplicar.

|Critérios de comparação|Filas Azure|Filas do barramento de serviço|
|---|---|---|
|Tamanho máximo da fila|**200 TB**<br/><br/>(limitado para a capacidade de uma conta de armazenamento único)|**1 GB para 80 GB**<br/><br/>(definido após a criação de uma fila e [Habilitando partição](service-bus-partitioning.md) – consulte a seção "Informações adicionais")|
|Tamanho máximo de mensagem|**64 KB**<br/><br/>(48 KB quando usando codificação **base 64** )<br/><br/>Azure suporta mensagens grandes combinando filas e blobs – ponto em que você pode fila até 200GB para um único item.|**256 KB** ou **1 MB**<br/><br/>(incluindo o cabeçalho e o corpo, tamanho máximo do cabeçalho: 64 KB).<br/><br/>Depende do [nível de serviço](service-bus-premium-messaging.md).|
|TTL máximo de mensagem|**7 dias**|**`TimeSpan.Max`**|
|Número máximo de filas|**Ilimitado**|**10.000**<br/><br/>(por namespace de serviço, pode ser aumentado)|
|Número máximo de clientes simultâneos|**Ilimitado**|**Ilimitado**<br/><br/>(limite de conexão simultânea 100 só se aplica a comunicação de baseada em protocolo TCP)|

### <a name="additional-information"></a>Informações adicionais

- Barramento de serviço impõe limites de tamanho de fila. O tamanho máximo da fila é especificado na criação da fila e pode ter um valor entre 1 e 80 GB. Se o valor de tamanho de fila definido na criação da fila é alcançado, as mensagens de entrada adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. Para obter mais informações sobre cotas no carregador de serviço, consulte [Cotas de barramento de serviço](service-bus-quotas.md).

- Você pode criar filas de barramento de serviço em tamanhos de 1, 2, 3, 4 ou 5 GB (o padrão é 1 GB). Com partição habilitado (que é o padrão), barramento de serviço cria 16 partições para cada GB que você especificar. Assim, se você criar uma fila que é 5 GB de tamanho, com 16 partições o tamanho máximo da fila se torna (5 * 16) = 80 GB. Você pode ver o tamanho máximo da fila particionada ou tópico examinando sua entrada no [portal do Azure][].

- Com filas do Azure, se o conteúdo da mensagem não for seguro XML, ele deverá ser **na Base 64** codificado. Se você **na Base 64**-codificar a mensagem, a carga de usuário pode ser até 48 KB, em vez de 64 KB.

- Com filas de barramento de serviço, cada mensagem armazenada em uma fila é composta por duas partes: um cabeçalho e um corpo. O tamanho total da mensagem não pode exceder o tamanho máximo de mensagem compatível com o nível de serviço.

- Quando os clientes se comunicar com filas de barramento de serviço através do protocolo TCP, o número máximo de conexões simultâneas para uma única fila de barramento de serviço está limitado a 100. Esse número é compartilhado entre remetentes e receptores. Se essa cota for atingida, as solicitações subsequentes para conexões adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. Esse limite não é imposto sobre clientes conectando-se às filas usando API baseada em REST.

- Se você precisar de mais de 10.000 filas em um único namespace de barramento de serviço, você pode contatar a equipe de suporte do Azure e solicitar um aumento. Para dimensionar além 10.000 filas com barramento de serviço, você também pode criar namespaces adicionais usando o [portal do Azure][].

## <a name="management-and-operations"></a>Gerenciamento e operações

Esta seção compara os recursos de gerenciamento fornecidos pelo filas filas do Azure e barramento de serviço.

|Critérios de comparação|Filas Azure|Filas do barramento de serviço|
|---|---|---|
|Protocolo de gerenciamento|**Coloque sobre HTTP/HTTPS**|**POSICIONE HTTPS**|
|Protocolo de tempo de execução|**Coloque sobre HTTP/HTTPS**|**POSICIONE HTTPS**<br/><br/>**AMQP 1.0 padrão (TCP com TLS)**|
|API gerenciada do .NET|**Sim**<br/><br/>(.NET gerenciados API do cliente de armazenamento)|**Sim**<br/><br/>(.NET gerenciado orientado API de mensagem)|
|C++ nativo|**Sim**|**Não**|
|API Java|**Sim**|**Sim**|
|API DE PHP|**Sim**|**Sim**|
|Node API|**Sim**|**Sim**|
|Suporte a metadados aleatório|**Sim**|**Não**|
|Regras de nomenclatura de fila|**Até 63 caracteres de comprimento**<br/><br/>(Letras em um nome de fila devem ser minúsculas).|**Até 260 caracteres**<br/><br/>(Nomes e caminhos de fila diferenciam maiusculas de minúsculas.)|
|Obter a função de comprimento de fila|**Sim**<br/><br/>(Valor aproximado se mensagens expiram além o TTL sem serem excluídas.)|**Sim**<br/><br/>(Valor exato, no momento.)|
|Função espiada|**Sim**|**Sim**|

### <a name="additional-information"></a>Informações adicionais

- Filas Azure oferecem suporte para aleatório atributos que podem ser aplicados a descrição de fila, na forma de pares nome/valor.

- Ambas as tecnologias de fila oferecem a capacidade de inspecionar uma mensagem sem precisar bloqueá-lo, que pode ser útil ao implementar uma ferramenta de navegador/explorer fila.

- Serviço barramento .NET orientado mensagens APIs aproveitar full duplex as conexões TCP para melhorar o desempenho quando comparadas a REST sobre HTTP e oferecem suporte o protocolo padrão AMQP 1.0.

- Nomes de filas Azure pode ter 3-63 caracteres, pode conter letras minúsculas, números e hifens. Para obter mais informações, consulte [filas de nomenclatura e metadados](https://msdn.microsoft.com/library/azure/dd179349.aspx).

- Nomes de fila do barramento de serviço podem ser até 260 caracteres e ter as regras de nomenclatura menos restritivas. Nomes de fila do barramento de serviço podem conter letras, números, pontos, hífens e sublinhados.

## <a name="authentication-and-authorization"></a>Autenticação e autorização

Esta seção descreve os recursos de autenticação e a autorização suportados pelo filas filas do Azure e barramento de serviço.

|Critérios de comparação|Filas Azure|Filas do barramento de serviço|
|---|---|---|
|Autenticação|**Chave simétrica**|**Chave simétrica**|
|Modelo de segurança|Acesso delegado por meio de tokens SAS.|SAS|
|Federação de provedor de identidade|**Não**|**Sim**|

### <a name="additional-information"></a>Informações adicionais

- Cada solicitação para qualquer um das tecnologias filas deve ser autenticada. Não há suporte para filas públicas com acesso anônimo. Usando [SAS](service-bus-sas-overview.md), você pode abordar esse cenário publicando uma SAS somente gravação, SAS somente leitura ou até mesmo uma SAS de acesso total.

- O esquema de autenticação fornecido pelo filas do Azure envolve o uso de uma chave simétrica, que é um baseado em hash mensagem Authentication Code (HMAC), calculada com o algoritmo SHA-256 e codificada como uma cadeia de caracteres **na Base 64** . Para obter mais informações sobre o protocolo respectivo, consulte [autenticação para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx). Filas de barramento de serviço oferecem suporte a um modelo semelhante usando chaves simétricas. Para obter mais informações, consulte [Autenticação de assinatura de acesso compartilhado com barramento de serviço](service-bus-shared-access-signature-authentication.md).

## <a name="cost"></a>Custo

Esta seção compara filas filas do Azure e barramento de serviço de uma perspectiva de custo.

|Critérios de comparação|Filas Azure|Filas do barramento de serviço|
|---|---|---|
|Custo da transação de fila|**$0.0036**<br/><br/>(por 100.000 transações)|**Nível básico**: **US $0,05**<br/><br/>(por milhões operações)|
|Operações faturáveis|**Todos os**|**Enviar/receber somente**<br/><br/>(nenhuma cobrança para outras operações)|
|Transações ociosas|**Faturáveis**<br/><br/>(Consultar uma fila vazia é contada como uma transação faturável.)|**Faturáveis**<br/><br/>(Um recebimento contra uma fila vazia é considerado uma mensagem faturável.)|
|Custo de armazenamento|**$0,07**<br/><br/>(por GB/mês)|**r $0,00**|
|Custos de transferência de dados de saída|**$0,12 - r $0.19**<br/><br/>(Dependendo da geografia.)|**$0,12 - r $0.19**<br/><br/>(Dependendo da geografia.)|

### <a name="additional-information"></a>Informações adicionais

- Transferências de dados são cobradas com base na quantidade total de dados, deixando dos data centers do Azure através da internet em um determinado período de cobrança.

- Transferências de dados entre os serviços do Azure localizados na mesma região não estão sujeitos a carga.

- Até o momento, todas as transferências de dados de entrada não estão sujeitos a carga.

- O suporte para sondagem longa, usando filas de barramento de serviço pode ser econômico em situações em que a entrega de baixa latência é necessária.

>[AZURE.NOTE] Todos os custos estão sujeitos a mudança. Esta tabela reflete preços atual e não inclui qualquer ofertas promocionais que atualmente podem estar disponíveis. Para obter informações atualizadas sobre preços do Azure, consulte a página de [preços do Azure](https://azure.microsoft.com/pricing/) . Para obter mais informações sobre preços do barramento de serviço, consulte [preços de barramento de serviço](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="conclusion"></a>Conclusão

Obtendo uma compreensão mais profunda das duas tecnologias, você poderá tomar uma decisão mais fundamentada sobre qual tecnologia fila usar e quando. A decisão sobre quando usar filas do Azure ou barramento de serviço filas claramente depende de vários fatores. Esses fatores podem depender muito as necessidades individuais de seu aplicativo e sua arquitetura. Se seu aplicativo já usa os principais recursos do Microsoft Azure, talvez você prefira escolher filas do Azure, especialmente se você precisar comunicação básica e mensagens entre serviços ou filas de necessidade que podem ser maiores do que 80 GB de tamanho.

Detecção, automática de duplicidades porque filas de barramento de serviço fornecem um número de recursos avançados, como sessões, transações, recursos de inatividade letras e durável publicar/assinar, eles podem ser a opção preferencial se você estiver criando um aplicativo híbrido ou se seu aplicativo caso contrário requer esses recursos.

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir fornecem mais orientações e informações sobre como usar filas filas do Azure ou barramento de serviço.

- [Como usar filas de barramento de serviço](service-bus-dotnet-get-started-with-queues.md)
- [Como usar o serviço de armazenamento de fila](../storage/storage-dotnet-how-to-use-queues.md)
- [Práticas recomendadas para melhorias de desempenho usando o barramento de serviço orientado a mensagens](service-bus-performance-improvements.md)
- [Apresentando tópicos no carregador de serviço Azure e filas](http://www.code-magazine.com/article.aspx?quickid=1112041)
- [O guia do desenvolvedor para barramento de serviço](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
- [Usando o serviço de fila no Azure](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)
- [Noções básicas sobre cobrança de armazenamento do Azure – largura de banda, transações e capacidade](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

[Portal do Azure]: https://portal.azure.com
 
