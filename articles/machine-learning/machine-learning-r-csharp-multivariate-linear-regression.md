<properties 
    pageTitle="Regressão Linear multivariate | Microsoft Azure" 
    description="Multivariate Regressão Linear" 
    services="machine-learning" 
    documentationCenter="" 
    authors="jaymathe" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016" 
    ms.author="jaymathe"/> 


#<a name="multivariate-linear-regression"></a>Multivariate Regressão Linear   
 

 
Suponha que você tem um conjunto de dados e gostaria de prever rapidamente uma variável dependente (y) para cada (i) com base em variáveis independentes. Regressão linear é uma técnica de estatística popular usada para tais previsões. Veja a variável dependente y é considerada um valor contínuo.  


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

Um cenário simple poderia ser onde o pesquisador está tentando prever a espessura de uma pessoa (y) com base em sua altura (x). Um cenário mais avançado pode ser onde o Pesquisador tem informações adicionais para a pessoa (como espessura, gênero, corrida) e tenta prever a espessura da pessoa. Este [serviço web]( https://datamarket.azure.com/dataset/aml_labs/multivariate_regression) caiba o modelo de regressão linear para os dados e saídas valor previsto (y) para cada um das observações dos dados.

>Este serviço da web poderia ser consumido por usuários – potencialmente por meio de um aplicativo móvel, através de um site ou até mesmo em um computador local, por exemplo. Mas também é o propósito do serviço da web servir como um exemplo de como o aprendizado de máquina do Azure pode ser usado para criar serviços web sobre código R. Com apenas algumas linhas de código de R e cliques de botão dentro do Azure Studio de aprendizado de máquina, um experimento pode ser criado com código de R e publicado como um serviço da web. O serviço da web pode ser publicado do Azure Marketplace e consumido por usuários e dispositivos em todo o mundo com nenhuma configuração de infraestrutura do autor do serviço da web.  

##<a name="consumption-of-web-service"></a>Consumo de serviço web  
Este serviço web fornece os valores previstos da variável dependente com base em variável independente para todas as observações. O serviço da web espera que o usuário final a entrada de dados como uma cadeia de caracteres onde linhas são separadas por vírgulas (,) e as colunas são separadas por ponto e vírgula (;). O serviço da web espera 1 linha por vez e espera para a variável dependente, a primeira coluna. Um conjunto de dados de exemplo pode ter esta aparência:

![Dados de exemplo][1]

Observações sem uma variável dependente devem ser entradas como "ND" para y. Os dados de entrada para o conjunto de dados acima seria a cadeia de caracteres a seguir: "10 5 2,18; 1; 6,6; 5.3; 2.1,7; 5; 5,22; 3; 4,12; 2; 1, disp; 3; 4". O resultado é o valor previsto para cada uma das linhas com base em variável independente. 

>Esse serviço, como hospedado do Azure Marketplace, é um serviço OData; Esses podem ser chamadas por meio de métodos de postar ou obter. 

Há várias maneiras de consumo do serviço de forma automática (é um aplicativo de exemplo [aqui](http://microsoftazuremachinelearning.azurewebsites.net/MultipleLinearRegressionService.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Iniciando o código c# para consumo de serviço web:

    public class Input
    {
            public string value;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { value = TextBox1.Text };
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


De dentro de aprendizado de máquina do Azure, um experimento em branco novo foi criado e dois [Executar scripts de R] [ execute-r-script] módulos foram recebidos para o espaço de trabalho. Este serviço web executa uma experiência de aprendizado de máquina do Azure com um script de R subjacente. Existem 2 partes para esse experimento: definição de esquema, modelo de treinamento + e pontuação. O primeiro módulo define a estrutura esperada do dataset entrada, onde a primeira variável é a variável dependente e as variáveis restantes são independentes. O segundo módulo se encaixa um modelo de regressão linear genérico para os dados de entrada.  
  
![Fluxo de experimento][3]

####<a name="module-1"></a>Módulo 1:
 
####<a name="schema-definition"></a>Definição de esquema  
    data <- data.frame(value = "1;2;3,4;5;6,7;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####<a name="module-2"></a>Módulo 2:
####<a name="lm-modeling"></a>Modelagem de LM   
    data <- maml.mapInputPort(1) # class: data.frame  
  
    data.split <- strsplit(data[1,1], ",")[[1]]  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- as.data.frame(t(data.split)) 
    data.split <- data.matrix(data.split) 
    data.split <- data.frame(data.split) 
    model <- lm(data.split)  

    out=data.frame(predict(model,data.split))  
    out <- data.frame(t(out))

    maml.mapOutputPort("out");  
 
##<a name="limitations"></a>Limitações
Este é um exemplo muito simples de um serviço da web de regressão linear vários. Como pode ser visto do código de exemplo acima, nenhum erro captura é implementado e o serviço considera tudo o que é uma variável contínua (sem recursos categóricos permitidos), como os valores de numéricos serviço apenas entradas no momento da criação deste serviço da web. Além disso, o serviço atualmente lida com tamanho de dados limitada, devido à natureza solicitação/resposta de chamada de serviço da web e ao fato de que o modelo está sendo ajustá-la toda vez que o serviço web é chamado. 

##<a name="faq"></a>Perguntas Freqüentes
Para perguntas frequentes sobre o consumo do serviço da web ou publicação para do Azure Marketplace, veja [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img1.png
[2]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img2.png
[3]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
