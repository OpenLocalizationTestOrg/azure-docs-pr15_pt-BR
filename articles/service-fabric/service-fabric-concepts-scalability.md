<properties
   pageTitle="Escalabilidade dos serviços de serviço tecidos | Microsoft Azure"
   description="Descreve como dimensionar serviços de estrutura de serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# <a name="scaling-service-fabric-applications"></a>Aplicativos de serviço tecidos dimensionamento
Azure tecidos serviço facilita a criação de aplicativos scalable por serviços, partições e réplicas balanceamento de carga em todos os nós em um cluster. Isso permite a utilização máxima dos recursos.

Escala de alta para aplicativos de serviço tecidos pode ser obtida de duas maneiras:

1. Dimensionamento no nível da partição

2. Dimensionamento no nível de nome de serviço

## <a name="scaling-at-the-partition-level"></a>Dimensionamento no nível da partição
Serviço tecidos suporta partição um serviço individual em várias partições menores. [Visão geral de partição](service-fabric-concepts-partitioning.md) fornece informações sobre os tipos de esquemas de partição que têm suporte. As réplicas de cada partição são distribuídas entre os nós em um cluster. Considere a possibilidade de um serviço que usa um esquema de partição ranged com uma chave de baixa de 0, uma chave de alta de 99 e quatro partições. Em um cluster de três nós, o serviço pode ser disposto com quatro réplicas que compartilham os recursos em cada nó, conforme mostrado aqui:

![Layout de partição com três nós](./media/service-fabric-concepts-scalability/layout-three-nodes.png)

Aumentar o número de nós permite tecidos de serviço utilizar os recursos sobre os novos nós movendo algumas das réplicas para nós vazios. Aumentando o número de nós para quatro, o serviço agora tem três réplicas executado em cada nó (de partições diferentes), permitindo desempenho e melhor utilização de recursos.

![Layout de partição com quatro nós](./media/service-fabric-concepts-scalability/layout-four-nodes.png)

## <a name="scaling-at-the-service-name-level"></a>Dimensionamento no nível de nome de serviço
Uma instância do serviço é uma instância específica de um nome de aplicativo e um nome de tipo de serviço (consulte [ciclo de vida do aplicativo de serviço tecidos](service-fabric-application-lifecycle.md)). Durante a criação de um serviço, você pode especificar a partição esquema (consulte [Serviços de partição tecidos de serviço](service-fabric-concepts-partitioning.md)) a ser usado.

O primeiro nível de dimensionamento é por nome do serviço. Você pode criar novas instâncias de um serviço, com diferentes níveis de partição, como seu antigas instâncias de serviço ficam ocupadas. Isso permite que os consumidores de serviço de novo usar instâncias de serviço menos ocupado, ao invés daquelas mais ocupadas.

Uma opção para aumentar a capacidade, bem como contagens de partição crescente ou decrescente, é criar uma nova instância de serviço com um novo esquema de partição. Isso adiciona complexidade, no entanto, como qualquer consumindo clientes precisam saber quando e como usar o serviço de forma diferente nomeado.

### <a name="example-scenario-embedded-dates"></a>Cenário de exemplo: inserida datas
Um cenário possível seria usar informações de data como parte do nome do serviço. Por exemplo, você pode usar uma instância de serviço com um nome específico para todos os clientes que ingressou em 2013 e outro nome para clientes que ingressou em 2014. Esse esquema de nomenclatura permite programaticamente aumentando os nomes dependendo da data (como abordagens de 2014, a instância do serviço de 2014 pode ser criada sob demanda).

No entanto, essa abordagem baseia-se nos clientes usando as informações de nomenclatura específicos do aplicativo que estão fora do escopo de Conhecimento de serviço tecidos.

- *Usando uma convenção de nomenclatura*: no 2013, quando seu aplicativo fica ativo, você cria um serviço chamado tecidos: / aplicativo/service2013. Perto do segundo trimestre de 2013, você criar outro serviço, chamado tecidos: / aplicativo/service2014. Dois desses serviços são do mesmo tipo de serviço. Essa abordagem, seu cliente precisam utilizar lógica para construir o nome de serviço apropriado com base no ano.

- *Usando um serviço de pesquisa*: outro padrão é fornecer um serviço de pesquisa secundária, que pode fornecer o nome do serviço para uma chave desejada. Novas instâncias de serviço, em seguida, podem ser criadas pelo serviço de pesquisa. O próprio serviço de pesquisa não reter quaisquer dados de aplicativo, somente os dados sobre os nomes de serviço que ele cria. Assim, baseada em ano exemplo acima, o cliente faria entrar em contato com o serviço de pesquisa para descobrir o nome do serviço manipulação de dados de um determinado ano e use esse nome de serviço para executar a operação real. O resultado da pesquisa de primeira pode ser armazenados em cache.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os conceitos de estrutura de serviço, consulte o seguinte:

- [Disponibilidade de serviços de estrutura de serviço](service-fabric-availability-services.md)

- [Serviços de serviço tecidos partição](service-fabric-concepts-partitioning.md)

- [Definir e gerenciar estado](service-fabric-concepts-state.md)
