<properties 
    pageTitle="Ajuste exponencial a previsão | Microsoft Azure" 
    description="Serviço da Web: suavização exponencial de previsão" 
    services="machine-learning" 
    documentationCenter="" 
    authors="xueshanz" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016" 
    ms.author="xueshzha"/> 


#<a name="forecasting---exponential-smoothing"></a>Previsão - Ajuste exponencial 

Este [serviço web]( https://datamarket.azure.com/dataset/aml_labs/ets) implementa o modelo de ajuste exponencial (ETS) para produzir previsões com base em dados históricos fornecidos pelo usuário. A demanda por um produto específico aumentará deste ano? Pode posso prever meu vendas de produtos para o Natal, para que eu pode planejar efetivamente os meu estoque? Modelos de previsão são chance dessas perguntas de endereço. Considerando os últimos dados, esses modelos de examinar tendências ocultas e sazonalidade prever tendências futuras.  


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 
>Este serviço da web poderia ser consumido por usuários – potencialmente por meio de um aplicativo móvel, através de um site ou até mesmo em um computador local, por exemplo. Mas também é o propósito do serviço da web servir como um exemplo de como o aprendizado de máquina do Azure pode ser usado para criar serviços web sobre código R. Com apenas algumas linhas de código de R e cliques de botão dentro do Azure Studio de aprendizado de máquina, um experimento pode ser criado com código de R e publicado como um serviço da web. O serviço da web pode ser publicado do Azure Marketplace e consumido por usuários e dispositivos em todo o mundo com nenhuma configuração de infraestrutura do autor do serviço da web.
 
##<a name="consumption-of-web-service"></a>Consumo de serviço web 
 
Este serviço aceita 4 argumentos e calcula as previsões ETS.
Os argumentos de entrada são:

* Frequência - indica a frequência dos dados processados (diariamente/semanais/mensais/trimestrais/anual).
* Horizonte - futuro previsão do tempo.
* Data - adicione os novos dados de série de tempo para o tempo.
* Valor - adicionar os novos valores de dados de série de tempo.

A saída do serviço é os valores calculados de previsão.

Entrada de exemplo pode ser: 

* Frequência - 12
* Horizonte - 12
* Data - 15/1/2012; 2/15/2012 15/3/2012; 15/4/2012; 15/5/2012; 15/6/2012; 15/7/2012; 8 / 15/2012 9/15/2012 15/10/2012; 15/11/2012; 15/12/2012; 15/1/2013; 2/15/2013 15/3/2013; 15/4/2013; 15/5/2013; 15/6/2013; 15/7/2013; 8 / 15/2013 15/9/2013 15/10/2013; 15/11/2013; 15/12/2013; 15/1/2014; 2/15/2014 3/15/2014; 15/4/2014; 15/5/2014; 15/6/2014; 15/7/2014; 8 / 15/2014; 15/9/2014
* Valor - 3.479; 3.68; 3.832; 3.941; 3.797; 3.586; 3.508; 3.731; 3.915; 3.844; 3.634; 3.549; 3.557; 3.785; 3.782; 3.601; 3.544; 3.556; número para 3,65; 3.709; 3.682; 3.511; 3.429 3.51 3.523; 3.525; 3.626; 3.695; 3.711; 3.711; 3.693; 3.571; 3.509
 
>Esse serviço, como hospedado do Azure Marketplace, é um serviço OData; Esses podem ser chamadas por meio de métodos de postar ou obter. 

Há várias maneiras de consumo do serviço de forma automática (é um aplicativo de exemplo [aqui](http://microsoftazuremachinelearning.azurewebsites.net/etsForecasting.aspx)).

###<a name="starting-c-code-for-web-service-consumption"></a>Iniciando o código c# para consumo de serviço web:
    public class Input
    {
            public string frequency;
            public string horizon;
            public string date;
            public string value;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };
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

De dentro de aprendizado de máquina do Azure, um experimento em branco novo foi criado. Dados de entrada de amostra foi carregados com um esquema de dados predefinidos. Vinculado ao esquema de dados é um [Script de R executar] [ execute-r-script] módulo que gera o ETS previsão modelo usando funções 'ets' e 'previsão' de R. 


![Fluxo de experimento][2]

####<a name="module-1"></a>Módulo 1:
 
    # Add in the CSV file with the data in the format shown below 
![Exemplo de dados][3]   

####<a name="module-2"></a>Módulo 2:
    # Data input
    data <- maml.mapInputPort(1) # class: data.frame
    library(forecast)
    
    # Preprocessing
    colnames(data) <- c("frequency", "horizon", "dates", "values")
    dates <- strsplit(data$dates, ";")[[1]]
    values <- strsplit(data$values, ";")[[1]]
    
    dates <- as.Date(dates, format = '%m/%d/%Y')
    values <- as.numeric(values)
    
    # Fit a time-series model
    train_ts<- ts(values, frequency=data$frequency)
    fit1 <- ets(train_ts)
    train_model <- forecast(fit1, h = data$horizon)
    plot(train_model)
    
    # Produce forcasting
    train_pred <- round(train_model$mean,2)
    data.forecast <- as.data.frame(t(train_pred))
    colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
    
    # Data output
    maml.mapOutputPort("data.forecast");

 
##<a name="limitations"></a>Limitações 

Este é um exemplo muito simples para fazer previsões ETS. Como pode ser visto do código de exemplo acima, nenhum erro captura é implementado e o serviço pressupõe que todas as variáveis são valores de contínua/positivo e a frequência deve ser um inteiro maior que 1. O comprimento de vetores de valor de data e deve ser o mesmo. A variável de data deve seguir o formato ' dd/mm/aaaa'.

##<a name="faq"></a>Perguntas Freqüentes
Para perguntas frequentes sobre o consumo do serviço da web ou publicação para do Azure Marketplace, veja [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img1.png
[2]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img2.png
[3]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
