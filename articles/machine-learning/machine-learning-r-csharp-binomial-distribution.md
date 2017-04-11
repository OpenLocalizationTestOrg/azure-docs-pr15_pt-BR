<properties 
    pageTitle="Pacote de distribuição binomial | Microsoft Azure" 
    description="Distribuição binomial Suite" 
    services="machine-learning" 
    documentationCenter="" 
    authors="ireiter" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="ireiter"/> 


#<a name="binomial-distribution-suite"></a>Distribuição binomial Suite




O pacote de distribuição Binomial é um conjunto de serviços de web de amostra ([Gerador Binomial](https://datamarket.azure.com/dataset/aml_labs/bdg5), [Calculadora de probabilidade]( https://datamarket.azure.com/dataset/aml_labs/bdp4), [Calculadora de Quantile]( https://datamarket.azure.com/dataset/aml_labs/bdq5)) que ajudar na gerando e lidando com distribuições binomial. Os serviços permitem que gerando uma sequência de distribuição binomial de qualquer comprimento, calculando quantiles ausência fornecidas probabilidade e calcular a probabilidade de um determinado quantile. Cada um dos serviços emite saídas diferentes com base no serviço selecionado (consulte a descrição abaixo). O pacote de distribuição Binomial baseia-se no R funções qbinom, rbinom e pbinom, que são incluídos no pacote de estatísticas de R. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>Esses serviços da web podem ser consumidos por usuários – potencialmente diretamente no marketplace, por meio de um aplicativo móvel, através de um site ou até mesmo em um computador local, por exemplo. Mas também é o propósito do serviço da web servir como um exemplo de como o aprendizado de máquina do Azure pode ser usado para criar serviços web sobre código R. Com apenas algumas linhas de código de R e cliques de botão dentro do Azure Studio de aprendizado de máquina, um experimento pode ser criado com código de R e publicado como um serviço da web. O serviço da web pode ser publicado do Azure Marketplace e consumido por usuários e dispositivos em todo o mundo – nenhuma configuração de infraestrutura do autor do serviço da web é necessária.

##<a name="consumption-of-web-service"></a>Consumo de serviço web
O pacote de distribuição Binomial inclui os seguintes 3 serviços.

###<a name="binomial-distribution-quantile-calculator"></a>Distribuição binomial Quantile Calculadora
Este serviço aceita 4 argumentos de uma distribuição normal e calcula o quantile associado.
Os argumentos de entrada são:

- p - um único agregados probabilidade de várias tentativas.  
- tamanho - o número de tentativas.
- PROB - a probabilidade de sucesso em uma versão de avaliação.
- Lado - L para o lado inferior da distribuição, U para o lado superior da distribuição. 

A saída do serviço é o quantile calculado que está associado a probabilidade de determinada.

###<a name="binomial-distribution-probability-calculator"></a>Calculadora de probabilidade de distribuição binomial
Este serviço aceita 4 argumentos de uma distribuição binomial e calcula a probabilidade associada.
Os argumentos de entrada são:

- p - um único quantile de um evento com distribuição binomial. 
- tamanho - o número de tentativas.
- PROB - a probabilidade de sucesso em uma versão de avaliação.
- lado - L para o lado inferior da distribuição, U para o lado superior da distribuição ou E que é igual a um único número de sucessos.

A saída do serviço é a probabilidade de cálculo que está associada a determinado quantile.

###<a name="binomial-distribution-generator"></a>Gerador de distribuição binomial
Este serviço aceita 3 argumentos de uma distribuição binomial e gera uma sequência aleatória de números binomially distribuídos. Os argumentos a seguir deverão ser fornecidos a ele dentro da solicitação:

- n - número de observações. 
- tamanho - o número de tentativas.
- PROB - a probabilidade de sucesso.

A saída do serviço é uma sequência de comprimento n com uma distribuição binomial com base nos argumentos tamanho e prob.

>Esse serviço, como hospedado do Azure Marketplace, é um serviço OData; Esses podem ser chamadas por meio de métodos de postar ou obter. 

Há várias maneiras de consumo do serviço de forma automática (aplicativos de exemplo estão aqui: [gerador](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionGenerator.aspx), [Calculadora de probabilidade](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionProbabilityCalculator.aspx), [Calculadora de Quantile](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionQuantileCalculator)). 

###<a name="starting-c-code-for-web-service-consumption"></a>Iniciando o código c# para consumo de serviço web:

###<a name="binomial-distribution-quantile-calculator"></a>Distribuição binomial Quantile Calculadora
    public class Input
    {
            public string p;
            public string size;
            public string prob;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void main()
    {
            var input = new Input() { p = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

###<a name="binomial-distribution-probability-calculator"></a>Calculadora de probabilidade de distribuição binomial
    public class Input
    {
            public string q;
            public string size;
            public string prob;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { q = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = " PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


###<a name="binomial-distribution-generator"></a>Gerador de distribuição binomial
    public class Input
    {
            public string n;
            public string size;
            public string p;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { n = TextBox1.Text, size = TextBox2.Text, p = TextBox3.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }





##<a name="creation-of-web-service"></a>Criação de serviço web 

>Este serviço da web foi criado usando aprendizado de máquina do Azure. Para uma avaliação gratuita, bem como vídeos introdutórios sobre a criação de experiências e [Serviços de publicação da web](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml). Abaixo está uma captura de tela da experiência que criou o código de exemplo e de serviço web para cada um dos módulos dentro do experimento.

###<a name="binomial-distribution-quantile-calculator"></a>Distribuição binomial Quantile Calculadora

![Criar espaço de trabalho][4]

####<a name="module-1"></a>Módulo 1:
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
####<a name="module-2"></a>Módulo 2:

    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }

    if (param$prob < 0 ) {
    print('Bad input: prob must be between 0 and 1')
    param$prob = 0
    } else if (param$prob > 1) {
    print('Bad input: prob must be between 0 and 1')
    param$prob = 1
    }

    quantile = qbinom(param$p,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    quantile

    if (param$side == 'U'){
    quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = F)
    band=subset(df,x>quantile)
    } else if (param$side =='L') {
    quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = T)
    band=subset(df,x<=quantile)
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(quantile)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");


###<a name="binomial-distribution-probability-calculator"></a>Calculadora de probabilidade de distribuição binomial

![Criar espaço de trabalho][5]

####<a name="module-1"></a>Módulo 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(q=5,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port


####<a name="module-2"></a>Módulo 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    prob = pbinom(param$q,size=param$size,prob=param$prob)
    prob.eq = dbinom(param$q,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    prob

    if (param$side == 'U'){
    prob = 1 - prob
    band=subset(df,x>param$q)
    } else if (param$side =='E') {
    prob = prob.eq
    band=subset(df,x==param$q)
    } else if (param$side =='L') {
    prob = prob
    band=subset(df,x<=param$q)
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

###<a name="binomial-distribution-generator"></a>Gerador de distribuição binomial

![Criar espaço de trabalho][6]

####<a name="module-1"></a>Módulo 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,size=10,p=.5);
    maml.mapOutputPort("data.set"); #send data to output port

####<a name="module-2"></a>Módulo 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    dist = rbinom(param$n,param$size,param$p)

    output = as.data.frame(t(dist))
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##<a name="limitations"></a>Limitações 
Estes são exemplos muito simples ao redor a distribuição binomial. Como pode ser visto do código de exemplo acima, captura de erro pouco é implementado.

##<a name="faq"></a>Perguntas Freqüentes
Para perguntas frequentes sobre o consumo do serviço da web ou publicação para do Azure Marketplace, veja [aqui](machine-learning-marketplace-faq.md).


[1]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_1.png

[2]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_2.png

[3]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_3.png

[4]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_4.png

[5]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_5.png

[6]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_6.png
 
