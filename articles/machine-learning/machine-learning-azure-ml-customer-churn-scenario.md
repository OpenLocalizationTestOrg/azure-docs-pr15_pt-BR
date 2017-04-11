<properties
    pageTitle="Analisar cliente rotatividade usando o aprendizado de máquina | Microsoft Azure"
    description="Estudo de caso de desenvolver um modelo integrado para analisar e pontuação rotatividade de cliente"
    services="machine-learning"
    documentationCenter=""
    authors="jeannt"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016" 
    ms.author="jeannt"/>

# <a name="analyzing-customer-churn-by-using-azure-machine-learning"></a>Analisar rotatividade de cliente usando o aprendizado de máquina do Azure

##<a name="overview"></a>Visão geral
Este tópico apresenta uma implementação de referência de um projeto de análise de rotatividade de cliente que é criado usando o Azure Studio de aprendizado de máquina. Ele discute modelos genéricos associados para todo resolver o problema de rotatividade de cliente industrial. Nós também medir a precisão dos modelos que são criados usando o aprendizado de máquina e podemos avaliar as direções para desenvolvimento futuro.  

### <a name="acknowledgements"></a>Confirmações

Esse experimento foi desenvolvido e testado por Serge Berger, cientista de dados de capital da Microsoft e Roger Barga, gerente de produto antigo para aprendizado de máquina do Microsoft Azure. A equipe de documentação Azure gratamente reconhece seus conhecimentos e Obrigado-los para o compartilhamento deste white paper.

>[AZURE.NOTE] Os dados usados para esse experimento não estão disponíveis publicamente. Para obter um exemplo de como criar um modelo de aprendizado de máquina para análise de rotatividade, consulte: [telecomunicações rotatividade modelo](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) na [Galeria de inteligência de Cortana](http://gallery.cortanaintelligence.com/)


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="the-problem-of-customer-churn"></a>O problema de rotatividade de cliente
Empresas no mercado consumidor e em todos os setores enterprise tem de lidar com rotatividade. Às vezes rotatividade é excessiva e influencia decisões de política. A solução tradicional é prever alto-propensity churners e suas necessidades através de um serviço de assistente, campanhas de marketing ou aplicando dispensations especiais de endereço. Essas abordagens podem variar do setor para setor e até mesmo a partir de um cluster de consumidor específico para outro dentro de um setor (por exemplo, telecomunicações).

O fator comum é que as empresas precisam minimizar esses esforços de retenção de cliente especial. Portanto, uma metodologia natural seria pontuação cada cliente com a probabilidade de rotatividade e os N maiores aquelas de endereço. Os clientes principais podem ser aquelas mais lucrativas; Por exemplo, em cenários mais sofisticados, uma função de lucro é empregada durante a seleção de candidatas dispensation especial. No entanto, essas considerações são apenas uma parte da estratégia abrangente para lidar com rotatividade. Empresas também precisam levar em conta risco (e tolerância de risco associado), o nível e o custo da intervenção e segmentação de cliente plausível.  

##<a name="industry-outlook-and-approaches"></a>Abordagens e outlook do setor
Sofisticados manipulação de rotatividade é um sinal de um setor desenvolvido. O exemplo clássico é o setor de telecomunicações onde assinantes são conhecidos frequentemente mudar de um provedor para outro. Este rotatividade voluntária é uma questão principal. Além disso, provedores acumulados conhecimento significativo sobre *rotatividade drivers*, quais são os fatores que clientes de unidade mudar.

Por exemplo, escolha fone de ouvido ou dispositivo é um driver conhecido de rotatividade da empresa de telefone celular. Como resultado, uma política popular é subsidiar o preço de um fone de ouvido para novos assinantes e carregando um preço total aos clientes existentes para uma atualização. Historicamente, essa política levou aos clientes saltos de um provedor para outro para obter um novo desconto, que por sua vez, tem solicitado provedores para refinar suas estratégias.

Alta volatilidade nas ofertas de fone é um fator que invalida rapidamente os modelos de rotatividade que são baseados em modelos de fone atual. Além disso, telefones celulares não são somente dispositivos de telecomunicações; Eles também são declarações de maneira (considere iPhone) e essas sociais corretas estão fora do escopo de conjuntos de dados de telecomunicações regular.

O resultado para modelagem é que você não pode planejar uma política de som simplesmente eliminando conhecidos motivos para rotatividade. Na verdade, uma estratégia de modelagem contínuo, incluindo modelos clássicos que mostre variáveis categóricos (como árvores de decisão), é **obrigatório**.

Usando grandes conjuntos de dados em seus clientes, organizações estão executando a análise de dados grande (em particular, detecção de rotatividade com base em dados grandes) como uma abordagem eficaz para o problema. Você pode encontrar mais informações sobre a abordagem de dados grande para o problema de rotatividade às recomendações na seção ETL.  

##<a name="methodology-to-model-customer-churn"></a>Metodologia para rotatividade de clientes do modelo
Um processo de solução de problemas comuns para resolver rotatividade de cliente é descrito em figuras 1-3:  

1.  Um modelo de risco permite que você considerar como ações afetam probabilidade e risco.
2.  Um modelo de intervenção permite que você considere como o nível de intervenção pode afetar a probabilidade de rotatividade e a quantidade de cliente do valor de vida útil (CLV).
3.  Essa análise permitem uma análise qualitativa que é encaminhada a uma campanha de marketing proativa destinos segmentos de clientes para fornecer a oferta ideal.  

![][1]

Essa abordagem encaminhar aparência é a melhor maneira de tratar rotatividade, mas ele vem com complexidade: temos desenvolver um arquétipo vários modelo e as dependências de rastreamento entre os modelos. Pode ser encapsulada a interação entre modelos, conforme mostrado no diagrama a seguir:  

![][2]

*Figura 4: Unified arquétipo vários modelo*  

Interação entre os modelos é chave se pudermos fornecer uma abordagem completa a retenção do cliente. Cada modelo necessariamente estiver prejudicado; Portanto, a arquitetura é um loop implícito (semelhante ao arquétipo definido pelo padrão de mineração de dados de CRISP-DM, [***3***]).  

O ciclo de geral de risco de decisão de marketing segmentação/Decomposição ainda é uma estrutura generalizada, que é aplicável a vários problemas de negócios. Análise de rotatividade é simplesmente um representante forte desse grupo de problemas porque ele exibe todas as características de um problema de negócios complexos que não permite que uma solução de previsão simplificada. Os aspectos sociais da abordagem moderna para rotatividade não são particularmente realçados na abordagem, mas os aspectos sociais são encapsulados em arquétipo modelagem, como seriam em qualquer modelo.  

Uma adição interessante aqui é a análise de dados grande. Hoje telecomunicações e empresas de varejo coletam dados completos sobre seus clientes e podemos facilmente pode prever que a necessidade de conectividade vários modelo se torne uma tendência comum, fornecida tendências atuais como a Internet das coisas e dispositivos de todos os lugares, que permitem empresa utilizam as soluções smart em várias camadas.  

 
##<a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implementando o arquétipo de modelagem no Studio de aprendizado de máquina
Considerando o problema apenas descrito, o que é a melhor maneira de implementar uma modelagem integrada e pontuação abordagem? Nesta seção, demonstraremos como podemos feito isso usando Studio de aprendizado de máquina do Azure.  

A abordagem vários modelo é ao criar um arquétipo global para rotatividade. Mesmo pontuação (previsão) parte da abordagem deve ser vários modelo.  

O diagrama a seguir mostra o protótipo que criamos, que utiliza quatro algoritmos pontuação no Studio de aprendizado de máquina prever rotatividade. O motivo para usar uma abordagem de vários modelo não é apenas criar um classificador ensemble para aumentar a precisão, mas também para se proteger contra ajuste excessivo e para aprimorar a seleção de recursos dirigidas.  

![][3]

*Figura 5: Protótipo de uma abordagem de modelagem de rotatividade*  

As seções a seguir fornecem mais detalhes sobre o protótipo pontuação modelo que estamos implementados usando Studio de aprendizado de máquina.  

###<a name="data-selection-and-preparation"></a>Preparação e seleção de dados
Os dados usados para criar os modelos e clientes de pontuação foi obtido uma solução CRM vertical, com os dados ofuscados para proteger a privacidade do cliente. Os dados contém informações sobre 8.000 assinaturas nos EUA, e ele combina três fontes: provisionamento de dados (metadados de assinatura), dados de atividade (uso do sistema) e dados de suporte do cliente. Os dados não inclui qualquer negócio informações relacionadas sobre os clientes; Por exemplo, ele não inclui pontuações de crédito ou metadados de fidelidade.  

Para simplificar, ETL e processos de limpeza de dados são sair do escopo porque vamos supor que preparação de dados já foi feitos em outro lugar.   

Seleção de recursos para modelagem baseia-se nos significância preliminar pontuação do conjunto de corretas, incluído no processo que usa o módulo floresta aleatório. Para a implementação no Studio de aprendizado de máquina, podemos calculada a média, MED e intervalos para recursos de representante. Por exemplo, adicionamos agregações para os dados qualitativos, como valores mínimo e máximo atividade do usuário.    

Nós também capturado temporais informações para os seis meses a mais recentes. Analisamos dados de um ano e podemos estabelecida que mesmo se houvesse tendências estatística significativas, o efeito sobre rotatividade é bastante reduzido após seis meses.  

O ponto mais importante é que todo o processo, incluindo ETL, seleção de recursos de modelagem e análise foi implementado no Studio de aprendizado de máquina, usando fontes de dados do Microsoft Azure.   

Os diagramas a seguir ilustram os dados que foi usados.  

![][4]

*Figura 6: Trecho da fonte de dados (ofuscada)*  

![][5]


*Figura 7: Recursos extraídos de fonte de dados*
 
> Observe que esses dados são particulares e, portanto, o modelo e os dados não podem ser compartilhados.
> No entanto, para um modelo semelhante usando dados publicamente disponíveis, consulte este exemplo experimentar na [Galeria de inteligência de Cortana](http://gallery.cortanaintelligence.com/): [Telecomunicações rotatividade de cliente](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Para saber mais sobre como você pode implementar um modelo de análise de rotatividade usando o pacote de inteligência de Cortana, recomendamos também [Este vídeo](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) por gerente de programa sênior dias da s Hyong Tok. 
> 

###<a name="algorithms-used-in-the-prototype"></a>Algoritmos usados no protótipo

Usamos os seguintes algoritmos de aprendizado de máquina quatro na criação de protótipo (sem personalização):  

1.  Regressão logística (LR)
2.  Árvore de decisão aumentadas (BT)
3.  Média perceptron (PA)
4.  Suporte a máquina de vetor (SVM)  


O diagrama a seguir ilustra uma parte da superfície de design experimento, que indica a sequência na qual os modelos foram criados:  

![][6]  


*Figura 8: Criação de modelos no Studio de aprendizado de máquina*  

###<a name="scoring-methods"></a>Métodos de pontuação
Podemos marcou os quatro modelos usando um conjunto de dados de treinamento rotuladas.  

Nós também enviado pontuação dataset a um modelo de comparável criado usando a edição da área de trabalho do SAS Enterprise mineração 12. Podemos medido a precisão do modelo de SAS e todos os quatro modelos Studio de aprendizado de máquina.  

##<a name="results"></a>Resultados
Nesta seção, apresentamos nossas conclusões sobre a precisão dos modelos, com base no conjunto de dados pontuação.  

###<a name="accuracy-and-precision-of-scoring"></a>Precisão e uma precisão de pontuação
Em geral, a implementação no aprendizado de máquina do Azure está atrás SAS em precisão cerca 10 a 15% (área em curva ou AUC).  

No entanto, a métrica mais importante no rotatividade é a taxa de misclassification: isto é, de churners as primeiras N como previsto pelo classificador, quais delas realmente faziam **não** rotatividade e ainda recebido tratamento especial? O diagrama a seguir compara essa taxa de misclassification para todos os modelos:  

![][7]


*Figura 9: Área de protótipo Passau em curva*

###<a name="using-auc-to-compare-results"></a>Usando AUC para comparar os resultados
Área em curva (AUC) é uma métrica que representa uma medida global do *Separabilidade* entre as distribuições de resultados para um grande número de positivos e negativos. É semelhante ao gráfico receptor operador característica (ROC) tradicional, mas uma diferença importante é que a métrica AUC não exige que você escolha um valor limite. Em vez disso, ele resume os resultados sobre **todas as** opções possíveis. Em contraste, o gráfico ROC tradicional mostra a taxa de positiva no eixo vertical e os falsos positivos no eixo horizontal e varia do limite de classificação.   

AUC geralmente é usado como uma medida do patrimônio para algoritmos diferentes (ou diferentes sistemas) porque ela permite modelos para ser comparado por meio de seus valores AUC. Essa é uma abordagem popular em setores como meteorology e biosciences. Portanto, AUC representa uma ferramenta popular para a avaliação de desempenho de classificador.  

###<a name="comparing-misclassification-rates"></a>Comparando taxas de misclassification
Podemos comparados as taxas de misclassification no conjunto de dados em questão usando os dados CRM de aproximadamente 8.000 assinaturas.  

-   A taxa de misclassification SAS foi 10 a 15%.
-   A taxa de misclassification Studio de aprendizado de máquina foi 15-20% para os churners início 200-300.  

No setor de telecomunicações, é importante somente os clientes que têm o maior risco para rotatividade oferecendo-los um serviço de assistente ou outro tratamento especial de endereço. Nesse ponto, a implementação de Studio de aprendizado de máquina obtém resultados da comparação com o modelo SAS.  

Com o mesmo token, precisão é mais importante do que a precisão como principalmente estamos interessados em Classificar corretamente churners possíveis.  

O diagrama a seguir da Wikipédia mostra a relação em um elemento gráfico excelentes, fáceis de entender:  

![][8]

*Figura 10: Relação entre precisão e precisão*

###<a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Resultados de precisão e a precisão do modelo de árvore de decisão aumentadas  

O gráfico a seguir exibe os resultados brutos da pontuação usando o protótipo de aprendizado de máquina para o modelo de árvore de decisão aprimorado, que vem a ser o mais preciso entre os quatro modelos:  

![][9]

*Figura 11: Características de modelo de árvore de decisão aumentadas*

##<a name="performance-comparison"></a>Comparação de desempenho
Podemos comparada a velocidade na qual os dados foi marcou usando os modelos de Studio de aprendizado de máquina e um modelo de comparável criados usando a edição da área de trabalho do SAS Enterprise mineração 12.1.  

A tabela a seguir resume o desempenho dos algoritmos:  

*Tabela 1. Desempenho geral (precisão) dos algoritmos*

| LR|BT|PA|SVM|
|---|---|---|---|
|Modelo de médio|O melhor modelo|Baixo desempenho|Modelo de médio|

Os modelos hospedados no Studio de aprendizado de máquina superou o desempenho SAS por 15-25% para velocidade de execução, mas precisão foi amplamente em paridade.  

##<a name="discussion-and-recommendations"></a>Discussão e recomendações
No setor de telecomunicações, várias práticas surgiram para analisar rotatividade, incluindo:  

-   Derivado métricas para quatro categorias fundamentais:
    -   **Entidade (por exemplo, uma assinatura)**. Provisionar informações básicas sobre a assinatura e/ou cliente que é o assunto da rotatividade.
    -   **Atividade**. Obter todas as informações de uso possíveis relacionados a entidade de, por exemplo, o número de logon.
    -   **Suporte ao cliente**. Coletar informações de logs de suporte ao cliente para indicar se a assinatura teve problemas ou interações com suporte ao cliente.
    -   **Dados da concorrência e de negócios**. Obter informações possíveis sobre o cliente (por exemplo, pode ser indisponível ou difícil de controlar).
-   Use prioridade a seleção do recurso de unidade. Isso significa que o modelo de árvore de decisão aumentadas sempre é uma abordagem prometendo.  

O uso dessas quatro categorias cria a ilusão de que uma abordagem simples *determinante* , com base em índices formados em fatores razoáveis por categoria, deve ser suficiente para identificar os clientes em risco de rotatividade. Infelizmente, embora essa noção parece plausível, é uma compreensão false. O motivo é que rotatividade é um efeito temporal e os fatores que contribuem para rotatividade geralmente são nos Estados temporárias. O que leva um cliente a considerar deixando hoje pode ser diferente amanhã, e ele certamente serão diferente seis meses a partir de agora. Portanto, um *probabilístico* modelo é uma necessidade.  

Essa observação importante é geralmente ignorada no business, que geralmente prefere uma abordagem de orientados a inteligência de negócios para análise, principalmente porque é fácil vender e admite automação simples.  

Entretanto, a promessa de análises de autoatendimento usando Studio de aprendizado de máquina é que as quatro categorias de informação, graduadas por divisão ou departamento, tornam-se uma fonte valiosa para aprendizado de máquina sobre rotatividade.  

Outro recurso interessante chegando em aprendizado de máquina do Azure é a capacidade de adicionar um módulo personalizado ao repositório de módulos predefinidos que já estão disponíveis. Esse recurso, essencialmente, cria uma oportunidade para selecionar bibliotecas e criar modelos para mercados verticais. É um diferencial importante de aprendizado de máquina do Azure no mercado.  

Esperamos continuar este tópico no futuro, principalmente relacionados a análise de dados grande.
  
##<a name="conclusion"></a>Conclusão
Este artigo descreve uma abordagem sensata para lidar com o problema comum de rotatividade de cliente, usando uma estrutura genérica. Estamos considerada um protótipo de pontuação modelos e implementado-lo usando o aprendizado de máquina do Azure. Por fim, podemos avaliados a precisão e o desempenho da solução protótipo em relação à algoritmos comparáveis em SAS.  

**Para obter mais informações:**  

Este artigo foi útil para você? Envie-nos seus comentários. Conte-nos em uma escala de 1 (ruim) a 5 (excelente), como você avalia este documento e por que você concedeu-esta classificação? Por exemplo:  

-   Você está classificação alta devido a ter bons exemplos, capturas de tela excelente, desmarque escrevendo ou outro motivo?
-   Você está classificação baixa devido a má exemplos, capturas de tela distorcida ou escrita confusa?  

Esse comentário nos ajudará a melhorar a qualidade de white papers que nós de lançamento.   

[Enviar comentários](mailto:sqlfback@microsoft.com).
 
##<a name="references"></a>Referências
Análise de previsão [1] : além do gerenciamento de informações de previsões, McKnight Oeste, julho/agosto de 2011, p.18-20.  

Artigo do Wikipedia [2] : [precisão e precisão](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: guia de mineração de dados passo a passo](http://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Marketing de grande volume: envolva os clientes com mais eficiência e agregar valor](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [telecomunicações rotatividade modelo](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) na [Galeria de inteligência de Cortana](http://gallery.cortanaintelligence.com/) 
 
##<a name="appendix"></a>Apêndice

![][10]

*Figura 12: Instantâneo de uma apresentação no rotatividade protótipo*


[1]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-10.png
