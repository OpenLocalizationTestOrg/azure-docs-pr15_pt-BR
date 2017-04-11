<properties
    pageTitle="Escolha uma SKU ou preços camada para pesquisa de Azure | Microsoft Azure"
    description="Pesquisa Azure pode ser provisionada nestes SKUs: livre, básicas e padrão, onde padrão está disponível em várias configurações de recursos e níveis de capacidade."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="10/24/2016"
    ms.author="heidist"/>

# <a name="choose-a-sku-or-pricing-tier-for-azure-search"></a>Escolha uma SKU ou nível de preço para pesquisa do Azure

Em pesquisa do Azure, um [serviço provisionado](search-create-service-portal.md) em um nível de preços específico ou SKU. Opções incluem **livre**, **Basic**ou **padrão**, onde **Standard** está disponível em várias configurações e capacidades. 

O objetivo deste artigo é para ajudá-lo a escolher um nível. Se a capacidade de um nível acaba por ser muito baixo, você precisará provisionar um novo serviço no nível superior e recarregar seus índices. Não há nenhuma atualização in-loco do serviço mesmo de uma SKU para outro. 

> [AZURE.NOTE] Depois de escolher uma camada e [provisionar um serviço de pesquisa](search-create-service-portal.md), você pode aumentar réplica e partição conta dentro do serviço. Para obter orientação, consulte [níveis de recursos de escala para cargas de trabalho de indexação e de consulta](search-capacity-planning.md).

## <a name="how-to-approach-a-pricing-tier-decision"></a>Como abordar uma decisão de nível de preço

Pesquisa do Azure, a camada determina capacidade, não a disponibilidade de recursos. Em geral, os recursos estão disponíveis em cada nível, incluindo os recursos de visualização. A única exceção é não há suporte para indexadores em HD S3.

> [AZURE.TIP] Recomendamos que você sempre provisionar um serviço de **livre** (um por assinatura, sem prazo de expiração) para que seu prontamente disponível para projetos de simplificada. Usar o serviço de **livre** para teste e avaliação; Crie um segundo serviço faturável na camada **básica** ou **padrão** para produção ou maiores cargas de trabalho de teste.

Capacidade e custos de executando o serviço vá lado a lado. Informações neste artigo podem ajudá-lo a decidir qual SKU oferece o equilíbrio certo, mas para qualquer uma dela seja útil, você precisa estimativas aproximadas pelo menos o seguinte:

- Número e tamanho dos índices que você planeja criar
- Número e tamanho dos documentos para carregar
- Alguns ideia de volume de consulta, em termos de consultas por segundo (QPS)

Número e tamanho são importantes porque limites máximos são acessados por meio de um limite rígido na contagem de índices ou documentos em um serviço ou em recursos (armazenamento ou réplicas) usados pelo serviço. O limite real de seu serviço é aquele que obtém usado primeiro: recursos ou objetos.

Com estimativas em mãos, as seguintes etapas devem simplificar o processo:

- **Etapa 1** Examine as descrições de SKU abaixo para saber sobre as opções disponíveis.
- **Etapa 2** Perguntas a seguir para chegar a uma decisão preliminar.
- **Etapa 3** Finalize sua decisão revisando rígidos limites de armazenamento e preços.

## <a name="sku-descriptions"></a>Descrições de SKU

A tabela a seguir fornece descrições de cada nível. 

Camada|Cenários principais
----|-----------------
**Livre**|Um serviço compartilhado, sem nenhum custo, usado para avaliação, investigações ou pequenas cargas de trabalho. Porque ele é compartilhado com outros assinantes, produtividade de consulta e indexação varia de acordo com quem mais está usando o serviço. Capacidade é pequena (50 MB ou 3 índices com backup 10.000 documentos cada).
**Básico**|Cargas de trabalho de produção pequenas em hardware dedicado. Altamente disponível. A capacidade é até 3 réplicas e 1 partição (2 GB).
**S1**|1 padrão suporta flexíveis combinações de partições (12) e réplicas (12), usadas para cargas de trabalho de produção média em hardware dedicado. Você pode alocar partições e no combinações compatíveis com um número máximo de unidades de pesquisa faturáveis 36. Nesse nível, partições são 25 GB e QPS é aproximadamente 15 consultas por segundo.
**S2**|2 padrão é executado maiores cargas de trabalho de produção usando as mesmas unidades de 36 pesquisa S1 mas com réplicas e partições tamanhos maiores. Nesse nível, partições são 100 GB e QPS é cerca de 60 consultas por segundo.
**S3**|3 padrão é executado proporcionalmente maiores cargas de trabalho de produção no maiores sistemas de fim, em configurações de até 12 partições ou 12 réplicas unidades de pesquisa em 36. Nesse nível, partições são 200 GB e QPS é mais de 60 consultas por segundo. 
**S3 HD**|3 padrão de alta densidade destina-se um grande número de índices menores. Você pode ter até 3 partições, cada 200 GB. QPS é mais de 60 consultas por segundo. 

> [AZURE.NOTE] Máximos de réplica e partição serão cobrados check-out como unidades de pesquisa (36 unidade máximo por serviço), que impõe um limite inferior eficaz que o que significa o máximo pelo valor nominal. Por exemplo, para usar o máximo de 12 réplicas, você poderia ter no máximo 3 partições (12 * 3 = 36 unidades). Da mesma forma, para usar partições máximos, reduza réplicas para 3. Consulte [níveis de recursos de escala para cargas de trabalho de indexação na pesquisa do Azure e de consulta](search-capacity-planning.md) para um gráfico em combinações permitidos.

## <a name="review-limits-per-tier"></a>Limites de revisão por nível

O gráfico a seguir é um subconjunto dos limites de [Limites de serviço na pesquisa do Azure](search-limits-quotas-capacity.md). Lista os fatores maior probabilidade de afetar uma decisão SKU. Você pode fazer referência a esse gráfico ao revisar as perguntas abaixo.

Recurso|Livre|Básico|S1|S2|S3 |S3 HD
---|---|---|---|----|---|----
Contrato de nível de serviço (SLA)|Não <sup>1</sup> |Sim |Sim  |Sim |Sim  |Sim 
Limites de índice|3|5|50|200|200|1000 <sup>2</sup>
Limites do documento|total de 10.000|1 milhão por serviço|15 milhões por partição |60 milhões por partição|120 milhões por partição |1 milhão por índice
Partições máximos|N/D |1 |12  |12 |12|3 <sup>2</sup>
Tamanho da partição|Total de 50 MB|2 GB por serviço|25 GB por partição |100 GB por partição (até no máximo 1,2 TB por serviço)|200 GB por partição (até no máximo 2,4 TB por serviço)|200 GB (até no máximo 600 GB por serviço)
Réplicas máximos|N/D |3 |12 |12 |12|12
Consultas por segundo|N/D|~ 3 por réplica|aproximadamente 15 por réplica|~ 60 por réplica|> 60 por réplica|> 60 por réplica

<sup>1</sup> SKUs de visualização e livre não acompanham SLAs. SLAs são aplicados quando uma SKU fique disponível.

<sup>2</sup> S3 e S3 HD são copiados infraestrutura de alta capacidade idênticos, mas cada um deles atinja o limite máximo de maneiras diferentes. S3 destina-se um número menor de índices muito grandes. Assim, o limite máximo é recurso ligadas (2,4 TB para cada serviço). S3 HD destina-se um grande número de índices muito pequenos. Nos índices de 1.000, S3 HD atinja os limites do formulário de restrições de índice. Se você for um cliente S3 HD que requer mais de 1.000 índices, contate o Microsoft Support para obter informações sobre como proceder.

## <a name="eliminate-skus-that-dont-meet-requirements"></a>Eliminar SKUs que não atende aos requisitos 

As questões a seguir podem ajudá-lo a chegar à decisão de SKU certa para sua carga de trabalho.

1. Você tem requisitos de **Contrato de nível de serviço (SLA)** ? Restringir a decisão de SKU padrão básico ou não-preview.
2. **Quantos índices** faça você exigir? Uma das variáveis maiores levando em uma decisão SKU é o número de índices suportada por cada SKU. Suporte de índice está em significativamente diferentes níveis em camadas preços inferiores. Requisitos do número de índices podem ser um primário determinante de uma decisão de SKU.
3. **Quantos documentos** serão carregados em cada índice? O número e o tamanho dos documentos determinará o tamanho eventual do índice. Considerando que você pode estimar o tamanho projetado do índice, você pode comparar esse número com o tamanho da partição por SKU, estendida pelo número de partições necessários para armazenar um índice desse tamanho. 
4. **O que é a carga de consulta esperado**? Depois de requisitos de armazenamento são compreendidos, considere cargas de trabalho de consulta. S2 e duas SKUs S3 oferecem produtividade perto equivalente, mas os requisitos de SLA serão descartar qualquer visualização SKUs. 
5. Se você estiver considerando a camada S2 ou S3, determine se você precisa [indexadores](search-indexer-overview.md). Indexadores ainda não estão disponíveis para a camada de HD S3. Abordagem alternativa é usar um modelo de envio para atualizações de índice, onde você escreve código do aplicativo para enviar um conjunto de dados para um índice.

A maioria dos clientes pode regra uma SKU específica ou reduzir com base em suas respostas para as perguntas acima. Se você ainda não tiver certeza qual SKU para ir com, você pode postar perguntas nos fóruns do MSDN ou StackOverflow ou contate o suporte do Azure para obter mais orientações.

## <a name="decision-validation-does-the-sku-offer-sufficient-storage-and-qps"></a>Validação de decisão: o SKU oferece QPS e armazenamento suficiente?

Como última etapa, revise a [página de preços](https://azure.microsoft.com/pricing/details/search/) e as [seções por serviço e por índice em limites de serviço](search-limits-quotas-capacity.md) para verificar suas estimativas contra limites de assinatura e de serviço. 

Se o preço ou o armazenamento requisitos estão fora dos limites, talvez você queira refatorar as cargas de trabalho entre vários serviços menores (por exemplo). No nível mais granular, você poderia reformular índices para ser menor ou usar filtros para tornar as consultas mais eficiente.

> [AZURE.NOTE] Requisitos de armazenamento podem ser excessiva inflated se documentos contiverem dados estranhos. Ideal seria documentos contêm apenas dados pesquisáveis ou metadados. Dados binários não pesquisável e devem ser armazenados separadamente (talvez em um armazenamento de tabela ou blob Azure) com um campo no índice para manter uma referência de URL para os dados externos. O tamanho máximo de um documento individual é 16 MB (ou menos quando você está em massa ao carregar vários documentos em uma solicitação). Consulte [limites de serviço na pesquisa do Azure](search-limits-quotas-capacity.md) para obter mais informações.

## <a name="next-step"></a>Próxima etapa

Quando você souber qual SKU é a opção adequada, prossiga com estas etapas:

- [Criar um serviço de pesquisa no portal](search-create-service-portal.md)
- [Alterar a alocação de partições e réplicas dimensionar seu serviço](search-capacity-planning.md)

