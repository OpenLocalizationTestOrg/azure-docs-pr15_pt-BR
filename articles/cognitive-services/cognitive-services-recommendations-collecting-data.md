<properties
    pageTitle="Coleta de dados para treinar seu modelo: recomendações API de aprendizado de máquina | Microsoft Azure"
    description="Azure máquina recomendações de aprendizagem - coleta de dados para treinar seu modelo"
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
    ms.date="09/06/2016"
    ms.author="luisca"/>

#  <a name="collecting-data-to-train-your-model"></a>Coleta de dados para treinar seu modelo #

A API de recomendações aprende suas transações passadas para encontrar itens que devem ser recomendado para um determinado usuário.

Após ter criado um modelo, você precisará fornecer dois informação antes de poder fazer qualquer treinamento: um arquivo de catálogo e dados de uso.

>   Se você tiver não fez isso, recomendamos que você conclua o [guia de início rápido](cognitive-services-recommendations-quick-start.md).


## <a name="catalog-data"></a>Dados de catálogo ##

### <a name="catalog-file-format"></a>Formato de arquivo de catálogo ###

O arquivo de catálogo contém informações sobre os itens que você estiver oferecendo a seu cliente.
Os dados do catálogo deverão seguir o seguinte formato:

- Sem recursos-`<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- Com recursos-`<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

#### <a name="sample-rows-in-a-catalog-file"></a>Linhas de amostra em um arquivo de catálogo

Sem recursos:

    AAA04294,Office Language Pack Online DwnLd,Office
    AAA04303,Minecraft Download Game,Games
    C9F00168,Kiruna Flip Cover,Accessories

Com recursos:

    AAA04294,Office Language Pack Online DwnLd,Office,, softwaretype=productivity, compatibility=Windows
    BAB04303,Minecraft DwnLd,Games, softwaretype=gaming,, compatibility=iOS, agegroup=all
    C9F00168,Kiruna Flip Cover,Accessories, compatibility=lumia,, hardwaretype=mobile

#### <a name="format-details"></a>Detalhes de formato

| Nome | Obrigatório | Tipo |  Descrição |
|:---|:---|:---|:---|
| Id do item |Sim | [A-z], [a-z], [0-9], [_] & #40. Sublinhado & 41; [-] & #40; traço & 41;<br> Comprimento máximo: 50 | Identificador exclusivo de um item. |
| Nome do item | Sim | Qualquer caractere alfanumérico<br> Comprimento máximo: 255 | Nome do item. |
| Categoria de item | Sim | Qualquer caractere alfanumérico <br> Comprimento máximo: 255 | Categoria à qual este item pertence (por exemplo, culinária livros, Drama...); pode ser vazio. |
| Descrição | Não, a menos que recursos estão presentes (mas podem estar vazias) | Qualquer caractere alfanumérico <br> Comprimento máximo: 4000 | Descrição deste item. |
| Lista de recursos | Não | Qualquer caractere alfanumérico <br> Comprimento máximo: 4000; Número máximo de recursos: 20 | Lista separada por vírgulas de nome do recurso = valor de recurso que pode ser usado para aprimorar recomendação de modelo.|

#### <a name="uploading-a-catalog-file"></a>Carregar um arquivo de catálogo

Examine a [referência à API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e1) para carregar um arquivo de catálogo.  

Observe que o conteúdo do arquivo de catálogo deve ser passado como o corpo da solicitação.

Se você carregar vários arquivos de catálogo para o mesmo modelo com várias chamadas, podemos irá inserir somente os novos itens de catálogo. Os itens existentes permanecerão com os valores originais. Você não pode atualizar dados do catálogo usando esse método.

>   Observação: O tamanho máximo de arquivo é 200MB.
>   O número máximo de itens do catálogo têm suportada é 100.000 itens.


## <a name="why-add-features-to-the-catalog"></a>Por que adicionar recursos ao catálogo?

O mecanismo de recomendações cria um modelo de estatístico que informa que itens costumam ser vinculou ou comprados por um cliente. Quando você tiver um novo produto que nunca tenha sido interagir com ele não é possível criar um modelo em ocorrências de colegas sozinho. Digamos que você inicie oferecendo um novo "violin infantis" no seu armazenamento, desde que você nunca vendeu que violin antes de você não pode decidir que outros itens recomendar com esse violin.

Dito isto, se o mecanismo conhece informações sobre essa violin (ou seja, ele é um instrumentos musicais é para filhos idades 7-10, não é um violin dispendioso, etc.), e em seguida, o mecanismo sabia outros produtos com recursos semelhantes. Por exemplo, você tem vendido do violin no passado e geralmente pessoas que compram violins tendem a comprar CDs de música clássica e suportes para música de planilha.  O sistema pode encontrar essas conexões entre os recursos e recomendações baseadas nos recursos enquanto seu novo violin tem pouco uso.

Recursos serão importados como parte dos dados do catálogo, e, em seguida, sua posição (ou a importância do recurso no modelo) associada quando uma compilação de classificação é feita. Ordem de recurso pode alterados de acordo com o padrão de dados de uso e tipo de itens. Mas para uso consistentes/itens, a classificação deve ter apenas pequenas flutuações. A classificação de recursos é um número não negativo. O número 0 significa que o recurso não foi classificado (acontece se você chamar essa API antes da conclusão da primeira compilação classificação). A data em que a classificação foi atribuída denomina-se a atualização de pontuação.


###<a name="features-are-categorical"></a>Recursos são Categorical

Isso significa que você deve criar os recursos que ela se pareça com uma categoria. Por exemplo, preço = 9.34 não é um recurso de categorias. Por outro lado, um recurso como priceRange = Under5Dollars é um recurso de categorias. Outro erro comum é usar o nome do item como um recurso. Isso pode causar o nome de um item para ser exclusivo para que ele não faria descrevem uma categoria. Verifique se que os recursos representam categorias de itens.


###<a name="how-manywhich-features-should-i-use"></a>Recursos como muitos/o que devo usar?


Finalmente as recomendações construir oferece suporte à criação de um modelo com até 20 recursos. Você poderia atribuir recursos de mais de 20 aos itens em seu catálogo, mas você deve fazer uma compilação de classificação e escolha somente os recursos classificados alto. (Um recurso com uma classificação de 2.0 ou mais é um recurso realmente boa usar!). 


###<a name="when-are-features-actually-used"></a>Quando o recursos são usados realmente?

Recursos são usados pelo modelo quando não há dados suficientes transação fornecer recomendações sobre informações de transação sozinhas. Portanto recursos terá o maior impacto nos "itens fria" – itens com poucas transações. Se todos os itens tem informações suficientes de transação, talvez você não precise enriquecer seu modelo com recursos.


###<a name="using-product-features"></a>Usando recursos de produto

Para usar recursos como parte da sua criação, que você precisa:

1. Certifique-se de que seu catálogo tem recursos quando você carregá-lo.

2. Dispare uma compilação de classificação. Isso fará a análise a importância/classificação dos recursos.

3. Disparar uma compilação de recomendações, definindo os seguintes parâmetros de compilação: defina useFeaturesInModel como true, allowColdItemPlacement como true, e modelingFeatureList deve ser definido para a lista separados por vírgulas dos recursos que você deseja usar para aprimorar seu modelo. Para obter mais informações, consulte [recomendações construir parâmetros de tipo](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d0) .





## <a name="usage-data"></a>Dados de uso ##
Um arquivo de uso contém informações sobre como esses itens são usados, ou as transações de sua empresa.

#### <a name="usage-format-details"></a>Detalhes de formato de uso
Um uso arquivo é um CSV (valor separado por vírgula) onde cada linha em um arquivo de uso representa uma interação entre um usuário e um item. Cada linha é formatada da seguinte maneira:<br>
`<User Id>,<Item Id>,<Time>,[<Event>]`



| Nome  | Obrigatório | Tipo | Descrição
|-------|------------|------|---------------
|Id de usuário|         Sim|[A-z], [a-z], [0-9], [_] & #40. Sublinhado & 41; [-] & #40; traço & 41;<br> Comprimento máximo: 255 |Identificador exclusivo de um usuário.
|Id do item|Sim|[A-z], [a-z], [0-9], [& #95;] & #40. Sublinhado & 41; [-] & #40; traço & 41;<br> Comprimento máximo: 50|Identificador exclusivo de um item.
|Tempo|Sim|Data no formato: DD/MM/ddTHH (por exemplo, 2013/06/20T10:00:00)|Tempo de dados.
|Evento|Não | Um destes procedimentos:<br>• Clique em<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Compra| O tipo de transação. |

#### <a name="sample-rows-in-a-usage-file"></a>Linhas de amostra em um arquivo de uso

    00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase
    0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase
    0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase
    00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase
    0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase
    00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

#### <a name="uploading-a-usage-file"></a>Ao carregar um arquivo de uso

Examine a [referência à API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e2) para carregar arquivos de uso.
Observe que você precisa passar o conteúdo do arquivo uso como o corpo da chamada HTTP.

>  Observação:

>  Tamanho máximo de arquivo: 200MB. Você pode carregar vários arquivos de uso.

>  Você precisa fazer o upload de um arquivo de catálogo antes de você começar a adicionar dados de uso para o seu modelo. Somente os itens no arquivo de catálogo serão usados durante a fase de treinamento. Todos os outros itens serão ignorados.

## <a name="how-much-data-do-you-need"></a>A quantidade de dados que você precisa?

A qualidade do seu modelo é muito dependente a qualidade e a quantidade de dados.
O sistema aprende quando os usuários compram itens diferentes (chamamos essa ocorrências co). Para compilações FBT, também é importante saber quais itens são comprados nas mesmas transações. 

Uma boa regra prática é ter a maioria dos itens ser em 20 transações ou mais, para que se você tivesse 10.000 itens em seu catálogo, recomendamos que você tenha pelo menos 20 vezes esse número de transações ou sobre 200.000 transações. Novamente, isso é uma regra geral. Você precisará experimentar com seus dados.

Quando você tiver criado um modelo, você pode executar uma [avaliação offline](cognitive-services-recommendations-buildtypes.md) para verificar como seu modelo é provavelmente executará.
