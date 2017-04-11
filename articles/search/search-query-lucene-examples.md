<properties
    pageTitle="Exemplos de consulta Lucene para pesquisa de Azure | Pesquisa do Microsoft Azure"
    description="Sintaxe de consulta Lucene para pesquisa confusa, pesquisa de proximidade, aumentando a termo, pesquisa de expressões regulares e pesquisa curinga."
    services="search"
    documentationCenter=""
    authors="LiamCa"
    manager="pablocas"
    editor=""
    tags="Lucene query analyzer syntax"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="liamca"
/>

# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Exemplos de sintaxe de consulta Lucene para a criação de consultas na pesquisa do Azure

Ao construir consultas de pesquisa do Azure, você pode usar a [sintaxe de consulta simples](https://msdn.microsoft.com/library/azure/dn798920.aspx) do padrão ou o alternativa [Lucene analisador de consulta na pesquisa do Azure](https://msdn.microsoft.com/library/azure/mt589323.aspx). O analisador de consulta Lucene suporta construções de consulta mais complexas, como consultas de escopo do campo, pesquisa confusa, pesquisa de proximidade, aumentando a termos e pesquisa de expressão reqular.

Neste artigo, você pode percorrer exemplos que exibem Lucene sintaxe de consulta e os resultados lado a lado. Exemplos de executar em um índice de pesquisa pré-carregado no [JSFiddle](https://jsfiddle.net/), um editor de código online para teste script e HTML. 

Clique com botão direito as URLs de exemplo de consulta para abrir JSFiddle em uma janela separada do navegador.

> [AZURE.NOTE] Os exemplos a seguir aproveitam um índice de pesquisa consiste trabalhos disponíveis com base em um conjunto de dados fornecido pela iniciativa de [Cidade de Nova York OpenData](https://nycopendata.socrata.com/) . Esses dados não devem ser considerados atual ou completa. O índice está em um serviço de área restrita fornecido pela Microsoft. Você não precisa de uma assinatura do Azure ou uma pesquisa do Azure para experimentar essas consultas.

## <a name="viewing-the-examples-in-this-article"></a>Exibindo os exemplos deste artigo

Todos os exemplos deste artigo especificam o analisador de consulta Lucene por meio do parâmetro de pesquisa**queryType** . Ao usar o analisador de consulta Lucene do seu código, você vai especificar o **queryType** em cada solicitação.  Os valores válidos incluem **simples**|**completo**, com **simples** como o padrão e **completo** para o analisador de consulta Lucene. Consulte [Procurar documentos (API de restante do serviço de pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) para obter detalhes sobre como especificar parâmetros de consulta.

**Exemplo 1** --mouse a seguinte consulta trecho abri-lo em uma nova página de navegador que carrega JSFiddle e executa a consulta:
- [& queryType = completo & Pesquisar = *](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

Essa consulta retornará documentos de nosso índice de trabalhos (carregado em um serviço de área restrita)

Na nova janela do navegador, você verá a fonte de JavaScript e a saída HTML lado a lado. O script faz referência a uma consulta, que é fornecida pelas URLs de exemplo neste artigo. Por exemplo, no **exemplo 1**, a consulta subjacente é da seguinte maneira:

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

Observe a consulta usa um índice de pesquisa do Azure pré-configurado chamado nycjobs. O parâmetro **searchFields** restringe a pesquisa para apenas o campo de título de negócios. O **queryType** está definido como **completo**, que instrui a pesquisa do Azure para usar o analisador de consulta Lucene para esta consulta.

### <a name="fielded-query-operation"></a>Operação de consulta por campo

Você pode modificar os exemplos deste artigo, especificando uma construção **fieldname:searchterm** para definir uma operação de consulta por campo, onde o campo é uma única palavra, e o termo de pesquisa também é uma única palavra ou frase, opcionalmente com operadores booleanos. Alguns exemplos incluem o seguinte:

- business_title:(senior NOT junior)
- estado: ("Nova York" e "Nova Jersey")

Certifique-se de colocar várias cadeias de caracteres entre aspas se quiser que as duas cadeias de caracteres a ser avaliada como uma única entidade, como neste caso procurando duas cidades distintas no campo local. Além disso, certifique-se de que o operador seja maiuscula como você pode ver com não e and.

O campo especificado em **fieldname:searchterm** deve ser um campo pesquisável. Consulte [Create Index (API de restante do serviço de pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) para obter detalhes sobre como os atributos de índice são usados em definições de campo.

## <a name="fuzzy-search"></a>Pesquisa confusa

Uma pesquisa confusa encontra correspondências em termos que têm uma construção similar. Por [Lucene documentação](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), distorcidas pesquisas são baseadas em [Damerau-Levenshtein distância](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Para fazer uma pesquisa confusa, use o til "~" símbolo no final de uma única palavra com um parâmetro opcional, um valor entre 0 e 2, que especifica a distância de editar. Por exemplo, "azul ~" ou "azul ~ 1" retornará azul, azuis e associar.

**Exemplo 2** --mouse o trecho de consulta a seguir para experimentar. Essa consulta procura por títulos de negócios com sênior o termo no-los, mas não iniciante:

- [& queryType = completo & Pesquisar = business_title:senior não iniciante](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="proximity-search"></a>Pesquisa de proximidade

Pesquisas de proximidade são usadas para localizar termos que estejam próximas umas das outras em um documento. Inserir um til "~" símbolo no final de uma frase seguido do número de palavras que criam o limite de proximidade. Por exemplo, "aeroporto de hotel" ~ 5 encontrará o hotel termos e aeroporto dentro de 5 palavras uns dos outros em um documento.

**Exemplo 3** - mouse o trecho de consulta a seguir. Essa consulta procura trabalhos com associar termos (onde ele está incorreta):

- [& queryType = completo & Pesquisar = business_title:asosiate ~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

**Exemplo 4** --botão direito do mouse na consulta. Pesquise trabalhos com o termo "analista sênior" onde ele é separado por não mais do que uma palavra:

- [& queryType = completo & Pesquisar = business_title: "analista sênior" ~ 1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Exemplo 5** – Experimente removendo novamente as palavras entre o termo "analista sênior".

- [& queryType = completo & Pesquisar = business_title: "analista sênior" ~ 0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting"></a>Aumento de termos

Aumentando a termo refere-se a um documento maior se ele contém o termo aumentado, relativo documentos que não contêm o termo de classificação. Isso difere do pontuação perfis em que os perfis de pontuação impulsionar determinados campos, ao invés de termos específicos. O exemplo a seguir ajuda a ilustrar as diferenças.

Considere a possibilidade de um perfil de pontuação que aumenta corresponde em um determinado campo, como **Gênero** no exemplo musicstoreindex. Aumento de termos pode ser usado para melhorar ainda mais determinada pesquisa termos superiores a outras pessoas. Por exemplo, "Rocha ^ 2 eletrônica" melhorará documentos que contêm os termos de pesquisa no campo **Gênero** superior a outros campos de pesquisa no índice. Além disso, os documentos que contêm o termo de pesquisa "Rocha" serão classificados superiores a outro termo de pesquisa "eletrônico" como resultado do valor de aumento de sensibilidade do termo (2).

Para aumentar um termo, use o acento circunflexo, "^", símbolo com um fator de aumento de sensibilidade (número) no final do termo que você está procurando. Maior fator de aumento de sensibilidade, mais relevante o termo será relativo outros termos de pesquisa. Por padrão, o fator de aumento de sensibilidade é 1. Embora o fator de aumento de sensibilidade deve ser positivo, ele pode ser menor que 1 (por exemplo, 0,2).

**Exemplo 6** - botão direito do mouse na consulta. Pesquisar trabalhos com o termo "analista de computador" onde podemos ver nenhum resultado com o computador de palavras e analista, ainda trabalhos de analista estão na parte superior dos resultados.

- [& queryType = completo & Pesquisar = analista de business_title:computer](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Exemplo 7** -- tente novamente, esse tempo aumentando resultados com o computador de termos sobre o analista de termo se ambas as palavras não existe.

- [& queryType = completo & Pesquisar = business_title:computer ^ 2 analista](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression"></a>Expressão regular

Uma pesquisa de expressão regular encontra uma correspondência com base no conteúdo entre barras "/", como documentadas na [classe RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Exemplo 8** - botão direito do mouse na consulta. Pesquise trabalhos com tanto o termo sênior ou escolar níveis fundamental.

- `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

A URL para este exemplo não serão renderizados corretamente na página. Como alternativa, copie a URL abaixo e cole o endereço de URL do navegador:    `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`


## <a name="wildcard-search"></a>Pesquisa curinga

Você pode usar sintaxe geralmente reconhecido para vários (\*) ou um único pesquisas de caracteres curinga de caractere (?). Observe que o analisador de consulta Lucene oferece suporte ao uso desses símbolos com um termo único e não uma frase.

**Exemplo 9** --botão direito do mouse na consulta. Pesquisar trabalhos que contêm o prefixo 'programa', que incluiria títulos de negócios com os termos de programação e programador nele.

- [& queryType = completo & $select = business_title & pesquisa = business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:prog*)

Não é possível usar um * ou? símbolo como o primeiro caractere de uma pesquisa.


## <a name="next-steps"></a>Próximas etapas

Tente especificar o analisador de consulta Lucene em seu código. Os links a seguir explicam como configurar consultas de pesquisa para .NET e a API REST. Os links usam a sintaxe simples padrão para que você precisará aplicar o que aprendeu deste artigo para especificar o **queryType**.

- [Índice de pesquisa do Azure usando o SDK do .NET da consulta](search-query-dotnet.md)
- [Consultar seu índice de pesquisa do Azure usando a API REST](search-query-rest-api.md)


 