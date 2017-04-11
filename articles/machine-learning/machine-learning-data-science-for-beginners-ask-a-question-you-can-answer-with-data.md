<properties
   pageTitle="Faça uma pergunta que você pode responder com dados - formula perguntas | Microsoft Azure"
   description="Aprenda a Formula uma pergunta de ciência de dados em ciência de dados para iniciantes M3. Inclui uma comparação de perguntas de classificação e regressão."
   keywords="perguntas de ciência de dados, formula perguntas, perguntas de regressão e perguntas de classificação, nitidez pergunta"
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

# <a name="ask-a-question-you-can-answer-with-data"></a>Faça uma pergunta que você pode responder com dados

## <a name="video-3-data-science-for-beginners-series"></a>Vídeo 3: Ciência de dados para série iniciantes

Aprenda a Formula uma pergunta de ciência de dados em ciência de dados para iniciantes M3. Este vídeo inclui uma comparação de perguntas para algoritmos de classificação e regressão.

Para obter o máximo proveito da série, assista todos eles. [Vá para a lista de vídeos](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-ask-a-question-you-can-answer-with-data]

## <a name="other-videos-in-this-series"></a>Outros vídeos desta série

*Ciência de dados para iniciantes* é uma rápida introdução às ciência de dados em cinco vídeos curtos.

  * Vídeo 1: [As respostas de ciência de dados 5 perguntas](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 s)*
  * Vídeo 2: [está pronto para ser ciência de dados a seus dados?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sec)*
  * Vídeo 3: Fazer uma pergunta que você pode responder com dados
  * Vídeo 4: [Prever uma resposta com um modelo simples](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sec)*
  * Vídeo 5: [Copiar o trabalho de outras pessoas para fazer ciência de dados](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 mínimo 18 sec)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Transcrição: Fazer uma pergunta que você pode responder com dados

Bem-vindo ao vídeo terceiro na série "Ciência de dados para iniciantes".  

Nesse, você receberá algumas dicas para formular uma pergunta que você pode fazer com os dados.

Você pode obter o máximo neste vídeo, se você primeiro observar os dois vídeos anteriores nesta série: "a ciência de dados 5 perguntas pode responder" e "É seus dados estão prontos para ciência de dados?"

## <a name="ask-a-sharp-question"></a>Faça uma pergunta nítida

Que já discutimos como ciência de dados é o processo de usar nomes (também chamados de categorias ou rótulos) e números prever uma resposta para uma pergunta. Mas não pode ser qualquer pergunta; ele deve ser um *pergunta nítida.*

Uma pergunta vaga não tem a serem respondidas com um nome ou um número. Deve ser uma pergunta nítida.

Imagine que você encontrou uma lâmpada mágica com um genie quem modo verdadeiro responderá qualquer pergunta. Mas é uma genie mal intencionada e ele vai tentar fazer sua resposta como vagas e confusa como ele pode obter ausente com. Você deseja fixá-lo para baixo com uma pergunta impecável assim que ele não pode ajudar mas informa o que você deseja saber.

Se você tivesse que fazer uma pergunta vaga, como "O que vai acontecer com minha stock?", o genie pode responder, "o preço será alterado". Esta é uma resposta confiáveis, mas não é muito útil.

Mas se você tivesse que fazer uma pergunta nítida, como "Qual o preço de venda do meu estoque será próxima semana?", o genie não pode ajudar mas dará uma determinada responder e prever um preço de venda.

## <a name="examples-of-your-answer-target-data"></a>Exemplos de sua resposta: dados de destino

Depois que você formula sua pergunta, verifique se você tem exemplos da resposta em seus dados.

Se nossa pergunta for "Qual o preço de venda do meu estoque será próxima semana?" em seguida, temos que garantir que nossos dados incluem o histórico de preços de ações.

Se nossa pergunta for "qual carro em meu frota vai falhar primeiro?" em seguida, temos que garantir que nossos dados incluem informações sobre falhas anteriores.

![Data de destino - exemplos de sua resposta. Formula uma pergunta de ciência de dados.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-target-data.png)

Esses exemplos de respostas são chamados de destino. Um destino é o que estamos tentando prever sobre pontos de dados no futuro, se você tiver uma categoria ou um número.

Se você não tiver quaisquer dados de destino, você precisará obter algumas. Você não conseguirá responder sua pergunta sem ele.

## <a name="reformulate-your-question"></a>Reformulate sua pergunta

Às vezes, você pode reformular sua pergunta para obter uma resposta mais útil.

A pergunta "É este dados ponto ou B?" prevê a categoria (ou nome ou rótulo) de algo. Para responder, podemos usar um *algoritmo de classificação*.

A pergunta "Quanto?" ou "Quantos"? prevê um valor. Para responder, podemos usar um *algoritmo de regressão*.

Para ver como podemos pode transformar esses, vamos examinar a pergunta, "qual notícia é mais interessantes para este leitor?" Ele pede uma previsão de uma única opção de muitas possibilidades - em outras palavras "É este ou B ou C ou D?" - e usaria um algoritmo de classificação.

Mas, essa pergunta pode ser mais fácil de responder se você reformular como "como interessante é cada matéria nesta lista para este leitor?" Agora você pode dar a cada artigo uma pontuação numérica e, em seguida, é fácil identificar o artigo maior pontuação. Este é um reformular da pergunta classificação em uma pergunta de regressão ou quanto?

![Reformulate sua pergunta. Pergunta de classificação versus pergunta de regressão.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-classification-question-vs-regression-question.png)

Como você pedir que uma pergunta é uma dica para qual algoritmo pode dar uma resposta.

Você encontrará certas famílias de algoritmos - como as mostradas em nosso exemplo de história de notícias - estão intimamente relacionadas. Você pode reformulate sua pergunta para usar o algoritmo que oferece a você a resposta mais úteis.

Mas, mais importante, faça essa pergunta nítida - a pergunta que você pode responder com dados. E certifique-se de que você tem os dados corretos respondê-la.

Que já discutimos alguns princípios básicos de uma pergunta que você pode responder com dados.

Certifique-se de fazer check-out os outros vídeos "Dados ciência para iniciantes", de aprendizado de máquina do Microsoft Azure.


## <a name="next-steps"></a>Próximas etapas

  * [Tente um experimento de ciência de dados primeiro com Studio de aprendizado de máquina](machine-learning-create-experiment.md)
  * [Obter uma introdução ao aprendizado de máquina no Microsoft Azure](machine-learning-what-is-machine-learning.md)
