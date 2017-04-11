<properties
    pageTitle="Tipos de compilação e qualidade de modelo: recomendações API | Microsoft Azure"
    description="Recomendações de aprendizado de máquina Azure - guia de início rápido"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="luisca"/>

#  <a name="build-types-and-model-quality"></a>Tipos de compilação e qualidade de modelo #

<a name="TypeofBuilds"></a>
## <a name="supported-build-types"></a>Tipos de compilação com suporte ##

A API de recomendações atualmente oferece suporte a dois tipos de compilação: *recomendação* e *FBT*. Cada um é criada usando algoritmos diferentes, e cada tem pontos fortes diferentes. Este documento descreve cada um desses compilações e técnicas para comparar a qualidade dos modelos gerado.

Se você tiver não fez isso, recomendamos que você conclua o [guia de início rápido](cognitive-services-recommendations-quick-start.md).

<a name="RecommendationBuild"></a>
### <a name="recommendation-build-type"></a>Tipo de compilação de recomendação ###

O tipo de compilação de recomendação usa fatoração de matriz para fornecer recomendações. Ele gera [recurso latente](https://en.wikipedia.org/wiki/Latent_variable) vetores com base em suas transações para descrever cada item e usa esses vetores latentes para comparar itens que são semelhantes.

Se você treinar o modelo com base em compras feitas no seu armazenamento de eletrônicos e fornece um telefone de 650 Lumia como a entrada para o modelo, o modelo irá retornar um conjunto de itens que tendem a ser adquiridos por pessoas que provavelmente vão comprar um telefone Lumia 650. Os itens podem não estar complementares. Neste exemplo, é possível que o modelo retornará outros telefones porque pessoas que gostam a 650 Lumia podem como outros telefones.

A compilação de recomendação tem dois recursos que tornam mais atraente:

* *Suporta a compilação de recomendação *fria item* posicionamento * *

Itens que não têm uso significativo são chamados fria itens. Por exemplo, se você receber uma remessa de um telefone que você nunca tenha vendido antes, o sistema não pode inferir recomendações para este produto em transações sozinhos. Isso significa que o sistema deverá aprender com informações sobre o produto em si.

Se você quiser usar o posicionamento de item fria, é necessário fornecer informações de recursos para cada um dos seus itens do catálogo. A seguir está as primeiras linhas de seu catálogo podem aparência (formato de anotação a chave = valor para os recursos).

>Pro2 6CX-00001, superfície, superfície, digite = Hardware, armazenamento = 128 GB, memória = 4G, fabricante = Microsoft

>73h-00013, ativação Xbox 360, jogos, digite = Software, Language = inglês, classificação = avançado

>WAH-0F05, Minecraft Xbox 360, jogos, * tipo = Software, Language = espanhol, classificação = jovens

Você também precisa definir os seguintes parâmetros de compilação:

| Parâmetro de compilação         | Anotações
|------------------     |-----------
|*useFeaturesInModel*     | Defina como **true**.  Indica se os recursos podem ser usados para aprimorar o modelo de recomendação.
|*allowColdItemPlacement*   | Defina como **true**. Indica se a recomendação também deve push itens fria via similaridade de recurso.
| *modelingFeatureList*   | Lista separada por vírgulas de nomes de recurso a ser usado na compilação recomendação para aprimorar a recomendação. Por exemplo, "idioma, armazenamento" para que o exemplo anterior.

**Recomendações de usuário dá suporte a compilação de recomendação**

Uma compilação de recomendação suporta [recomendações de usuário](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd). Isso significa que ele pode fornecer recomendações personalizadas para usuários com base em seus históricos de transação. Para obter recomendações de usuário, você pode fornecer a ID de usuário ou o histórico recente de transações para esse usuário.

Um exemplo clássico de onde você talvez queira aplicar recomendações de usuário é ao entrar na página de boas-vindas. Lá, você pode promover conteúdo que se aplica ao usuário específico.

Talvez você também queira aplicar um tipo de compilação recomendações quando o usuário está prestes a fazer check-out. Neste ponto, você tem a lista de itens que o usuário está prestes a comprar, e você pode fornecer recomendações com base nas cesta de mercado atual.

#### <a name="recommendations-build-parameters"></a>Recomendações construir parâmetros

| Nome  |   Descrição |    Tipo de <br>  valores válidos, <br> (valor padrão)
|-------|-------------------|------------------
| *NumberOfModelIterations* |   O número de iterações que efetua o modelo é refletido pelo tempo de computação geral e a precisão do modelo. Quanto maior o número, mais precisa o modelo, mas o tempo de computação demora mais.  |   Número inteiro, <br>  10 a 50 <br>(40)
| *NumberOfModelDimensions* |   O número de dimensões está relacionado ao número de recursos que o modelo tentará localizar em seus dados. Aumentar o número de dimensões permitirá a melhor ajuste fino dos resultados em clusters menores. No entanto, muitas dimensões impedirá o modelo Localizando correlações entre itens. |  Número inteiro, <br> 10 a 40 <br>(20) |
| *ItemCutOffLowerBound* |  Define o número mínimo de um item deve estar no ser considerado parte do modelo de pontos de uso. |        Número inteiro, <br> 2 ou maior <br> (2) |
| *ItemCutOffUpperBound* |  Define o número máximo de pontos de uso que um item deve estar no ser considerado parte do modelo. |  Número inteiro, <br>2 ou maior<br> (2147483647) |
|*UserCutOffLowerBound* |   Define o número mínimo de transações que um usuário deve ter realizado para ser considerado parte do modelo. | Número inteiro, <br> 2 ou maior <br> (2)
| *UserCutOffUpperBound* |  Define o número máximo de transações de que um usuário deve ter realizado para ser considerado parte do modelo. | Número inteiro, <br>2 ou maior <br> (2147483647)|
| *UseFeaturesInModel* |    Indica se os recursos podem ser usados para aprimorar o modelo de recomendação. |     Booliano<br> Padrão: True
|*ModelingFeatureList* |    Lista separados por vírgulas de nomes de recurso a ser usado na compilação recomendação para aprimorar a recomendação. Ele depende dos recursos que são importantes. |    Cadeia de caracteres, até 512 caracteres
| *AllowColdItemPlacement* |    Indica se a recomendação também deve push itens fria via similaridade de recurso. | Booliano <br> Padrão: falso
| *EnableFeatureCorrelation*    | Indica se os recursos podem ser usados em raciocínio. | Booliano <br> Padrão: falso
| *ReasoningFeatureList* |  Lista separados por vírgulas de nomes de recurso a ser usado para raciocínio frases, como explicações de recomendação. Ele depende dos recursos que são importantes para os clientes. | Cadeia de caracteres, até 512 caracteres
| *EnableU2I* | Habilite recomendações personalizadas, também chamadas de usuário para item recomendações (U2I). | Booliano <br>Padrão: True
|*EnableModelingInsights* | Define se avaliação offline deve ser executada para coletar ideias de modelagem (ou seja, métricas de diversidade e precisão). Se definido como true, um subconjunto de dados não será usado para treinamento porque ele precisará ser reservada para teste do modelo. Leia mais sobre [avaliações offline](#OfflineEvaluation). | Booliano <br> Padrão: falso
| *SplitterStrategy* | Se enable ideias de modelagem estiver definido como *true*, isso é como os dados devem ser divididos para fins de avaliação.  | Cadeia de caracteres, *RandomSplitter* ou *LastEventSplitter* <br>Padrão: RandomSplitter


<a name="FBTBuild"></a>
### <a name="fbt-build-type"></a>Tipo de compilação FBT ###

A compilação de (FBT) junto frequentemente comprada faz uma análise que conta o número de vezes dois ou três produtos diferentes conjunta ocorrem juntas. Ele classifica os conjuntos com base em uma função de similaridade (**ocorrências co**, **Jaccard**, **tire**).

Pense **Jaccard** e **tire** como maneiras para normalizar as ocorrências de colegas.  Isso significa que os itens serão retornados somente se eles onde comprou junto com o item de propagação.

Em nosso exemplo de telefone Lumia 650, telefone X será retornado somente se telefone X foi adquirida na mesma sessão como o telefone Lumia 650. Como isso pode ser provável, esperamos que itens complementar para o 650 Lumia a ser retornado; Por exemplo, um protetor de tela ou um adaptador de energia para o 650 Lumia.

Atualmente, os dois itens são considerados ser adquiridos na mesma sessão se ocorrerem em uma transação com a mesma ID de usuário e o carimbo de hora.

FBT compilações não suportam itens fria, porque por definição esperam dois itens a serem comprados na mesma transação. Enquanto FBT compilações podem retornar conjuntos de itens (conjuntos), eles não suportam recomendações personalizadas porque eles aceitarem um item de propagação único como a entrada.


#### <a name="fbt-build-parameters"></a>Parâmetros de construção FBT

| Nome  |   Descrição |       Tipo de  <br> valores válidos, <br> (valor padrão)
|-------|---------------|-----------------------
| *FbtSupportThreshold* | Como conservador o modelo é. Número de ocorrências colegas de itens a serem considerados para modelagem. |  Número inteiro, <br> 3-50 <br> (6)
| *FbtMaxItemSetSize* | Circunda o número de itens em um conjunto frequente.| Número inteiro  <br> 2-3 <br> (2)
| *FbtMinimalScore* | Pontuação mínimo que um conjunto frequente deve ter a serem incluídos no resultados retornados. Maior melhor. | Duplo <br> 0 e acima <br> (0)
| *FbtSimilarityFunction* | Define a função de similaridade para ser usado pela compilação. **Tire** melhora serendipity, **ocorrência co** melhora a capacidade de previsão e **Jaccard** é uma combinação entre os dois. | Cadeia de caracteres,  <br>  <i>cooccurrence, levantar, jaccard</i><br> Padrão: <i>jaccard</i>

<a name="SelectBuild"></a>
## <a name="build-evaluation-and-selection"></a>Seleção e avaliação de compilação ##

Neste guia pode ajudá-lo a determinar se você deve usar uma compilação de recomendações ou uma compilação FBT, mas ele não fornece uma resposta definitiva em casos onde você poderia usar qualquer um deles. Além disso, mesmo se você souber que você deseja usar um tipo de compilação FBT, ainda convém escolher **Jaccard** ou **tire** como a função de similaridade.

A melhor maneira de selecionar entre dois compilações diferentes é testá-las no mundo real (avaliação online) e acompanhar uma taxa de conversão para as compilações diferentes. A taxa de conversão pode ser medida com base em cliques de recomendação, o número real compras de recomendações mostradas, ou até mesmo quando os valores de compra real quando as recomendações diferentes foram mostradas. Você pode selecionar sua métrica de taxa de conversão com base em seu objetivo de negócio.

Em alguns casos, talvez você queira avaliar o modelo offline antes de colocá-lo em produção. Enquanto a avaliação offline não é uma substituição de avaliação online, ele pode servir como uma métrica.

<a name="OfflineEvaluation"></a>
## <a name="offline-evaluation"></a>Avaliação offline  ##

O objetivo de uma avaliação offline é prever diversidade de recomendações (o número de itens que são recomendados) e precisão (o número de usuários que compram um dos itens Recomendados).
Como parte da avaliação de métricas de diversidade e precisão, o sistema localiza uma amostra de usuários e divide as transações para esses usuários em dois grupos: o conjunto de dados de treinamento e o conjunto de dados de teste.

> [AZURE.NOTE]Para usar métricas offline, você deve ter carimbos de hora nos seus dados de uso.
> Dados de tempo são necessários para dividir uso corretamente entre conjuntos de dados de teste e treinamento.

> Além disso, avaliação offline não pode levar a resultados para arquivos de uso pequenas. Para a avaliação seja completa, deve haver um mínimo de 1.000 pontos de uso no dataset teste.

<a name="Precision"></a>
### <a name="precision-at-k"></a>Precisão em k ###
A tabela a seguir representa a saída da avaliação offline precisão-em-k.

| K | 1 | 2 | 3 |   4 |     5
|---|---|---|---|---|---|
|Porcentagem |   13.75 | 18.04   | 21 |  24.31 | 26.61
|Usuários de teste |    10.000 |    10.000 |    10.000 |    10.000 |    10.000
|Usuários considerados | 10.000 |    10.000 |    10.000 |    10.000 |    10.000
|Usuários que não são considerados | 0 | 0 | 0 | 0 | 0

#### <a name="k"></a>K
Na tabela anterior, *k* representa o número de recomendações mostrada para o cliente. A tabela seguinte: "Se durante o período de teste, somente uma recomendação foi mostrada aos clientes, apenas 13.75 dos usuários prefere ter adquirido essa recomendação." Esta política baseia-se na suposição de que o modelo foi treinamento com dados de compra. Outra maneira de dizer isso é que a precisão em 1 é 13.75.

Você notará que, à medida que mais itens são mostrados para o cliente, a probabilidade do cliente compra de um item recomendado vai para cima. Para a experiência anterior, a probabilidade será praticamente dobra a porcentagem de 26.61 quando 5 itens são recomendados.

#### <a name="percentage"></a>Porcentagem
A porcentagem de usuários que interagir com pelo menos uma das recomendações *k* é mostrada. A porcentagem é calculada dividindo o número de usuários que interagir com pelo menos uma recomendação pelo número total de usuários considerados. Consulte usuários considerados para obter mais informações.

#### <a name="users-in-test"></a>Usuários de teste
Dados nessa linha representam o número total de usuários no dataset teste.

#### <a name="users-considered"></a>Usuários considerados
Um usuário só será considerado se o sistema recomendados pelo menos *k* itens baseados no modelo gerado usando o conjunto de dados de treinamento.

#### <a name="users-not-considered"></a>Usuários que não são considerados
Dados nessa linha representam todos os usuários que não são considerados. Os usuários que não recebem pelo menos *k* itens recomendados.

Usuário não são considerado = usuários de teste – usuários considerados

<a name="Diversity"></a>
### <a name="diversity"></a>Diversidade ###
Métricas de diversidade medem o tipo de itens recomendados. A tabela a seguir representa a saída da avaliação offline diversidade.

|Balde percentil |    0-90|  90-99| 99-100
|------------------|--------|-------|---------
|Porcentagem        | 34.258 | 55.127| 10.615


Total de itens recomendados: 100,000

Itens exclusivos recomendados: 954

#### <a name="percentile-buckets"></a>Classificações de percentil
Cada Balde percentil é representado por um intervalo (valores mínimo e máximo que variam entre 0 e 100). Os itens próximo 100 são os itens mais populares e os itens próximo 0 são menos populares. Por exemplo, se o valor de porcentagem para o Balde de percentil de 99-100 é 10.6, significa que 10.6 porcentagem das recomendações retornado somente o superior um por cento itens mais populares. O valor do percentil Balde mínimo é inclusivo e o valor máximo é exclusivo, exceto para 100.
#### <a name="unique-items-recommended"></a>Itens exclusivos recomendados
Os itens exclusivos recomendados métrica mostra o número de itens distintos que foram retornados para avaliação.
#### <a name="total-items-recommended"></a>Total de itens Recomendados
O total de itens Recomendados métrica mostra o número de itens recomendados. Alguns podem estar duplicatas.

<a name="ImplementingEvaluation"></a>
### <a name="offline-evaluation-metrics"></a>Métricas de avaliação offline ###
As métricas offline diversidade e precisão podem ser útil quando você selecionar quais compilação para usar. Em tempo de compilação, como parte das respectivas FBT ou recomendação construir parâmetros:

-   Defina o parâmetro de compilação *enableModelingInsights* como **true**.
-   Opcionalmente, selecione a *splitterStrategy* (um dos *RandomSplitter* ou *LastEventSplitter*).
*RandomSplitter* divide os dados de uso no trem e teste define baseada na porcentagem de teste de determinado *randomSplitterParameters* e aleatório propagar valores.
*LastEventSplitter* divide os dados de uso no trem e testar conjuntos baseados na última transação para cada usuário.

Isso irá disparar uma compilação que usa apenas um subconjunto dos dados para treinamento e usa o restante dos dados para calcular métricas de avaliação.  Após a compilação, para obter a saída da avaliação, você precisa chamar a [API de métricas de compilação de Get](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/577eaa75eda565095421666f), passando o respectivo *modelId* e *buildId*.

 A seguir está a saída JSON para a avaliação de amostra.


    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }
