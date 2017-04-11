<properties
    pageTitle="Treinar novamente um novo serviço web usando os cmdlets do PowerShell de gerenciamento de aprendizado de máquina | Microsoft Azure"
    description="Saiba como programaticamente treinar novamente um modelo e atualizar o serviço da web para usar o modelo de treinamento recentemente no aprendizado de máquina do Azure usando os cmdlets do PowerShell de gerenciamento de aprendizado de máquina."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondlaghaeian"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="v-donglo"/>

# <a name="retrain-a-new-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Treinar novamente um novo serviço web usando os cmdlets do PowerShell de gerenciamento de aprendizado de máquina

Quando você treinar novamente um novo serviço web, você pode atualizar a definição de serviço web de previsão para referenciar o novo modelo de treinamento.  

## <a name="prerequisites"></a>Pré-requisitos

Você deve configurou um experimento de treinamento e um experimento previsão conforme mostrado no [aprendizado de máquina treinar novamente modela programaticamente](machine-learning-retrain-models-programmatically.md). 

>[AZURE.IMPORTANT] O experimento previsão deve ser implantado como uma serviço web de aprendizado de máquina do Gerenciador de recursos do Azure (nova) com base. 
 
Para obter informações adicionais sobre Implantando web services, consulte [implantar um serviço da web de aprendizado de máquina do Azure](machine-learning-publish-a-machine-learning-web-service.md).

Esse processo requer que você tenha instalado os Cmdlets de aprendizado de máquina do Azure. Para obter informações sobre como instalar os cmdlets de aprendizado de máquina, consulte a referência de [Cmdlets de aprendizado de máquina do Azure](https://msdn.microsoft.com/library/azure/mt767952.aspx) no MSDN.

Copiados as seguintes informações da saída novos treinamentos:

* BaseLocation
* RelativeLocation

As etapas que você executa são:

1.  Entre sua conta do Gerenciador de recursos do Azure.
2.  Obter a definição de serviço web
3.  Exporte a definição de serviço Web como JSON
4.  Atualize a referência para o blob ilearner no JSON.
5.  Importar o JSON para uma definição de serviço Web
6.  Atualizar o serviço da web com a nova definição de serviço Web

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Entre sua conta do Gerenciador de recursos do Azure

Primeiro você deve entrar sua conta do Azure de dentro do ambiente do PowerShell usando o cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) .

## <a name="get-the-web-service-definition"></a>Obter a definição de serviço Web

Em seguida, obter o serviço Web chamando o cmdlet [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) . A definição de serviço Web é uma representação interna do modelo de treinamento do serviço da web e não pode ser modificada diretamente. Certifique-se de que você está recuperando definição do serviço Web para o seu experimento previsão e não seu experimento de treinamento.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Para determinar o nome do grupo de recursos de um serviço da web existente, execute o cmdlet Get-AzureRmMlWebService sem parâmetros para exibir os serviços da web em sua assinatura. Localize o serviço da web e, em seguida, examine a sua ID de serviço web. O nome do grupo de recursos é o quarto elemento na identificação, logo após o elemento *resourceGroups* . No exemplo a seguir, o nome do grupo de recursos é padrão-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Como alternativa, para determinar o nome do grupo de recursos de um serviço da web existente, faça logon no portal de serviços da Web de aprendizado de máquina do Microsoft Azure. Selecione o serviço da web. O nome do grupo de recursos é o elemento quinto da URL do serviço da web, logo após o elemento *resourceGroups* . No exemplo a seguir, o nome do grupo de recursos é padrão-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Exporte a definição de serviço Web como JSON

Para modificar a definição do modelo de treinamento para usar o recentemente treinamento modelo, primeiro é preciso usar o cmdlet [AzureRmMlWebService de exportação](https://msdn.microsoft.com/library/azure/mt767935.aspx) para exportá-lo para um arquivo em formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Atualize a referência para o blob ilearner no JSON.

Os ativos, localize o [modelo treinamento], atualizar o valor de *uri* no nó *locationInfo* com o URI do blob ilearner. O URI é gerado combinando o *BaseLocation* e o *RelativeLocation* da saída do BES treinamento chamada.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition"></a>Importar o JSON para uma definição de serviço Web

Você deve usar o cmdlet [AzureRmMlWebService de importação](https://msdn.microsoft.com/library/azure/mt767925.aspx) para converter o arquivo JSON modificado novamente em uma definição de serviço Web que você pode usar para atualizar o experimento Predicative.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Atualizar o serviço da web com a nova definição de serviço Web

Finalmente, você deve usar cmdlet [AzureRmMlWebService de atualização](https://msdn.microsoft.com/library/azure/mt767922.aspx) para atualizar o experimento previsão.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Resumo

Usando os cmdlets de gerenciamento do PowerShell de aprendizado de máquina, você pode atualizar o modelo de treinamento de um serviço Web previsão habilitando cenários, como:

* Modelo de periódico treinamento com novos dados.
* Distribuição de um modelo para os clientes com o objetivo de permitindo que eles treinar novamente o modelo usando seus próprios dados.
