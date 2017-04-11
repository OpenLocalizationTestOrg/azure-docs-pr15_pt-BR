<properties
    pageTitle="Treinar novamente um serviço web previsão existente | Microsoft Azure"
    description="Saiba como treinar novamente um modelo e atualizar o serviço da web para usar o modelo de treinamento recentemente no aprendizado de máquina do Azure."
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
    ms.date="10/06/2016"
    ms.author="v-donglo"/>


# <a name="retrain-an-existing-predictive-web-service"></a>Treinar novamente um serviço web previsão existente

Este documento descreve o processo de novos treinamentos para o cenário a seguir:

* Você tem uma experiência de treinamento e um experimento previsão que tenha implantado como um serviço web operacionalizada.
* Você tem novos dados que você deseja que o seu serviço web previsão usar para realizar sua pontuação.

Começando com o seu serviço da web existente e experiências, você precisa siga estas etapas:

1. Atualize o modelo.
    1. Modificar seu experimento de treinamento para permitir saídas e entradas de serviço web.
    2. Implante o experimento treinamento como um serviço web novos treinamentos.
    3. Use lote de execução de serviço (BES do experimento de treinamento) treinar novamente o modelo.
2. Use cmdlets do PowerShell de aprendizado de máquina do Azure para atualizar o experimento previsão.
    1.  Entre sua conta do Gerenciador de recursos do Azure.
    2.  Obtenha a definição de serviço web.
    3.  Exporte a definição de serviço web como JSON.
    4.  Atualize a referência para o blob ilearner no JSON.
    5.  Importe o JSON para uma definição de serviço web.
    6.  Atualize o serviço da web com uma nova definição de serviço web.

## <a name="deploy-the-training-experiment"></a>Implantar o experimento de treinamento

Para implantar o experimento treinamento como um serviço web novos treinamentos, você deve adicionar entradas de serviço web e saídas para o modelo. Conectando-se em um módulo de *Saída de serviço Web* para o experimento * [Modelo de trem] [ train-model] * módulo, você habilitar o experimento de treinamento produzir um novo modelo de treinamento que você pode usar no seu experimento previsão. Se você tiver um módulo de *Avaliar modelo* , você também pode anexar saída de serviço web para obter os resultados de avaliação como saída.

Para atualizar seu experimento de treinamento:

1. Conecte um módulo de *Entrada de serviço da Web* à sua entrada de dados (por exemplo, um módulo de *Limpar dados ausentes* ). Normalmente, você deseja garantir que seus dados de entrada são processados da mesma maneira como seus dados de treinamento original.
2. Conecte um módulo de *Saída de serviço Web* para a saída do seu módulo de *Modelo de trem* .
3. Se você tiver um módulo de *Modelo avaliar* e você deseja que os resultados da avaliação de saída, conecte um módulo de *Saída de serviço Web* para a saída do módulo *Avaliar modelo* .

Execute o seu experimento.

Em seguida, você deve implantar o experimento treinamento como um serviço web que produz um modelo de treinamento e resultados de avaliação do modelo.  

Na parte inferior da tela experimento, clique em **Configurar o serviço Web**e selecione **Implantar o serviço da Web [novo]**. O portal de serviços de Web de aprendizado de máquina Azure abre a página de **Implantar o serviço da Web** . Digite um nome para seu serviço da web, escolha um plano de pagamento e, em seguida, clique em **implantar**. Você só pode usar o método de execução de lote para a criação de modelos de treinamento.


## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Treinar novamente o modelo com novos dados usando BES

Neste exemplo, estamos usando c# para criar o aplicativo novos treinamentos. Você também pode usar o código de amostra Python ou R para realizar essa tarefa.

Para chamar as APIs novos treinamentos:

1. Criar um aplicativo de console c# no Visual Studio (**novo** > **projeto** > **Área de trabalho do Windows** > **Aplicativo de Console**).
2.  Entre portal de serviços de Web de aprendizado de máquina.
3.  Clique em serviço da web que você está trabalhando.
2.  Clique em **consumir**.
3.  Na parte inferior da página **Consume** , na seção **Exemplo de código** , clique em **lote**.
5.  Copie o código c# de exemplo para execução em lotes e cole o arquivo Program.cs. Certifique-se de que o namespace permanecerá intacto.

Adicione o pacote do NuGet Microsoft.AspNet.WebApi.Client, conforme especificado nos comentários. Para adicionar a referência ao Microsoft.WindowsAzure.Storage.dll, você precisará primeiro instalar a [biblioteca de cliente dos serviços de armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

A captura de tela a seguir mostra a página **Consume** no portal do Azure serviços de Web de aprendizado de máquina.

![Consumir página][1]

### <a name="update-the-apikey-declaration"></a>Atualizar a declaração de apikey

Localize a declaração **apikey** :

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Na seção **informações de consumo básica** da página **Consume** , localize a chave primária e copiá-lo para a declaração de **apikey** .

### <a name="update-the-azure-storage-information"></a>Atualizar as informações de armazenamento do Azure

O código de exemplo BES carrega um arquivo de uma unidade local (por exemplo, "C:\temp\CensusIpnput.csv") para o armazenamento do Azure, processa e grava os resultados de volta ao armazenamento do Azure.  

Para atualizar as informações de armazenamento do Azure, você deve recuperar o nome da conta de armazenamento, chave e informações do contêiner para sua conta de armazenamento do Azure portal clássico e, em seguida, atualizar o correspondi após a execução do seu experimento, o fluxo de trabalho resultante deve ser semelhante ao seguinte:

![Fluxo de trabalho resultante após a execução][4]valores de NG no código.

1. Entre portal do Azure clássico.
1. Na coluna de navegação à esquerda, clique em **armazenamento**.
1. Na lista de contas de armazenamento, selecione um para armazenar o modelo retrained.
1. Na parte inferior da página, clique em **Gerenciar teclas de acesso**.
1. Copiar e salvar a **Chave primária de acesso** e feche a caixa de diálogo.
1. Na parte superior da página, clique em **contêineres**.
1. Selecione um contêiner existente, ou crie um novo e salvar o nome.

Localize o *StorageAccountName*, *StorageAccountKey*e *StorageContainerName* declarações e atualizar os valores que você salvou a partir do portal clássico.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Você também deve garantir que o arquivo de entrada está disponível no local especificado no código.

### <a name="specify-the-output-location"></a>Especifique o local de saída

Quando você especificar o local de saída na solicitação de carga, a extensão do arquivo especificado no *RelativeLocation* deve ser especificada como `ilearner`. Consulte o exemplo a seguir:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

A seguir está um exemplo de saída novos treinamentos: ![treinamento de saída][6]

## <a name="evaluate-the-retraining-results"></a>Avaliar os resultados de novos treinamentos

Quando você executa o aplicativo, a saída inclui a URL e o símbolo de assinaturas de acesso compartilhado que são necessários para acessar os resultados de avaliação.

Você pode ver os resultados de desempenho do modelo retrained combinando o *BaseLocation*, *RelativeLocation*e *SasBlobToken* dos resultados de saída para *output2* (conforme mostrado na imagem de saída de novos treinamentos anterior) e colar a URL completa para a barra de endereços do navegador.  

Examine os resultados para determinar se o modelo recentemente treinamento executa suficiente para substituir a existente.

Copie o *BaseLocation*, *RelativeLocation*e *SasBlobToken* dos resultados de saída.

## <a name="retrain-the-web-service"></a>Treinar novamente o serviço da web

Quando você treinar novamente um novo serviço web, você pode atualizar a definição de serviço web de previsão para referenciar o novo modelo de treinamento. A definição de serviço web é uma representação interna do modelo de treinamento do serviço da web e não pode ser modificada diretamente. Certifique-se de que você está recuperando definição do serviço web para o seu experimento previsão e não seu experimento de treinamento.

## <a name="sign-in-to-azure-resource-manager"></a>Entrar para Azure Gerenciador de recursos

Você deve entrar pela primeira sua conta do Azure de dentro do ambiente do PowerShell usando o cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) .

## <a name="get-the-web-service-definition-object"></a>Obter o objeto de definição de serviço Web

Em seguida, obtenha o objeto de definição de serviço Web chamando o cmdlet [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) .

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Para determinar o nome do grupo de recursos de um serviço da web existente, execute o cmdlet Get-AzureRmMlWebService sem parâmetros para exibir os serviços da web em sua assinatura. Localize o serviço da web e, em seguida, examine a sua ID de serviço web. O nome do grupo de recursos é o quarto elemento na identificação, logo após o elemento *resourceGroups* . No exemplo a seguir, o nome do grupo de recursos é padrão-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Como alternativa, para determinar o nome do grupo de recursos de um serviço da web existente, entre portal do Azure serviços de Web de aprendizado de máquina. Selecione o serviço da web. O nome do grupo de recursos é o elemento quinto da URL do serviço da web, logo após o elemento *resourceGroups* . No exemplo a seguir, o nome do grupo de recursos é padrão-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Exportar o objeto de definição de serviço Web como JSON

Para modificar a definição do modelo de treinamento para usar o modelo de treinamento recentemente, primeiro é preciso usar o cmdlet [AzureRmMlWebService de exportação](https://msdn.microsoft.com/library/azure/mt767935.aspx) para exportá-lo para um arquivo de formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Atualizar a referência para o blob ilearner

Os ativos, localize o [modelo treinamento], atualizar o valor de *uri* no nó *locationInfo* com o URI do blob ilearner. O URI é gerado combinando o *BaseLocation* e o *RelativeLocation* da saída do BES treinamento chamada.

     "asset3": {
        "name": "Retrain Sample [trained model]",
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

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importar o JSON para um objeto de definição de serviço Web

Você deve usar o cmdlet [AzureRmMlWebService de importação](https://msdn.microsoft.com/library/azure/mt767925.aspx) para converter o arquivo JSON modificado novamente em um objeto de definição de serviço Web que você pode usar para atualizar o experimento predicative.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Atualizar o serviço web

Finalmente, use o cmdlet [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) para atualizar o experimento previsão.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -

[1]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
