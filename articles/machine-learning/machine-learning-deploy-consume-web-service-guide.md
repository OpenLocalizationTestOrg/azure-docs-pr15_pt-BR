<properties
    pageTitle="Serviços da Web do aprendizado de máquina Azure: Implantação e consumo | Microsoft Azure"
    description="Recursos de implantação e consumir serviços da web."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="v-donglo"/>

# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Serviços da Web do aprendizado de máquina Azure: Implantação e consumo

Você pode usar o aprendizado de máquina do Azure para implantar modelos como serviços da web e fluxos de trabalho de aprendizado de máquina. Esses serviços da web, em seguida, podem ser usados para chamar os modelos de aprendizado de máquina de aplicativos pela Internet para fazer previsões em tempo real ou no modo em lotes. Como os serviços web são RESTful, você pode chamá-las em vários idiomas e plataformas, como .NET e Java, programação e de aplicativos, como o Excel.

As próximas seções fornecem links para instruções passo a passo, código e documentação para ajudar você a começar.

## <a name="deploy-a-web-service"></a>Implantar um serviço web

### <a name="with-azure-machine-learning-studio"></a>Com o aprendizado de máquina Azure Studio

Studio de aprendizado de máquina e o portal de serviços da Web de aprendizado de máquina do Microsoft Azure ajudar você a implantar e gerenciar um serviço web sem escrever código.

Os links a seguir fornecem informações gerais sobre como implantar um novo serviço web:

* Para obter uma visão geral sobre como implantar um novo serviço web baseado em Gerenciador de recursos do Azure, consulte [implantar um novo serviço web](machine-learning-webservice-deploy-a-web-service.md).
* Para instruções passo a passo sobre como implantar um serviço web, consulte [implantar um serviço da web de aprendizado de máquina do Azure](machine-learning-publish-a-machine-learning-web-service.md).
* Para detalhes completos sobre como criar e implantar um serviço web, consulte [passo a passo etapa 1: criar um espaço de trabalho de aprendizado de máquina](machine-learning-walkthrough-1-create-ml-workspace.md).
* Para obter exemplos específicos que implantar um serviço web, consulte:

    * [Explicação passo a passo etapa 5: Implantar o serviço da web de aprendizado de máquina do Azure](machine-learning-walkthrough-5-publish-web-service.md)
    * [Como implantar um serviço web para várias regiões](machine-learning-how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Com o provedor de recursos de serviços web APIs (APIs do Gerenciador de recursos do Azure)

O provedor de recursos de aprendizado de máquina do Azure para serviços web permite implantação e gerenciamento de serviços da web usando chamadas de API REST. Para obter detalhes adicionais, consulte a referência de [Serviço de Web de aprendizagem de máquina (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) no MSDN.

### <a name="with-powershell-cmdlets"></a>Com os cmdlets do PowerShell

Provedor de recursos de aprendizado de máquina Azure para serviços web permite implantação e gerenciamento de serviços da web usando cmdlets do PowerShell.

Para usar os cmdlets, você deve entrar pela primeira sua conta do Azure de dentro do ambiente do PowerShell usando o cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) . Se você estiver familiarizado com a forma de chamar os comandos do PowerShell que são baseados no Gerenciador de recursos, consulte [Usando o PowerShell Azure com o Gerenciador de recursos do Azure](../powershell-azure-resource-manager.md#login-to-your-azure-account).

Para exportar seu experimento previsão, use [Este código de exemplo](https://github.com/ritwik20/AzureML-WebServices). Depois de criar o arquivo de .exe do código, você pode digitar:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Executando o aplicativo cria um modelo de JSON de serviço web. Para usar o modelo para implantar um serviço web, você deve adicionar as seguintes informações:

* Chave e nome da conta de armazenamento

    Você pode obter o nome da conta de armazenamento e a chave do [portal do Azure](https://portal.azure.com/) ou o [Azure portal clássico](http://manage.windowsazure.com/).
* ID do plano de compromisso

    Você pode obter a identificação de plano a partir do portal de [Serviços de Web de aprendizado de máquina Azure](https://services.azureml.net) entrar e clicando em um nome de plano.

Adicioná-los para o modelo JSON como filhos do nó de *Propriedades* do mesmo nível como o nó *MachineLearningWorkspace* .

Aqui está um exemplo:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Consulte os seguintes artigos e o código de exemplo para obter detalhes adicionais:

* Referência de [Cmdlets de aprendizado de máquina do Azure]( https://msdn.microsoft.com/library/azure/mt767952.aspx) no MSDN
* Exemplo [passo a passo](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) no GitHub

## <a name="consume-the-web-services"></a>Consumir os serviços web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Dos serviços da Web de aprendizagem máquina Azure UI (teste)

Você pode testar seu serviço da web a partir do portal de serviços de Web de aprendizado de máquina Azure. Isso inclui o serviço de solicitação-resposta (RR) de teste e interfaces de serviço de execução de lote (BES).

* [Implantar um novo serviço web](machine-learning-webservice-deploy-a-web-service.md)
* [Implantar um serviço da web de aprendizado de máquina do Azure](machine-learning-publish-a-machine-learning-web-service.md)
* [Explicação passo a passo etapa 5: Implantar o serviço da web de aprendizado de máquina do Azure](machine-learning-walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>Do Excel

Você pode baixar um modelo do Excel que consome o serviço da web:

* [Consumo de um serviço da web de aprendizado de máquina do Azure do Excel](machine-learning-consuming-from-excel.md)
* [Suplemento do Excel para serviços de Web de aprendizado de máquina Azure](machine-learning-excel-add-in-for-web-services.md)


### <a name="from-a-rest-based-client"></a>De um cliente baseado em REST

Serviços de Web de aprendizado de máquina Azure são APIs RESTful. Você pode consumir essas APIs de várias plataformas, como o .NET, Python, R, Java, etc. A página de **Consume** de seu serviço da web no [portal de serviços da Web de aprendizado de máquina do Microsoft Azure](https://services.azureml.net) tem o código de exemplo que pode ajudá-lo a começar. Para obter mais informações, consulte [como consumir um serviço da web de aprendizado de máquina Azure que tenha sido implantado de uma experimento de aprendizado de máquina](machine-learning-consume-web-services.md).

