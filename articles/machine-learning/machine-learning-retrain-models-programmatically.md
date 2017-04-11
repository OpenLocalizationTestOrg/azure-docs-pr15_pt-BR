<properties
    pageTitle="Treinar novamente os modelos de aprendizado de máquina programaticamente | Microsoft Azure"
    description="Saiba como programaticamente treinar novamente um modelo e atualizar o serviço da web para usar o modelo de treinamento recentemente no aprendizado de máquina do Azure."
    services="machine-learning"
    documentationCenter=""
    authors="raymondlaghaeian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="raymondl;garye;v-donglo"/>


# <a name="retrain-machine-learning-models-programmatically"></a>Treinar novamente os modelos de aprendizado de máquina programaticamente  

Este passo a passo, você aprenderá programaticamente treinar novamente um serviço de Web de aprendizagem do Azure máquina usando c# e o serviço de execução de lote de aprendizado de máquina.

Depois que você tiver retrained o modelo, as instruções a seguir mostra como atualizar o modelo no seu serviço da web de previsão:

- Se você implantou um serviço da web clássico no portal de serviços de Web de aprendizado de máquina, consulte [treinar novamente um serviço da web clássico](machine-learning-retrain-a-classic-web-service.md). 
- Se você implantou um novo serviço web, consulte [treinar novamente um novo serviço web usando os cmdlets de gerenciamento de aprendizado de máquina](machine-learning-retrain-new-web-service-using-powershell.md).

Para obter uma visão geral do processo de novos treinamentos, consulte [treinar novamente um modelo de aprendizado de máquina](machine-learning-retrain-machine-learning-model.md).

Se você quer começar com seu novo Gerenciador de recursos do Azure com base web Services existente, consulte [treinar novamente um serviço web previsão existente](machine-learning-retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Criar um experimento de treinamento
 
Neste exemplo, você usará "exemplo 5: avaliar trem, teste, para classificação binária: Dataset adultos" das amostras de aprendizado de máquina do Microsoft Azure. 
    
Para criar o experimento:

1.  Entrar no Microsoft Azure Studio de aprendizagem de máquina. 
2.  No canto inferior direito do painel, clique em **novo**.
3.  Selecione Samples Microsoft, Sample 5.
4.  Para renomear o experimento, na parte superior da tela de experimento, selecione o nome do experimento "exemplo 5: avaliar trem, teste, para classificação binária: Dataset adultos".
5.  Tipo de modelo de censo.
6.  Na parte inferior da experiência da tela, clique em **Executar**.
7.  Clique em **Configurar o serviço web** e selecione o **serviço da web Retraining**. 

    ![Experiência inicial.][2]

Diagrama 2: Iniciais experimento.

## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Criar um experimento previsão e publicar como um serviço da web  

Em seguida, você criar um experimento Predicative.

1.  Na parte inferior da tela experimento, clique em **Configurar o serviço Web** e selecione **Previsão serviço da Web**. Isso salva o modelo como um modelo de treinamento e adiciona módulos de entrada e saída do serviço web. 
2.  Clique em **Executar**. 
3.  Após o experimento execução for concluída, clique em **implantar Web [clássico]** ou **Implantar o serviço Web [novo]**.

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Implantar o experimento treinamento como um serviço da web de treinamento

Para treinar novamente o modelo de treinamento, você deve implantar o experimento de treinamento que você criou como um serviço da web Retraining. Este serviço web precisa de um módulo de *Saída de serviço Web* conectado ao * [Modelo de trem] [ train-model] * módulo, para poder produzir novos modelos de treinamento.

1. Para retornar para a experiência de treinamento, clique no ícone de experiências no painel esquerdo, o experimento chamado modelo de censo.  
2. Na caixa de pesquisa pesquisar experimento itens, digite serviço web. 
3. Arraste um módulo de *Entrada de serviço da Web* para a tela experimento e conecte sua saída para o módulo de *Limpar dados ausentes* .  Isso garante que os seus dados novos treinamentos são processados da mesma maneira que seus dados de treinamento original.
4. Arraste dois módulos de *serviço web de saída* para a tela experimento. Conecte a saída do módulo *Trem modelo* a um e a saída do módulo *Avaliar modelo* à outra. A saída de serviço web para o **Modelo de trem** nos dá o novo modelo de treinamento. A saída anexada a **Avaliar modelo** retorna saída do módulo, que é o resultado de desempenho.
5. Clique em **Executar**. 

Em seguida, você deve implantar o experimento treinamento como um serviço da web que produz um modelo de treinamento e resultados de avaliação do modelo. Para fazer isso, o próximo conjunto de ações dependem se você estiver trabalhando com um serviço da web clássico ou um novo serviço web.  
  
**Serviço web clássico**

Na parte inferior da tela experimento, clique em **Configurar o serviço Web** e selecione **Implantar o serviço Web [clássico]**. O serviço Web **Dashboard** é exibida com a chave de API e a página de ajuda de API para execução em lotes. Somente o método de execução em lotes pode ser usado para a criação de modelos de treinamento.

**Novo serviço da web**

Na parte inferior da tela experimento, clique em **Configurar o serviço Web** e selecione **Implantar [novo] serviço Web**. O portal de serviços Web aprendizado de máquina do Web serviço Azure abre a página de serviço da web de implantar. Digite um nome para seu serviço da web e escolha um plano de pagamento e clique em **implantar**. Somente o método de execução em lotes pode ser usado para a criação de modelos de treinamento

Nos dois casos, após experimento em execução, o fluxo de trabalho resultante deve ter a seguinte aparência:

![Resultante fluxo de trabalho após a execução.][4]

Diagrama 3: Resultantes fluxo de trabalho após a execução.

## <a name="retrain-the-model-with-new-data-using-bes"></a>Treinar novamente o modelo com novos dados usando BES

Neste exemplo, estiver usando c# para criar o aplicativo de novos treinamentos. Você também pode usar o código de exemplo Python ou R para realizar essa tarefa.

Para chamar as APIs de treinamento:

1. Criar um aplicativo de Console c# no Visual Studio (Novo -> projeto -> Windows Desktop -> aplicativo de Console).
2.  Entre portal do serviço de Web de aprendizado de máquina.
3.  Se você estiver trabalhando com um serviço da web clássico, clique em **Clássico Web Services**.
    1.  Clique no serviço da web que você está trabalhando com.
    2.  Clique na extremidade padrão.
    3.  Clique em **consumir**.
    4.  Na parte inferior da página **Consume** , na seção **Exemplo de código** , clique em **lote**.
    5.  Continue para a etapa 5 deste procedimento.
4.  Se você estiver trabalhando com um novo serviço web, clique em **Serviços da Web**.
    1.  Clique no serviço da web que você está trabalhando com.
    2.  Clique em **consumir**.
    3.  Na parte inferior da página Consume, na seção **Exemplo de código** , clique em **lote**.
5.  Copie o código c# de exemplo para execução em lotes e cole o arquivo Program.cs, certifique-se de que o namespace permanecerá intacto.

Adicione o pacote do Nuget Microsoft.AspNet.WebApi.Client conforme especificado nos comentários. Para adicionar a referência ao Microsoft.WindowsAzure.Storage.dll, você precisará primeiro instalar a biblioteca de cliente para serviços de armazenamento do Microsoft Azure. Para obter mais informações, consulte [Serviços de armazenamento do Windows](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Atualizar a declaração de apikey

Localize a declaração **apikey** .

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Na seção **informações de consumo básica** da página **Consume** , localize a chave primária e copiá-lo para a declaração de **apikey** .

### <a name="update-the-azure-storage-information"></a>Atualizar as informações de armazenamento do Azure

O código de exemplo BES carrega um arquivo de uma unidade local (por exemplo "C:\temp\CensusIpnput.csv") para o armazenamento do Azure, processa e grava os resultados de volta ao armazenamento do Azure.  

Para realizar esta tarefa, você deve recuperar as informações de nome, chave e contêiner de conta do armazenamento para sua conta de armazenamento do portal do Azure clássico e os valores correspondentes de atualização no código. 

1. Entrar no portal do Azure clássico.
1. Na coluna de navegação à esquerda, clique em **armazenamento**.
1. Na lista de contas de armazenamento, selecione um para armazenar o modelo retrained.
1. Na parte inferior da página, clique em **Gerenciar teclas de acesso**.
1. Copiar e salvar a **Chave primária de acesso** e feche a caixa de diálogo. 
1. Na parte superior da página, clique em **contêineres**.
1. Selecione um contêiner existente ou crie um novo e salvar o nome.

Localize as declarações *StorageAccountName*, *StorageAccountKey*e *StorageContainerName* e atualizar os valores que você salvou a partir do portal do Azure.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name
            
Você também deve garantir que o arquivo de entrada está disponível no local especificado no código. 

### <a name="specify-the-output-location"></a>Especifique o local de saída

Ao especificar o local de saída na solicitação de carga, a extensão do arquivo especificado em *RelativeLocation* deve ser especificada como ilearner. 

Consulte o exemplo a seguir:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

>[AZURE.NOTE] Os nomes de seus locais de saída podem ser diferentes nesta explicação baseado na ordem em que você adicionou os módulos de saída de serviço web. Como configurar esse experimento de treinamento com duas saídas, os resultados incluem informações de local de armazenamento para ambas.  

![Treinamento de saída][6]

Diagrama 4: Treinamento saída.

## <a name="evaluate-the-retraining-results"></a>Avaliar os resultados de novos treinamentos
 
Quando você executa o aplicativo, a saída inclui o token de Associações e URL necessário para acessar os resultados de avaliação.

Você pode ver os resultados de desempenho do modelo retrained combinando o *BaseLocation*, *RelativeLocation*e *SasBlobToken* dos resultados de saída para *output2* (conforme mostrado na imagem de saída de novos treinamentos anterior) e colar a URL completa na barra de endereços do navegador.  

Examine os resultados para determinar se o modelo recentemente treinamento executa suficiente para substituir a existente.

Copie o *BaseLocation*, *RelativeLocation*e *SasBlobToken* dos resultados de saída, você usará durante o processo de novos treinamentos.

## <a name="next-steps"></a>Próximas etapas

Se você implantou o serviço web de previsão clicando em **Implantar o serviço Web [clássico]**, consulte [treinar novamente um serviço da web clássico](machine-learning-retrain-a-classic-web-service.md).

Se você implantou o serviço web de previsão clicando em **Implantar [novo] serviço Web**, consulte [treinar novamente um novo serviço web usando os cmdlets de gerenciamento de aprendizado de máquina](machine-learning-retrain-new-web-service-using-powershell.md).

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/