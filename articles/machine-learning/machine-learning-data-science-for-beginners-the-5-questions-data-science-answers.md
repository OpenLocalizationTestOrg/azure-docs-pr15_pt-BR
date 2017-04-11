<properties
   pageTitle="As perguntas de ciência de 5 dados - ciência de dados para iniciantes | Microsoft Azure"
   description="Obtenha uma rápida introdução às ciência de dados de ciência de dados para iniciantes, cinco vídeos curtos que começam com as respostas de ciências 5 perguntas dados."
   keywords="fazendo ciência de dados, iniciantes de ciência de dados, ciência de dados para iniciantes, tipos de perguntas, perguntas de ciência de dados, vídeo de ciência de dados"
   services="machine-learning"
   documentationCenter="na"
   authors="cjgronlund"
   manager="jhubbard"
   editor="cjgronlund"/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/20/2016"
   ms.author="cgronlun;garye"/>

# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Ciência de dados para iniciantes vídeo 1: 5 a perguntas respostas de ciência de dados

Obtenha uma rápida introdução às ciência de dados de *Ciência de dados para iniciantes* em cinco vídeos curtos de um cientista dados superior. Estes vídeos são básica, mas útil, se você estiver interessado em fazendo ciência de dados ou trabalhar com cientistas de dados.

Este vídeo primeiro faz sobre os tipos de perguntas que ciência de dados pode responder. Para obter o máximo proveito da série, assista todos eles. [Vá para a lista de vídeos](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-series-the-5-questions-data-science-answers]

## <a name="other-videos-in-this-series"></a>Outros vídeos desta série

*Ciência de dados para iniciantes* é uma rápida introdução às dados ciência demorando totais cerca de 25 minutos. Confira os outros quatro vídeos:

  * Vídeo 1: O 5 perguntas respostas de ciência de dados
  * Vídeo 2: [está pronto para ser ciência de dados a seus dados?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sec)*
  * [Fazer uma pergunta que você pode responder com dados](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) de vídeo 3: *(4 sec 17 min)*
  * Vídeo 4: [Prever uma resposta com um modelo simples](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sec)*
  * Vídeo 5: [Copiar o trabalho de outras pessoas para fazer ciência de dados](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 mínimo 18 sec)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Transcrição: O 5 perguntas respostas de ciência de dados

Oi! Bem-vindo aos série de vídeos *Ciência de dados para iniciantes*.

Ciência de dados pode ser complicada, portanto eu apresentaremos as Noções básicas para aqui sem qualquer equações ou jargão de programação de computador.

Neste vídeo primeiro, falaremos sobre "o 5 perguntas respostas de ciência de dados."

Ciência de dados usa nomes (também conhecido como categorias ou rótulos) e números para prever respostas para perguntas.

Ele pode ser surpreendente, mas *há apenas cinco perguntas que respostas de ciência de dados*:

  * É esta A ou B?
  * Isso é estranho?
  * Quanto – ou – quantos?
  * Como isso é organizado?
  * O que devo fazer em seguida?

  Cada uma dessas perguntas é respondida por uma família separada dos métodos de aprendizado de máquina, chamados algoritmos.


É útil pensar sobre um algoritmo como uma receita e seus dados como os componentes. Um algoritmo informa como combinar e misture os dados para obter uma resposta. Computadores são como Misturador. Eles fazem a maior parte do trabalho pesado do algoritmo para você e funcionam bem rápida.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Pergunta 1: É este ou B? usa algoritmos de classificação

Vamos começar com a pergunta: É este ou B?

![Algoritmos de classificação: É este ou B?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-classification-algorithms.png)

Essa família de algoritmos é chamada de classificação de classe dois.

Ele é útil para qualquer questão com apenas dois possíveis respostas.

Por exemplo:

  * Este pneus falhará nas próxima milhas 1.000: Sim ou não?
  * Que apresenta na mais clientes: um cupom de US $5 ou um desconto de 25%?

Essa pergunta também pode ser rephrased para incluir mais de duas opções: É este ou B ou C ou D, etc.?  Isso é chamado de classificação multiclass e é útil quando você tem várias — ou milhares — possíveis respostas. Classificação multiclass escolhe o mais provável.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Pergunta 2: Este é estranho? usa algoritmos de detecção de anomalia

A próxima pergunta ciência de dados pode responder é: É Este estranho? Essa pergunta é respondida por uma família de algoritmos chamado detecção de anomalias.

![Algoritmos de detecção de anomalias: É Este estranho?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-anomaly-detection-algorithms.png)


Se você tiver um cartão de crédito, você já tiver tirou proveito de detecção de anomalias. Sua empresa de cartão de crédito analisa seus padrões de compra, para que eles alertá-lo fraude possível. Encargos que são "estranhas" podem ser uma compra em uma loja em que você normalmente não comprar ou comprar um item incomum caro.

Essa pergunta pode ser útil de muitas maneiras. Por exemplo:

  * Se você tiver um carro com medidores de pressão, talvez você queira saber: É medidor pressão lendo normal?
  * Se você estiver monitorando a internet, você desejaria saber: É esta mensagem da internet típico?

Detecção de anomalias sinalizadores eventos inesperados ou incomuns ou comportamentos. Ele fornece dicas onde procurar problemas.



## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Pergunta 3: Quanto? ou como muitos? usa algoritmos de regressão

Aprendizado de máquina também pode prever a resposta como muito? ou como muitos? Família de algoritmo que responde a essa pergunta é chamada de regressão.

![Algoritmos de regressão: quanto? ou como muitos?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-regression-algorithms.png)


Algoritmos de regressão fazer previsões numéricos, tais como:

  * Qual a temperatura será próxima terça-feira?  
  * Qual será meu quarta vendas do trimestre?

Elas ajudam a qualquer pergunta que solicitem um número.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Pergunta 4: Como é isso organizado? usa algoritmos de agrupamento

Agora as duas últimas perguntas estão um pouco mais avançados.

Às vezes você deseja compreender a estrutura de um conjunto de dados - como isso é organizado? Para essa pergunta, você não tem exemplos que você já sabe resultados para.

Há muitas maneiras para separar da estrutura de dados. Uma abordagem é cluster. Ele separa dados em natural "agrupa," para interpretação mais fácil. Com clusters, não há nenhuma resposta certa.

![Agrupamento algoritmos: como isso é organizado?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-clustering-algorithms.png)

Exemplos comuns de perguntas clusters são:

  * Quais visualizadores como os mesmos tipos de filmes?
  * Quais modelos de impressora falham da mesma maneira?

Compreendendo como os dados são organizados, pode melhor compreender - e prever - comportamentos e eventos.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Pergunta 5: O que devo fazer agora? usa Reforce seu aprendizado algoritmos

A última pergunta – o que devo fazer agora? – usa uma família de algoritmos chamado aprendizagem Reforce seu.

Aprendizagem Reforce seu foi inspirada como o cérebro do rats e pessoas responde a punição e prêmios. Esses algoritmos saiba de resultados e decidir sobre a próxima ação.

Normalmente, aprendizado Reforce seu é uma boa opção para sistemas automatizados que deverá fazer muitas pequenas decisões sem orientação humana.

![Reforce seu algoritmos de aprendizagem: o que devo fazer em seguida?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-reinforcement-learning-algorithms.png)

Perguntas-respostas são sempre sobre qual ação deve ser tomada - geralmente por um computador ou um robô. Exemplos são:

  * Se sou um sistema de controle de temperatura de uma casa: ajustar a temperatura ou deixá-lo onde está?  
  * Se eu sou um carro auto carro: em uma luz amarela, freio ou acelerar?  
  * Para vácuo robô: mantenha Aspiração de pó ou voltar para a estação de cobrança?

Algoritmos de aprendizagem Reforce seu reunir dados conforme eles ir, aprendizado de avaliação e erro.

Isso é-ciência de dados de perguntas de 5 a pode responder.



## <a name="next-steps"></a>Próximas etapas

  * [Tente um experimento de ciência de dados primeiro com Studio de aprendizado de máquina](machine-learning-create-experiment.md)
  * [Obter uma introdução ao aprendizado de máquina no Microsoft Azure](machine-learning-what-is-machine-learning.md)
