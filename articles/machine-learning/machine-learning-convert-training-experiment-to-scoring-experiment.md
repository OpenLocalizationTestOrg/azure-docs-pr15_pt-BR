<properties
    pageTitle="Converter um experimento de treinamento de aprendizado de máquina em um experimento previsão | Microsoft Azure"
    description="Como converter um experimento de treinamento de aprendizado de máquina, usado para o seu modelo de análise de previsão, para um experimento previsão que pode ser implantado como um serviço web de treinamento."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="garye"/>

# <a name="convert-a-machine-learning-training-experiment-to-a-predictive-experiment"></a>Converter um experimento de treinamento de aprendizado de máquina em um experimento previsão

Azure aprendizado de máquina permite construir, testar e implantar soluções de análise de previsão.

Depois que você criou e iteração em um *treinamento experimentar* treinar seu modelo de análise de previsão, e estiver pronto para usá-lo para pontuação novos dados, você precisa preparar e simplificar seu experimento de pontuação. Em seguida, você pode implantar esse *experimento previsão* como um serviço web Azure para que os usuários podem enviar dados para o seu modelo e receber previsões do seu modelo.

Convertendo em um experimento previsão, obtida seu modelo de treinamento pronto para ser implantado como um serviço da web. Os usuários do serviço web enviarão dados de entrada para o seu modelo e seu modelo enviará de volta os resultados de previsão. Assim como converter em um experimento previsão você desejará ter em mente como você espera que seu modelo para ser usado por outras pessoas.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

O processo de converter um experimento de treinamento para um experimento previsão envolve três etapas:

1.  Salve o modelo de aprendizado de máquina que você já treinamento e substitua a [Modelo de trem] e algoritmo de aprendizado de máquina[ train-model] módulos com seu modelo de treinamento salvo.
2.  Corte o experimento para somente os módulos que são necessárias para pontuação. Um experimento treinamento inclui um número de módulos que são necessários para treinamento, mas não são necessárias depois que o modelo é treinamento e prontas para usar em pontuação.
3.  Defina onde seu experimento você aceitará dados do usuário de serviço web e quais os dados serão retornados.

## <a name="set-up-web-service-button"></a>Botão Configurar o serviço da Web

Depois de executar seu experimento (botão**Executar** na parte inferior da tela experimento), o botão **Definir o serviço da Web** (selecione a opção de **Previsão serviço da Web** ) executará para você as três etapas de conversão de seu experimento de treinamento em um experimento previsão:

1.  Salva seu modelo de treinamento como um módulo na seção **Modelos de treinamento** da paleta módulo (à esquerda da tela experimento), em seguida, substitui a [Modelo de trem] e algoritmo de aprendizado de máquina[ train-model] módulos com o modelo de treinamento salvo.
2.  Ele remove módulos que não claramente são necessários. Em nosso exemplo, isso inclui os [Dados de divisão][split], 2º [Pontuação modelo][score-model]e [Avaliar modelo] [ evaluate-model] módulos.
3.  Cria a entrada de serviço da Web e módulos de saída e adiciona-los em locais padrão em seu experimento.

Por exemplo, o seguinte experimento treina um modelo de árvore de decisão aumentadas dois classe usando dados de censo de exemplo:

![Experiência de treinamento][figure1]

Os módulos nesse experimento executam basicamente quatro diferentes funções:

![Funções do módulo][figure2]

Quando você converte esse experimento de treinamento em um experimento previsão, alguns desses módulos não são mais necessários ou eles têm uma finalidade diferente:

- **Dados** - os dados neste conjunto de dados de exemplo não é usado durante a pontuação - o usuário do serviço web fornecerá os dados sejam totalizados. No entanto, os metadados deste conjunto de dados, como tipos de dados, é usado pelo modelo de treinamento. Então você precisa manter o dataset no experimento previsão para que ele possa fornecer esses metadados.

- **Preparação** - dependendo dos dados que serão enviados para pontuação, esses módulos podem ou não ser necessários para processar dados de entrada.

    Por exemplo, neste exemplo, o conjunto de dados de exemplo pode ter valores ausentes e inclui colunas que não são necessários para treinar o modelo. Portanto um [Limpar dados ausentes] [ clean-missing-data] módulo foi incluído para lidar com valores ausentes e [Selecionar colunas em Dataset] [ select-columns] módulo foi incluído para excluir essas colunas extras do fluxo de dados. Se você souber que os dados que serão enviados para pontuação pelo serviço da web não terão valores ausentes, você poderá remover os [Dados ausentes limpa] [ clean-missing-data] módulo. No entanto, como [Selecionar colunas em Dataset] [ select-columns] módulo ajuda a definir o conjunto de recursos sendo marcou, módulo precisa permanecer.

- **Treinar** - depois que o modelo foi treinado com êxito, você salvá-lo como um módulo de modelo de treinamento único. Você então substituir esses módulos individuais com o modelo de treinamento salvo.

- **Pontuação** - neste exemplo, o módulo de divisão é usado para dividir o fluxo de dados em um conjunto de dados de teste e treinamento. O experimento previsão isso não é necessária e pode ser removido. Da mesma forma, 2º de [Modelo de pontuação] [ score-model] módulo e o [Modelo de avaliar] [ evaluate-model] módulo são usados para comparar os resultados de dados de teste, para que esses módulos são também não é necessária a previsão experimento. O [Modelo de pontuação] de restantes[ score-model] módulo, no entanto, é necessário para retornar um resultado de pontuação pelo serviço da web.

Aqui está a aparência do nosso exemplo após clicar em **Definir o serviço da Web**:

![Convertido experimento previsão][figure3]

Isso pode ser suficiente para preparar seu experimento para ser implantado como um serviço da web. No entanto, você talvez queira algum trabalho adicional específicos para seu experimento.

### <a name="adjust-input-and-output-modules"></a>Ajustar módulos de entrada e saídos

No seu experimento de treinamento, você usou um conjunto de dados de treinamento e depois faziam algum processamento para obter os dados em um formulário que o algoritmo de aprendizado de máquina necessários. Se os dados que você espera receber pelo serviço da web não precisará esse processamento, você pode mover o **módulo de entrada de serviço Web** para um nó diferente no seu experimento.

Por exemplo, por padrão, **Defina o serviço Web** coloca o módulo de **entrada de serviço da Web** na parte superior do seu fluxo de dados, como na figura acima. No entanto, se os dados de entrada não precisará esse processamento, em seguida, você pode posicionar manualmente a **entrada de serviço da Web** passaram os módulos de processamento de dados:

![Movendo a entrada de serviço web][figure4]

Os dados de entrada fornecidos pelo serviço da web agora passará diretamente para o módulo do modelo de pontuação sem qualquer pré-processamento.

Da mesma forma, por padrão, **Defina o serviço Web** coloca o módulo de saída de serviço Web na parte inferior do seu fluxo de dados. Neste exemplo, o serviço web retornarão ao usuário a saída do [Modelo de pontuação] [ score-model] módulo que inclui o vetor de dados de entrada completa plus a pontuação de resultados.

No entanto, se você preferir retornar algo diferente - por exemplo, apenas os resultados da pontuação e não o vetor inteiro de dados de entrada -, então, você podem inserir um [Selecionar colunas em Dataset] [ select-columns] módulo excluir todas as colunas, exceto os resultados de pontuação. Você move o módulo de **saída de serviço Web** para a saída de [Selecionar colunas em Dataset] [ select-columns] módulo:

![Movendo a saída de serviço web][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Adicionar ou remover módulos de processamento de dados adicionais

Se houver mais módulos no seu experimento que você sabe que não será necessário durante a pontuação, elas podem ser removidas. Por exemplo, como podemos tiver movido o módulo de **entrada de serviço da Web** a um ponto após os módulos de processamento de dados, podemos remover os [Dados ausentes limpa] [ clean-missing-data] módulo do experimento previsão.

Nosso previsão experimento agora esta aparência:

![Removendo o módulo adicional][figure6]

### <a name="add-optional-web-service-parameters"></a>Adicionar parâmetros opcionais de serviço Web

Em alguns casos, você talvez queira permitir que o usuário do seu serviço da web alterar o comportamento de módulos quando o serviço for acessado. *Parâmetros de serviço da Web* permitem que você faça isso.

Um exemplo comum é configurar a [Importar dados] [ import-data] módulo para que o usuário do serviço web implantado pode especificar uma fonte de dados diferente quando o serviço web for acessado. Ou configurando a [Exportar dados] [ export-data] módulo para que um destino diferente pode ser especificado.

Você pode definir parâmetros de serviço Web e associá-los a um ou mais parâmetros de módulo, e você pode especificar se elas são necessárias ou opcionais. O usuário do serviço da web, em seguida, pode fornecer valores para esses parâmetros quando o serviço é acessado e as ações de módulo serão modificadas adequadamente.

Para obter mais informações sobre os parâmetros de serviço Web, consulte [Usando parâmetros serviço de Web de aprendizagem do Azure máquina ] [ webserviceparameters].

[webserviceparameters]: machine-learning-web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Implantar o experimento previsão como um serviço da web

Agora que o experimento previsão foi preparado suficiente, você pode implantá-lo como um serviço web Azure. Usando o serviço web, os usuários podem enviar dados para o seu modelo e o modelo retornará de previsões.

Para obter mais informações sobre o processo de implantação completa, consulte [implantar um serviço da web de aprendizado de máquina do Azure][deploy]

[deploy]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
