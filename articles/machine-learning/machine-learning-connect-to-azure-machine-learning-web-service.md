<properties
    pageTitle="Conectar a um serviço de Web de aprendizado de máquina | Microsoft Azure"
    description="C# ou Python, se conecte a um serviço da Web de aprendizado de máquina do Azure usando uma chave de autorização."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016" 
    ms.author="garye" />


# <a name="connect-to-an-azure-machine-learning-web-service"></a>Conectar a um serviço de Web de aprendizado de máquina Azure

A experiência de desenvolvedor de aprendizado de máquina do Azure é uma API do serviço Web para fazer previsões de entrada de dados em tempo real ou no modo em lotes. Use Studio de aprendizado de máquina do Azure para criar previsões e implantar um serviço Web de aprendizado de máquina do Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Para saber mais sobre como criar e implantar um serviço Web de aprendizado de máquina usando Studio de aprendizado de máquina:

- Para um tutorial sobre como criar um experimento no Studio de aprendizado de máquina, consulte [criar seu primeiro experimento](machine-learning-create-experiment.md).
- Para obter detalhes sobre como implantar um serviço Web, consulte [implantar um serviço Web de aprendizado de máquina](machine-learning-publish-a-machine-learning-web-service.md).
- Para obter mais informações sobre o aprendizado de máquina em geral, visite o [Centro de documentação de aprendizado de máquina](https://azure.microsoft.com/documentation/services/machine-learning/).

## <a name="azure-machine-learning-web-service"></a>Serviço da Web de aprendizado de máquina Azure ##

Com o serviço Web de aprendizado de máquina do Azure, um aplicativo externo se comunica com um modelo de pontuação de fluxo de trabalho de aprendizado de máquina em tempo real. Uma chamada de serviço Web de aprendizado de máquina retorna resultados de previsão para um aplicativo externo. Para fazer com que um serviço Web de aprendizado de máquina chamada, você passar uma chave de API que é criada quando você implanta uma previsão. O serviço Web de aprendizado de máquina baseia-se no restante, uma opção de arquitetura populares para projetos de programação da web.

Azure aprendizado de máquina tem dois tipos de serviços:

- Serviço de solicitação-resposta (RR) – uma baixa latência, altamente escalável serviço que fornece uma interface para os modelos de estado criado e implantado a partir do Studio de aprendizado de máquina.
- Serviço de execução de lote (BES) – um assíncrono que pontuações um lote para registros de dados de serviço.

Para obter mais informações sobre serviços da Web de aprendizado de máquina, consulte [implantar um serviço Web de aprendizado de máquina](machine-learning-publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Obter uma chave de autorização de aprendizado de máquina do Azure ##

Quando você implanta seu experimento, chaves de API são geradas para o serviço da Web. Você pode recuperar as chaves de vários locais.

## <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>A partir do portal de serviços da Web de aprendizado de máquina do Microsoft Azure

Entre portal de [Serviços da Web de aprendizado de máquina do Microsoft Azure](https://services.azureml.net) .

Para recuperar a chave de API para um serviço Web de aprendizado de máquina nova:

1. No portal de serviços de Web de aprendizado de máquina Azure, clique em **Serviços Web do** menu superior.
2. Clique no serviço da Web para o qual você deseja recuperar a chave.
3. No menu superior, clique em **Consume**.
4. Copie e salve a **Chave primária**.


Para recuperar a chave de API para um serviço Web de aprendizado de máquina clássico:

1. No portal de serviços de Web de aprendizado de máquina Azure, clique em **Serviços Web do clássico** menu superior.
2. Clique no serviço da Web com o qual você está trabalhando.
3. Clique na extremidade para o qual você deseja recuperar a chave.
3. No menu superior, clique em **Consume**.
4. Copie e salve a **Chave primária**.

## <a name="classic-web-service"></a>Serviço da Web clássico ##

 Você também pode recuperar uma chave para um serviço Web clássico de Studio de aprendizado de máquina ou o portal do Azure.

### <a name="machine-learning-studio"></a>Studio de aprendizado de máquina ###

1. No Studio de aprendizado de máquina, clique em **Serviços WEB** à esquerda.
2. Clique em um serviço da Web. A **chave API** está na guia **Painel de controle** .

### <a name="azure-portal"></a>Portal do Azure ###

1. Clique em **APRENDIZADO de máquina** à esquerda.
2. Clique no espaço de trabalho no qual seu serviço da Web está localizado.
3. Clique em **serviços da WEB**.
4. Clique em um serviço da Web.
5. Clique em um ponto de extremidade. "API chave" está para baixo no canto inferior direito.

## <a id="connect"></a>Conectar a um serviço Web de aprendizado de máquina

Você pode conectar a um serviço da Web de aprendizado de máquina usando qualquer linguagem de programação que ofereça suporte a resposta e solicitação HTTP. Você pode exibir exemplos de c#, Python e R de uma página de ajuda de serviço Web de aprendizado de máquina.

**Ajuda da API de aprendizado de máquina** Ajuda da API de aprendizado de máquina é criada quando você implanta um serviço Web. Consulte [explicação passo a passo de aprendizado de máquina Azure - implantar o serviço Web](machine-learning-walkthrough-5-publish-web-service.md).
Ajuda da API de aprendizado de máquina contém detalhes sobre uma previsão de serviço Web.

2. Clique no serviço da Web com o qual você está trabalhando.
3. Clique na extremidade para o qual você deseja exibir a página de ajuda de API.
3. No menu superior, clique em **Consume**.
3. Clique em **página de ajuda de API** na solicitação-resposta ou pontos de extremidade de execução de lote.

**Modo de exibição API de aprendizado de máquina ajuda para um serviço Web novo**

Na máquina Azure Portal de serviços da Web de aprendizagem:

1. Clique em **Serviços da WEB** no menu superior.
2. Clique no serviço da Web para o qual você deseja recuperar a chave.

Clique em **Consume** para obter os URIs o Reposonse de solicitação e a serviços de execução de lote e o código de exemplo em c#, R e Python.

Clique em **Swagger API** para obter a documentação de Swagger com base para as APIs chamadas dos URIs fornecido.

### <a name="c-sample"></a>Amostra c# ###

Para se conectar a um serviço Web de aprendizado de máquina, use um **HttpClient** passando ScoreData. ScoreData contém um FeatureVector, um vetor n-dimensional dos recursos numéricos que representa o ScoreData. Você autenticar para o serviço de aprendizado de máquina com uma chave API.

Para se conectar a um serviço Web de aprendizado de máquina, o pacote de NuGet **Microsoft.AspNet.WebApi.Client** deve ser instalado.

**Instalar o Microsoft.AspNet.WebApi.Client NuGet no Visual Studio**

1. Publicar o conjunto de dados de Download do UCI: 2 adultos classe dataset serviço Web.
2. Clique em **Ferramentas** > **Gerenciador de pacote NuGet** > **Package Manager Console**.
2. Escolha o **pacote de instalação Microsoft.AspNet.WebApi.Client**.

**Para executar o código de exemplo**

1. Publicar "exemplo 1: baixar o conjunto de dados do UCI: adulto 2 classe dataset" experimento, parte do conjunto de amostra de aprendizado de máquina.
2. Atribua apiKey com a chave de um serviço Web. Consulte a **obter uma chave de autorização de aprendizado de máquina do Azure** acima.
3. Atribua serviceUri com o URI de solicitação.


### <a name="python-sample"></a>Amostra de Python ###

Para se conectar a um serviço Web de aprendizado de máquina, use a biblioteca de **urllib2** passando ScoreData. ScoreData contém um FeatureVector, um vetor n-dimensional dos recursos numéricos que representa o ScoreData. Você autenticar para o serviço de aprendizado de máquina com uma chave API.


**Para executar o código de exemplo**

1. Implantar "exemplo 1: baixar o conjunto de dados do UCI: adulto 2 classe dataset" experimento, parte do conjunto de amostra de aprendizado de máquina.
2. Atribua apiKey com a chave de um serviço Web. Consulte a seção **obter uma chave de autorização de aprendizado de máquina do Azure** perto do início deste artigo.
3. Atribua serviceUri com o URI de solicitação.
