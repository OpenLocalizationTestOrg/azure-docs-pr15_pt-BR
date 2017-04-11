<properties
   pageTitle="Planejamento da capacidade de cluster serviço tecidos | Microsoft Azure"
   description="Considerações de planejamento da capacidade de cluster de estrutura de serviço. Níveis de Nodetypes, durabilidade e confiabilidade"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Capacidade de cluster de estrutura de serviço considerações de planejamento

Para qualquer implantação de produção, planejamento de capacidade é uma etapa importante. Aqui estão alguns dos itens que você deve considerar como parte do processo.

- O número de tipos de nós com que seu cluster precisa começar
- As propriedades de cada tipo de nó (tamanho, principal, internet opostas, número de VMs, etc.)
- As características de confiabilidade e durabilidade do cluster

Deixe-nos Analise resumidamente cada um desses itens.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>O número de tipos de nós com que seu cluster precisa começar

Primeiro, você precisa descobrir o que o cluster que você está criando vai ser usado para e que tipos de aplicativos você planeja implantar neste cluster. Se você não estiver claro sobre a finalidade do cluster, você provavelmente ainda não está pronto para inserir a processo de planejamento de capacidade.

Estabelece o número de tipos de nós com que seu cluster precisa começar.  Cada tipo de nó é mapeado para um conjunto de escala de máquina Virtual. Cada tipo de nó, em seguida, pode ser dimensionado para cima ou para baixo independentemente, ter diferentes conjuntos de portas abertas e pode ter métricas de capacidade diferente. Portanto a decisão do número de tipos de nós essencialmente resume ao considerando seguinte:

- Seu aplicativo tem vários serviços e qualquer uma delas precisa ser público ou voltado para a internet? Aplicativos típicos contêm um serviço de gateway front-end que recebe entrada de um cliente e um ou mais serviços back-end que se comunicar com os serviços de front-end. Nesse caso, portanto você acabar tendo pelo menos dois tipos de nó.

- Seus serviços (que constituem seu aplicativo) tem necessidades de infraestrutura diferentes como maior de RAM ou superiores ciclos de CPU? Por exemplo, vamos supor que o aplicativo que você deseja implantar contém um serviço de front-end e um serviço de back-end. O serviço de front-end pode executar em VMs menores (tamanhos de máquina virtual como D2) que tem portas abertas à internet.  O serviço de back-end, no entanto, é que requer muita computação e precisa executar no maiores VMs (com tamanhos de máquina virtual como D4, D6, D15) que não são internet opostas.

 Neste exemplo, embora você possa decidir colocar todos os serviços no tipo de um nó, é recomendável que você colocá-los em um cluster com dois tipos de nó.  Isso permite que cada tipo de nó ter propriedades distintas como conectividade com a internet ou o tamanho da máquina virtual. O número de VMs pode ser dimensionado independentemente, bem como.  

- Desde que você não pode prever o futuro, vá com fatos que você conhece e decida no número de tipos de nós que seus aplicativos necessitam começar. Você sempre pode adicionar ou remover tipos de nós mais tarde. Um cluster de estrutura de serviço deve ter o tipo de pelo menos um nó.

## <a name="the-properties-of-each-node-type"></a>As propriedades de cada tipo de nó

O **tipo de nó** podem ser vistos como equivalentes às funções em serviços de nuvem. Tipos de nó definem os tamanhos de máquina virtual, o número de VMs e suas propriedades. Cada tipo de nó que é definido em um cluster de estrutura de serviço está configurado como um conjunto de escala de máquina Virtual separada. Os conjuntos de escala de máquina virtual são que você pode usar para implantar e gerenciar um conjunto de máquinas virtuais como um conjunto de recursos de computação de uma Azure. Sendo definida como escala de máquina virtual distintas define, cada tipo de nó, em seguida, pode ser dimensionado para cima ou para baixo independentemente, ter diferentes conjuntos de portas abertas e pode ter métricas de capacidade diferente.

Seu cluster pode ter mais de um tipo de nó, mas o tipo de nó principal (primeira aquele que você define no portal) deve ter pelo menos cinco VMs para clusters usados para cargas de trabalho de produção (ou pelo menos três VMs para clusters de teste). Se você estiver criando o cluster usando um modelo do Gerenciador de recursos, você encontrará um atributo **primário** sob a definição de tipo de nó. O tipo de nó primário é o tipo de nó onde os serviços de sistema do serviço tecidos são colocados.  

### <a name="primary-node-type"></a>Tipo de nó principal
Para um cluster com vários tipos de nós, você precisará escolher um deles sejam principal. Aqui estão as características de um tipo de nó principal:

- O tamanho mínimo de VMs para o tipo de nó primário é determinado pela camada de durabilidade que você escolher. O padrão para a camada de durabilidade é Bronze. Role para baixo para obter detalhes sobre o que é a camada de durabilidade e os valores que pode demorar.  

- O número mínimo de VMs para o tipo de nó primário é determinado pelo nível de confiabilidade que você escolher. O padrão para a camada de confiabilidade é prata. Role para baixo para obter detalhes sobre o que é o nível de confiabilidade e os valores que pode demorar.

- Os serviços do sistema tecidos de serviço (por exemplo, o Gerenciador de Cluster serviço ou repositório de imagem) são colocados no tipo de nó primário e então a confiabilidade e durabilidade do cluster é determinada pelo valor de nível de confiabilidade e o valor de nível de durabilidade selecionado para o tipo de nó principal.

![Captura de tela de um cluster que tem dois tipos de nó ][SystemServices]


### <a name="non-primary-node-type"></a>Tipo de nó não principal
Para um cluster com vários tipos de nós, há um tipo de nó primário e o resto deles não principal. Aqui estão as características de um tipo de nó não principal:

- O tamanho mínimo de VMs para este tipo de nó é determinado pela camada de durabilidade que você escolher. O padrão para a camada de durabilidade é Bronze. Role para baixo para obter detalhes sobre o que é a camada de durabilidade e os valores que pode demorar.  

- O número mínimo de VMs para este tipo de nó pode ser uma. No entanto, você deve escolher esse número com base no número de réplicas dos/serviços de aplicativo que você gostaria de executar nesse tipo de nó. O número de VMs em um tipo de nó pode ser aumentado depois você implantou o cluster.


## <a name="the-durability-characteristics-of-the-cluster"></a>As características de durabilidade do cluster

A camada de durabilidade é usada para indicar ao sistema os privilégios que suas VMs tem com a infraestrutura de Azure subjacente. Em tipo de nó principal, esse privilégio permite tecidos de serviço pausar qualquer solicitação de infraestrutura de nível de máquina virtual (como uma nova imagem de máquina virtual, reinicialização de máquina virtual ou migração de máquina virtual) que afetam os requisitos de quorum para os serviços do sistema e seus serviços com estado. Os tipos de nó não principal, esse privilégio permite tecidos de serviço pausar qualquer solicitação de infraestrutura de nível de máquina virtual como reinicialização de máquina virtual, a nova imagem de máquina virtual, a migração de máquina virtual etc., que afetam os requisitos de quorum para seus serviços com estado em execução.

Esse privilégio é expressa nos seguintes valores:

- Ouro - os trabalhos de infraestrutura pode ser pausado por uma duração de 2 horas por UD

- Silver - os trabalhos de infraestrutura pode ser pausado por uma duração de 30 minutos por UD (isso atualmente não está habilitado para uso. Uma vez habilitado isso estará disponível em todas as VMs padrão de núcleo único e acima).

- Bronze - sem privilégios. Este é o padrão.

## <a name="the-reliability-characteristics-of-the-cluster"></a>As características de confiabilidade do cluster

O nível de confiabilidade é usado para definir o número de réplicas dos serviços de sistema que você deseja executar neste cluster do tipo de nó principal. O mais o número de réplicas, mais confiável os serviços do sistema estão no seu cluster.  

O nível de confiabilidade pode levar os seguintes valores.

- Platinum - executar os serviços do sistema com um destino réplica Definir contagem de 9

- Ouro - executar os serviços do sistema com um destino réplica Definir contagem de 7

- Silver - executar os serviços do sistema com um destino réplica Definir contagem de 5

- Bronze - executar os serviços do sistema com um destino réplica Definir contagem de 3

>[AZURE.NOTE] O nível de confiabilidade que você escolher determina o número mínimo de nós do que seu tipo de nó primário deve ter. O nível de confiabilidade não possui o tamanho máximo do cluster. Você pode ter um cluster de 20 nó, que está executando na confiabilidade Bronze.

 Você pode optar por atualizar a confiabilidade do seu cluster de um nível para outro. Isso acionará as atualizações de cluster necessária alterar a réplica de serviços de sistema conjunto contagem. Aguarde a atualização em andamento para ser concluída antes de fazer outras alterações ao cluster, como adicionar nós etc.  Você pode monitorar o andamento da atualização do serviço tecidos Explorer ou executando [Get-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt126012.aspx)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas

Após concluir a planejamento de capacidade e configurar um cluster, leia o seguinte:
- [Segurança de cluster tecidos de serviço](service-fabric-cluster-security.md)
- [Introdução de modelo de integridade do serviço tecidos](service-fabric-health-introduction.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
