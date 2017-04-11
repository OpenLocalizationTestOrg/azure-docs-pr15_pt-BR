<properties 
    pageTitle="Barramento de serviço Azure | Microsoft Azure" 
    description="Uma introdução ao uso barramento de serviço para conectar-se a aplicativos do Azure para outro software." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/31/2016" 
    ms.author="sethm"/>

# <a name="azure-service-bus"></a>Barramento de serviço Azure

Se um aplicativo ou serviço é executado na nuvem ou no local, geralmente ele precisa interagir com outros aplicativos ou serviços. Para fornecer uma maneira amplamente útil para fazer isso, o Microsoft Azure oferece barramento de serviço. Este artigo leva uma olhada essa tecnologia, descrevendo o que é e por que talvez você queira usá-lo.

## <a name="service-bus-fundamentals"></a>Fundamentos de barramento de serviço

Ligue para situações diferentes para diferentes estilos de comunicação. Às vezes, permitindo que aplicativos enviar e receber mensagens por meio de uma simple fila é a melhor solução. Em outras situações, uma fila ordinário não é suficiente; uma fila com um mecanismo de publicação e assinatura é melhor. Em alguns casos, tudo o que realmente é preciso é uma conexão entre aplicativos; filas não são necessárias. Barramento de serviço fornece todas as três opções, permitindo que seus aplicativos para interagir de várias maneiras diferentes.

Barramento de serviço é um serviço de nuvem de vários locatários, o que significa que o serviço é compartilhado por vários usuários. Cada usuário, como um desenvolvedor de aplicativos, cria um *namespace*, depois define os mecanismos de comunicação que ela precisa dentro desse namespace. Figura 1 mostra como isso é a aparência.

![][1]
 
**Figura 1: Barramento de serviço fornece um serviço de vários locatário para conexão de aplicativos através da nuvem.**

Dentro de um namespace, você pode usar uma ou mais instâncias de quatro mecanismos de comunicação diferentes, cada um deles conecta aplicativos de maneira diferente. As opções são:

- *Filas*, que permitem a comunicação bidirecional um. Cada fila atua como um intermediário (às vezes chamado de um *agente*) que armazena mensagens enviadas até que elas são recebidas. Cada mensagem for recebida por um único destinatário.
- *Tópicos*que fornecem comunicação bidirecional um usando *assinaturas*- um único tópico pode ter várias assinaturas. Como uma fila, um tópico atua como um intermediário, mas cada assinatura, opcionalmente, pode usar um filtro para receber apenas as mensagens que correspondem aos critérios específicos.
- *Retransmissões*, que fornecem comunicação bidirecional. Ao contrário dos tópicos e filas, uma retransmissão não armazena mensagens em trânsito; não é um corretor. Em vez disso, ele só as passa para o aplicativo de destino.

Quando você cria uma fila, tópico ou retransmissão, você atribuir um nome. Esse nome combinado com que você chamado seu namespace, cria um identificador exclusivo para o objeto. Aplicativos podem fornecer esse nome ao barramento de serviço, use essa fila, tópico ou retransmissão para se comunicar uns com os outros. 

Para usar qualquer um desses objetos no cenário de retransmissão, aplicativos do Windows podem usar o Windows Communication Foundation (WCF). Para filas e tópicos, aplicativos do Windows podem usar APIs de mensagens definidos pelo barramento de serviço. Para facilitar esses objetos usar aplicativos não-Windows, a Microsoft fornece SDKs para Java, node e outros idiomas. Você também pode acessar filas e tópicos usando APIs REST sobre HTTP (s). 

É importante entender que apesar de serviço de barramento em si é executado na nuvem (ou seja, em centros de dados Azure da Microsoft), podem executar aplicativos que usá-lo em qualquer lugar. Você pode usar barramento de serviço para conectar aplicativos em execução no Azure, por exemplo, ou aplicativos executados dentro de seu próprio data center. Você também pode usá-lo para se conectar a um aplicativo em execução no Azure ou em outra plataforma de nuvem com um aplicativo local ou tablets e telefones. Também é possível conectar domésticos eletrodomésticos, sensores e outros dispositivos para um aplicativo central ou a qualquer outra. Barramento de serviço é um mecanismo de comunicação na nuvem acessível de praticamente qualquer lugar. Como você usa depende o que seus aplicativos precisam fazer.

## <a name="queues"></a>Filas

Suponha que você decida se conectar dois aplicativos usando uma fila de barramento de serviço. Figura 2 ilustra essa situação.

![][2]
 
**Figura 2: Filas de barramento de serviço fornecem fila assíncrona unidirecional.**

O processo é simples: um remetente envia uma mensagem para uma fila de barramento de serviço e um receptor seleciona essa mensagem em algum momento posterior. Uma fila pode ter apenas um único destinatário, como mostra a Figura 2. Ou vários aplicativos podem ler a partir da mesma fila. Na última situação, cada mensagem é lido por apenas um receptor. Para um serviço múltipla cast, você deve usar um tópico.

Cada mensagem tem duas partes: um conjunto de propriedades, cada um par chave/valor e corpo de uma mensagem binário. Como elas são usadas depende de qual um aplicativo está tentando fazer. Por exemplo, um aplicativo enviando uma mensagem sobre uma venda recente pode incluir as propriedades *Vendedor = "Ava"* e *quantidade = 10000*. Corpo da mensagem pode conter uma imagem digitalizada do contrato assinado da venda ou, se não houver uma, basta permanecerão vazio.

Um receptor pode ler uma mensagem de uma fila de barramento de serviço de duas maneiras diferentes. A primeira opção, chamada *ReceiveAndDelete*, remove uma mensagem da fila e exclui-lo imediatamente. Isso é simples, mas se o receptor trava antes de terminar de processar a mensagem, a mensagem serão perdida. Porque ele foi removido da fila, nenhum outro receptor possa acessá-lo. 

A segunda opção, *PeekLock*, é destinada a ajudar com esse problema. Como o **ReceiveAndDelete**, uma leitura **PeekLock** remove uma mensagem da fila. Ela não exclui a mensagem, no entanto. Em vez disso, ele bloqueia a mensagem, tornando-a invisível para outros destinatários, e depois aguarda um dos três eventos:

- Se o receptor processa a mensagem com êxito, ele chama **concluída**e fila exclui a mensagem. 
- Se o receptor decidir que não é possível processar a mensagem com êxito, ele chama **abandonar**. Fila remove o bloqueio da mensagem e o disponibiliza para outros destinatários.
- Se o receptor chama nenhuma dessas dentro de um período configurável (por padrão, 60 segundos), a fila assume que o receptor falhou. Nesse caso, ele se comporta como se o receptor tivesse chamado **abandonar**, disponibilizando a mensagem para outros destinatários.

Observe que pode acontecer aqui: a mesma mensagem pode ser entregue duas vezes, talvez a dois receptores diferentes. Aplicativos usando filas de barramento de serviço devem estar preparados para isso. Para facilitar a detecção de duplicidades, que cada mensagem possui uma propriedade **MessageID** exclusiva que, por padrão, permanece o mesmo independentemente como muitas vezes a mensagem é lida de uma fila. 

Filas são úteis em algumas situações. Eles permitem que aplicativos para se comunicar mesmo quando ambos não estejam executando ao mesmo tempo, algo que é especialmente útil com lote e aplicativos móveis. Uma fila com vários receptores também fornece balanceamento de carga automático, desde que as mensagens enviadas são distribuídas entre esses receptores.

## <a name="topics"></a>Tópicos

Útil como estão, filas não estão sempre a solução certa. Às vezes, os tópicos de barramento de serviço são melhores. Figura 3 ilustra esse conceito.

![][3]
 
**Figura 3: Com base no filtro que especifica um aplicativo de inscrição, ele pode receber algumas ou todas as mensagens enviadas para um tópico de barramento de serviço.**

Um *tópico* é semelhante de muitas maneiras para uma fila. Mensagens de envio de remetentes a um tópico da mesma forma que eles enviarem mensagens para uma fila e as mensagens sejam idênticas conforme com filas. A grande diferença é que tópicos permitem cada aplicativo de recebimento criar sua própria *assinatura* definindo um *filtro*. Um assinante verá apenas as mensagens que correspondam a esse filtro. Por exemplo, a Figura 3 mostra um remetente e um tópico com três assinantes, cada uma com seu próprio filtro:

- Assinante 1 recebe apenas as mensagens que contêm a propriedade *Vendedor = "Ava"*.
- Assinante 2 recebe mensagens que contêm a propriedade *Vendedor = "Ruby"* e/ou contêm uma propriedade de *valor* cujo valor é maior que 100,000. Talvez Ruby é o gerente de vendas, para que ela quer ver próprias vendas e todas as vendas grandes, independentemente de quem são seus criadores.
- Assinante 3 definiu seu filtro como *True*, o que significa que ele recebe todas as mensagens. Por exemplo, esse aplicativo pode ser responsável por manter uma trilha de auditoria e, portanto, ela precisa ver todas as mensagens.

Como com filas, os assinantes de um tópico podem ler mensagens usando **ReceiveAndDelete** ou **PeekLock**. Ao contrário de filas, no entanto, uma única mensagem enviada para um tópico pode ser recebida por várias assinaturas. Essa abordagem, normalmente chamado de *Publicar e assinar* (ou *pub/sub*), é útil sempre que vários aplicativos estão interessados nas mesmas mensagens. Definindo o filtro à direita, cada assinante pode toque apenas a parte do fluxo de mensagens que ele precisa ver.

## <a name="relays"></a>Retransmissões

Tópicos e filas fornecem comunicação assíncrona unidirecional por meio de um agente. Tráfego flui em apenas uma direção e não há nenhuma conexão direta entre remetentes e receptores. Mas o que acontece se você não quiser isso? Suponha que seus aplicativos precisam enviar e receber mensagens, ou talvez você deseja um link direto entre eles e você não precisa de um agente para armazenar as mensagens. Para lidar com cenários como esta, barramento de serviço fornece *retransmite*, como mostra a Figura 4.

![][4]
 
**Figura 4: Retransmissão de barramento de serviço fornece comunicação bidirecional síncrona entre aplicativos.**

A pergunta óbvia perguntar retransmissões é: por que usar um? Mesmo se não precisar filas, por que fazer aplicativos comunicar-se através de um serviço de nuvem, em vez de apenas interagir diretamente? A resposta é que falar diretamente pode ser difícil que você imagina.

Suponha que você deseja conectar dois aplicativos de locais, ambos executando dentro dos data centers corporativos. Cada um desses aplicativos fica atrás de um firewall, e cada data center provavelmente usa NAT (NAT). O firewall bloqueia os dados de entrada em apenas algumas portas e NAT significa que cada aplicativo seja executado na máquina não tem um endereço IP fixo que você pode acessar diretamente de fora do data center. Sem ajuda adicional, conectando esses aplicativos na Internet pública é problemático.

Uma retransmissão de barramento de serviço pode ajudar. Para se comunicar bidirecional por meio de uma retransmissão, cada aplicativo estabelece uma conexão de TCP de saída com barramento de serviço e mantém aberto. Todas as comunicações entre os dois aplicativos percorram sobre essas conexões. Porque cada conexão foi estabelecida de dentro do data center, o firewall permite o tráfego de entrada para cada aplicativo sem abrir novas portas. Essa abordagem também obtém com o problema NAT, porque cada aplicativo tem um ponto de extremidade consistente na nuvem em toda a comunicação. Trocando dados por meio de relay, os aplicativos podem evitar os problemas que faria caso contrário dificultam a comunicação. 

Para usar retransmissões barramento de serviço, aplicativos confiam no Windows Communication Foundation (WCF). Barramento de serviço fornece ligações do WCF que tornam mais simples para aplicativos do Windows interagir por meio de retransmissões. Aplicativos que já utilizam WCF podem normalmente apenas especificar um dessas ligações e conversar entre si por meio de uma retransmissão. Ao contrário dos tópicos e filas, no entanto, usando retransmissões de aplicativos não-Windows, enquanto possível, requer o alguns esforço de programação; Não há bibliotecas padrão são fornecidas.

Ao contrário dos tópicos e filas, aplicativos não criam explicitamente retransmissões. Em vez disso, quando um aplicativo que deseja receber mensagens estabelece uma conexão TCP com barramento de serviço, uma retransmissão é criada automaticamente. Quando a conexão for interrompida, a retransmissão é excluída. Para habilitar um aplicativo para localizar a retransmissão criada por um ouvinte específico, barramento de serviço oferece um registro que permite que aplicativos localizar uma retransmissão específica por nome.

Retransmissões são a solução certa quando você precisa comunicação direta entre aplicativos. Por exemplo, considere um sistema de reserva de linhas aéreas executando em um data center local que deve ser acessado da seleção de quiosques, dispositivos móveis e outros computadores. Aplicativos executados em todos esses sistemas poderiam dependem retransmissões de barramento de serviço na nuvem para se comunicar, onde quer que eles podem estar em execução.

## <a name="summary"></a>Resumo

Conectando aplicativos sempre foi parte da criação de soluções completas e o intervalo de cenários que necessitam de aplicativos e serviços para se comunicar com os outros estiver definido para aumentar conforme mais aplicativos e dispositivos estiverem conectados à Internet. Fornecendo tecnologias baseadas em nuvem para atingir isso por meio de filas, tópicos e retransmissões, barramento de serviço tem como objetivo tornar esta função essencial fácil de implementar e amplamente disponíveis.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do barramento de serviço do Azure, siga estes links para saber mais.

- Como usar [filas de barramento de serviço](service-bus-dotnet-get-started-with-queues.md)
- Como usar [Tópicos de barramento de serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- Como usar [retransmissão de barramento de serviço](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md)
- [Exemplos de barramento de serviço](service-bus-samples.md)

[1]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_01_architecture.png
[2]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_02_queues.png
[3]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[4]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_04_relay.png
