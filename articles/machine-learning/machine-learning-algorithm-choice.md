<properties
    pageTitle="Como escolher algoritmos de aprendizado de máquina | Microsoft Azure"
    description="Como escolher algoritmos de aprendizado de máquina do Windows Azure para o aprendizado supervisionado e sem supervisão no clustering, classificação ou regressão experiências."
    services="machine-learning"
    documentationCenter=""
    authors="brohrer"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>
    
<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="08/09/2016"
    ms.author="brohrer;garye" />

# <a name="how-to-choose-algorithms-for-microsoft-azure-machine-learning"></a>Como escolher algoritmos para o aprendizado de máquina do Microsoft Azure

A resposta à pergunta "Qual máquina algoritmo de aprendizagem devo usar?" é sempre "Depende." Ela depende do tamanho, a qualidade e a natureza dos dados. Ela depende do que você deseja fazer com a resposta. Depende de como a matemática do algoritmo foi convertida em instruções para o computador que você está usando. E ela depende em quanto tempo você ter. Até mesmo os mais experientes cientistas de dados não podem determinar qual algoritmo executará recomendadas antes de tentá-los.

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Roteiro para o algoritmo de aprendizado de máquina

A **Microsoft Azure máquina aprendizagem algoritmo Cheat Sheet** ajuda você a escolher a algoritmo para suas soluções de análise de previsão da biblioteca de aprendizagem do Microsoft Azure máquina de algoritmos de aprendizado de máquina à direita.
Este artigo explora como usá-lo.

> [AZURE.NOTE] Para baixar a folha cheat e acompanhe neste artigo, vá para [roteiros para algoritmo para o Microsoft Azure máquina aprendizagem Studio de aprendizado de máquina](machine-learning-algorithm-cheat-sheet.md).

Esta planilha cheat tem uma audiência específica muito em mente: um cientista início dados com o aprendizado de máquina de nível superior, tentando escolha um algoritmo começar com no Azure máquina aprendizagem Studio. Isso significa que faz algumas generalizações e oversimplifications, mas ele apontará para uma direção segura. Isso também significa que há vários algoritmos não listado aqui. À medida que cresce de aprendizado de máquina do Windows Azure para englobam um conjunto mais completo dos métodos disponíveis, vamos adicioná-los.

Estas recomendações são compilada de comentários e dicas de muita cientistas de dados e especialistas de aprendizado de máquina. Nós não concordar em todos os lugares, mas eu tentou harmonizar nossas opiniões em um consenso grosseira. A maioria das instruções de divergência começa com "Depende..."

### <a name="how-to-use-the-cheat-sheet"></a>Como usar a planilha cheat

Leia os rótulos de caminho e o algoritmo no gráfico como "para * &lt;rótulo de caminho&gt; * usar * &lt;algoritmo&gt;*." Por exemplo, "para *velocidade* use *dois Regressão logística de classe*." Às vezes, mais de uma filial serão aplicadas.
Em alguns casos, nenhum deles será uma opção perfeita. Eles estão destinados a ser recomendações de regra geral, portanto, não se preocupe sendo exato.
Várias cientistas de dados que eu falaram com disse que a maneira somente se encontrar o algoritmo muito melhor é tentar todos eles.

Aqui está um exemplo da [Galeria de inteligência de Cortana](http://gallery.cortanaintelligence.com/) de um experimento que tenta vários algoritmos contra os mesmos dados e compara os resultados: [Comparar classe múltiplos classificadores: carta reconhecimento](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

>[AZURE.TIP] Para baixar e imprimir um diagrama que fornece uma visão geral dos recursos do Studio de aprendizado de máquina, consulte [o diagrama de visão geral dos recursos de Studio de aprendizado de máquina do Windows Azure](machine-learning-studio-overview-diagram.md).

## <a name="flavors-of-machine-learning"></a>Tipos de aprendizado de máquina

### <a name="supervised"></a>Supervisionados

Algoritmos de aprendizado supervisionadas fazer previsões com base em um conjunto de exemplos. Por exemplo, preços de ações históricos podem ser usado para as suposições de risco a preços futuros. Cada exemplo usado para treinamento está rotulado com o valor de interesse — neste caso, o preço da ação. Um algoritmo de aprendizado supervisionadas procura padrões nos rótulos de valor. Ele pode usar qualquer informação que talvez sejam relevante — o dia da semana, a temporada, dados financeiros da empresa, o tipo da indústria, a presença dos eventos geopolicitical destrutivos — e cada algoritmo procura por diferentes tipos de padrões. Depois que o algoritmo encontrou o melhor padrão puder, ele usa esse padrão para fazer previsões para dados de testes sem rótulo — preços de amanhã.

Este é um tipo popular e úteis de aprendizado de máquina. Com uma exceção, todos os módulos no aprendizado de máquina do Windows Azure são supervisionados algoritmos de aprendizagem. Há vários tipos de aprendizado supervisionado específicos que são representados dentro de aprendizagem do Windows Azure máquina: detecção de classificação, de regressão e anomalia.

* **Classificação**. Quando os dados estão sendo usados para prever uma categoria, aprendizado supervisionado também é chamado de classificação. Esse é o caso, ao atribuir uma imagem como uma imagem de um gato' ' ou 'cachorro'. Quando houver apenas duas opções, isso é chamado **dois classe** ou **classificação binomial**. Quando houver mais categorias, como quando prever o prêmio de torneio o comportamento de março estranho NCAA, esse problema é conhecido como **várias classe classificação**.

* **Regressão**. Quando um valor está sendo previsto, assim como acontece com preços de ações, aprendizado supervisionado é chamado de regressão.

* **Detecção de anomalias**. Em alguns casos, o objetivo é identificar os pontos de dados que são simplesmente incomuns. Na detecção de fraude, por exemplo, nenhum dos padrões de gastos de cartão de crédito altamente incomum é suspeito. As variações possíveis são tão várias e os exemplos de treinamento tão poucas, que não é viável para saber quais atividades fraudulentas aparência. A abordagem percorrida detecção de anomalias é simplesmente Saiba que atividade normal se parece com (usando um transações não fraudulentos de histórico) e identifique tudo o que é significativamente diferente.

### <a name="unsupervised"></a>Sem supervisão

No aprendizado sem supervisão, os pontos de dados têm sem rótulos associados a eles. Em vez disso, o objetivo de um algoritmo de aprendizado sem supervisão é para organizar os dados de alguma maneira ou para descrever sua estrutura. Isso pode significar o agrupamento em clusters ou localizando maneiras diferentes de observar complexa de dados para que ele apareça mais simples ou mais organizada.

### <a name="reinforcement-learning"></a>Aprendizado Reforce seu

Reforce seu aprendizado, o algoritmo obtém a escolher uma ação em resposta a cada ponto de dados. O algoritmo de aprendizado também recebe um sinal de recompensa um pouco mais tarde, indicando a decisão como boa foi.
Com base nisso, o algoritmo modifica sua estratégia para alcançar a mais alta recompensa. Não há atualmente nenhuma Reforce seu aprendizado módulos de algoritmo no aprendizado de máquina do Azure. Aprendizado Reforce seu é comum em robotics, em que o conjunto de leituras do sensor em um ponto no tempo é um ponto de dados e o algoritmo deve escolher a ação de próxima do robô. Também é que um natural ajustados para Internet das coisas aplicativos.

## <a name="considerations-when-choosing-an-algorithm"></a>Considerações durante a escolha de um algoritmo

### <a name="accuracy"></a>Precisão

Obtendo a resposta mais precisa possíveis nem sempre é necessário.
Às vezes uma aproximação é adequada, dependendo se você deseja usá-lo para. Se for esse o caso, você pode ser capaz de reduzir o tempo de processamento drasticamente por projetando-se com mais de métodos aproximados. Outra vantagem dos métodos aproximados mais é que eles tendem a fim de evitar [overfitting](https://youtu.be/DQWI1kvmwRg)naturalmente.

### <a name="training-time"></a>Tempo de treinamento

O número de minutos ou horas necessárias para treinar um modelo varia muito entre algoritmos. O tempo de treinamento é geralmente rigorosamente associado a precisão — um geralmente acompanha o outro. Além disso, alguns algoritmos são mais sensíveis ao número de pontos de dados que outros.
Quando o tempo é limitado podem gerar a escolha de algoritmo, especialmente quando o conjunto de dados é grande.

### <a name="linearity"></a>Linearidade

Muitos dos algoritmos de aprendizado de máquina de fazer uso de linearidade. Algoritmos de classificação linear pressupõem que classes podem ser separadas por uma linha reta (ou seu analógicos superior dimensional). Isso inclui Regressão logística e suportam máquinas de vetor (conforme implementado no aprendizado de máquina do Windows Azure).
Algoritmos de regressão linear pressupõem que as tendências de dados sigam uma linha reta. Essas suposições não são ruim para alguns problemas, mas em outras pessoas que trazem precisão para baixo.

![Classe não linear bounday][1]

***Limite de classe não linear*** *-depender de um algoritmo de classificação linear for resultar em precisão baixa*

![Dados com uma tendência não linear][2]

***Dados com uma tendência não linear*** *-usar um método de regressão linear seria gerar muito erros maiores que o necessário*

Apesar de seus riscos, algoritmos lineares são muito populares como uma primeira linha de ataque. Eles tendem a ser algoritmo simples e rápida para treinar.

### <a name="number-of-parameters"></a>Número de parâmetros

Os parâmetros são os botões de que um cientista dados chega ao ativar ao configurar um algoritmo. Eles são números que afetam o comportamento do algoritmo, tolerância de erro ou o número de iterações ou opções entre variantes de como o algoritmo se comporta. O tempo de treinamento e a precisão do algoritmo em alguns casos, podem ser bastante sensíveis para obter apenas as configurações corretas. Normalmente, algoritmos com os parâmetros de grandes números exigem o maioria por tentativa e erro para encontrar uma boa combinação.

Como alternativa, há um bloco de módulo [de varredura parâmetro](machine-learning-algorithm-parameters-optimize.md) no aprendizado de máquina do Azure que tenta automaticamente todas as combinações de parâmetro em qualquer granularidade que você escolher. Embora seja uma ótima maneira de certificar-se de que você tenha estendido o espaço do parâmetro, o tempo necessário para treinar um modelo exponencial aumenta com o número de parâmetros.

A vantagem é que ter vários parâmetros geralmente indica que um algoritmo tem a maior flexibilidade. Geralmente, ela pode atingir precisão muito bom. Fornecido a que você pode encontrar a combinação certa de configurações de parâmetro.

### <a name="number-of-features"></a>Número de recursos

Para determinados tipos de dados, o número de recursos pode ser muito grande em comparação com o número de pontos de dados. Isso geralmente é o caso com genetics ou dados textuais. Grande número de recursos pode sobrecarrega alguns algoritmos de aprendizagem, fazendo com que o treinamento unfeasibly longo de tempo. Suporte vetor máquinas são particularmente bem adequadas para este caso (veja abaixo).

### <a name="special-cases"></a>Casos especiais

Alguns algoritmos de aprendizado fazem suposições específicas sobre a estrutura de dados ou os resultados desejados. Se você pode encontrar um que atenda às suas necessidades, ele pode fornecer resultados mais úteis, previsões mais precisas ou tempos mais rápidos de treinamento.

|**Algoritmo**|**Precisão**|**Tempo de treinamento**|**Linearidade**|**Parâmetros**|**Notas**|
|---|:---:|:---:|:---:|:---:|---|
|**Classificação de dois classe**| | | | | |
|[Regressão logística](https://msdn.microsoft.com/library/azure/dn905994.aspx)                    | |●|●|5| |
|[floresta de decisão](https://msdn.microsoft.com/library/azure/dn906008.aspx)|●|○| |6| |
|[Selva de decisão](https://msdn.microsoft.com/library/azure/dn905976.aspx)|●|○| |6|Pegadas de pouca memória|
|[árvore de decisão aumentado](https://msdn.microsoft.com/library/azure/dn906025.aspx)|●|○| |6|Superfície de memória grande|
|[rede neural](https://msdn.microsoft.com/library/azure/dn905947.aspx)|●| | |9|[Personalização adicional é possível](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[Média de perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx)|○|○|●|4| |
|[máquina de vetor de suporte](https://msdn.microsoft.com/library/azure/dn905835.aspx)| |○|●|5|BOM para conjuntos de recursos grandes|
|[máquina de vetor suporte localmente profundo](https://msdn.microsoft.com/library/azure/dn913070.aspx)|○| | |8|BOM para conjuntos de recursos grandes|
|[Máquina de ponto dos Bayes](https://msdn.microsoft.com/library/azure/dn905930.aspx)| |○|●|3| |
|**Classificação de várias classe**| | | | | |
|[Regressão logística](https://msdn.microsoft.com/library/azure/dn905853.aspx)| |●|●|5| |
|[floresta de decisão](https://msdn.microsoft.com/library/azure/dn906015.aspx)|●|○| |6| |
|[Selva de decisão](https://msdn.microsoft.com/library/azure/dn905963.aspx)|●|○| |6|Pegadas de pouca memória|
|[rede neural](https://msdn.microsoft.com/library/azure/dn906030.aspx)|●| | |9|[Personalização adicional é possível](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[One-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx)|-|-|-|-|Consulte as propriedades do método dois classe selecionada|
|**Regressão**| | | | | |
|[linear](https://msdn.microsoft.com/library/azure/dn905978.aspx)| |●|●|4| |
|[Bayesiana linear](https://msdn.microsoft.com/library/azure/dn906022.aspx)| |○|●|2| |
|[floresta de decisão](https://msdn.microsoft.com/library/azure/dn905862.aspx)|●|○| |6| |
|[árvore de decisão aumentado](https://msdn.microsoft.com/library/azure/dn905801.aspx)|●|○| |5|Superfície de memória grande|
|[floresta Fast quantile](https://msdn.microsoft.com/library/azure/dn913093.aspx)|●|○| |9|Distribuições ao invés previsões de ponto|
|[rede neural](https://msdn.microsoft.com/library/azure/dn905924.aspx)|●| | |9|[Personalização adicional é possível](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[POISSON](https://msdn.microsoft.com/library/azure/dn905988.aspx)| | |●|5|Tecnicamente linear de log. Para prever contagens|
|[ordinal](https://msdn.microsoft.com/library/azure/dn906029.aspx)| | | |0|Para prever a ordem de classificação|
|**Detecção de anomalia**| | | | | |
|[máquina de vetor de suporte](https://msdn.microsoft.com/library/azure/dn913103.aspx)|○|○| |2|Especialmente bom para conjuntos de recursos grandes|
|[Detecção de anomalias baseada em PCA](https://msdn.microsoft.com/library/azure/dn913102.aspx)| |○|●|3| |
|[K-significa](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)| |○|●|4|Um algoritmo de cluster|


**Propriedades de algoritmo:**

**●** - mostra excelente precisão, treinamento fast horas e o uso de linearidade

**○** - mostra boa precisão e tempos de treinamento moderada

## <a name="algorithm-notes"></a>Notas de algoritmo

### <a name="linear-regression"></a>Regressão linear

Conforme mencionado anteriormente, [regressão linear](https://msdn.microsoft.com/library/azure/dn905978.aspx) ajusta uma linha (ou plano ou hyperplane) para o conjunto de dados. Ele é uma força de trabalho, simple e rápida, mas pode ser muito simples para alguns problemas.
Marque aqui para obter um [tutorial de regressão linear](machine-learning-linear-regression-in-azure.md).

![Dados com uma tendência linear][3]

***Dados com uma tendência linear***

### <a name="logistic-regression"></a>Regressão logística

Embora erroneamente inclui 'regressão' no nome de usuário, Regressão logística é realmente uma ferramenta poderosa para a classificação de [classe de dois](https://msdn.microsoft.com/library/azure/dn905994.aspx) e [multiclass](https://msdn.microsoft.com/library/azure/dn905853.aspx) . É rápida e simples. O fato de que ele usa de uma '-curva com formato, em vez de uma linha reta torna um ajuste natural para dividindo os dados em grupos. Limites de classe linear oferece Regressão logística, portanto quando você usá-lo, certifique-se que uma aproximação linear é algo com que você puder conviver.

![Regressão logística aos dados de classe dois com apenas um recurso][4]

***Uma regressão logística aos dados de classe dois com apenas um recurso*** *-o limite da classe é o ponto no qual a curva logística é mais próximo ambas as classes*

### <a name="trees-forests-and-jungles"></a>Árvores, florestas e jungles

Árvores de decisão aumentado ([regressão](https://msdn.microsoft.com/library/azure/dn905801.aspx) e [dois classe](https://msdn.microsoft.com/library/azure/dn906025.aspx)), jungles de decisão ([classe dois](https://msdn.microsoft.com/library/azure/dn905976.aspx) e [multiclass](https://msdn.microsoft.com/library/azure/dn905963.aspx)) e florestas de decisão ([regressão](https://msdn.microsoft.com/library/azure/dn905862.aspx), [dois classe](https://msdn.microsoft.com/library/azure/dn906008.aspx)e [multiclass](https://msdn.microsoft.com/library/azure/dn906015.aspx)) são todos baseadas em árvores de decisão, uma máquina fundamental conceito de aprendizagem. Há muitas variantes árvores de decisão, mas todos eles fazem a mesma coisa — Subdivida o espaço de recurso em regiões com principalmente o mesmo rótulo. Eles podem ser regiões da categoria consistente ou do valor de constante, dependendo se você estiver fazendo a classificação ou regressão.

![Árvore de decisão subdivide um espaço de recurso][5]

***Uma árvore de decisão subdivide um espaço de recurso em regiões dos valores aproximadamente uniformes***

Como um espaço de recurso pode ser subdividido em regiões arbitrariamente pequenos, é fácil imaginar dividindo-la levemente suficiente para ter um ponto de dados por região — um exemplo extremo de excedido. Para evitar isso, um grande conjunto de árvores são construídas com cuidado matemático especial tomado que as árvores não são correlacionadas. A média dos "floresta decisão" é uma árvore que evita excedido. Florestas de decisão podem usar muita memória. Jungles decisões são uma variant que consome menos memória às custas de um tempo ligeiramente menor de treinamento.

Árvores de decisão aumentado Evite excedido limitando quantas vezes, eles podem subdivida e como alguns pontos de dados são permitidos em cada região. O algoritmo constrói uma sequência de árvores, cada um deles aprende para compensar o erro pela árvore antes da esquerda. O resultado é um aluno muito preciso que tende a usar muita memória. Para a descrição técnica completo, confira o [papel de original de Friedman](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Regressão de quantile floresta rápida](https://msdn.microsoft.com/library/azure/dn913093.aspx) é uma variação do árvores de decisão para o caso especial onde você deseja saber não apenas o valor (médio) típico dos dados dentro de uma região, mas também sua distribuição na forma de quantiles.

### <a name="neural-networks-and-perceptrons"></a>Perceptrons e redes neurais

Redes neurais são inspiradas brain aprendizagem algoritmos que abrangem a problemas de [regressão](https://msdn.microsoft.com/library/azure/dn905924.aspx) , [dois classe](https://msdn.microsoft.com/library/azure/dn905947.aspx)e [multiclass](https://msdn.microsoft.com/library/azure/dn906030.aspx). Eles vêm em uma variedade infinita, mas as redes neurais dentro de aprendizado de máquina do Windows Azure são todos de forma de gráficos acíclica direcionadas. Isso significa que recursos de entrada são passados para a frente (nunca retroceder) por meio de uma sequência de camadas antes que está sendo transformado em saídas. Cada camada, entradas são ponderadas em várias combinações, somadas e passadas para a próxima camada. Essa combinação de cálculos simples resulta na capacidade de saber tendências de limites e dados sofisticados de classe, aparentemente por mágica. Em muitos níveis redes desse tipo realizar o "aprendizado detalhado" que alimenta tantos tech relatórios e ciência ficção.

Este alto desempenho não vêm gratuitamente, no entanto. Redes neurais podem levar muito tempo para treinar, especialmente para grandes conjuntos de dados com muitos recursos. Eles também têm mais parâmetros do que a maioria dos algoritmos, o que significa que limpeza do parâmetro expande muito o tempo de treinamento.
E, para esses overachievers que desejam [especificar sua próprias estrutura da rede](http://go.microsoft.com/fwlink/?LinkId=402867), as possibilidades são inesgotável.

<a name="boundaries-learned-by-neural-networks6"></a>![Limites aprendidos por redes neurais][6]
---------------------------

***Os limites aprendidos por redes neurais podem ser complexa e irregular***

A [classe dois na média perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) é a resposta de redes neurais para o aumento tempos de treinamento. Ele usa uma estrutura de rede que proporciona aos limites da classe linear. É quase primitivo pelos padrões de hoje, mas ele tem um longo histórico do trabalho de modo eficiente e é pequeno para saber rapidamente.

### <a name="svms"></a>SVMs

Suporte vetor máquinas (SVMs) encontre o limite que separa classes por como uma grande margem possível. Quando as duas classes não podem ser claramente separadas, os algoritmos encontre o limite recomendado, que eles podem. Como escrito no aprendizado de máquina do Windows Azure, a [classe dois SVM](https://msdn.microsoft.com/library/azure/dn905835.aspx) faz isso com apenas uma linha reta. (No SVM-fala, ele usa um kernel linear.) Porque torna essa aproximação linear, é possível executar rapidamente. Onde ele realmente brilha é com dados intenso de recurso, como texto ou genoma. Nesses casos SVMs são capazes de separar classes mais rapidamente e com menos excedido que a maioria dos outros algoritmos, além de exigir que apenas uma pequena quantidade de memória.

![Limite de classe de máquina de vetor de suporte][7]

***Um limite de classe de máquina de vetor típica de suporte maximiza a margem separando duas classes***

Outro produto do Microsoft Research, o [SVM localmente profundidade de dois classe](https://msdn.microsoft.com/library/azure/dn913070.aspx) é um variant não linear de SVM que mantém a maioria da eficiência velocidade e memória da versão linear. Ele é ideal para casos onde a abordagem linear não fornece precisos o suficiente respostas. Os desenvolvedores mantidos ele fast por dividi o problema em um monte de pequenos problemas SVM lineares. Leia a [descrição completa](http://research.microsoft.com/um/people/manik/pubs/Jose13.pdf) para obter detalhes sobre como eles extraídas desativa este truques.

Usando uma extensão inteligente do SVMs não lineares, a [uma classe SVM](https://msdn.microsoft.com/library/azure/dn913103.aspx) desenha um limite que descreve rigorosamente todo o conjunto de dados. É útil para a detecção de anomalia. Quaisquer novos pontos de dados que não se encaixam essa fronteira extremo estão ser notável incomuns.

### <a name="bayesian-methods"></a>Métodos de Bayesiana

Métodos de Bayesiana têm uma qualidade altamente desejável: eles evitam excedido. Eles fazem isso fazendo algumas suposições com antecedência sobre a distribuição de probabilidade da resposta. Outro subproduto dessa abordagem é que eles tenham pouquíssimas parâmetros. Aprendizado de máquina Azure tem ambos os algoritmos Bayesiana para classificação ([máquina de ponto dos dois classe Bayes](https://msdn.microsoft.com/library/azure/dn905930.aspx)) e de regressão ([Bayesiana de regressão linear](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Observe que essas pressupõem que os dados podem ser divididos ou ajustar com uma linha reta.

Em uma nota de histórica, máquinas de ponto dos Bayes foram desenvolvidas Microsoft Research. Eles têm algum trabalho teórico extremamente incríveis atrás deles. O aluno interessado seja direcionado para o [artigo original na JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) e um [blog criteriosos por Gustavo Camargo](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Algoritmos especializados

Se você tiver um objetivo muito específico, você pode estar sorte. Dentro da coleção de aprendizado de máquina do Azure existem algoritmos especializados a previsão de classificação ([regressão ordinal](https://msdn.microsoft.com/library/azure/dn906029.aspx)), a previsão de contagem ([Poisson regressão](https://msdn.microsoft.com/library/azure/dn905988.aspx)) e detecção de anomalia (um com base na [análise principais componentes](https://msdn.microsoft.com/library/azure/dn913102.aspx) e outro com base em s [suportar máquina vetor](https://msdn.microsoft.com/library/azure/dn913103.aspx)).
E há um solitário cluster algoritmo também ([K-meios](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)).

![Detecção de anomalias baseada em PCA][8]

***Detecção de anomalias baseada em PCA*** *-a maioria dos dados enquadra uma distribuição stereotypical; pontos tenha se desviado drasticamente do que a distribuição forem suspeitas*

![Conjunto de dados agrupado usando meios de K][9]

***Um conjunto de dados é agrupado em 5 agrupamentos usando meios de K***

Há também [classificador multiclass de one-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx)um ensemble, que divide o problema de classificação de classe N em problemas de classificação de classe de dois N-1. As propriedades de linearidade, tempo de treinamento e precisão são determinadas pelo classificadores dois classe usados.

![Classe de dois classificadores combinados para formar um classificador três classe][10]

***Um par de classe dois classificadores combinar para formar um classificador três classe***

Aprendizado de máquina Azure também inclui o acesso a uma estrutura de aprendizado de máquina poderosa sob o título do [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
VW defies categorização aqui, desde que ele pode saber a problemas de regressão quanto de classificação e até mesmo aprender com dados parcialmente sem rótulo. Você pode configurá-lo para usar qualquer um de um número de algoritmos, funções de perda e algoritmos de otimização de aprendizagem. Ele foi projetado desde o início backup seja eficiente, paralelo e extremamente rápido. Ele lida com conjuntos de recursos absurdamente grande com pouco esforço aparente.
Introdução e administrada pelo John Langford do Microsoft Research próprio, VW é uma entrada de uma fórmula em um campo de algoritmos de carro stock. Nem todo problema satisfaz VW, mas se sua contiver, ele pode valer a pena para subir a curva de aprendizado em sua interface. Ele também está disponível como [código-fonte aberto autônomo](https://github.com/JohnLangford/vowpal_wabbit) em vários idiomas.


<!-- Media -->

[1]: ./media/machine-learning-algorithm-choice/image1.png
[2]: ./media/machine-learning-algorithm-choice/image2.png
[3]: ./media/machine-learning-algorithm-choice/image3.png
[4]: ./media/machine-learning-algorithm-choice/image4.png
[5]: ./media/machine-learning-algorithm-choice/image5.png
[6]: ./media/machine-learning-algorithm-choice/image6.png
[7]: ./media/machine-learning-algorithm-choice/image7.png
[8]: ./media/machine-learning-algorithm-choice/image8.png
[9]: ./media/machine-learning-algorithm-choice/image9.png
[10]: ./media/machine-learning-algorithm-choice/image10.png
