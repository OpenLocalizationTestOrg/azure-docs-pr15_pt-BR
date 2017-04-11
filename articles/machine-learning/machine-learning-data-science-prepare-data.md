<properties
    pageTitle="Aprendizado de máquina de tarefas para preparar os dados para enhanced | Microsoft Azure"
    description="Pré-processar e limpar dados prepará-lo para aprendizado de máquina."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016" 
    ms.author="bradsev" />


# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Aprendizado de máquina de tarefas para preparar os dados para aprimorado

Pré-processamento e limpeza de dados são tarefas importantes que normalmente devem ser realizadas antes de conjunto de dados pode ser usado efetivamente para aprendizado de máquina. Dados processados costuma ruído e não confiáveis e podem estar ausente valores. Usar esses dados para modelagem pode produzir resultados incorretos. Essas tarefas fazem parte do processo de ciência de dados a equipe (TDSP) e normalmente seguem uma exploração inicial de um conjunto de dados usado para descobrir e planejar o pré-processamento obrigatório. Para obter instruções mais detalhadas sobre o processo TDSP, consulte as etapas descritas no [Processo de ciência de dados de equipe](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Pré-processamento e limpeza de tarefas, como a tarefa de exploração de dados, podem ser realizadas em uma ampla variedade de ambientes, como SQL ou seção ou Studio de aprendizado de máquina do Azure e com diversas ferramentas e idiomas, como R ou Python, dependendo de onde seus dados são armazenados e como ele é formatado. Como TDSP é iterativo natureza, essas tarefas podem ocorrer em várias etapas do fluxo de trabalho do processo.

Este artigo apresenta vários conceitos de processamento de dados e tarefas que podem ser feitas antes ou depois de a inclusão de dados para o aprendizado de máquina do Azure.

Para um exemplo de pré-processamento concluído dentro do studio de aprendizado de máquina do Azure e exploração de dados, consulte o vídeo de [pré-processamento de dados no Azure Studio de aprendizado de máquina](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) .


## <a name="why-pre-process-and-clean-data"></a>Por que previamente processar e limpar dados?

Dados do mundo real são coletados de várias origens e processos e ele podem conter irregularidades ou dados corrompidos comprometer a qualidade do conjunto de dados. Os problemas de qualidade de dados típica que surgem são:

* **Incompleto**: dados não possui atributos ou contendo valores ausentes.
* **Noisy**: dados contém registros incorretos ou destaques.
* **Inconsistente**: dados contém registros conflitantes ou discrepâncias.

Dados de qualidade são um pré-requisito para modelos de previsão de qualidade. Para evitar "lixo in, lixo check-out" e melhorar a qualidade de dados e, portanto, o desempenho de modelo, é fundamental para conduzir uma tela de integridade de dados para identificar problemas de dados antecipado e decidir sobre o processamento de dados correspondente e etapas de limpeza.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Quais são algumas telas de integridade de dados típica empregados?

Nós pode verificar a qualidade geral dos dados marcando:

* O número de **registros**.
* O número de **atributos** (ou **recursos**).
* Os de atributo **tipos de dados** (nominal, ordinal ou contínuo).
* O número de **valores em falta**.
* **Boa formação** dos dados.
    * Se os dados estiverem em TSV ou CSV, verifique que os separadores de linha e separadores de coluna sempre corretamente separam colunas e linhas.
    * Se os dados estiverem em formato HTML ou XML, verifique se os dados são bem formados com base em seus respectivos padrões.
    * Análise também pode ser necessário para extrair informações estruturadas de dados estruturados semiestruturados ou não.
* **Os registros de dados inconsistentes**. Verifique o intervalo de valores permitidos. Por exemplo, se os dados contiverem MÉDIAS do aluno, verifique se é a média do intervalo designado dizem 0 ~ 4.

Quando você encontrar problemas com dados, **as etapas de processamento** são necessárias que muitas vezes envolve a limpeza de valores ausentes, normalização de dados, diferenciação, processamento para remover e/ou substituir os caracteres inseridos que podem afetar o alinhamento dos dados de texto, mistos tipos de dados em comum campos e outras pessoas.

**Aprendizado de máquina do azure consome dados tabulares bem formados**.  Se os dados já estão em formato tabular, processamento de pré-lançamento de dados pode ser realizado diretamente com aprendizado de máquina Azure no Studio aprendizado de máquina.  Se dados não estão em formato tabular, digamos que ele se encontra em XML, a análise pode ser necessária para converter os dados em formato tabular.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Quais são algumas das principais tarefas no processamento de pré-lançamento de dados?

* **Limpeza de dados**: preencher ou valores ausentes, detectar e remover dados de ruído e destaques.
* **Transformação de dados**: normalizar dados para reduzir as dimensões e ruído.
* **Redução de dados**: registros de dados ou atributos para fácil manipulação de dados de exemplo.
* **Diferenciação de dados**: converter atributos contínuos nos atributos de categorias para facilidade de uso com determinados métodos de aprendizado de máquina.
* **Limpeza de texto**: remover os caracteres inseridos que podem causar alinhamento incorreto de dados, para, por exemplo, guias inseridas em um arquivo de dados separado, incorporado novas linhas que podem quebrar registros, etc.

As seções a seguir detalham algumas dessas etapas de processamento de dados.

## <a name="how-to-deal-with-missing-values"></a>Como lidar com valores ausentes?

Para lidar com valores ausentes, é melhor primeiro identificar o motivo para os valores ausentes a alça de melhor o problema. Métodos de manipulação de valor ausente típica são:

* **Exclusão**: remover registros com valores ausentes
* **Substituição de fictício**: substituir valores ausentes por um valor fictício: por exemplo, _desconhecido_ para categorias ou 0 para valores numéricos.
* **Significam substituição**: se os dados ausentes não forem numéricos, substitua os valores ausentes a média.
* **Substituição frequente**: se os dados ausentes forem categóricos, substitua os valores ausentes com o item mais frequente
* **Substituição de regressão**: usar um método de regressão para substituir valores ausentes com valores de existência.  

## <a name="how-to-normalize-data"></a>Como normalizar dados?

Normalização de dados novamente escalas de valores numéricos em um intervalo especificado. Métodos de normalização de dados populares incluem:

* **Normalização Mín-Máx**: linear transformar os dados em um intervalo, digamos, entre 0 e 1, onde o valor mínimo é dimensionado para valor 0 e máx para 1.
* **Pontuação Z normalização**: Dimensionar dados com base em média e desv_padrão: divida a diferença entre os dados e a média pelo desvio padrão.
* **Dimensionamento decimal**: dimensionar os dados, movendo o ponto decimal do valor do atributo.  

## <a name="how-to-discretize-data"></a>Como diferenciar dados?

Dados podem ser discretized Convertendo valores contínuos em atributos nominais ou intervalos. Algumas maneiras de fazer isso são:

* **Largura igual Binning**: dividir o intervalo de todos os valores possíveis de um atributo em grupos de N do mesmo tamanho e atribuir os valores que estejam em um binário com o número binário.
* **Altura igual Binning**: dividir o intervalo de todos os valores possíveis de um atributo em grupos de N, cada um contendo o mesmo número de instâncias, em seguida, atribuir os valores que estejam em um binário com o número binário.  

## <a name="how-to-reduce-data"></a>Como reduzir os dados?

Há vários métodos para reduzir o tamanho dos dados para manipulação de dados mais fácil. Dependendo do tamanho dos dados e o domínio, os seguintes métodos podem ser aplicados:

* **Amostra de registro**: os registros de dados de exemplo e escolha apenas o subconjunto representativo de dados.
* **Atributo amostragem**: selecionar somente um subconjunto dos atributos mais importantes dos dados.  
* **Agregação**: dividir os dados em grupos e armazenar os números para cada grupo. Por exemplo, os números de receita diária de uma cadeia de restaurante nos últimos 20 anos podem ser agregados de receita mensal para reduzir o tamanho dos dados.  

## <a name="how-to-clean-text-data"></a>Como limpar dados de texto?

**Campos de texto em dados tabulares** podem incluir caracteres que afetam os limites de alinhamento e/ou o registro de colunas. Por exemplo, incorporado guias em um alinhamento incorreto coluna do arquivo separado causa e inseridos caracteres de nova linha quebrar linhas de registro. Manipulação de codificação de texto inadequada ao escrever/ler texto leva a perda de informações, inadvertida introdução de caracteres ilegíveis, por exemplo, nulos e pode também afeta análise de texto. Análise cuidado e editando podem ser necessárias para limpar os campos de texto para alinhamento adequado e/ou dados de extrair estruturada de dados de texto não estruturados ou semiestruturados.

**Exploração de dados** oferece uma exibição antecipada com os dados. Um número de problemas de dados pode ser descoberto durante esta etapa e métodos correspondentes podem ser aplicados para resolver esses problemas.  É importante fazer perguntas como o que é a fonte do problema e como o problema pode ter sido introduzido. Isso também ajuda você a decidir sobre as etapas de processamento de dados que precisam ser levados para resolvê-los. O tipo de ideias um pretende deriva os dados também pode ser usado para priorizar o esforço de processamento de dados.

## <a name="references"></a>Referências

>*Mineração de dados: conceitos e técnicas*, terceira edição, Morgan Kaufmann, 2011, Jiawei hangul, Micheline Kamber e Jian Pei
