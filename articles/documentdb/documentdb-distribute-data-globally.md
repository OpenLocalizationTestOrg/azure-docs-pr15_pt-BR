<properties
   pageTitle="Distribuir dados globalmente com DocumentDB | Microsoft Azure"
   description="Saiba mais sobre a recuperação de dados, failover e replicação geográfica de escala planeta usando globais bancos de dados do Azure DocumentDB, um serviço de banco de dados NoSQL totalmente gerenciado."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="kipandya"/>
   
   
# <a name="distribute-data-globally-with-documentdb"></a>Distribuir dados globalmente com DocumentDB

> [AZURE.NOTE] Distribuição global de bancos de dados de DocumentDB é geralmente disponível e automaticamente habilitado para todas as contas DocumentDB recém-criado. Estamos trabalhando para habilitar a distribuição global em todas as contas existentes, mas enquanto isso, se quiser que a distribuição global habilitada em sua conta, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e podemos será habilite-lo para você agora.

Azure DocumentDB foi projetado para atender às necessidades dos aplicativos IoT consiste milhões de dispositivos distribuídos globalmente e aplicativos de escala de internet que fornecem experiências altamente ágil aos usuários em todo o mundo. Esses sistemas de banco de dados enfrentam o desafio de atingir o acesso de baixa latência aos dados do aplicativo de várias regiões geográficas com garantias de consistência e a disponibilidade de dados bem definida. Como um sistema de banco de dados globalmente distribuído, DocumentDB simplifica a distribuição global de dados, proporcionando as contas de banco de dados totalmente gerenciado, várias regiões que fornecem limpar compensações entre consistência, disponibilidade e desempenho, todos com garantias correspondentes. Contas de banco de dados de DocumentDB são oferecidas com alta disponibilidade, latências de ms único dígito, vários [níveis de consistência bem definido] [consistency], failover regional transparente com APIs múltiplas home pages e a capacidade de dimensionar consumidor produtividade e armazenamento em todo o mundo. 

  
## <a name="configuring-multi-region-accounts"></a>Configurar contas de várias regiões

Configurar sua conta de DocumentDB dimensionar em todo o mundo pode ser feito em menos de um minuto por meio do [portal do Azure](documentdb-portal-global-replication.md). Tudo o que você precisa fazer é selecionar o nível de consistência direita entre vários níveis de consistência bem definida com suporte e associar qualquer número de regiões Azure à sua conta de banco de dados. Níveis de consistência DocumentDB fornecem limpar compensações entre garantia de consistência específico e desempenho. 

![DocumentDB oferece vários bem definidas modelos de consistência (reduzida) à sua escolha][1]

Bem DocumentDB oferece vários definidas modelos de consistência (reduzida) à sua escolha.

Selecionar o nível de consistência direito depende de dados garantir a consistência necessidades de seu aplicativo. DocumentDB automaticamente replica seus dados em todas as regiões especificadas e garante a consistência que você selecionou para a sua conta de banco de dados. 


## <a name="using-multi-region-failover"></a>Usando várias regiões failover 

DocumentDB Azure é capaz de contas do banco de dados de failover transparente em várias regiões Azure – as novas [APIs múltiplas home pages] [ developingwithmultipleregions] garantia que seu aplicativo pode continuar a usar um ponto de extremidade lógico e não é interrompido pelo failover. Failover é controlado por você, fornecendo flexibilidade para colocar seu banco de dados em outro lugar de conta no evento qualquer dos intervalo de condições de falha possíveis ocorrer, incluindo falhas de aplicativos, infraestrutura, serviço ou regionais (reais ou simuladas). Em caso de falha de regionais DocumentDB, o serviço transparente falhará sobre sua conta de banco de dados e seu aplicativo continua acessar dados sem perder a disponibilidade. Enquanto DocumentDB oferece [disponibilidade de 99,99% SLAs][sla], você pode testar o final do seu aplicativo para propriedades de extremidade de disponibilidade por simular uma falha regional ambos, [programaticamente] [ arm] bem como por meio do Portal do Azure.


## <a name="scaling-across-the-planet"></a>Dimensionamento no planeta
DocumentDB permite independentemente provisionar produtividade e consumir armazenamento para cada conjunto de DocumentDB em qualquer escala, globalmente em todas as regiões associadas a uma conta de banco de dados. Uma coleção de DocumentDB é automaticamente distribuída globalmente e gerenciada em todas as regiões associadas a uma conta de banco de dados. Coleções dentro de sua conta de banco de dados podem ser distribuídas entre qualquer uma das regiões Azure no qual o [serviço de DocumentDB está disponível][serviceregions]. 

A produtividade adquirido e armazenamento consumida para cada conjunto de DocumentDB está automaticamente provisionado em todas as regiões igualmente. Isso permite que seu aplicativo perfeitamente dimensionar entre o globo [pagando somente para a produtividade e armazenamento que você está usando em cada hora][pricing]. Por exemplo, se você tiver provisionado RUs de 2 milhões de um conjunto de DocumentDB, em seguida, cada uma das regiões associados a uma conta de banco de dados obtém RUs de 2 milhões para esse conjunto. Isso é ilustrado abaixo.

![Taxa de transferência de dimensionamento para um conjunto de DocumentDB em quatro regiões][2]

DocumentDB garante < 10 ms ler e < 15 ms escrever latências em P99. As solicitações de leitura nunca abrangem limite de data center para garantir os [requisitos de consistência que você selecionou][consistency]. As gravações são sempre quorum confirmada localmente antes de serem reconhecidas aos clientes. Cada conta de banco de dados está configurada com prioridade de região de gravação. A região designada com prioridade mais alta irá atuar como a região atual de gravação para a conta. Todos os SDKs transparente encaminhará gravações de conta do banco de dados para a região atual de gravação. 

Finalmente, desde que DocumentDB é completamente [independente de esquema] [ vldb] -você nunca tenha que me preocupar Gerenciando/atualização de esquemas ou índices secundários vários data centers. Suas [consultas SQL] [ sqlqueries] continuar a trabalhar enquanto seu aplicativo e modelos de dados continuam a evoluir. 


## <a name="enabling-global-distribution"></a>Habilitando a distribuição global 

Você pode optar por fazer seus dados localmente ou globalmente distribuídos associando qualquer conta de banco de dados de uma ou mais regiões Azure com um DocumentDB. Você pode adicionar ou remover regiões à sua conta de banco de dados a qualquer momento. Para habilitar a distribuição global usando o portal, veja [como executar replicação de banco de dados global de DocumentDB usando o portal do Azure](documentdb-portal-global-replication.md). Para habilitar a distribuição global programaticamente, consulte [desenvolvimento com contas de DocumentDB de várias regiões](documentdb-developing-with-multiple-regions.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os dados distribuindo globalmente com DocumentDB nos seguintes artigos:

* [Taxa de transferência e armazenamento de um conjunto de provisionamento] [throughputandstorage]
* [APIs múltiplas home pages via restante. .NET, Java, Python e SDKs de nó] [developingwithmultipleregions]
* [Níveis de consistência em DocumentDB] [consistency]
* [SLAs de disponibilidade] [sla]
* [Gerenciar a conta de banco de dados] [manageaccount]

[1]: ./media/documentdb-distribute-data-globally/consistency-tradeoffs.png
[2]: ./media/documentdb-distribute-data-globally/collection-regions.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[pcolls]: documentdb-partition-data.md
[consistency]: documentdb-consistency-levels.md
[consistencytradeooffs]: ./documentdb-consistency-levels/#consistency-levels-and-tradeoffs
[developingwithmultipleregions]: documentdb-developing-with-multiple-regions.md
[createaccount]: documentdb-create-account.md
[manageaccount]: documentdb-manage-account.md
[manageaccount-consistency]: documentdb-manage-account.md#consistency
[throughputandstorage]: documentdb-manage.md
[arm]: documentdb-automation-resource-manager-cli.md
[regions]: https://azure.microsoft.com/regions/
[serviceregions]: https://azure.microsoft.com/en-us/regions/#services 
[pricing]: https://azure.microsoft.com/pricing/details/documentdb/
[sla]: https://azure.microsoft.com/support/legal/sla/documentdb/ 
[vldb]: http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf
[sqlqueries]: documentdb-sql-query.md

