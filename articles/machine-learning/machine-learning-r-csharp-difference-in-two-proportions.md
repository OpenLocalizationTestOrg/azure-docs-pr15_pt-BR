<properties 
    pageTitle="Diferença no teste de proporções | Microsoft Azure" 
    description="Diferença no teste de proporções" 
    services="machine-learning" 
    documentationCenter="" 
    authors="aniedea" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="aniedea"/> 


#<a name="difference-in-proportions-test"></a>Diferença no teste de proporções


São duas proporções estatística diferentes? Suponha que um usuário deseja comparar duas filmes para determinar se um filme possui uma proporção significativamente maior de 'gosta' quando comparadas a outra. Com uma amostra grande, pode haver uma diferença estatística significativa entre as proporções 0,50 e 0.51. Com uma pequena amostra, pode não haver dados suficientes para determinar se essas proporções são realmente diferentes. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Este [serviço web]( https://datamarket.azure.com/dataset/aml_labs/prop_test) conduz um teste de hipóteses da diferença em dois proporções com base na entrada do usuário do número de sucessos e o número total de tentativas para os grupos de comparação de 2. Em um cenário de possível, este serviço da web poderia ser chamado de dentro de um aplicativo de comparação de filme, informando ao usuário se um dos filmes é 'gostou' com mais frequência do que a outra, com base nas classificações de filme.

>Este serviço da web poderia ser consumido por usuários – potencialmente por meio de um aplicativo móvel, através de um site ou até mesmo em um computador local, por exemplo. Mas também é o propósito do serviço da web servir como um exemplo de como o aprendizado de máquina do Azure pode ser usado para criar serviços web sobre código R. Com apenas algumas linhas de código de R e cliques de botão dentro do Azure Studio de aprendizado de máquina, um experimento pode ser criado com código de R e publicado como um serviço da web. O serviço da web pode ser publicado do Azure Marketplace e consumido por usuários e dispositivos em todo o mundo com nenhuma configuração de infraestrutura do autor do serviço da web.


##<a name="consumption-of-web-service"></a>Consumo de serviço web

Este serviço aceita 4 argumentos e uma hipótese teste de proporções.

Os argumentos de entrada são:

* Successes1 - o número de eventos de sucesso no exemplo 1.
* Successes2 - o número de eventos de sucesso no exemplo 2.
* Total1 - tamanho da amostra 1.
* Total2 - tamanho da amostra 2.

A saída do serviço é o resultado da hipótese testar juntamente com o chi-square estatística, df, valor-p e proporção em limites de 1/2 e intervalo de confiança de amostra.

>Esse serviço, como hospedado do Azure Marketplace, é um serviço OData; Esses podem ser chamadas por meio de métodos de postar ou obter. 

Há várias maneiras de consumo do serviço de forma automática (é um aplicativo de exemplo [aqui](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Iniciando o código c# para consumo de serviço web:

    public class Input
    {
            public string successes1;
            public string successes2;
            public string total1;
            public string total2;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { successes1 = TextBox1.Text, successes2 = TextBox2.Text, total1 = TextBox3.Text, total2 = TextBox4.Text };
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

De dentro de aprendizado de máquina do Azure, um experimento em branco novo foi criado com dois [Executar scripts de R] [ execute-r-script] módulos. No primeiro módulo que o esquema de dados é definido, enquanto o segundo módulo usa o comando prop.test dentro R para executar o teste de hipótese de 2 proporções. 


###<a name="experiment-flow"></a>Fluxo de experimento:

![Fluxo de experimento][2]


####<a name="module-1"></a>Módulo 1:
    ####Schema definition  
    data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
    maml.mapOutputPort("data.set"); #send data to output port
    dataset1 = maml.mapInputPort(1) #read data from input port
    

####<a name="module-2"></a>Módulo 2:

    test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

    result=data.frame(
    result=ifelse(test$p.value<0.05,"The proportions are different!",
    "The proportions aren't different statistically."),
    ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
    pvalue=round(test$p.value,4),
    proportion1=round(test$estimate[1],4),
    proportion2=round(test$estimate[2],4),
    confintlow=round(test$conf.int[1],4),
    confinthigh=round(test$conf.int[2],4)) 

    maml.mapOutputPort("result"); #output port
    

##<a name="limitations"></a>Limitações 

Este é um exemplo muito simples para um teste de diferença em 2 proporções. Como pode ser visto do código de exemplo acima, nenhum erro captura é implementado e o serviço pressupõe que todas as variáveis são contínuas.

##<a name="faq"></a>Perguntas Freqüentes
Para perguntas frequentes sobre o consumo do serviço da web ou publicação para do Azure Marketplace, veja [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
