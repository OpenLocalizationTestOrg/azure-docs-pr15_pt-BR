<properties 
    pageTitle="Pacote de serviço Web distribuição normal | Microsoft Azure" 
    description="Pacote de serviço Web distribuição normal" 
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

#<a name="normal-distribution-suite"></a>Pacote de distribuição normal



O pacote de distribuição Normal é um conjunto de serviços de web de amostra ([gerador]( https://datamarket.azure.com/dataset/aml_labs/ndg7), [Calculadora de Quantile]( https://datamarket.azure.com/dataset/aml_labs/ndq5), [Calculadora de probabilidade]( https://datamarket.azure.com/dataset/aml_labs/ndp5)) que ajudar na gerando e manipulando distribuições normal. Os serviços permitem gerando uma sequência de distribuição normal de qualquer comprimento, calculando quantiles de uma determinada probabilidade e, em seguida, calculando a probabilidade de um determinado quantile. Cada um dos serviços emite saídas diferentes com base no serviço selecionado (consulte a descrição abaixo). O pacote de distribuição Normal baseia-se no R funções qnorm, rnorm e pnorm, que são incluídos no pacote de estatísticas de R.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>Este serviço da web poderia ser consumido por usuários – potencialmente por meio de um aplicativo móvel, através de um site ou até mesmo em um computador local, por exemplo. Mas também é o propósito do serviço da web servir como um exemplo de como o aprendizado de máquina do Azure pode ser usado para criar serviços web sobre código R. Com apenas algumas linhas de código de R e cliques de botão dentro do Azure Studio de aprendizado de máquina, um experimento pode ser criado com código de R e publicado como um serviço da web. O serviço da web pode ser publicado do Azure Marketplace e consumido por usuários e dispositivos em todo o mundo com nenhuma configuração de infraestrutura do autor do serviço da web.  
 

##<a name="consumption-of-web-service"></a>Consumo de serviço web
O pacote de distribuição Normal inclui os seguintes 3 serviços.

###<a name="normal-distribution-quantile-calculator"></a>Calculadora de Quantile de distribuição normal
Este serviço aceita 4 argumentos de uma distribuição normal e calcula o quantile associado.

Os argumentos de entrada são:

* p - uma única probabilidade de um evento com distribuição normal. 
* Média - a média de distribuição normal.
* SD - o desvio padrão da distribuição normal. 
* Lado - L para o lado inferior da distribuição e U para o lado superior da distribuição.

A saída do serviço é o quantile calculado que está associado a probabilidade de determinada.

###<a name="normal-distribution-probability-calculator"></a>Calculadora de probabilidade de distribuição normal
Este serviço aceita 4 argumentos de uma distribuição normal e calcula a probabilidade associada.

Os argumentos de entrada são:

* p - um único quantile de um evento com distribuição normal. 
* Média - a média de distribuição normal.
* SD - o desvio padrão da distribuição normal. 
* Lado - L para o lado inferior da distribuição e U para o lado superior da distribuição.

A saída do serviço é a probabilidade de cálculo que está associada a determinado quantile.

###<a name="normal-distribution-generator"></a>Gerador de distribuição normal
Este serviço aceita 3 argumentos de uma distribuição normal e gera uma sequência aleatória de números que são normalmente distribuídos. Os argumentos a seguir deverão ser fornecidos a ele dentro da solicitação:

* n - o número de observações. 
* Média - a média de distribuição normal.
* SD - o desvio padrão da distribuição normal. 

A saída do serviço é uma sequência de comprimento n com uma distribuição normal com base nos argumentos média e sd.

>Esse serviço, como hospedado do Azure Marketplace, é um serviço OData; Esses podem ser chamadas por meio de métodos de postar ou obter. 

Há várias maneiras de consumo do serviço de forma automática (aplicativos de exemplo estão aqui: [gerador](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx), [Calculadora de probabilidade](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx), [Calculadora de Quantile](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx)).

###<a name="starting-c-code-for-web-service-consumption"></a>Iniciando o código c# para consumo de serviço web:

###<a name="normal-distribution-quantile-calculator"></a>Calculadora de Quantile de distribuição normal
    public class Input
    {
            public string p;
            public string mean;
            public string sd;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { p = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


###<a name="normal-distribution-probability-calculator"></a>Calculadora de probabilidade de distribuição normal
    public class Input
    {
            public string q;
            public string mean;
            public string sd;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { q = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

###<a name="normal-distribution-generator"></a>Gerador de distribuição normal
    public class Input
    {
            public string n;
            public string mean;
            public string sd;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { n = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text };
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
>Este serviço da web foi criado usando aprendizado de máquina do Azure. Para uma avaliação gratuita, bem como vídeos introdutórios sobre a criação de experiências e [Serviços de publicação da web](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml). 

Abaixo está uma captura de tela da experiência que criou o código de exemplo e de serviço web para cada um dos módulos dentro do experimento.

###<a name="normal-distribution-quantile-calculator"></a>Calculadora de Quantile de distribuição normal

Fluxo de experimento:

![Fluxo de experimento][2]
 
    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }
    q = qnorm(param$p,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    q = 2* param$mean - q
    } else if (param$side =='L') {
    q = q
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(q)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");
    
###<a name="normal-distribution-probability-calculator"></a>Calculadora de probabilidade de distribuição normal
Fluxo de experimento:

![Fluxo de experimento][3]
 
    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(q=-1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    prob = pnorm(param$q,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    prob = 1 - prob
    } else if (param$side =='B') {
    prob = ifelse(prob<=0.5,prob * 2, 1)
    } else if (param$side =='L') {
    prob = prob
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");
    
###<a name="normal-distribution-generator"></a>Gerador de distribuição normal
Fluxo de experimento:

![Fluxo de experimento][4]

    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,mean=0,sd=1);
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    dist = rnorm(param$n,mean=param$mean,sd=param$sd)

    output = as.data.frame(t(dist))

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##<a name="limitations"></a>Limitações 

Estes são exemplos muito simples ao redor de uma distribuição normal. Como pode ser visto do código de exemplo acima, captura de erro pouco é implementado.

##<a name="faq"></a>Perguntas Freqüentes
Para perguntas frequentes sobre o consumo do serviço da web ou publicação para do Azure Marketplace, veja [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png
 
