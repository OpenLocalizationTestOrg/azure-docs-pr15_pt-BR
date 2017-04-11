<properties 
    pageTitle="Análise de sobrevivência com aprendizado de máquina Azure | Microsoft Azure" 
    description="Probabilidade de ocorrência de evento de análise de sobrevivência" 
    services="machine-learning" 
    documentationCenter="" 
    authors="zhangya" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="zhangya"/> 


#<a name="survival-analysis"></a>Análise de sobrevivência 

Em muitos cenários, o resultado principal em avaliação é o tempo para um evento de interesse. Em outras palavras, a pergunta "quando este evento ocorrerá?" é solicitado. Como exemplos, considere situações onde os dados descreve o tempo decorrido (dias, anos, quilometragem, etc.) até o evento de interesse (relapse doenças, grau de PhD recebidos, falha de freio teclado) ocorre. Cada instância dos dados representa um objeto específico (um paciente, um aluno, um carro, etc.).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Este [serviço web]( https://datamarket.azure.com/dataset/aml_labs/survivalanalysis) responde à pergunta "qual é a probabilidade de que o evento de interesse ocorrerá por n de tempo para o objeto x?" Fornecendo um modelo de análise de sobrevivência, esse serviço web permite aos usuários fornecer dados para treinar o modelo e testá-lo. O tema principal da experiência é modelar o comprimento de tempo decorrido até que ocorra o evento de interesse. 

>Este serviço da web poderia ser consumido por usuários – potencialmente por meio de um aplicativo móvel, através de um site ou até mesmo em um computador local, por exemplo. Mas também é o propósito do serviço da web servir como um exemplo de como o aprendizado de máquina do Azure pode ser usado para criar serviços web sobre código R. Com apenas algumas linhas de código de R e cliques de botão dentro do Azure Studio de aprendizado de máquina, um experimento pode ser criado com código de R e publicado como um serviço da web. O serviço da web pode ser publicado do Azure Marketplace e consumido por usuários e dispositivos em todo o mundo com nenhuma configuração de infraestrutura do autor do serviço da web.  

##<a name="consumption-of-web-service"></a>Consumo de serviço web

O esquema de dados de entrada do serviço da web é mostrado na tabela a seguir. Seis informações são necessárias como a entrada: dados de treinamento, dados de teste, hora de interesse, o índice de "Hora" dimensões, o índice de dimensão "evento" e os tipos de variável (contínuo ou fator). Os dados de treinamento são representados por uma cadeia, onde as linhas são separadas por vírgula e as colunas são separadas por ponto e vírgula. O número de recursos dos dados é flexível. Todos os elementos na cadeia de entrada devem ser numéricos. Os dados de treinamento, a dimensão de "Hora" indica que o número de unidades de tempo (dias, anos, quilometragem, etc.) decorridos desde o ponto de partida do estudo (um paciente recebendo programas de tratamento de medicamentos, um estudo PhD inicial do aluno, um carro comecem a ser controlado pelo empenho, etc.) até o evento de interesse (paciente retorno ao uso do mesmo, o aluno obtendo o grau de PhD, carro tendo falha de freio teclado etc.) ocorre. A dimensão "evento" indica se o evento de interesse ocorre no final do estudo. Um valor de "evento = 1" significa que o evento de interesse ocorre no momento indicado pelo dimensão "Hora". "evento = 0" significa que o evento de interesse não ocorreu no momento indicado pela dimensão de "Hora".

- trainingdata - uma cadeia de caracteres. Linhas são separadas por vírgula e as colunas são separadas por ponto e vírgula. Cada linha inclui dimensão de "Hora", "evento" dimensão e variáveis de previsão.
- testingdata - uma linha de dados que contém variáveis de previsão para um objeto específico.
- time_of_interest - o tempo decorrido de juros n.
- index_time - o índice de coluna da dimensão "Hora" (a partir de 1).
- index_event - o índice de coluna da dimensão "evento" (a partir de 1).
- variable_types - uma cadeia de caracteres com ponto e vírgula como separadores nele. 0 representa variáveis contínuas e 1 representa variáveis de fator.


O resultado é a probabilidade de um evento que ocorre por uma hora específica. 

>Esse serviço, como hospedado do Azure Marketplace, é um serviço OData; Esses podem ser chamadas por meio de métodos de postar ou obter. 

Há várias maneiras de consumo do serviço de forma automática (é um aplicativo de exemplo [aqui](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx)). 

###<a name="starting-c-code-for-web-service-consumption"></a>Iniciando o código c# para consumo de serviço web:
    public class Input
    {
            public string trainingdata;
            public string testingdata;
            public string timeofinterest;
            public string indextime;
            public string indexevent;
            public string variabletypes;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { trainingdata = TextBox1.Text, testingdata = TextBox2.Text, timeofinterest = TextBox3.Text, indextime = TextBox4.Text, indexevent = TextBox5.Text, variabletypes = TextBox6.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }




A interpretação desse teste é da seguinte maneira. Pressupondo que a meta dos dados é modelar o tempo decorrido até o retorno ao uso de medicamentos para os pacientes que recebeu um dos programas do tratamento de duas. A saída as leituras de serviço web: para pacientes sendo 35 anos, tendo anterior medicamento tratamento 2 vezes, executando o programa de tratamento longo residencial, e com o uso de heroin e cocaine, a probabilidade de retornar para o uso de medicamento é 95.64% por dia 500.

##<a name="creation-of-web-service"></a>Criação de serviço web

>Este serviço da web foi criado usando aprendizado de máquina do Azure. Para uma avaliação gratuita, bem como vídeos introdutórios sobre a criação de experiências e [Serviços de publicação da web](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml). Abaixo está uma captura de tela da experiência que criou o código de exemplo e de serviço web para cada um dos módulos dentro do experimento.

De dentro de aprendizado de máquina do Azure, um experimento em branco novo foi criado e dois [Executar scripts de R] [ execute-r-script] módulos foram recebidos para o espaço de trabalho. O esquema de dados foi criado com um simples [Executar scripts de R][execute-r-script], que define o esquema de dados de entrada para o serviço da web. Este módulo é vinculado ao segundo [Executar scripts de R] [ execute-r-script] módulo, que principais trabalho. Este módulo faz pré-processamento de dados, criação de modelo e previsões. Na etapa dados pré-processamento, os dados de entrada são representados por uma cadeia de caracteres longa é transformados e convertidos em um quadro de dados. Na etapa de construção de modelo, um pacote de R externo "survival_2.37-7.zip" é instalado para conduzir a análise de sobrevivência. Em seguida, a função de "coxph" é executada após uma tarefas de processamento de dados da série. Os detalhes da função "coxph" para análise de sobrevivência podem ser lidos da documentação do R. Na etapa previsão, uma instância de teste for fornecida para o modelo de treinamento com a função de "surfit" e a curva de sobrevivência para esta instância de teste é gerada como variável de "curva". Por fim, a probabilidade do tempo de interesse é obtida. 

###<a name="experiment-flow"></a>Fluxo de experimento:

![Experimente fluxo][1]

####<a name="module-1"></a>Módulo 1:

    #Data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1”
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    
    # 0 - continuous; 1 -  factor
    variable_types="0;0;1;1"

    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)

    maml.mapOutputPort("sampleInput"); #send data to output port
    
####<a name="module-2"></a>Módulo 2:

    #Read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")

    # Preprocessing training data
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)

    # Preprocessing testing data
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))

    # Preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types

    # Necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)

    # Prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # Construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # Fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # Based on user input, find the event occurrence probability
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }

    #Pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##<a name="limitations"></a>Limitações

Este serviço da web pode levar apenas valores numéricos como variáveis de recurso (colunas). A coluna "evento" pode levar apenas o valor 0 ou 1. A coluna "Hora" deve ser um inteiro positivo.

##<a name="faq"></a>Perguntas Freqüentes
Para perguntas frequentes sobre o consumo do serviço da web ou publicação para do Azure Marketplace, veja [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
