<properties 
    pageTitle="Dicionário com base em análise de sentimento | Microsoft Azure" 
    description="Dicionário com base em análise de sentimento" 
    services="machine-learning" 
    documentationCenter="" 
    authors="pengxia" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/16/2016" 
    ms.author="pengxia"/> 



#<a name="lexicon-based-sentiment-analysis"></a>Dicionário com base em análise de sentimento 

Como você medir dos usuários opiniões e atitudes em direção marcas ou tópicos redes sociais online, como o Facebook postagens, tweets, revisões, etc.? Análise de sentimento fornece um método para analisar essas perguntas.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Geralmente, há dois métodos para análise de sentimento. Um está usando um algoritmo de aprendizagem supervisionada e o outro pode ser tratado como aprendizagem sem supervisão. Um algoritmo de aprendizagem supervisionada geralmente cria um modelo de classificação em um grande corpo com anotações. Sua precisão principalmente se baseia a qualidade da anotação e geralmente o processo de treinamento levará muito tempo. Além disso, quando podemos aplicar o algoritmo a outro domínio, o resultado geralmente não é bom. Em comparação com supervisionada usos de aprendizagem sem supervisão de aprendizado baseados no dicionário, um dicionário sentimento, que não requer armazenar um corpo de dados grandes e treinamento - que torna todo o processo muito mais rápido. 

Nosso [serviço](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) foi criada com o dicionário de subjetividade MPQA (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/), que é um dos dicionários de subjetividade mais comumente usados. Há 5097 palavras positivas negativas e 2533 MPQA. E todas estas palavras são anotadas como polaridade forte ou fraca. O corpo inteiro está sob licença pública geral GNU. O serviço da web pode ser aplicado a qualquer frases curtas, como tweets e postagens do Facebook. 

>Este serviço da web poderia ser consumido por usuários – potencialmente por meio de um aplicativo móvel, através de um site ou até mesmo em um computador local por exemplo. Mas também é o propósito do serviço da web servir como um exemplo de como o aprendizado de máquina do Azure pode ser usado para criar serviços web sobre código R. Com apenas algumas linhas de código de R e cliques de botão dentro do Azure Studio de aprendizado de máquina, um experimento pode ser criado com código de R e publicado como um serviço da web. O serviço da web pode ser publicado do Azure Marketplace e consumido por usuários e dispositivos em todo o mundo com nenhuma configuração de infraestrutura do autor do serviço da web.

##<a name="consumption-of-web-service"></a>Consumo de serviço web

Os dados de entrada podem ser qualquer texto, mas o serviço web funciona melhor com frases curtas. O resultado é um valor numérico entre -1 e 1. Qualquer valor abaixo de 0 indica que o sentimento do texto é negativo; Se positivo acima de 0. O valor absoluto do resultado indica a força do sentimento associado. 

>Esse serviço, como hospedado do Azure Marketplace, é um serviço OData; Esses podem ser chamadas por meio de métodos de postar ou obter. 

Há várias maneiras de consumo do serviço de forma automática (é um aplicativo de exemplo [aqui](http://microsoftazuremachinelearning.azurewebsites.net/)).

###<a name="starting-c-code-for-web-service-consumption"></a>Iniciando o código c# para consumo de serviço web:

    public class ScoreResult
    {
            [DataMember]
            public double result
            {
                get;
                set;
            }
    }

    void main()
    {
            using (var wb = new WebClient())
            {
                var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
                DataServiceContext ctx = new DataServiceContext(acitionUri);
                var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
                var cache = new CredentialCache();
    
                cache.Add(acitionUri, "Basic", cred);
                ctx.Credentials = cache;
                var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
                ScoreResult scoreResult = query.ElementAt(0);
                double result = scoreResult.result;
            }
    }



A entrada é "É um bom dia de hoje." O resultado é "1", que indica um sentimento positivo associado com a frase de entrada. 

##<a name="creation-of-web-service"></a>Criação de serviço web
>Este serviço da web foi criado usando aprendizado de máquina do Azure. Para uma avaliação gratuita, bem como vídeos introdutórios sobre a criação de experiências e [Serviços de publicação da web](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml). Abaixo está uma captura de tela da experiência que criou o código de exemplo e de serviço web para cada um dos módulos dentro do experimento.


De dentro de aprendizado de máquina do Azure, um experimento em branco novo foi criado. A figura a seguir mostra o fluxo de experimento da análise de sentimento com base no dicionário. O arquivo "sent_dict.csv" é o dicionário de subjetividade MPQA e é definido como uma das entradas de [Executar scripts de R][execute-r-script]. Outra entrada é uma revisão amostra do dataset Amazon revisão para teste, onde estamos realizado seleção, modificação de nome de coluna e dividir as operações. Podemos usar um pacote de hash para armazenar o dicionário de subjetividade na memória e acelerar o processo de cálculo de pontuação. O texto inteiro será indexado por pacote "tm" e em comparação com a palavra no dicionário sentimento. Finalmente, uma pontuação será calculada adicionando a espessura de cada palavra subjetiva no texto. 

###<a name="experiment-flow"></a>Fluxo de experimento:

![Experimente fluxo][2]


####<a name="module-1"></a>Módulo 1:
    
    # Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
 
   # <a name="install-hash-package"></a>Instale o pacote de hash
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)

    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
    polarity_ratio <- 0
    polarity_total <- 0
    not <- 0
    sentence <- tolower(dataset2[m,1])
    if (nchar(sentence) > 0){
        token_array <- scan_tokenizer(sentence)
        for (j in 1:length(token_array)){
            word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
            for (k in 1:length(negation_word)){
              if (word == negation_word[k]){
                not <- (not+1) %% 2

              }
            }
            if (word != ""){
                if (!is.null(sent_dict[[word]])){
                  polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
                  polarity_total <- polarity_total + abs(sent_dict[[word]])
                }
            }
          
        }
    }
    if (polarity_total > 0){
        result <- c(result, polarity_ratio/polarity_total)
    }else{
        result<- c(result,0)
    }
    }

    # Sample operation
    data.set <- data.frame(result)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
    


##<a name="limitations"></a>Limitações

De uma perspectiva de algoritmo, a análise de sentimento com base no dicionário é uma ferramenta de análise sentimento geral, que não pode funcionar melhor do que o método de classificação para campos específicos. O problema de negação não é bem resolvido. Nós codificar negação várias palavras em nosso programa, mas uma melhor maneira é usar um dicionário de negação e criar algumas regras. O serviço da web executa melhor em frases curtas e simples, como tweets e postagens do Facebook, que em sentenças longas e complexas como revisões Amazon. 

##<a name="faq"></a>Perguntas Freqüentes
Para perguntas frequentes sobre o consumo do serviço da web ou publicação para do Azure Marketplace, veja [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

 
