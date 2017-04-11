<properties
    pageTitle="A saída JSON para análise de fluxo | Microsoft Azure"
    description="Saiba como a análise de fluxo pode direcionar o Azure DocumentDB para saída JSON, para arquivamento de dados e consultas de baixa latência em dados JSON não estruturados."
    keywords="Saída JSON"
    documentationCenter=""
    services="stream-analytics,documentdb"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="target-azure-documentdb-for-json-output-from-stream-analytics"></a>Destino do Azure DocumentDB à saída JSON da análise de fluxo

Análise de fluxo pode direcionar [DocumentDB do Azure](https://azure.microsoft.com/services/documentdb/) para JSON saída, permitindo que consultas de baixa latência e de arquivamento de dados em dados JSON não estruturados. Saiba como implementar essa integração melhor.

Para aqueles que não estão familiarizados com DocumentDB, dê uma olhada no [caminho de aprendizagem do DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para começar.

## <a name="basics-of-documentdb-as-an-output-target"></a>Noções básicas de DocumentDB como um destino de saída
A saída do Azure DocumentDB a análise de fluxo permite escrita seu fluxo de processamento de resultados como saída JSON para seus conjuntos de que DocumentDB. Análise de fluxo não criar conjuntos no seu banco de dados, em vez disso, exigir que você crie-los antecipadamente. Isso é para que os custos de cobranças de conjuntos de DocumentDB são transparentes para você e para que você pode ajustar o desempenho, a consistência e a capacidade dos seus conjuntos diretamente usando as [APIs DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx). É recomendável usar um banco de dados de DocumentDB por fluxo de trabalho para separar logicamente seus conjuntos de um fluxo de trabalho.

Algumas das opções de conjunto de DocumentDB são detalhadas abaixo.

## <a name="tune-consistency-availability-and-latency"></a>Ajustar a consistência, disponibilidade e latência

Para corresponder às suas necessidades de aplicativo, DocumentDB permite fino ajustar o banco de dados e conjuntos e fazer concessões entre consistência, disponibilidade e latência. Dependendo de quais níveis de consistência de leitura as necessidades do seu cenário em relação a ler e gravar latência, que você pode escolher um nível de consistência em sua conta do banco de dados. Também por padrão, DocumentDB permite a indexação síncrono em cada operação CRUD à sua coleção. Essa é outra opção úteis para controlar o desempenho de leitura/gravação em DocumentDB. Para obter mais informações sobre este tópico, leia o artigo de [alterar os níveis de consistência do banco de dados e consulta](../documentdb/documentdb-consistency-levels.md) .

## <a name="choose-a-performance-level"></a>Escolha um nível de desempenho

Conjuntos de DocumentDB podem ser criados em 3 níveis de desempenho diferentes (S1, S2 ou S3), que determina a taxa de transferência disponível para CRUDs a esse conjunto. Além disso, o desempenho é afetado pelos níveis de indexação/consistência em seu conjunto. Consulte [Este artigo](../documentdb/documentdb-performance-levels.md) para entender esses níveis de desempenho em detalhes.

## <a name="upserts-from-stream-analytics"></a>Upserts de análise de fluxo

Integração de análise de fluxo com DocumentDB permite que você inserir ou atualizar registros em seu conjunto de DocumentDB com base em uma determinada coluna ID do documento. Isso também é conhecido como um *Upsert*.

Análise de fluxo utiliza uma abordagem Upsert otimista, onde as atualizações são feitas somente quando inserir falha devido a um conflito de ID do documento. Essa atualização é executada pela análise de fluxo como um PATCH, para que ele permite atualizações parciais para o documento, ou seja, a adição de novas propriedades ou substituir que uma propriedade existente é executada de forma incremental. Observe que as alterações nos valores de propriedades de matriz no seu resultado de documento JSON em toda a matriz obtendo sobrescrito, ou seja, a matriz não é mesclado.

## <a name="data-partitioning-in-documentdb"></a>Dados partição no DocumentDB

Conjuntos de DocumentDB permitem que você partição seus dados com base nos padrões de consulta e necessidades de desempenho do seu aplicativo. Cada coleção pode conter até 10GB de dados (máximo) e atualmente não há nenhuma maneira para dimensionar (ou estouro) em um conjunto. Para dimensionar, a análise de fluxo permite que você escreva para vários conjuntos com um determinado prefixo (consulte detalhes de uso abaixo). Análise de fluxo usa a estratégia de [Resolução de partição de Hash](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) consistente baseada no usuário fornecido PartitionKey coluna para partição seus registros de saída. O número de coleções com o prefixo determinado na hora de início do trabalho streaming é usado como a contagem de partição de saída, para que o trabalho grava em paralelo (conjuntos de DocumentDB = partições de saída). Para um único conjunto de S3 com preguiçoso fazendo indexação somente insere, sobre 0,4 taxa de transferência de gravação de MB/s pode ser esperada. Usar vários conjuntos pode permitir que você atingir maior capacidade e maior produtividade.

Se você pretende aumentar a contagem de partição no futuro, talvez você precise interromper o seu trabalho, reparticionar os dados de seus conjuntos de existentes em novos conjuntos e reinicie o trabalho de análise de fluxo. Mais detalhes sobre como usar PartitionResolver e solicitada uma nova partição juntamente com código de amostra, serão incluídos em uma postagem de acompanhamento. Também é o artigo [partição em DocumentDB](../articles/documentdb-partition-data.md#developing-a-partitioned-application) fornece detalhes sobre isso.

## <a name="documentdb-settings-for-json-output"></a>Configurações de DocumentDB para saída JSON

Criando DocumentDB como uma saída no fluxo Analytics gera um aviso para obter informações conforme mostrado abaixo. Esta seção fornece uma explicação da definição de propriedades.

![tela de saída de análise de fluxo de documentdb](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output.png)  

-   **Alias de saída** – um alias para se referir a saída na sua consulta ASA  
-   **Nome da conta** – o nome ou o ponto de extremidade URI da conta DocumentDB.  
-   **Chave da conta** – a tecla de acesso compartilhado para a conta de DocumentDB.  
-   **Banco de dados** – DocumentDB o nome do banco de dados.  
-   **Padrão de nome de coleção** – o padrão de nome de conjunto das coleções de arquivos a ser usado. O formato de nome do conjunto pode ser construído usando o token de opcional {partição}, onde partições Iniciar de 0. A seguir estão entradas válidas de amostra:  
   1\) MyCollection – um conjunto nomeado "MyCollection" deve existir.  
   2\) MyCollection {partição} – tais conjuntos devem existir – "MyCollection0", "MyCollection1", "MyCollection2" e assim por diante.  
-   **Chave de partição** – o nome do campo em eventos de saída usado para especificar a chave para a partição de saída entre conjuntos. Para saída única coleção, qualquer coluna saída aleatório pode ser usado por exemplo PartitionId.  
-   **ID do documento** – opcional. O nome do campo em eventos de saída usado para especificar a chave primária no qual inserir ou atualizar operações se baseiam.  
