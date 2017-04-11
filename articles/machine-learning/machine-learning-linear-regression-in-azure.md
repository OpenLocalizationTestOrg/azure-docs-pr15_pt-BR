<properties 
    pageTitle="Usando Regressão Linear no aprendizado de máquina | Microsoft Azure" 
    description="Uma comparação dos modelos de regressão linear no Excel e no Studio de aprendizado de máquina do Azure" 
    metaKeywords="" 
    services="machine-learning" 
    documentationCenter="" 
    authors="garyericson" 
    manager="jhubbard" 
    editor="cgronlun"  />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/09/2016" 
    ms.author="kbaroni;garye" />

# <a name="using-linear-regression-in-azure-machine-learning"></a>Usando regressão linear no aprendizado de máquina do Azure

> *Kate Baroni* e *Manuel Boatman* são arquitetos de solução de enterprise no Centro de ideias de dados de excelência da Microsoft. Neste artigo, eles descrevem sua experiência migrar um pacote de análise de regressão existente para uma solução baseada em nuvem usando o aprendizado de máquina do Azure.  
 
&nbsp; 
  
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  
 
## <a name="goal"></a>Meta

Nosso projeto é iniciada com dois objetivos em mente:  

1. Usar a análise de previsão para melhorar a precisão do projeções de receita mensal da nossa organização  
2. Use o Azure ML para confirmar, otimizar, aumentar a velocidade e a escala de nossos resultados.  

Como muitas empresas, nossa organização passa por uma processo de previsão de receita mensal. Nossa pequena equipe de analistas de negócios foi designado usando aprendizado de máquina para o processo de suporte e melhore a precisão de previsão.  A equipe passou vários meses de coleta de dados de várias fontes e executando os atributos de dados por meio de identificação chaves atributos relevantes para a previsão de vendas de serviços de análise estatística.  Próximas etapas era começar modelos de estatísticas de regressão protótipos os dados do Excel.  Em poucas semanas tivemos um modelo de regressão do Excel que foi superando o campo atual e finanças processos de previsão. Essa se tornou o resultado de previsão da linha de base.  


Em seguida, tivemos a próxima etapa para movendo nosso análise de previsão sobre a ML do Azure para descobrir como Azure ML pode melhorar desempenho de previsão.


## <a name="achieving-predictive-performance-parity"></a>Obtenção de paridade de desempenho de previsão

Nossa primeira prioridade foi atingir paridade entre modelos de regressão ML do Azure e Excel.  Recebe os mesmos dados e a mesma divisão de treinamento e teste dados queremos atingir paridade de desempenho de previsão entre o Excel e Azure ML.   Inicialmente, podemos falha. Modelo do Excel superou o desempenho do modelo do Azure ML.   A falha foi devido a uma falta de compreensão da configuração de ferramenta base no Azure ML. Após a sincronização com a equipe de produto do Azure ML, podemos obtida uma melhor compreensão da base de configuração necessárias para nossos conjuntos de dados e alcançado paridade entre os dois modelos.  

### <a name="create-regression-model-in-excel"></a>Criar um modelo de regressão no Excel
Nossa regressão Excel usado o modelo de regressão linear padrão encontrado de análise do Excel. 

Estamos calculado *significam % absoluta erro* e usaram como medida do desempenho para o modelo.  Levou 3 meses chegar a um modelo de trabalho usando o Excel.  Podemos colocado muita o aprendizado para o experimento ML Azure que, por fim, foi vantajoso em Noções básicas sobre requisitos.

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>Criar experimento comparável no aprendizado de máquina do Azure  
Seguimos estas etapas para criar nosso experimento no Azure ML:  

1.  Carregado o conjunto de dados como um arquivo csv Azure ML (arquivo muito pequeno)
2.  Criado um novo experimento e usado [Selecionar colunas em Dataset] [ select-columns] módulo para selecionar os mesmos recursos de dados usados no Excel   
3.  Os [Dados de divisão] usados[ split] módulo (com o modo de *Expressão relativa* ) para dividir os dados em exata mesmos conjuntos de trem como tivesse sido feito no Excel  
4.  Tentei com da [Regressão Linear] [ linear-regression] módulo (somente opções padrão), documentadas e comparado os resultados ao nosso modelo de regressão do Excel

### <a name="review-initial-results"></a>Revisar os resultados iniciais
Primeiro, o modelo do Excel claramente superou o desempenho do modelo do Azure ML:  

|   |Excel|Azure ML|
|---|:---:|:---:|
|Desempenho|   |  |
|<ul style="list-style-type: none;"><li>Ajustado R quadrado</li></ul>| 0,96 |N/D|
|<ul style="list-style-type: none;"><li>Coeficiente de <br />Determinação</li></ul>|N/D|   0.78<br />(precisão baixa)|
|Significam erro absoluto |  $9. 5M|  $ 19.4 M|
|Significam erro absoluto (%)|   6.03%|  12,2%

Quando podemos executou nosso processo e resultados pelos desenvolvedores e cientistas de dados da equipe do Azure ML, eles rapidamente fornecidos algumas dicas úteis.  

* Quando você usa a [Regressão Linear] [ linear-regression] módulo no Azure ML, dois métodos são fornecidos:
    *  Online descendente de gradiente: Talvez seja mais adequado para problemas de maior escala
    *  Quadrados menos comum: Este é o método a maioria das pessoas pensar quando eles ouvir regressão linear. Para pequenos conjuntos de dados, ordinário mínimos quadrados pode ser uma melhor opção.
*  Considere a possibilidade de ajuste o parâmetro de nível 2 Regularization peso para melhorar o desempenho. Ele é definido como 0,001 por padrão e para nosso pequeno conjunto de dados, podemos defini-lo como 0,005 para melhorar o desempenho.    

### <a name="mystery-solved"></a>Mystery resolvido!
Quando podemos aplicado as recomendações, podemos alcançado o mesmo desempenho de linha de base no Azure ML como com o Excel:   

|| Excel|Azure ML (inicial)|Azure ML com mínimos quadrados|
|---|:---:|:---:|:---:|
|Valor de rotulado  |Dados reais (numérico)|mesmo|mesmo|
|Aprendiz  |Excel -> dados análise -> regressão|Regressão linear.|Regressão linear|
|Opções de aprendiz|N/D|Padrões|ordinário mínimos quadrados<br />NÍVEL 2 = 0,005|
|Conjunto de dados|26 linhas, 3 recursos, 1 rótulo.   Todos os numérico.|mesmo|mesmo|
|Dividir: trem|Treinamento do Excel nas linhas primeiro 18, testadas nos últimos 8 linhas.|mesmo|mesmo|
|Dividir: teste|Fórmula de regressão aplicada à última 8 linhas do Excel|mesmo|mesmo|
|**Desempenho**||||
|Ajustado R quadrado|0,96|N/D||
|Coeficiente de determinação|N/D|0.78|0.952049|
|Significam erro absoluto |$9. 5M|$ 19.4 M|$9. 5M|
|Significam erro absoluto (%)|<span style="background-color: 00FF00;">6.03%</span>|12,2%|<span style="background-color: 00FF00;">6.03%</span>|

Além disso, os coeficientes de Excel comparado bem para os pesos de recurso no modelo de treinamento do Azure:

||Coeficientes do Excel|Espessuras de recurso Azure|
|---|:---:|:---:|
|Intercepção/inclinação|19470209.88|19328500|
|Um recurso|0.832653063|0.834156|
|Recurso B|11071967.08|11007300|
|Recurso C|25383318.09|25140800|

## <a name="next-steps"></a>Próximas etapas

Queremos consumir o serviço da web do Azure ML dentro do Excel.  Nossos analistas de negócios dependem do Excel e precisamos de uma maneira de chamar o serviço da web do Azure ML com uma linha de dados do Excel e ainda retornar o valor previsto para o Excel.   

Também queremos otimizar nosso modelo, usando as opções e algoritmos disponíveis no Azure ML.

### <a name="integration-with-excel"></a>Integração com o Excel
Nossa solução foi tornem operacional nosso modelo de regressão Azure ML pela criação de um serviço da web do modelo de treinamento.  Em poucos minutos, o serviço da web foi criado e chamamos-lo diretamente no Excel para retornar um valor de receita previsto.    

A seção do *Painel de serviços da Web* inclui uma pasta de trabalho do Excel podem ser baixada.  A pasta de trabalho vem pré-formatado com as API e o esquema de informações do serviço web inseridas.   Quando você clica em *Baixar pasta de trabalho do Excel*, ele é aberto e você poderá salvá-lo em seu computador local.    

![][1]
 
Com a pasta de trabalho aberta, copie os parâmetros predefinidos para a seção de parâmetro azul conforme mostrado abaixo.  Depois que os parâmetros são inseridos, Excel chama o serviço da web AzureML e marcou a previstas rótulos serão exibida na seção de valores previstos verde.  A pasta de trabalho continuará criar previsões para parâmetros com base no seu modelo de treinamento para todos os itens de linha inserida em parâmetros.   Para obter mais informações sobre como usar esse recurso, consulte [consumindo um serviço da Web do Azure máquina aprendizagem do Excel](machine-learning-consuming-from-excel.md). 

![][2]
 
### <a name="optimization-and-further-experiments"></a>Otimização e experiências ainda mais
Agora que tivemos uma linha de base com o nosso modelo do Excel, podemos ahead movido para otimizar nosso modelo de regressão Linear do Azure ML.  Usamos o módulo [seleção de recursos com base no filtro] [ filter-based-feature-selection] melhorar a nossa seleção de dados iniciais elementos e ele ajudaram atingir um melhor desempenho de 4.6% significam erro absoluto.   Para projetos futuros usaremos este recurso que foi possível salvar nos semanas no iteração atributos de dados para localizar o conjunto de recursos usar para modelagem certo.  

Próximo nós planeja incluir algoritmos adicionais como [Bayesiana] [ bayesian-linear-regression] ou [Árvores de decisão aumentou] [ boosted-decision-tree-regression] em nosso experimento para comparar o desempenho.    

Se você quiser experimentar regressão, um boa dataset tentar é o conjunto de dados de amostra de regressão de eficiência de energia, que tem muitos atributos numéricos. O conjunto de dados é fornecido como parte dos conjuntos de dados de exemplo no Studio ML.  Você pode usar uma variedade de módulos de aprendizado para prever carga aquecimento ou refrigeração carga.  O gráfico abaixo é que uma comparação de desempenho de regressão diferentes aprende contra a eficiência energética dataset prever para a variável de destino refrigeração carga: 

|Modelo|Significam erro absoluto|Média de raiz quadrado erro|Erro absoluto relativo|Erro ao quadrado relativas|Coeficiente de determinação
|---|---|---|---|---|---
|Árvore de decisão aumentadas|0.930113|1.4239|0.106647|0.021662|0.978338
|Regressão linear (gradiente descendente)|2.035693|2.98006|0.233414|0.094881|0.905119
|Rede neural regressão|1.548195|2.114617|0.177517|0.047774|0.952226
|Regressão linear (ordinário mínimos quadrados)|1.428273|1.984461|0.163767|0.042074|0.957926  

## <a name="key-takeaways"></a>Principais argumentos 

Aprendemos muito por de regressão em execução do Excel e aprendizado de máquina do Azure experimenta em paralelo. Criar o modelo de linha de base no Excel e comparando-o com modelos usando Azure ML [Regressão Linear] [ linear-regression] ajudou conosco Saiba ML do Azure e descobrimos oportunidades para melhorar o desempenho de seleção e o modelo de dados.         

Também descobrimos que ele é recomendável usar [seleção de recursos com base no filtro] [ filter-based-feature-selection] para acelerar previsão futuros projetos.  Aplicando a seleção de recursos aos seus dados, você pode criar um modelo aprimorado no Azure ML com melhor desempenho geral. 

A capacidade de transferir a previsão analítico previsão do Azure ML ao Excel sistemático permite um aumento significativo na capacidade com êxito fornecer resultados para uma audiência de usuário de negócios ampla.     


## <a name="resources"></a>Recursos
Alguns recursos estão listados para ajudar que você trabalhar com regressão:  

* Regressão no Excel.  Se você nunca tiver tentado regressão no Excel, este tutorial facilita: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Regressão vs de previsão.  Tyler Chessman escreveu um artigo de blog explicando como tempo série previsões no Excel, que contém a descrição do Iniciante boa de regressão linear. [http://sqlmag.com/SQL-Server-Analysis-Services/Understanding-Time-Series-Forecasting-Concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts)  
*   Regressão Linear ordinário mínimos quadrados: Falhas, problemas e armadilhas.  Para uma introdução e a discussão de regressão: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ )

[1]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 
