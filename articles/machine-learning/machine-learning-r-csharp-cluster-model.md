<properties 
    pageTitle="Modelo de cluster | Microsoft Azure" 
    description="Modelo de cluster" 
    services="machine-learning" 
    documentationCenter="" 
    authors="FrancescaLazzeri" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="lazzeri"/> 


#<a name="cluster-model"></a>Modelo de cluster    

Como podemos prever grupos de comportamentos dos sistema de crédito para reduzir o risco charge-desativada de emissores de cartão de crédito? Como podemos definir grupos de características de identificação de funcionários para melhorar seu desempenho no trabalho? Como os médicos podem classificar pacientes em grupos com base nas características do seus infecciosas? Em princípio, todas essas perguntas podem ser respondidas por meio da análise de cluster.   


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 
   
Análise de cluster classifica um conjunto de observações em dois ou mais grupos mutuamente exclusivos desconhecidos com base nas combinações de variáveis. A finalidade da análise de cluster é de um sistema de organização de observações, geralmente pessoas ou suas características, em grupos, de descobrir onde membros dos grupos compartilham propriedades em comum. Esse [serviço](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) usa a metodologia de significa K, uma técnica de cluster comumente usada, aos dados aleatório de cluster em grupos. Este serviço da web utiliza os dados e o número de k clusters como entrada e produz previsões das quais os grupos de k ao qual pertence cada observações. 

>Este serviço da web poderia ser consumido por usuários – potencialmente por meio de um aplicativo móvel, através de um site ou até mesmo em um computador local, por exemplo. Mas também é o propósito do serviço da web servir como um exemplo de como o aprendizado de máquina do Azure pode ser usado para criar serviços web sobre código R. Com apenas algumas linhas de código de R e cliques de botão dentro do Azure Studio de aprendizado de máquina, um experimento pode ser criado com código de R e publicado como um serviço da web. O serviço da web pode ser publicado do Azure Marketplace e consumido por usuários e dispositivos em todo o mundo com nenhuma configuração de infraestrutura do autor do serviço da web.  

##<a name="consumption-of-web-service"></a>Consumo de serviço web   
Este serviço web agrupa os dados em um conjunto de grupos de k e saídas a atribuição de grupo para cada linha. O serviço da web espera que o usuário final a entrada de dados como uma cadeia de caracteres onde linhas são separadas por vírgulas (,) e as colunas são separadas por ponto e vírgula (;). O serviço da web espera 1 linha por vez. Um conjunto de dados de exemplo pode ter esta aparência:

![Dados de exemplo][1]

Suponha que o usuário deseja separar esses dados em 3 grupos mutuamente exclusivos. Os dados de entrada para o conjunto de dados acima seria o seguinte: valor = "10 5 2,18; 1; 6,7; 5; 5,22; 3; 4,12; 2; 1,10; 3; 4"; k = "3". O resultado é a associação previsto para cada uma das linhas.

>Esse serviço, como hospedado do Azure Marketplace, é um serviço OData; Esses podem ser chamadas por meio de métodos de postar ou obter. 

Há várias maneiras de consumo do serviço de forma automática (é um aplicativo de exemplo [aqui](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Iniciando o código c# para consumo de serviço web:

    public class Input
    {
            public string value;
            public string k;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
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

De dentro de aprendizado de máquina do Azure, um experimento em branco novo foi criado e dois [Executar scripts de R] [ execute-r-script] módulos puxado para o espaço de trabalho. O esquema de dados foi criado com um simples [Executar scripts de R][execute-r-script]. Em seguida, o esquema de dados foi vinculado à seção de modelo cluster, criada novamente com um [Script de R executar][execute-r-script]. No [Script de R executar] [ execute-r-script] usado para o modelo de cluster, o serviço da web, em seguida, utiliza a função de "k-significa", que é pré-criadas no [Executar scripts de R] [ execute-r-script] de aprendizado de máquina do Azure.    
   

     
![Fluxo de experimento][3]

####<a name="module-1"></a>Módulo 1: 
    #Enter the input data as a string 
    mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
    
    maml.mapOutputPort("mydata");     
    

####<a name="module-2"></a>Módulo 2:
    # Map 1-based optional input ports to variables
    mydata <- maml.mapInputPort(1) # class: data.frame

    data.split <- strsplit(mydata[1,1], ",")[[1]]
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- as.data.frame(t(data.split))

    data.split <- data.matrix(data.split)
    data.split <- data.frame(data.split)

    # K-Means cluster analysis
    fit <- kmeans(data.split, mydata$k) # k-cluster solution

    # Get cluster means 
    aggregate(data.split,by=list(fit$cluster),FUN=mean)
    # Append cluster assignment
    mydatafinal <- data.frame(t(fit$cluster))
    n_col=ncol(mydatafinal)
    colnames(mydatafinal) <- paste("V",1:n_col,sep="")

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("mydatafinal");
   
 
##<a name="limitations"></a>Limitações
Este é um exemplo muito simples de um serviço web cluster. Como pode ser visto do código de exemplo acima, nenhum erro captura é implementado e o serviço considera tudo o que é uma variável contínua (sem recursos categóricos permitidos), como os valores de numéricos serviço apenas entradas no momento da criação deste serviço da web. Além disso, o serviço atualmente lida com tamanho de dados limitada, devido à natureza solicitação/resposta de chamada de serviço da web e ao fato de que o modelo está sendo ajustá-la toda vez que o serviço web é chamado. 

##<a name="faq"></a>Perguntas Freqüentes
Para perguntas frequentes sobre o consumo do serviço da web ou publicação para do Azure Marketplace, veja [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
