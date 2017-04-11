<properties
    pageTitle="Recurso de seleção no processo de ciência de dados de equipe | Microsoft Azure" 
    description="Explica a finalidade da seleção do recurso e fornece exemplos de sua função no processo de aperfeiçoamento de dados de aprendizado de máquina."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="zhangya;bradsev" />


# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Seleção de recursos no processo de ciência de dados a equipe (TDSP)

Este artigo explica as finalidades da seleção de recurso e fornece exemplos de sua função no processo de aperfeiçoamento de dados de aprendizado de máquina. Esses exemplos são criados do Azure Studio de aprendizado de máquina. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Este tópico explica a finalidade da seleção do recurso e fornece exemplos de sua função no processo de aperfeiçoamento de dados de aprendizado de máquina. Esses exemplos são criados do Azure Studio de aprendizado de máquina. 

A engenharia e a seleção de recursos é uma parte do TDSP descrita a [qual é o processo de ciência de dados de equipe?](data-science-process-overview.md). Engenharia de recurso e seleção são partes da etapa **desenvolver recursos** da TDSP.

* **engenharia de recurso**: esse processo tentativas para criar recursos relevantes adicionais dos recursos de brutos existentes nos dados e para aumentar a capacidade de previsão para o algoritmo de aprendizado.

* **seleção de recursos**: esse processo seleciona o subconjunto principais recursos de dados originais em uma tentativa de reduzir a dimensionalidade do problema treinamento.

Normalmente **engenharia de recurso** é aplicada primeiro para gerar recursos adicionais e, em seguida, na etapa de **seleção de recursos** é feita para eliminar recursos sem importância, redundantes ou altamente correlacionados.


## <a name="filtering-features-from-your-data---feature-selection"></a>Recursos de filtragem de dados - seleção de recursos 

Seleção de recursos é um processo que normalmente é aplicado para a construção de conjuntos de dados de treinamento para tarefas de modelagem de previsão como classificação ou regressão tarefas. O objetivo é selecionar um subconjunto dos recursos do dataset original que reduzir suas dimensões usando um conjunto mínimo de recursos para representar a quantidade máxima de variação nos dados. Esse subconjunto de recursos são, em seguida, os recursos somente para ser incluído para treinar o modelo. Seleção de recursos tem duas finalidades principais.

* Primeiro, seleção de recurso com frequência aumenta a precisão de classificação eliminando sem importância, redundantes ou altamente correlacionadas recursos.
* Segundo, ele diminui o número de recursos que torna o processo de treinamento de modelo mais eficiente. Isso é particularmente importante para alunos que custam de treinar como máquinas de vetor de suporte.

Embora a seleção do recurso atingir reduzir o número de recursos do conjunto de dados usados para treinar o modelo, ele não é normalmente chamado pelo termo "redução dimensionalidade". Métodos de seleção do recurso extrair um subconjunto de recursos originais dos dados sem alterá-los.  Métodos de redução de dimensionalidade utilizam os recursos de engenharia que podem transformar os recursos originais e, portanto, modificá-los. Análise de componente de capital, análise de correlação canônico e Singular Decomposição de valor são exemplos de métodos de redução de dimensionalidade.

Entre outros, uma categoria aplicada amplamente dos métodos de seleção de recurso em um contexto supervised é chamada de "seleção de recursos de filtro com base". Avaliando a correlação entre cada recurso e o atributo de destino, esses métodos aplicam uma medida estatística para atribuir uma pontuação a cada recurso. Os recursos, em seguida, são classificados pela pontuação, que pode ser usada para ajudar a definir o limite para manter ou eliminar um recurso específico. Pessoa correlação, informações comum e o teste de quadrados Chi são exemplos das medidas estatísticas usadas nesses métodos.

No Azure Studio de aprendizado de máquina, existem módulos fornecidos para seleção de recursos. Como mostrado na figura a seguir, esses módulos incluem [seleção de recurso com base no filtro] [ filter-based-feature-selection] [Fisher Linear Discriminant análise]e[fisher-linear-discriminant-analysis].

![Exemplo de seleção de recurso](./media/machine-learning-data-science-select-features/feature-Selection.png)


Considere, por exemplo, o uso da [seleção de recursos com base no filtro] [ filter-based-feature-selection] módulo. Para sua conveniência, podemos continuar a usar o exemplo de mineração de texto descrito acima. Suponha que queremos criar um modelo de regressão depois de um conjunto de 256 recursos são criados por meio do [Recurso de hash] [ feature-hashing] módulo, e que a variável de resposta é "Col1" e representa um livro revisar classificações variando de 1 a 5. Definindo "Recurso método pontuação" seja "Pearson correlação", "coluna de destino" seja "Col1" e "Quantos recursos desejados" para 50. Em seguida, o módulo [seleção de recursos com base no filtro] [ filter-based-feature-selection] produzirão um conjunto de dados que contém recursos de 50 junto com o atributo de destino "Col1". A figura a seguir mostra o fluxo desse experimento e os parâmetros de entrada que acabei de descrever.

![Exemplo de seleção de recurso](./media/machine-learning-data-science-select-features/feature-Selection1.png)

A figura a seguir mostra os conjuntos de dados resultantes. Cada recurso é marcou com base na correlação Pearson entre si mesmo e o atributo de destino "Col1". Os recursos com pontuações superiores são mantidos.

![Exemplo de seleção de recurso](./media/machine-learning-data-science-select-features/feature-Selection2.png)

As pontuações correspondentes dos recursos selecionados são mostradas na figura a seguir.

![Exemplo de seleção de recurso](./media/machine-learning-data-science-select-features/feature-Selection3.png)

Aplicando esta [seleção de recursos com base no filtro] [ filter-based-feature-selection] módulo, 50 ausência 256 recursos estiverem selecionados porque eles têm os recursos mais correlacionados com a variável de destino "Col1", com base no método de pontuação "Pearson correlação".

## <a name="conclusion"></a>Conclusão
Engenharia de recurso e seleção de recursos são dois comumente de engenharia e recursos selecionados aumentam a eficiência do processo de treinamento que tenta extrair as informações da chave contida nos dados. Eles também melhoram o poder desses modelos para classificar os dados de entrada com precisão e prever resultados de interesse mais verdadeiramente. Engenharia de recurso e seleção também podem combinar para tornar o aprendizado mais computação manejável. Ela faz isso aprimorando e, em seguida, reduzindo o número de recursos necessários para calibre ou treinar um modelo. Os recursos selecionados para treinar o modelo matematicamente falando, são um conjunto mínimo de variáveis independentes que explicam os padrões nos dados e, em seguida, prever resultados com êxito.

Observe que nem sempre é necessariamente executar a seleção de engenharia ou recurso de recursos. Se é necessária ou não depende dos dados, podemos ter ou coletar, o algoritmo de que nós escolha e o objetivo da experiência.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/
 
