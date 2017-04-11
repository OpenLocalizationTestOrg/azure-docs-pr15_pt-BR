<properties
   pageTitle="Gerenciador de recursos de Cluster do serviço tecidos - afinidade | Microsoft Azure"
   description="Visão geral da configuração afinidade para serviços de estrutura de serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configurando e usando afinidade de serviço em estrutura de serviço

Afinidade é um controle que é fornecido principalmente para ajudar a facilitar a transição de aplicativos monolíticos maiores no mundo de nuvem e microservices. Dito que ele também pode ser usado em determinados casos como uma otimização legítima para melhorar o desempenho dos serviços, embora isso pode ter efeitos de lado.

Digamos que você estiver agregando um aplicativo maior que ou simplesmente não foi projetado com microservices em mente, a estrutura de serviço. Essa transição é realmente comuns e tivemos vários clientes (internos e externos) nessa situação. Você iniciar erguendo o aplicativo inteiro para o ambiente, obtendo-empacotados em funcionamento. E comece a dividi-la em diferentes serviços menores que todos conversar entre si.

Há um "Oops...". O "Oops" geralmente se encaixa em uma destas categorias:

1. Alguns componentes X no aplicativo monolítico tinham uma dependência não documentada no componente Y e podemos acabou de em serviços separados. Desde que esses agora estiver executando em nós diferentes no cluster, elas são quebradas.
2.  Estes itens se comunicar através do (local pipes nomeado | memória compartilhada | arquivos no disco), mas eu realmente precisa ser capaz de atualizá-lo de maneira independente para acelerar o processo um pouco. Vai remover a dependência difícil posteriormente.
3.  Tudo está correto, mas ele acontece que esses dois componentes são realmente muito informal/desempenho confidencial. Quando eles movido-los para serviços separados geral tanked de desempenho do aplicativo ou latência maior. Como resultado, o aplicativo geral não atende às expectativas.

Nesses casos nós não quer perder nosso trabalho refatoração e não quiser voltar para o monolito, mas precisamos algum sentido da localidade. Isto irá persistir tanto até que estamos pode recriar os componentes para trabalhar naturalmente como serviços ou podemos resolver as expectativas de desempenho alguma outra maneira, se possível.

O que fazer? Bem, você poderia tentar ativar afinidade.

## <a name="how-to-configure-affinity"></a>Como configurar afinidade
Para configurar afinidade, você definir uma relação de afinidade entre dois serviços diferentes. Você pode pensar afinidade como "apontando" um serviço em outro e dizendo "esse serviço só pode executar onde que está sendo executado." Às vezes, podemos consulte afinidade como um relacionamento pai/filho (onde você aponte o filho no pai). Disso, garante que as réplicas ou instâncias de um serviço são colocadas em nós do mesmos como réplicas ou instâncias de outra.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## <a name="different-affinity-options"></a>Opções de afinidade diferentes
Afinidade é representada por meio de um dos vários esquemas de correlação e tem dois modos diferentes. O modo mais comum de afinidade é o que chamamos de NonAlignedAffinity. NonAlignedAffinity a réplicas ou instâncias de diferentes serviços são colocadas em nós do mesmos. O outro modo é AlignedAffinity. Alinhado afinidade é útil somente com os serviços com estado. Configurar dois serviços com informações de estado para alinharam afinidade garante que as cores primárias desses serviços são colocadas em nós como si mesmos. Ele também faz com que cada par de secundários para esses serviços deve ser posicionado em nós do mesmos. Também é possível (embora menos comum) configurar NonAlignedAffinity para serviços com estado. Para NonAlignedAffinity as réplicas diferentes dos dois serviços com estado seriam ser proximidade da localização nos mesmos nós, mas nenhuma tentativa seria feita para alinhar seus primários ou secundários.

![Modos de afinidade e seus efeitos][Image1]

### <a name="best-effort-desired-state"></a>Melhor estado de esforço desejado
Há algumas diferenças entre afinidade e arquiteturas monolíticos. Muitas delas são porque uma relação de afinidade é a melhor solução. Os serviços em um relacionamento de afinidade são bem diferentes entidades que podem falhar e ser movidas independentemente. Também existem causas para por uma relação de afinidade pode ser desfeito. Por exemplo, limitações de capacidade onde apenas alguns dos objetos do serviço na relação afinidade cabem em um determinado nó. Nesses casos, embora haja uma relação de afinidade no lugar, ele não pode ser aplicado devido as outras restrições. Se for possível impor todas as outras restrições e afinidade posteriormente a violação da restrição afinidade será corrigida automaticamente.  

### <a name="chains-vs-stars"></a>Cadeias versus estrelas
Hoje não possamos cadeias de modelo de relações de afinidade. Isso significa que um serviço que é um filho em um relacionamento de afinidade não pode ser um pai em outra relação afinidade. Se você deseja modelar esse tipo de relação, você terá efetivamente modelá-lo como uma estrela, em vez de uma cadeia. Para fazer isso, o filho mais baixos poderia ser pai pai da criança "intermediário" em vez disso. Dependendo da organização dos seus serviços, isso pode exigir a criação de um serviço de "espaço reservado" para servir como o pai para vários filhos.

![Cadeias versus estrelas no contexto de relações de afinidade][Image2]

Outra coisa nota sobre relações de afinidade hoje é que eles são direcionais. Isso significa que a regra "afinidade" apenas impõe que o filho é onde está o pai. Se por exemplo o pai imediato falhar ao longo para outro nó, em seguida, o Gerenciador de recursos de Cluster não realmente achar que há algo errado até que ele observar que os filhos não está localizado com um pai; a relação não é aplicada imediatamente.

### <a name="partitioning-support"></a>Suporte a partição
A observação final sobre afinidade é que afinidade relações não são suportadas onde o pai estiver particionado. Isso é algo que pode suportamos eventualmente, mas hoje não é permitido.

## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre as outras opções disponíveis para configurar serviços de check-out o tópico sobre as outras configurações do Gerenciador de recursos de Cluster disponíveis [Saiba como configurar os serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Muitos motivos onde as pessoas usam afinidade, como limitar os serviços em um pequeno conjunto de máquinas e tentando agregar a carga de um conjunto de serviços, melhor com suporte através de grupos de aplicativos. Fazer check-out de [Grupos de aplicativos](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png
