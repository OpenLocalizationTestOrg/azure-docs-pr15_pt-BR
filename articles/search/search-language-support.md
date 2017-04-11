<properties
   pageTitle="Criar um índice para documentos em vários idiomas em pesquisa do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
   description=" Pesquisa Azure suporta 56 idiomas, aproveitando analisadores de idioma do Lucene e processamento de linguagem Natural de tecnologia da Microsoft."
   services="search"
   documentationCenter=""
   authors="yahnoosh"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="07/14/2016"
   ms.author="jlembicz"/>

# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Criar um índice para documentos em vários idiomas em pesquisa do Azure
> [AZURE.SELECTOR]
- [Portal](search-language-support.md)
- [RESTANTE](https://msdn.microsoft.com/library/azure/dn879793.aspx)
- [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)

Ampliando o poder dos analisadores de idioma é tão fácil quanto definindo uma propriedade em um campo pesquisado na definição de índice. Agora você pode executar esta etapa no portal.

Abaixo estão capturas de tela dos blades Portal Azure para pesquisa de Azure que permitem que os usuários definam um esquema de índice. Deste lâmina, os usuários podem criar todos os campos e defina a propriedade de analisador para cada um deles.

> [AZURE.IMPORTANT] Você só pode definir um analisador de idioma durante a definição de campo, como em ao criar um novo índice desde o início para cima ou ao adicionar um novo campo a um índice existente. Certifique-se de que você especifica totalmente todos os atributos, incluindo o analisador, ao criar o campo. Você não conseguirá editar os atributos ou alterar o tipo de analisador depois que você salvar suas alterações.

## <a name="define-a-new-field-definition"></a>Definir uma nova definição de campo

1. Entre no [Portal do Azure](https://portal.azure.com) e abra a lâmina de serviço do seu serviço de pesquisa.
2. Clique em **Adicionar índice** na barra de comandos na parte superior do painel de serviço para iniciar um novo índice ou abrir um índice existente para definir um analisador em novos campos que você está adicionando a um índice existente.
3. A lâmina de campos é exibida, oferecendo opções para definir o esquema de índice, incluindo a guia analisador usado para escolher um analisador de idioma.
4. Em campos, inicie uma definição de campo fornecer um nome, escolhendo o tipo de dados e definição de atributos para marcar o campo como texto completo pesquisáveis, recuperáveis nos resultados de pesquisa, pode ser usados em estruturas de navegação de faceta, classificável e assim por diante. 
5. Antes de passar para o próximo campo, abra a guia **Analisador** . 

   
![][1]
*Para selecionar um analisador, clique na guia analisador na lâmina campos*

## <a name="choose-an-analyzer"></a>Escolha um analisador

6. Role para localizar o campo que você está definindo. 
7. Se você ainda não tiver marcado o campo como pesquisável, clique na caixa de seleção agora para marcá-la como **pesquisável**.
8. Clique na área de análise para exibir a lista de analisadores disponíveis.
9. Escolha o analisador que você deseja usar.

![][2]
*Selecione uma das analyzers com suporte para cada campo*

Por padrão, todos os campos de pesquisa usam o [Analisador de Lucene padrão](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) que é independente de idioma. Para exibir a lista completa de analisadores com suporte, consulte [Suporte de idioma na pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Quando o analisador de idioma estiver selecionado para um campo, ele será usado com cada solicitação de pesquisa e indexação para esse campo. Quando uma consulta é emitida contra vários campos usando analyzers diferentes, a consulta será processada independentemente pelas analyzers certos para cada campo.

Muitos aplicativos web e móveis servem usuários em todo o mundo usando diferentes idiomas. É possível definir um índice para um cenário assim, criando um campo para cada idioma com suporte.

![][3]
*Definição de índice com um campo de descrição para cada idioma com suporte*

Se o idioma do agente de uma consulta de emissão for conhecido, uma solicitação de pesquisa pode estar limitada a um campo específico usando o parâmetro de consulta **searchFields** . Somente contra a descrição em polonês receberá a seguinte consulta:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2015-02-28`

Você pode consultar seu índice do portal, usando o **Explorador de pesquisa** para colar em uma consulta semelhante ao mostrado acima. Explorador de pesquisa está disponível na barra de comando na lâmina serviço de. Consulte [consulta índice de pesquisa do Azure no portal](search-explorer.md) para obter detalhes.

Às vezes, o idioma do agente de uma consulta de emissão não for conhecido, caso em que a consulta pode ser emitida em relação a todos os campos simultaneamente. Se necessário, preferência de resultados em um determinado idioma pode ser definida usando [perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx). No exemplo abaixo, correspondências encontradas na descrição em inglês serão totalizadas superior relativo correspondências em polonês e francês:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2015-02-28`

Se você for um desenvolvedor .NET, observe que você pode configurar analisadores de idioma usando o [SDK do Azure pesquisa .NET](http://www.nuget.org/packages/Microsoft.Azure.Search). A versão mais recente inclui suporte para os analisadores de idioma do Microsoft também.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
