Fábrica de dados é um serviço de vários locatário que tem os seguintes limites de padrão no lugar para certificar-se de assinaturas de cliente estão protegidas contra uns dos outros cargas de trabalho. Muitos dos limites podem ser facilmente elevados para sua assinatura para cima até o limite máximo contatando o suporte. 

**Recurso** | **Limite padrão** | **Limite máximo**
-------- | ------------- | -------------
fábricas de dados em uma assinatura do Azure | 50 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
canais dentro de uma fábrica de dados | 2500 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
conjuntos de dados dentro de uma fábrica de dados | 5000 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
fatias simultâneas por conjunto de dados | 10 | 10
bytes por objeto para objetos de pipeline <sup>1</sup> | 200 KB | 2000 KB
bytes por objeto de conjunto de dados e objetos de serviço vinculada <sup>1</sup> | 100 KB | 2000 KB
Cores de cluster sob demanda HDInsight dentro de uma assinatura <sup>2</sup> | 48 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Nuvem de unidade de movimentação de dados <sup>3</sup> | 8 | [Contate o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Repetir contagem para execução de atividade de pipeline | 1000 | MaxInt (32 bits)

<sup>1</sup> pipeline, conjunto de dados e objetos de serviço vinculada representam um agrupamento lógico de sua carga de trabalho. Limites para esses objetos não estão relacionadas a quantidade de dados que você pode mover de processo com o serviço de fábrica de dados do Azure. Fábrica de dados foi projetada para dimensionar tratar petabytes de dados.

<sup>2</sup> HDInsight sob demanda cores estão alocados fora a assinatura que contém a fábrica de dados. Como resultado, o limite acima é fábrica dados impostas limite de núcleo para cores de HDInsight sob demanda e é diferente do limite core associado a sua assinatura do Azure.

<sup>3</sup> unidade de movimentação de dados de nuvem (DMU —) está sendo usada em uma operação de cópia de nuvem na nuvem. É uma medida que representa a potência (uma combinação de CPU, memória e alocação de recursos de rede) de uma única unidade na fábrica de dados. Você pode obter maior produtividade de cópia aproveitando mais DMUs para alguns cenários. Consulte a seção de [unidades de movimentação de dados de nuvem](../../articles/data-factory/data-factory-copy-activity-performance.md#cloud-data-movement-units) em detalhes.

**Recurso** | **Limite inferior padrão** | **Limite mínimo**
-------- | ------------------- | -------------
Intervalo de agendamento | 15 minutos | 15 minutos
Intervalo entre tentativas de repetição | 1 segundo | 1 segundo
Repetir o valor de tempo limite | 1 segundo | 1 segundo


### <a name="web-service-call-limits"></a>Limites de chamada de serviço Web

Gerenciador de recursos de Azure tem limites para chamadas de API. Você pode fazer chamadas de API em um ritmo dentro dos [limites de API do Gerenciador de recursos do Azure](../azure-subscription-service-limits.md#resource-group-limits). 


