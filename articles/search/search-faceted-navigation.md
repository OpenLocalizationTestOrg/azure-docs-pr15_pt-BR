<properties 
    pageTitle="Como implementar navegação com faceta na pesquisa do Azure | Microsoft Azure | categorias de navegação de pesquisa" 
    description="Adicione navegação com faceta aos aplicativos que se integram ao Azure pesquisa, um serviço de pesquisa de nuvem hospedado no Microsoft Azure." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

#<a name="how-to-implement-faceted-navigation-in-azure-search"></a>Como implementar navegação com faceta na pesquisa do Azure

Navegação com faceta é um mecanismo de filtragem que fornece navegação de busca detalhada auto direcionado em aplicativos de pesquisa. Embora o termo 'Navegação com faceta' pode estar familiarizado, quase é um dado que você usou um antes. Como mostra o exemplo a seguir, navegação com faceta é nada mais que as categorias usadas para filtrar os resultados.

## <a name="what-it-looks-like"></a>Aparência

 ![][1]
  
Aspectos podem ajudá-lo a encontrar o que você está procurando, garantindo que não obtém zero resultados. Como um desenvolvedor, aspectos permitem expor os critérios de pesquisa mais útil para navegar no seu corpo de pesquisa. Em aplicativos de varejo online, navegação com faceta geralmente criada sobre marcas, departamentos (lugar infantil), tamanho, preço, popularidade e classificações. 

Implementação de navegação com faceta difere entre tecnologias de pesquisa e pode ser muito complexa. Em pesquisa do Azure, navegação com faceta é construída em tempo de consulta, usando atribuídos campos especificados anteriormente no seu esquema. Nas consultas que seu aplicativo cria, uma consulta deve enviar *parâmetros de consulta de faceta* para receberem os valores de filtro de faceta disponíveis para esse conjunto de resultados do documento. Cortar, na verdade, o resultado de documento definida, o aplicativo deve aplicar um `$filter` expressão.

Em termos de desenvolvimento de aplicativos, escrever código que constrói consultas constitui em massa do trabalho. Muitos dos comportamentos aplicativo que você gostaria de navegação com faceta é fornecido pelo serviço, incluindo suporte interno para configurar intervalos e obter contagens de resultados de faceta. O serviço também inclui padrões sensatas que ajudarão-lo a evitar estruturas de navegação difíceis. 

Este artigo contém as seções a seguir:

- [Como criá-lo](#howtobuildit)
- [Criar a camada de apresentação](#presentationlayer)
- [Construa o índice](#buildindex)
- [Verificar a qualidade dos dados](#checkdata)
- [Criar a consulta](#buildquery)
- [Dicas sobre como controlar a navegação com faceta](#tips)
- [Navegação com faceta com base em valores de intervalo](#rangefacets)
- [Navegação com faceta com base em GeoPoints](#geofacets)
- [Testá-la](#tryitout)

##<a name="why-use-it"></a>Por que usá-lo
Os aplicativos de pesquisa mais eficientes tem vários modelos de interação, além de uma caixa de pesquisa. Navegação com faceta é um ponto de entrada alternativos para pesquisa, oferecendo uma alternativa conveniente para digitar expressões de pesquisa complexa manualmente.

##<a name="know-the-fundamentals"></a>Conhecer os conceitos básicos

Se você for novo para pesquisar o desenvolvimento, a melhor maneira de pensar navegação com faceta é que ela mostra as possibilidades para pesquisa auto direcionada. É um tipo de experiência de pesquisa de busca detalhada, com base em filtros predefinidos, usados para limitar os resultados de pesquisa por meio de ações de ponto-e-clicar rapidamente. 

**Modelo de interação**

A experiência de pesquisa de navegação com faceta é iterativa, então vamos começar compreendendo-la como uma sequência de consultas que Desdobrar em resposta às ações do usuário.

O ponto de partida é uma página de aplicativo que fornece navegação com faceta, normalmente colocada na periferia. Navegação com faceta costuma ser uma estrutura de árvore, com caixas de seleção para cada valor ou texto clicável. 

1.  Uma consulta enviada para a pesquisa do Azure Especifica a estrutura de navegação com faceta por meio de um ou mais parâmetros de consulta de faceta. Por exemplo, a consulta pode incluir `facet=Rating`, talvez com um `:values` ou `:sort` opção para refinar ainda mais a apresentação.
2.  A camada de apresentação processa uma página de pesquisa que fornece navegação com faceta, usando os aspectos especificados na solicitação.
3.  Considerando uma estrutura de navegação com faceta que inclui classificação, o usuário clica em "4" para indicar que somente os produtos com uma classificação de 4 ou superior devem ser mostrados. 
4.  Em resposta, o aplicativo envia uma consulta que inclui`$filter=Rating ge 4` 
5.  A camada de apresentação atualiza a página, mostrando um conjunto de resultados reduzida, contendo apenas os itens que satisfaçam os novos critérios (neste caso, produtos classificados 4 e superior).

Uma faceta é um parâmetro de consulta, mas não o confunda com entrada de consulta. Ele nunca é usado como critérios de seleção em uma consulta. Em vez disso, considere os parâmetros de consulta de faceta como entradas para a estrutura de navegação Voltar na resposta. Para cada parâmetro de consulta de faceta que você fornecer, pesquisa Azure irá avaliar quantos documentos estão nos resultados da parciais para cada valor de faceta.

Observe o `$filter` na etapa 4. Este é um aspecto importante de navegação com faceta. Embora aspectos e filtros são independentes da API, você precisará ambos para proporcionar a experiência que pretende. 

**Padrões de design**

No código do aplicativo, o padrão é usar parâmetros de consulta de faceta para retornar a estrutura de navegação com faceta juntamente com os resultados de faceta, além de uma expressão de $filter que manipula o evento click. Pense na `$filter` expressão como code-behind a filtragem de real dos resultados da pesquisa retornados à camada de apresentação. Dado um aspecto de cores, clicando na cor vermelha é implementado através de um `$filter` expressão que seleciona somente os itens que possuem uma cor de vermelho. 

**Noções básicas de consulta no Azure pesquisa**

Pesquisa do Azure, uma solicitação é especificada por meio de um ou mais parâmetros de consulta (consulte [Documentos de pesquisa](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obter uma descrição de cada um deles). Nenhum dos parâmetros de consulta são necessárias, mas você deve ter pelo menos uma para que uma consulta para ser válida.

Precisão, geralmente compreendida como a capacidade de filtrar visitas sem importância, é obtida por meio de uma ou ambas dessas expressões:

- **pesquisa =**<br/>
O valor do parâmetro constitui a expressão de pesquisa. Pode ser uma única parte de texto ou uma expressão complexa de pesquisa que inclui vários termos e operadores. No servidor, uma expressão de pesquisa é usada para pesquisa de texto completo, consultando campos de pesquisa no índice para obter a correspondência de termos, retornando resultados em ordem de classificação. Se você definir `search` como nulo, consulta execução está acima do índice inteiro (ou seja, `search=*`). Neste caso, outros elementos da consulta, tais como um `$filter` ou perfil de pontuação, serão os principais fatores que afetam quais documentos são retornados `($filter`) e em que ordem (`scoringProfile` ou `$orderb`y).

- **$filter =**<br/>
Um filtro é um poderoso mecanismo para limitar o tamanho dos resultados da pesquisa com base nos valores de atributos de documento específico. A `$filter` for avaliada primeiro, seguido por lógica faceting que gera os valores disponíveis e contagens correspondentes para cada valor

Expressões de pesquisa complexa diminuirá o desempenho da consulta. Sempre que possível, utilize expressões de filtro bem construído para aumentar a precisão e melhorar o desempenho da consulta.

Para entender melhor como um filtro adiciona mais precisão, compare uma expressão complexa de pesquisa para um que inclui uma expressão de filtro:

- `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`

- `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Embora ambas as consultas sejam válidas, o segundo é superior se você estiver procurando não motéis com estacionamento em Seattle. A primeira consulta depende essas palavras específicas sejam mencionados ou não mencionados em campos de cadeia de caracteres como nome, descrição e qualquer outro campo que contém dados pesquisáveis. A segunda consulta procura correspondências precisas nos dados estruturados e provavelmente será muito mais precisos.

Em aplicativos que incluam navegação com faceta, você desejará Certifique-se de que cada ação do usuário em uma estrutura de navegação com faceta é acompanhada por uma redução dos resultados de pesquisa, obtidos por meio de uma expressão de filtro.

<a name="howtobuildit"></a>
##<a name="how-to-build-it"></a>Como criá-lo

Navegação com faceta na pesquisa do Azure é implementada em seu código de aplicativo que cria a solicitação, mas depende elementos predefinidos no seu esquema.

Predefinidas na sua pesquisa índice é o `Facetable [true|false]` atributo de índice, defina nos campos selecionados para ativar ou desativar seu uso em uma estrutura de navegação com faceta. Sem `"Facetable" = true`, um campo não pode ser usado em navegação de faceta.

Em tempo de consulta, o código do seu aplicativo cria uma solicitação que inclui `facet=[string]`, um parâmetro de solicitação que fornece o campo a faceta por. Uma consulta pode ter vários aspectos, tais como `&facet=color&facet=category&facet=rating`, cada um deles separados por um caractere e comercial (&).

Código do aplicativo também deve construir uma `$filter` expressão para manipular os eventos de clique em navegação com faceta. A `$filter` reduz os resultados da pesquisa, usando o valor de faceta como critérios de filtro.

Pesquisa Azure retorna os resultados de pesquisa, pelo termo inseridos pelo usuário, juntamente com as atualizações para a estrutura de navegação com faceta. Pesquisa do Azure, navegação com faceta é uma construção de nível único, com valores de faceta e contagens de resultados quantos forem encontrados para cada um deles.

A camada de apresentação no seu código fornece a experiência do usuário. Ele deve listar as partes partes do painel de navegação com faceta, como o rótulo, valores, caixas de seleção e a contagem. A API de REST de pesquisa do Azure é independente de plataforma, portanto, use qualquer idioma e a plataforma desejada. O importante é incluir elementos de interface do usuário que oferecem suporte à atualização incremental, com o estado de interface do usuário atualizada como cada faceta adicional está selecionada. 

Nas seções a seguir, vamos dar uma olhada mais atenta como criar cada parte, começando com a camada de apresentação.

<a name="presentationlayer"></a>
##<a name="build-the-presentation-layer"></a>Criar a camada de apresentação

Trabalho volta da camada de apresentação pode ajudá-lo a revelar requisitos que podem ser perdidos caso contrário e compreendam quais recursos são essenciais para a experiência de pesquisa.

Em termos de navegação com faceta, sua página da web ou aplicativo exibe a estrutura de navegação com faceta, detecta na página de entrada do usuário e insere os elementos alterados. 

Para aplicativos web, AJAX geralmente é usada na camada de apresentação porque ela permite que você atualize alterações incrementais. Você também pode usar ASP.NET MVC ou qualquer outra plataforma de visualização que pode se conectar a um serviço de pesquisa do Azure sobre HTTP. O aplicativo de amostra referido neste artigo – **AdventureWorks catálogo** – é um aplicativo ASP.NET MVC.

O exemplo a seguir, tirado de aplicativo de exemplo, de arquivo **cshtml** cria uma estrutura HTML dinâmica para exibir a navegação com faceta na sua página de resultados de pesquisa. No exemplo, navegação com faceta baseia-se na página de resultados de pesquisa e aparecerá depois que o usuário tenha enviado um termo de pesquisa.

Observe que cada faceta tem um rótulo (cores, categorias, preços), uma associação a um campo com faceta (cor, nome de categoria, listPrice) e um `.count` parâmetro, usado para retornar o número de itens encontrados para esse resultado faceta.

  ![][2]
 

> [AZURE.TIP] Ao criar a página de resultados de pesquisa, lembre-se de adicionar um mecanismo para limpar aspectos. Se você usar caixas de seleção, os usuários podem facilmente intuit como limpar os filtros. Para outros layouts, talvez seja necessário um padrão de navegação estrutural ou outra abordagem criativa. Por exemplo, no aplicativo de amostra AdventureWorks catálogo, você pode clicar no título, catálogo de AdventureWorks, redefina a página de pesquisa.

<a name="buildindex"></a>
##<a name="build-the-index"></a>Construa o índice

Faceting é habilitado por um campo no índice, via esse atributo de índice: `"Facetable": true`.  
Todos os tipos de campo que possivelmente podem ser usados em navegação com faceta estão `Facetable` por padrão. Esses tipos de campo incluem `Edm.String`, `Edm.DateTimeOffset`e todos os tipos de campo numérico (essencialmente, todos os tipos de campo são facetable exceto `Edm.GeographyPoint`, que não podem ser usados em navegação com faceta). 

Ao criar um índice, uma prática recomendada para navegação com faceta é explicitamente desativar faceting para campos que nunca devem ser usados como uma faceta.  Em particular, os campos de cadeia de caracteres para valores simples, como um nome de produto ou ID, devem ser definidos como `"Facetable": false` para evitar seu uso acidental (e ineficaz) em uma navegação com faceta.

A seguir está o esquema para o aplicativo de amostra AdventureWorks catálogo (cortado de alguns atributos para reduzir o tamanho geral):

 ![][3]
 
Observe que `Facetable` estiver desativado para campos de cadeia de caracteres que não devem ser usados como aspectos, como uma ID ou nome. Desativando faceting desativar onde você não precisa ajuda a manter pequeno o tamanho do índice e geralmente melhora o desempenho.

> [AZURE.TIP] Como prática recomendada, inclua o conjunto completo de atributos de índice para cada campo. Embora `Facetable` está ativada por padrão para quase todos os campos, intencionalmente definindo cada atributo pode ajudá-lo a considerar as implicações de cada decisão de esquema. 

<a name="checkdata"></a>
##<a name="check-for-data-quality"></a>Verificar a qualidade dos dados 

Ao desenvolvimento de qualquer aplicativo centrados em dados, preparar os dados geralmente é uma das partes maiores do trabalho. Aplicativos de pesquisa não são exceção. A qualidade dos dados tem um efeito direto no se a estrutura de navegação com faceta materializes conforme esperado, bem como sua eficácia ajudá-lo a construir filtros para reduzir o resultado definido.

Em pesquisa do Azure, o corpo de pesquisa é formado dos documentos que preenchem um índice. Documentos fornecem os valores que são usados para calcular aspectos. Se você quiser faceta por marca ou preço, cada documento deve conter valores para *BrandName* e *ProductPrice* que são válidos, consistente e produtivo como uma opção de filtro.

Alguns lembretes de movimente para o que estão listados abaixo:

- Para cada campo que você deseja faceta por, pergunte-se se ele contém valores que são adequadas como filtros na pesquisa auto direcionada. Os valores devem ser suficientemente distinto para oferecer uma opção clara entre as opções de concorrência curto e descritivo.
- Erros ortográficos ou valores quase correspondentes. Se você faceta em cor e valores de campo incluir laranja e Ornage (um erro de ortografia), uma faceta baseada no campo cor seria pegue ambos.
- Texto caso misto também pode causar confusão na navegação com faceta, com laranja e laranja aparecendo como dois valores diferentes. 
- Versões do mesmo valor única e plurais podem resultar em uma faceta separada para cada um.

Como você pode imaginar, auditoria preparar os dados é um aspecto essencial de navegação com faceta eficaz.

<a name="buildquery"></a>
##<a name="build-the-query"></a>Criar a consulta

O código que você escreve para compilar consultas deve especificar todas as partes de uma consulta válida, incluindo expressões de pesquisa, aspectos, filtros, pontuação perfis – nada usadas para planejar uma solicitação. Nesta seção, exploraremos onde aspectos se encaixam uma consulta e como os filtros são usados com aspectos para produzir um conjunto de resultados reduzida.

Um exemplo com frequência é um bom lugar para começar. O exemplo a seguir, tirado de arquivo **CatalogSearch.cs** , cria uma solicitação que cria navegação faceta com base na cor, categoria e preço. 

Observe que aspectos integrais neste aplicativo de exemplo. A experiência de pesquisa no catálogo de AdventureWorks destina-se ao redor de navegação com faceta e filtros. Isso é evidente no posicionamento proeminente de navegação com faceta na página. O aplicativo de exemplo inclui parâmetros URI para aspectos (cor, categoria, preços) como propriedades do método de pesquisa (como construído no aplicativo de amostra).

  ![][4]
 
Um parâmetro de consulta de faceta é definido como um campo e dependendo o tipo de dados, pode ser mais parametrizada pelo lista delimitada por vírgulas, que inclui `count:<integer>`, `sort:<>`, `intervals:<integer>`, e `values:<list>`. Uma lista de valores tem suporte para dados numéricos ao configurar intervalos. Consulte [Procurar documentos (API de pesquisa do Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obter detalhes de uso.

Juntamente com aspectos, a solicitação formulada por seu aplicativo também deve criar filtros para restringir o conjunto de documentos de candidato com base em uma seleção de valor faceta. Para uma loja de bicicleta, navegação com faceta fornece dicas para perguntas como "quais cores, fabricantes e tipos de bicicletas estiverem disponíveis", enquanto a filtragem de respostas a perguntas como "quais Bicicletas exatas são vermelhas, Bicicletas de montanha, neste preço intervalo".

Quando um usuário clica em "Vermelho" para indicar que devem ser mostrados apenas produtos vermelhos, a próxima consulta o aplicativo envia incluiria `$filter=Color eq ‘Red’`.

## <a name="best-practices-for-faceted-navigation"></a>Práticas recomendadas de navegação com faceta

A lista a seguir resume algumas práticas recomendadas.

- **Precisão**<br/>
Use os filtros. Se você confiar no apenas expressões de pesquisa sozinhos, truncamento podem causar um documento a ser retornado que não tenha o valor de faceta precisas em qualquer um de seus campos. 

- **Campos de destino**<br/>
Na Facetado de busca detalhada, você normalmente deseja incluir apenas os documentos que têm o valor de faceta em um campo específico (Facetado), não em qualquer lugar em todos os campos pesquisáveis. Adicionar um filtro reforça o campo de destino direcionando o serviço pesquisar somente no campo Facetado para um valor coincidente.

- **Eficiência de índice**<br/>
Se o aplicativo usa navegação com faceta exclusivamente (ou seja, sem caixa de pesquisa), você pode marcar o campo como `searchable=false`, `facetable=true` para produzir um índice mais compacto. Além disso, a indexação ocorre apenas em valores de faceta inteiro, com nenhuma quebra de palavra ou indexação de partes do componente de um valor de várias palavra.

- **Desempenho**<br/>
Filtros restringir o conjunto de documentos de candidato para pesquisa e excluí-los de classificação. Se você tiver um grande conjunto de documentos, usando uma análise de faceta muito seletivo abaixo muitas vezes você terá significativamente melhor desempenho.


<a name="tips"></a> 
##<a name="tips-on-how-to-control-faceted-navigation"></a>Dicas sobre como controlar a navegação com faceta

Abaixo está uma folha de dica com orientações sobre questões específicas.

**Adicionar rótulos para cada campo na navegação de faceta**

Rótulos geralmente são definidos em HTML ou formulário (**cshtml** no aplicativo de amostra). Não há nenhuma API em Pesquisar Azure rótulos de navegação de faceta ou qualquer outro tipo de metadados.

**Definir os campos que podem ser usados como faceta**

Lembre-se de que o esquema de índice determina quais campos estão disponíveis para usar como uma faceta. Considerando que um campo é facetable, a consulta especifica quais campos devem ser faceta por. O campo pelo qual você está faceting fornece os valores que aparecem abaixo do rótulo. 

Os valores que aparecem em cada etiqueta são recuperados do índice. Por exemplo, se o campo faceta for *cor*, os valores disponíveis para filtragem adicional serão os valores para esse campo (vermelho, preto e assim por diante).

Para numéricos e DateTime somente valores, você pode definir explicitamente valores no campo faceta (por exemplo, `facet=Rating,values:1|2|3|4|5`). Uma lista de valores é permitida para esses tipos de campo simplificar a separação dos resultados de faceta em intervalos contíguos (qualquer um dos intervalos com base em valores numéricos ou períodos de tempo). 

**Cortar resultados de faceta**

Resultados de faceta são encontrados nos resultados da pesquisa que correspondem a um termo de faceta de documentos. No exemplo a seguir, nos resultados de pesquisa para *computação em nuvem*, 254 itens também têm *especificação interna* como um tipo de conteúdo. Itens não são necessariamente mutuamente exclusivos. Se um item atende aos critérios de ambos os filtros, ela é contada em cada um deles. Isso é possível quando faceting na `Collection(Edm.String)` campos, que geralmente são usados para implementar a marcação do documento.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Em geral, se você achar que os resultados de faceta constantemente são muito grandes, recomendamos que você adicionar mais filtros, conforme explicado nas seções anteriores, para dar mais opções para limitar a pesquisa de seus usuários do aplicativo.

**Limitar itens no painel de navegação faceta**

Para cada campo Facetado na árvore de navegação, há um limite padrão de 10 valores. Esse padrão faz sentido para estruturas de navegação porque ele mantém a lista de valores a um tamanho gerenciável. Você pode substituir o padrão atribuindo um valor para contar.

- `&facet=city,count:5`Especifica que somente as 5 primeiros cidades encontradas na parte superior os resultados classificado são retornadas como um resultado de faceta. Considerando um termo de pesquisa de "aeroporto" e 32 correspondências, se a consulta especifica `&facet=city,count:5`, somente as primeiras cinco cidades exclusivas com a maioria dos documentos nos resultados da pesquisa são incluídas nos resultados da faceta.

Observe a distinção entre resultados de faceta e resultados de pesquisa. Resultados da pesquisa são todos os documentos que correspondem à consulta. Resultados de faceta são as correspondências para cada valor de faceta. No exemplo, os resultados da pesquisa incluirá nomes de cidades que não estão na lista de classificação faceta (5 em nosso exemplo). Os resultados são filtrados por meio de navegação com faceta ficam visíveis para o usuário quando ele ou ela limpa aspectos ou escolhe outros aspectos além de cidade. 

> [AZURE.NOTE] Abordando `count` quando houver mais de um tipo pode ser confuso. A tabela a seguir oferece um resumo de como o termo é usado no API de pesquisa do Azure, código de exemplo e documentação. 

- `@colorFacet.count`<br/>
No código de apresentação, você deve ver um parâmetro de contagem na faceta, usada para exibir o número de resultados de faceta. Nos resultados de faceta, contagem indica o número de documentos que correspondem no termo de faceta ou intervalo.

- `&facet=City,count:12`<br/>
Em uma consulta de faceta, você pode definir contagem a um valor.  O padrão é 10, mas você pode configurá-lo superior ou inferior. Definindo `count:12` obtém o início 12 correspondências nos resultados de faceta por contagem de documentos.

- "`@odata.count`"<br/>
Na resposta da consulta, esse valor indica o número de itens correspondentes nos resultados da pesquisa. Em média, é maior do que a soma de todos os resultados de faceta combinado, devido a presença de itens que correspondem ao termo de pesquisa, mas não tiver nenhuma correspondência de valor faceta.


**Níveis na navegação com faceta** 

Conforme mencionado, não há nenhum suporte direto para aninhamento aspectos em uma hierarquia. Prontos para uso, navegação com faceta suporta apenas um nível de filtros. Entretanto, existem soluções alternativas. Você pode codificar uma estrutura hierárquica faceta em uma `Collection(Edm.String)` com uma entrada aponte por hierarquia. Implementação dessa solução alternativa está além do escopo deste artigo, mas você pode ler sobre coleções em [OData por exemplo](http://msdn.microsoft.com/library/ff478141.aspx). 

**Validar campos**

Se você criar a lista de aspectos dinamicamente com base na entrada do usuário não confiável, você deve validar que os nomes dos campos facetados são válidos ou os nomes de escape ao criar URLs usando `Uri.EscapeDataString()` no .NET ou o equivalente em sua plataforma de escolha.

**Contagens nos resultados de faceta**

Ao adicionar um filtro a uma consulta com faceta, talvez você queira manter a instrução de faceta (por exemplo, `facet=Rating&$filter=Rating ge 4`). Tecnicamente, faceta = classificação não é necessária, mas mantê-lo retorna as contagens de valores de faceta para classificações 4 e superior. Por exemplo, se um usuário clica em "4" e a consulta inclui um filtro for maior ou igual a "4", contagens são retornadas para cada classificação de 4 e superior.  

**Implicações de fragmentação nas contagens de faceta**

Em determinadas circunstâncias, talvez descubra que contagens de faceta não coincidem os conjuntos de resultados (consulte [navegação com faceta na pesquisa do Azure (fórum post)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Contagens de faceta podem estar corretas devido a arquitetura de fragmentação. Cada índice de pesquisa tem vários fragmentos, cada um deles relatórios e os aspectos de N principais por contagem de documento, que depois é combinada em um único resultado. Se alguns fragmentos tem um monte de correspondência valores, enquanto outras pessoas tiverem o menor, você descobrirá que alguns valores de faceta estão faltando ou em contado nos resultados.

Embora esse comportamento pode mudar a qualquer momento, se você encontrar esse comportamento hoje, você pode trabalhar em torno dele artificial aumentando a contagem:<number> para um número muito grande para impor completo de relatórios a partir de cada fragmentar. Se o valor de count: for maior que ou igual ao número de valores exclusivos no campo, você garantem resultados precisos. No entanto, quando contagens de documento são realmente altas, que há uma perda de desempenho, portanto usado esta opção criteriosamente.

<a name="rangefacets"></a>
##<a name="facet-navigation-based-on-a-range-values"></a>Navegação de faceta com base em um intervalo de valores

Faceting sobre intervalos é um requisito comum de aplicativo de pesquisa. Intervalos são suportados para dados numéricos e valores de DateTime. Você pode ler mais sobre cada abordagem nos [Documentos de pesquisa (API de pesquisa do Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Pesquisa Azure simplifica a construção de intervalo, fornecendo duas abordagens para computação de um intervalo. Para as duas abordagens, pesquisa Azure cria os intervalos apropriados fornecidos as entradas que você forneceu. Por exemplo, se você especificar valores de intervalo de 10 | 20 | 30, ele criará automaticamente intervalos de 0 -10, 10 a 20, 20 a 30. O aplicativo de exemplo remove qualquer intervalos que estão vazios. 

**Método 1: Usar o parâmetro de intervalo**<br/>
Para definir aspectos de preço em incrementos de US $10, você deve especificar:`&facet=price,interval:10`


**Método 2: Usar uma lista de valores**<br/>
Para dados numéricos, você pode usar uma lista de valores.  Considere o intervalo de faceta para listPrice, processado da seguinte maneira:

  ![][5]

O intervalo especificado no arquivo **CatalogSearch.cs** usando uma lista de valores:

    facet=listPrice,values:10|25|100|500|1000|2500

Cada intervalo foi criado usando 0 como ponto de partida, um valor da lista como um ponto de extremidade e então cortada do intervalo anterior para criar intervalos distintos. Pesquisa Azure fará isso como parte da navegação com faceta. Você não precisa escrever código para estruturar cada intervalo.

### <a name="build-a-filter-for-facet-ranges"></a>Criar um filtro para intervalos de faceta ###

Para filtrar os documentos baseados em um intervalo selecionado pelo usuário, você pode usar o `"ge"` e `"lt"` filtrar operadores em uma expressão de duas partes que define os pontos de extremidade do intervalo. Por exemplo, se o usuário escolhe o intervalo de 10 a 25, o filtro seria `$filter=listPrice ge 10 and listPrice lt 25`.

No aplicativo de amostra, a expressão de filtro usa parâmetros **priceFrom** e **priceTo** para definir os pontos de extremidade. O método de **BuildFilter** no **CatalogSearch.cs** contém a expressão de filtro que oferece a você os documentos dentro de um intervalo.

  ![][6]

<a name="geofacets"></a> 
##<a name="filtered-navigation-based-on-geopoints"></a>Navegação filtrada com base em GeoPoints

É comum para ver os filtros que ajudarão você a escolher um repositório, restaurante ou destino com base em sua proximidade ao seu local atual. Embora esse tipo de filtro pode parecer com navegação com faceta, é realmente apenas um filtro. Devemos mencionar aqui para aqueles que está procurando especificamente conselhos de implementação para esse problema de design específico.

Há duas funções geoespaciais na pesquisa do Azure, **geo.distance** e **geo.intersects**.

- A função **geo.distance** retorna a distância em quilômetros entre dois pontos, sendo um um campo e uma sendo uma constante passado como parte do filtro. 

- A função **geo.intersects** retorna verdadeira se um determinado ponto é dentro de um determinado polígono, onde o ponto é um campo e o polígono especificado como uma lista de constante de coordenadas passadas como parte do filtro.

Você pode encontrar exemplos de filtro na [sintaxe de expressões de OData (Azure pesquisa)](http://msdn.microsoft.com/library/azure/dn798921.aspx).

<a name="tryitout"></a>
##<a name="try-it-out"></a>Testá-la

Demonstração do Azure pesquisa Adventure Works no Codeplex contém os exemplos mencionados neste artigo. Enquanto você trabalha com os resultados da pesquisa, assista a URL de alterações em construção da consulta. Este aplicativo acontece acrescentar aspectos ao URI enquanto você seleciona cada um deles.

1.  Configure o aplicativo de amostra para usar sua URL do serviço e a chave de api. 

    Observe que o esquema que está definido no arquivo Program.cs do projeto CatalogIndexer. Especifica os campos de facetable para cor, listPrice, tamanho, peso, nome de categoria e modelName.  Apenas algumas dessas (cor, listPrice, nome de categoria) realmente são implementadas em navegação com faceta.

3.  Execute o aplicativo. 

    Primeiro, apenas a caixa de pesquisa fica visível. Você pode clique no botão Pesquisar imediatamente para obter todos os resultados ou digite um termo de pesquisa.

    ![][7]
 
4.  Insira um termo de pesquisa, como bicicleta e clique em **Pesquisar**. A consulta é executada rapidamente.
 
    Uma estrutura de navegação com faceta também é retornada com os resultados da pesquisa.  Na URL, aspectos de cores, categorias e os preços são nulos. Na página de resultados da pesquisa, a estrutura de navegação com faceta inclui contagens para cada resultado de faceta.

     ![][8]
 
5.  Clique em uma cor, category e intervalo de preços. Aspectos são nulos em uma pesquisa inicial, mas como eles assumir valores, os resultados da pesquisa são cortados de itens que não correspondem. Observe que o URI seleciona as alterações à sua consulta.

    ![][9]
 
6.  Para limpar a consulta faceta para que você pode experimentar comportamentos de consulta diferente, clique em **Catálogo de AdventureWorks** na parte superior da página.

    ![][10]
 
<a name="nextstep"></a>
##<a name="next-step"></a>Próxima etapa

Para testar seu conhecimento, você pode adicionar um campo de faceta para *modelName*. O índice já está configurado para esta faceta, portanto, nenhuma alteração ao índice é necessárias. Mas, você precisará modificar o HTML para incluir uma nova faceta para modelos e adicione o campo faceta para o construtor de consultas.

Para mais ideias em princípios de design de navegação com faceta, recomendamos os links a seguir:

- [Projetando para pesquisa faceta](http://www.uie.com/articles/faceted_search/)
- [Padrões de design: Navegação com faceta](http://alistapart.com/article/design-patterns-faceted-navigation)

Você também pode assistir [Detalhamento da pesquisa do Azure](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). No 45:25, há uma demonstração sobre como implementar aspectos.

<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/Facet-1-slide.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx

 