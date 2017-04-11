Você pode criar vários serviços dentro de uma assinatura, cada um deles configurado em um nível específico, limitado somente pelo número de serviços permitidos em cada camada. Por exemplo, você poderia criar até 12 serviços no nível básico e outro 12 na camada S1 dentro da mesma assinatura. Para saber mais sobre níveis, consulte [Escolher uma SKU ou camadas para pesquisa do Azure](../articles/search/search-sku-tier.md).

Limites máximos de serviço podem ser elevados mediante solicitação. Contate o suporte do Azure se você precisar de mais serviços dentro da mesma assinatura.

Recurso|Livre|Básico|S1|S2|S3 |S3 HD <sup>1</sup>
---|---|---|---|----|---|----
Serviços de máximos |1 |12 |12  |6 |6 |6 
Escala máxima no SO <sup>2</sup>|N / <sup>3</sup>|SO 3 <sup>4</sup> |36 SO|36 SO|36 SO|SO 12, 3 SO <sup>5</sup>

<sup>1</sup> S3 HD não suporta [indexadores](../articles/search/search-indexer-overview.md) neste momento. 

<sup>2</sup> unidades de pesquisa (SO) são faturáveis unidades por serviço, alocada como uma *réplica* ou uma *partição*. Você precisa ambos os recursos de armazenamento, indexação e operações de consulta. Para saber mais sobre as combinações válidas que permanecem sob os limites máximos, consulte [níveis de recursos de escala para cargas de trabalho de consulta e índice](../articles/search/search-capacity-planning.md). 

<sup>3</sup> livre se baseia em recursos compartilhados usados por vários assinantes. Neste nível, não há nenhuma recursos dedicados para um assinante individual. Por esse motivo, escala máxima está marcada como não aplicável.

<sup>4</sup> basic tem uma partição fixa. Neste nível, SUs adicional é usado para alocar mais réplicas para cargas de trabalho de aumento na consulta.

<sup>5</sup> S3 HD tem uma estrutura de alocação diferentes em termos de combinações permitidas. Para réplicas, você pode ter um máximo de 12. Para partições, o máximo é 3.




