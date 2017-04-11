<properties 
    pageTitle="Considerações de desempenho e otimização de pesquisa Azure | Microsoft Azure" 
    description="Ajustar o desempenho de pesquisa do Azure e configurar escala ideal" 
    services="search" 
    documentationCenter="" 
    authors="LiamCavanagh" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="liamca"/>

# <a name="azure-search-performance-and-optimization-considerations"></a>Azure considerações de desempenho e otimização de pesquisa

Uma experiência de pesquisa ótima é uma chave para o sucesso do mobile muitos e aplicativos da web. De imóveis, usado mercados de carro catálogos online, pesquisa rápida e resultados relevantes afetarão a experiência do cliente. Este documento destina-se à ajuda você a descobrir as práticas recomendadas para como obter o máximo proveito do Azure procurar, especialmente cenários avançados com requisitos sofisticados para escalabilidade, em vários idiomas de suporte ou classificação personalizada.  Além disso, este documento descreve internos e aborda abordagens que funcionam bem em aplicativos cliente do mundo real.

## <a name="performance-and-scale-tuning-for-search-services"></a>Desempenho e ajuste de escala para serviços de pesquisa

Nós são usadas para pesquisar mecanismos como Bing e Google e o alto desempenho que eles oferecem.  Como resultado, quando os clientes usam habilitados para pesquisa web ou aplicativo móvel, eles esperam características semelhantes de desempenho.  Ao otimizar para desempenho de pesquisa, uma das abordagens melhores é focalizar latência, que é o tempo que uma consulta leva para concluir e retornar resultados.  Quando a otimização de latência de pesquisa é importante:

1. Escolha uma latência de destino (ou quantidade máxima de tempo) que solicitem uma pesquisa típica levará para serem concluídas.

2. Criar e testar uma carga de trabalho real em relação a seu serviço de pesquisa com um conjunto de dados realista para medir essas taxas de latência.

3. Iniciar com um número baixo de consultas por segundo (QPS) e continuar aumentar o número executado no teste até que a latência da consulta fique abaixo a latência de destino definido.  Esta é uma comparação importante para ajudá-lo a planejar escala à medida que seu aplicativo cresce em uso.

4. Sempre que possível, reutilize conexões HTTP.  Se você estiver usando o SDK do Azure pesquisa .NET, isso significa que você deve reutilizar uma instância ou uma instância de [SearchIndexClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchindexclient.aspx) , e se você estiver usando a API REST, você deve reutilizar um único HttpClient.
 
Ao criar esses testar cargas de trabalho, há algumas características de pesquisa do Azure a ter em mente:

1. É possível push portanto muitas pesquisas ao mesmo tempo, que os recursos disponíveis no seu serviço de pesquisa do Azure serão sobrecarregados.  Quando isso acontece, você verá os códigos de resposta HTTP 503.  Por isso, é melhor começar com vários intervalos de solicitações de pesquisa para ver as diferenças em taxas de latência conforme você adicionar mais solicitações de pesquisa.

2. Carregando de conteúdo para pesquisa de Azure terão impacto sobre o desempenho geral e a latência do serviço de pesquisa do Azure.  Se você pretende enviar dados enquanto os usuários estão realizando pesquisas, é importante levar essa carga de trabalho em consideração em seus testes.

3. Nem todo consulta de pesquisa faz com os mesmos níveis de desempenho.  Por exemplo, uma sugestão de pesquisa ou de pesquisa de documento normalmente executará mais rápido do que uma consulta com um número significativo de aspectos e filtros.  É melhor fazer as várias consultas que esperava ver em consideração ao criar seus testes.  

4. Variação de solicitações de pesquisa é importante porque se você executar continuamente as mesmas solicitações de pesquisa, o cache de dados será iniciado fazer com que o desempenho fique melhor do que ela pode com uma consulta mais diferentes definida.

> [AZURE.NOTE] [Testes de carga de Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) é uma maneira realmente boa para realizar os testes de referência que permite a você executar solicitações HTTP que seria necessário para executar consultas em relação a pesquisa do Azure e permite paralelização de solicitações.

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Dimensionamento Azure procurar taxas de consulta alto e limitado solicitações

Quando você estiver recebendo muitas solicitações limitadas ou excede as taxas de latência de destino de uma carga de consulta maior, você pode procurar para diminuir taxas de latência em uma das duas maneiras:

1. **Aumentar réplicas:**  Uma réplica é semelhante a uma cópia dos seus dados permitindo a pesquisa do Azure para carregar solicitações de saldo contra várias cópias.  Todos balanceamento de carga e replicação de dados em réplicas é gerenciada pela pesquisa do Azure e você pode alterar o número de réplicas alocado para o serviço a qualquer momento.  Você pode alocar até 12 réplicas em um serviço de pesquisa padrão e 3 em um serviço de pesquisa básica.  Réplicas podem ser ajustadas a partir do [Portal Azure](search-create-service-portal.md) ou usando a [API de gerenciamento de pesquisa do Azure](search-get-started-management-api.md).

2. **Aumentar nível de pesquisa:**  Pesquisa Azure vem em um [número de níveis](https://azure.microsoft.com/pricing/details/search/) e cada um desses níveis oferece níveis diferentes de desempenho.  Em alguns casos, você pode ter tantas consultas que a camada que você está em não pode fornecer taxas de suficientemente baixa latência, mesmo quando réplicas estão esgotando.  Nesse caso, você talvez queira considerar aproveitando um dos níveis mais altos de pesquisa como a camada de S3 de pesquisa do Azure que é adequado para cenários com grandes quantidades de documentos e cargas de trabalho de consulta muito alta.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Dimensionamento pesquisar Azure lentas consultas individuais

Outro motivo por que as taxas de latência podem ser lentas é de uma única consulta levando muito tempo para ser concluída.  Nesse caso, a adição de réplicas não aumentará taxas de latência.  Para esse caso há duas opções disponíveis:

1. **Aumentar partições** Uma partição é um mecanismo para divisão seus dados entre recursos extras.  Por esse motivo, quando você adiciona uma segunda partição, seus dados obtém divididos em dois.  Uma terceira partição divide seu índice em três, etc.  Isso também tem o efeito que em alguns casos, consultas lentas executará mais rápido devido a paralelização de computação.  Há alguns exemplos de onde temos visto este paralelização funcionam muito bem com consultas que tenham consultas de baixa seletividade.  Isso consiste em consultas que correspondam a vários documentos ou quando faceting precisa fornecer contagens pela grande número de documentos.  Como há muitas computação necessária para pontuação a relevância dos documentos ou para contar o número de documentos, adicionando partições extras pode ajudar a fornecer computação adicional.  

   Pode haver um máximo de 12 partições no serviço de pesquisa padrão e 1 partição no serviço de pesquisa básica.  Partições podem ser ajustadas a partir do [Portal Azure](search-create-service-portal.md) ou usando a [API de gerenciamento de pesquisa do Azure](search-get-started-management-api.md).

2. **Limitar os campos de alta cardinalidade:** Um campo de cardinalidade alta consiste em um facetable ou filtrável campo que tem um número significativo de valores exclusivos e como resultado, leva vários recursos para calcular resultados sobre.   Por exemplo, definir um campo de ID do produto ou a descrição como facetable/filtráveis faria para cardinalidade alta porque a maioria dos valores de documento ao documento é exclusiva. Sempre que possível, limite o número de campos de cardinalidade alta.

3. **Aumentar nível de pesquisa:**  Mover para cima até um nível superior do Azure pesquisa pode ser outra maneira de melhorar o desempenho das consultas lentas.  Cada nível superior também fornece CPU mais rápida e mais memória que pode ter um impacto positivo no desempenho de consulta.

## <a name="scaling-for-availability"></a>Dimensionamento de disponibilidade

Réplicas não apenas ajudam a reduzir a latência da consulta, mas também podem permitir alta disponibilidade.  Com uma única réplica, você deve esperar periódico tempo de inatividade devido a reinicialização do servidor após atualizações de software ou para outros eventos de manutenção que ocorram.  Como resultado, é importante levar em consideração se o seu aplicativo requer alta disponibilidade de pesquisas (consultas), além de gravações (eventos de indexação).  Pesquisa Azure oferece opções de SLA sobre todas as ofertas de pesquisa paga com os seguintes atributos:

- 2 réplicas para alta disponibilidade das cargas de trabalho de somente leitura (consultas)
- réplica 3 ou mais para alta disponibilidade das cargas de trabalho de leitura e gravação (consultas e indexação)

Para obter mais detalhes sobre isso, visite o [Contrato de nível de serviço de pesquisa do Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Como réplicas são cópias de seus dados, ter várias réplicas permite a pesquisa do Azure para máquina reinicialização e manutenção contra uma réplica ao mesmo tempo enquanto permitir consultas continuar a ser executada em outras réplicas.  Por que motivo, você também precisa considerar como esse tempo de inatividade pode afetar as consultas que agora precisam ser executada em um menos cópia dos dados.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Dimensionamento distribuídos geográfica cargas de trabalho e forneça a redundância geográfica

Para cargas de trabalho distribuído por localização geográfica, você encontrará que usuários localizados longe do data center onde seu serviço de pesquisa do Azure está hospedado terá taxas maiores de latência.  Por isso, muitas vezes é importante ter vários serviços de pesquisa em regiões que estão mais próximo a esses usuários.  Pesquisa Azure atualmente não fornecem um método automatizado de replicação de localização geográfica índices de pesquisa do Azure em regiões, mas há algumas técnicas que podem ser usadas que podem tornar esse processo simples para implementar e gerenciar. Eles são organizados nas próximas seções.

O objetivo de um conjunto distribuído de localização geográfica de serviços de pesquisa é ter dois ou mais índices disponíveis em duas ou mais regiões onde um usuário será roteado para o serviço de pesquisa do Azure que fornece a latência mais baixa, como visto neste exemplo:

   ![Tabela de referência cruzada de serviços por região][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Manter os dados em sincronia entre vários serviços de pesquisa do Azure

Há duas opções para manter seus serviços de pesquisa distribuída em sincronia que consistem usando o [Indexador de pesquisa do Azure](search-indexer-overview.md) ou a API do Push (também conhecido como a [API de REST de pesquisa do Azure](https://msdn.microsoft.com/library/dn798935.aspx)).  

### <a name="azure-search-indexers"></a>Pesquisa Azure indexadores

Se você estiver usando o indexador de pesquisa do Azure, você já estiver importando alterações nos dados de um armazenamento de dados central como DB do SQL Azure ou DocumentDB. Quando você cria uma nova pesquisa serviço, você simplesmente também criar um novo indexador de pesquisa do Azure para esse serviço que aponta para esse mesmo armazenamento de dados. Dessa maneira, sempre novas alterações vêm para o armazenamento de dados, eles serão depois indexados por de vários indexadores.  

Aqui está um exemplo de como essa arquitetura ficaria.

   ![Única fonte de dados com indexador distribuído e combinações de serviço][2]


### <a name="push-api"></a>API de envio 
Se você estiver usando a API de envio por Push do Azure pesquisa para [atualizar o conteúdo no índice de pesquisa do Azure](https://msdn.microsoft.com/library/dn798930.aspx), você pode manter sua vários serviços de pesquisa em sincronia Enviando alterações para todos os serviços de pesquisa sempre que uma atualização é necessária.  Quando isso é importante garantir que lidar com casos em que uma atualização do serviço de uma pesquisa falha e bem-sucedida de uma ou mais atualizações.

## <a name="leveraging-azure-traffic-manager"></a>Aproveitando Azure Gerenciador de tráfego

[Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) permite para rotear solicitações para vários sites localização geográfica que contam por vários serviços de pesquisa do Azure.  Uma vantagem do Gerenciador de tráfego é que ela pode teste pesquisa do Azure para garantir que ela está disponível e rotear os usuários aos serviços de pesquisa alternativos em caso de tempo de inatividade.  Além disso, se você estiver roteamento de solicitações de pesquisa por meio de Sites do Azure, Azure tráfego Manager permite que você carregue os casos de saldo onde o site está para cima, mas não pesquisa do Azure.  Aqui está um exemplo do que a arquitetura que utiliza o tráfego Manager.

   ![Tabela de referência cruzada de serviços por região, com o Gerenciador de tráfego central][3]

## <a name="monitoring-performance"></a>Monitorar o desempenho

Pesquisa Azure oferece a capacidade de analisar e monitorar o desempenho do seu serviço por meio de [Análise de tráfego de pesquisa (STA)](search-traffic-analytics.md). Por meio de STA, você pode opcionalmente efetuar operações de pesquisa individuais, bem como métricas agregadas a uma conta de armazenamento do Azure que pode ser processada para análise ou visualizada no Power BI.  Uso de métricas STA, você pode examinar estatísticas de desempenho como o número médio de consultas ou tempos de resposta de consulta.  Além disso, o log de operações permite que você analisar detalhes de operações de pesquisa específicos.

STA é uma ferramenta valiosa para compreender as taxas de latência dessa perspectiva de pesquisa do Azure.  Como as métricas de desempenho de consulta conectadas são baseadas no período de tempo que uma consulta leva para ser totalmente processadas na pesquisa do Azure (do momento que seja solicitado quando ela é enviada), você será capaz de usar isso para determinar se os problemas de latência são a do lado do serviço de pesquisa do Azure ou fora do serviço, como de latência de rede.  

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os limites de serviços e níveis de preços para cada um deles, consulte [limites de serviço na pesquisa do Azure](search-limits-quotas-capacity.md).

Visite o [planejamento da capacidade](search-capacity-planning.md) para saber mais sobre as combinações de partição e réplica.

Para busca mais detalhada sobre o desempenho e para ver algumas demonstrações de como implementar as otimizações abordadas neste artigo, assista a este vídeo:

> [AZURE.VIDEO azurecon-2015-azure-search-best-practices-for-web-and-mobile-applications]

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png