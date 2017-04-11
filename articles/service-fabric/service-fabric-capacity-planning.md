<properties
   pageTitle="Capacidade de planejamento para os aplicativos de serviço tecidos | Microsoft Azure"
   description="Descreve como identificar o número de nós de computação necessários para um aplicativo de serviço tecidos"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="markfuss"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>


# <a name="capacity-planning-for-service-fabric-applications"></a>Capacidade de planejamento para aplicativos de serviço tecidos


Este documento ensina estimar a quantidade de recursos (CPUs, RAM, armazenamento em disco) necessários para executar os aplicativos do Azure serviço tecidos. É comum para as necessidades de recursos alterar ao longo do tempo. Você normalmente exige alguns recursos como você desenvolver/testar seu serviço e, em seguida, exige mais recursos como entrar em produção e seu aplicativo aumenta em popularidade. Quando você cria seu aplicativo, considerar os requisitos de longo prazo e fazer escolhas que permitem que o seu serviço atender às demanda do cliente alta.

 Quando você cria um cluster de estrutura de serviço, você decidir quais tipos de máquinas virtuais (VMs) constituem o cluster. Cada máquina virtual vem com uma quantidade limitada de recursos na forma de armazenamento de disco, largura de banda de rede, RAM e CPUs (cores e velocidade). À medida que seu serviço cresce ao longo do tempo, você pode atualizar para VMs que oferecem mais recursos e/ou adicionar mais VMs ao seu cluster. Para fazer o último, você deve projetar seu serviço inicialmente para que ele possa aproveitar novos VMs que seja adicionado dinamicamente ao cluster.

Alguns serviços não gerenciar pouco ou nenhum dados nas VMs próprios. Portanto, capacidade de planejamento para esses serviços deve se concentrar principalmente no desempenho, o que significa que selecionando as CPUs apropriadas (cores e velocidade) das VMs. Além disso, você deve considerar a largura de banda de rede, incluindo frequência transferências de rede estão ocorrendo e a quantidade de dados está sendo transferida. Se seu serviço precisa executar bem como aumentos de uso do serviço, você pode adicionar mais VMs ao saldo cluster e carregar os pedidos de rede entre todas as VMs.

Para os serviços que gerenciam grandes quantidades de dados nas VMs, planejamento da capacidade deve se concentrar principalmente no tamanho. Assim, você deve considerar cuidadosamente a capacidade de armazenamento de disco e RAM da VM. O sistema de gerenciamento de memória virtual do Windows torna espaço em disco parecer RAM para código do aplicativo. Além disso, o tempo de execução do serviço tecidos fornece inteligente paginação mantendo apenas quente em memória e mover os dados frio em disco. Aplicativos, portanto, podem usar mais memória do que está física disponível na máquina virtual. Basta ter mais RAM aumenta o desempenho, desde que a máquina virtual pode manter mais armazenamento de disco na RAM. A máquina virtual que você selecionar deve ter um disco grande o suficiente para armazenar os dados que você deseja na máquina virtual. Da mesma forma, a máquina virtual deve ter RAM suficiente para fornecer o desempenho desejado. Se os dados do seu serviço crescem ao longo do tempo, você pode adicionar mais VMs ao cluster e partição os dados em todas as VMs.

## <a name="determine-how-many-nodes-you-need"></a>Determinar quantos nós necessários

Partição seu serviço permite que você dimensionar dados do seu serviço. Para obter mais informações sobre partição, consulte [Tecidos partição de serviço](service-fabric-concepts-partitioning.md). Cada partição deve caber dentro de uma única VM, mas várias partições (pequenas) podem ser colocadas em uma única VM. Portanto, ter mais partições pequenas fornece maior flexibilidade que ter alguns partições maiores. A desvantagem é que muitas partições aumenta sobrecarga de estrutura de serviço e você não poderá realizar operações realizadas em partições. Também há mais tráfego de rede potencial se seu código de serviço frequentemente precisar acessar partes dos dados que são armazenados na partições diferentes. Ao criar seu serviço, você deve considerar cuidadosamente esses prós e contras chegar a uma estratégia eficaz de partição.

Vamos supor que seu aplicativo tem um único serviço estado que tenha um tamanho de armazenamento que você espera crescer para DB_Size GB em um ano. Você está disposto a adicionar mais aplicativos (e partições) como você experiência crescimento além desse ano.  O fator de replicação (FR), que determina o número de réplicas para seu serviço afeta o DB_Size total. O DB_Size total em todas as réplicas é o fator de replicação multiplicado por DB_Size.  Node_Size representa a espaço em disco/RAM por nó que você deseja usar para o serviço. Para obter melhor desempenho, a DB_Size deve caber na memória no cluster e um Node_Size que está ao redor de RAM da máquina virtual deve ser escolhido. Por alocar um Node_Size maior que a capacidade de RAM, você depender a paginação fornecida pelo runtime tecidos de serviço. Portanto, seu desempenho pode não ser ideal se seus dados inteiros são considerados quente (desde então os dados é paginação em/check-out). No entanto, para muitos serviços onde apenas uma fração dos dados está quente, é mais econômico.

O número de nós necessários para o desempenho máximo pode ser calculado da seguinte maneira:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Conta para o crescimento

Talvez você queira calcular o número de nós com base no que você espera seu serviço a crescer, além do DB_Size que você começou com DB_Size. Em seguida, aumente o número de nós à medida que seu serviço cresce para que você não é provisionamento excessivo o número de nós. Mas o número de partições deve ser baseado no número de nós que são necessárias quando você estiver executando o serviço em crescimento máximo.

É bom ter algumas máquinas extras disponíveis a qualquer momento para que você pode manipular qualquer picos inesperados ou falha (por exemplo, se alguns VMs ir para baixo).  Embora a capacidade extra deve ser determinada usando seu picos esperados, ponto de partida é reservar alguns VMs extras (5 a 10% extra).

Anterior pressupõe um único serviço estado. Se você tiver mais de um serviço de estado, você precisa adicionar o DB_Size associado com outros serviços na equação. Como alternativa, você pode calcular o número de nós separadamente para cada serviço com estado.  Seu serviço pode ter réplicas ou partições que não são equilibradas. Tenha em mente que partições também podem ter mais dados do que as outras pessoas. Para obter mais informações sobre particionamento, consulte [partição artigo sobre práticas recomendadas](service-fabric-concepts-partitioning.md). No entanto, a equação anterior é partição e réplica independente, porque o serviço tecidos assegura que as réplicas estão distribuídas entre os nós de maneira otimizada.


## <a name="use-a-spreadsheet-for-cost-calculation"></a>Usar uma planilha para o cálculo do custo

Agora vamos colocar alguns números reais na fórmula. Uma [planilha de exemplo](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) mostra como planejar a capacidade de um aplicativo que contém três tipos de objetos de dados. Para cada objeto, podemos aproximado seu tamanho e quantos objetos esperadas. Nós também selecionar quantos réplicas queremos de cada tipo de objeto. A planilha calcula a quantidade total de memória sejam armazenados no cluster.

Podemos Insira um tamanho de máquina virtual e o custo mensal. Com base no tamanho da máquina virtual, a planilha informa o número mínimo de partições que você deve usar para dividir seus dados para ajustá-la física em nós. Você pode desejar um número maior de partições para acomodar o cálculo específico do seu aplicativo e precisa de tráfego de rede. A planilha mostra o número de partições que está gerenciando os objetos de perfil de usuário aumentou de um a seis.

Agora, com base em todas essas informações, a planilha mostra que você possa obter todos os dados com as partições desejadas e réplicas física em um cluster de 26-node. No entanto, este cluster poderia ser densamente compactado, portanto, você pode querer alguns nós adicionais para acomodar as atualizações e falhas de nó. A planilha também mostra que ter mais de 57 nós fornece nenhum valor adicional porque você teria nós vazios. Novamente, talvez você queira ir acima 57 nós mesmo assim para acomodar as atualizações e falhas de nó. Você pode ajustar a planilha para corresponder às necessidades específicas do seu aplicativo.   

![Planilha de cálculo de custos][Image1]



## <a name="next-steps"></a>Próximas etapas

Fazer check-out [partição serviço tecidos serviços] [ 10] para saber mais sobre partição seu serviço.



<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
