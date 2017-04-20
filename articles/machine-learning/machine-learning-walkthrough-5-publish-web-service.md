<properties
    pageTitle="Etapa 5: Implantar o serviço Web de aprendizado de máquina | Microsoft Azure"
    description="Etapa 5 da desenvolver um passo a passo de solução de previsão: implantar um experimento previsão no Studio de aprendizado de máquina como um serviço Web."
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
    ms.date="10/05/2016"
    ms.author="garye"/>


# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Passo a passo etapa 5: Implantar o serviço Web de aprendizado de máquina do Azure

Esta é a etapa quinta da explicação, [desenvolver uma solução de análise de previsão no aprendizado de máquina do Windows Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Criar um espaço de trabalho de aprendizado de máquina](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Carregar dados existentes](machine-learning-walkthrough-2-upload-data.md)
3.  [Criar um novo experimento](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Treinar e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  **Implantar o serviço Web**
6.  [Acessar o serviço Web](machine-learning-walkthrough-6-access-web-service.md)

----------

Para conceder a outras pessoas a oportunidade de usar o modelo de previsão desenvolvidos nesta explicação, podemos implantá-lo como um serviço Web no Windows Azure.

Até esse ponto podemos estiver tendo problemas com nosso modelo de treinamento. Mas o serviço implantado não mais é vai fazer treinamento - gera previsões por pontuação a entrada do usuário com base em nosso modelo. Portanto, vamos fazer algumas preparação para converter esse experimento de um experimento de ***treinamento*** para uma ***previsão de indisponibilidade*** experimentar. 

Este é um processo de duas etapas:  

1. Converter o *treinamento experimentar* criamos em um *experimento previsão*
2. Implantar o experimento previsão como um serviço Web

Mas, em primeiro lugar, precisamos cortar nesse experimento um pouco. No momento, temos dois modelos diferentes do experimento, mas apenas queremos um modelo quando podemos implantá-la como um serviço Web.  

Digamos que decidimos que o modelo de árvore aumentado era o melhor modelo a ser usado. Portanto, a primeira coisa a fazer é remover a [Classe dois suporte vetor máquina] [ two-class-support-vector-machine] módulo e os módulos que foram usados para treinamento-lo. Convém fazer uma cópia da experiência pela primeira vez, clicando em **Salvar como** na parte inferior da tela de experimento.

É preciso excluir os seguintes módulos:  

- [Suporte a dois classe vetor automática][two-class-support-vector-machine]
- [Treinar modelo] [ train-model] e [Modelo de pontuação] [ score-model] módulos que estavam conectados a ele
- [Normalizar dados] [ normalize-data] (ambos deles)
- [Avaliar o modelo][evaluate-model]

Selecione o módulo e pressione a tecla Delete, ou com o botão direito do módulo e selecione **Excluir**.

Agora, estamos prontos para implantar esse modelo usando a [Classe dois aumentada Decision Tree][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter o experimento treinamento em um experimento previsão

Convertendo para um experimento previsão envolve três etapas:

1. Salvar o modelo, podemos ter treinados e substitua nossos módulos de treinamento
2. Reduzir o experimento para remover os módulos somente eram necessárias para treinamento
3. Definir onde o serviço Web aceitará entrada e onde ele gera a saída

Felizmente, todas as três etapas podem ser realizadas clicando em **serviço Web definido para cima** na parte inferior da tela de experimento (Selecionar a opção de **serviço Web de previsão** ).

Quando você clica em um **serviço Web definido para cima**, várias coisas acontecem:

- O modelo e exibir treinado é salvo como um único módulo **Modelo treinados** na paleta de módulo à esquerda da tela de experimento (você pode encontrá-lo em **Modelos treinados**).
- Módulos que foram usados para treinamento serão removidos. Especificamente:
  - [Árvore de decisão de classe dois aumentado][two-class-boosted-decision-tree]
  - [Modelo de treinamento][train-model]
  - [Dados de divisão][split]
  - o segundo [Script de R executar] [ execute-r-script] módulo que foi usado para dados de teste
- O modelo treinado salvo é adicionado volta para o experimento.
- Módulos de **entrada de serviço da Web** e de **saída do serviço Web** são adicionados.

> [AZURE.NOTE] O experimento foi salvo em duas partes em guias que foram adicionados na parte superior da tela de experimento: o experimento treinamento original é sob a guia **treinamento faça experiências**e o experimento previsão recém-criado é sob **previsíveis experimentar**.

Precisamos entrem em uma única etapa adicional com esse determinado experimento.
Adicionamos dois [Executar o Script de R] [ execute-r-script] módulos para fornecer uma função de ponderação aos dados de treinamento e teste. Não é necessário fazer isso no modelo do final.

Máquina Learning Studio removido de um [Script de R executar] [ execute-r-script] módulo quando ele removido a [divisão] [ split] module. Agora podemos agora pode remover o outro e conecte-se o [Editor de metadados] [ metadata-editor] diretamente ao [Modelo de pontuação][score-model].    

Nosso experimento agora deve parecer com isso:  

![O modelo e exibir treinado de pontuação][4]  

> [AZURE.NOTE] Você pode estar se perguntando por que podemos esquerda o dataset de dados de cartão de crédito alemão UCI a previsão experimento. O serviço irá usar os dados do usuário, não o conjunto de dados original, portanto deixar por que o conjunto de dados original no modelo?
>
>É verdade que o serviço não precisa os dados de cartão de crédito original. Mas precisará ter o esquema de dados, que inclui informações como quantas colunas existem e quais colunas são numéricas. Essas informações do esquema são necessárias interpretar os dados do usuário. Podemos deixar esses componentes conectados de modo que o módulo de pontuação tem o esquema do conjunto de dados quando o serviço está sendo executado. Os dados não são usados, apenas o esquema.  

Execute o experimento pela última vez (clique em **Executar**). Se você quiser verificar se o modelo ainda está funcionando, clique na saída do [Modelo de pontuação] [ score-model] módulo e selecione **Exibir resultados**. Você verá que os dados originais são exibidos, juntamente com o valor de risco de crédito ("marcados rótulos") e o valor de probabilidade pontuação ("marcados probabilidades".) 

## <a name="deploy-the-web-service"></a>Implantar o serviço Web

É possível implantar o experimento como um serviço da Web clássico ou um novo serviço da Web com base no Gerenciador de recursos do Windows Azure.

### <a name="deploy-as-a-classic-web-service"></a>Implantar como um serviço da Web clássico ###

Para implantar um serviço Web clássico, derivado do nosso experimento, clique em **Implantar o serviço da Web** , abaixo da tela de e selecione **Implantar o serviço Web [clássico]**. Máquina Learning Studio implanta o experimento como um serviço Web e vai para o painel para o serviço Web. A partir daqui, você pode retornar ao experimento (**Exibir instantâneo** ou **Exibir mais recente**) e executar um teste simples do serviço da Web (consulte **o serviço Web de teste** abaixo). Há também informações para a criação de aplicativos que podem acessar o serviço da Web (mais detalhes na próxima etapa desse passo a passo).

![Painel de serviço Web][6]

Você pode configurar o serviço clicando na guia **configuração** . Aqui é possível modificar o nome do serviço (ele tiver dado o nome experimento por padrão) e atribua a ele uma descrição. Você também pode dar mais amigáveis etiquetas das colunas de entrada e saída.  

![Configurar o serviço Web][5]  

### <a name="deploy-as-a-new-web-service"></a>Implantar como um novo Web service

Para implantar um serviço Web novo derivado do nosso experimento, clique em **Implantar o serviço da Web** , abaixo da tela e **Implantar o serviço de Web [novo]**. Máquina Learning Studio transfere você para a página de experimento implantar serviços Web de aprendizado de máquina do Azure.

Insira um nome para o serviço da Web e selecione um plano de preço. Se você tiver um plano de preço existente, que você poderá selecioná-lo, caso contrário, você deve criar um novo plano de preço para o serviço. 

1.  Na lista suspensa de **Plano de preço** , selecione um plano existente ou selecione a opção de **Selecionar o novo plano** .
2.  Em **Nome do plano**, digite um nome que identifica o plano na sua conta.
3.  Selecione uma das **Camadas de planejar mensal**. O padrão de camadas de plano para os planos para sua região padrão e seu serviço da Web é implantado para essa região.

Clique em **implantar** e a página de **início rápido** para sua abre do serviço Web.

Você pode configurar o serviço clicando-se a opção de menu **Configurar** . Aqui é possível modificar o nome do serviço (ele tiver dado o nome experimento por padrão) e atribua a ele uma descrição. 

Para testar a selecionar de serviço da Web, clique na opção de menu **de teste** (consulte a **testar o serviço Web** abaixo). Para obter informações sobre como criar aplicativos que podem acessar o serviço da Web, clique na opção de menu de **Consume** (mais detalhes na próxima etapa desse passo a passo).

> [AZURE.TIP] Depois que você implantou-lo, você poderá atualizar o serviço Web. Por exemplo, se você quiser alterar o seu modelo, edite o experimento treinamento, ajustar os parâmetros do modelo e clique em **Implantar o serviço da Web**. Selecione **implantar Web [clássico]** ou **Implantar o serviço Web [novo]**. Quando você implanta o experimento novamente, ele substitui o serviço Web, agora usando seu modelo atualizado.  

## <a name="test-the-web-service"></a>Testar o serviço Web

### <a name="test-a-classic-web-service"></a>Testar um serviço da Web clássico

Você pode testar o serviço na máquina Learning Studio ou no portal de serviços da Web de aprendizagem do Windows Azure máquina. Testando no portal de serviços da Web de aprendizagem do Windows Azure máquina tem a vantagem de permitir que você habilite 

**Testar em máquina Learning Studio**

Na página do **painel** , clique no botão de **teste** em um **Ponto de extremidade padrão**. Uma caixa de diálogo será pop-up e pergunta se para os dados de entrada para o serviço. Estas são as mesmas colunas que aparecia no conjunto de dados de risco de crédito alemão original.  

Insira um conjunto de dados e clique em **Okey**. 

**Testar no portal de serviços da Web de aprendizagem do Windows Azure máquina**

Na página do **painel** , clique no link de visualização de **teste** em um **Ponto de extremidade padrão**. A página de teste no portal de serviços Web do Windows Azure máquina aprendizagem para o ponto de extremidade de serviço Web abre e solicita os dados de entrada para o serviço. Estas são as mesmas colunas que aparecia no conjunto de dados de risco de crédito alemão original.

Clique em **Solicitação-resposta de teste**. 

No serviço da Web, a entrada de dados por meio do módulo de **entrada de serviço da Web** , por meio do [Editor de metadados] [ metadata-editor] módulo e para o [Modelo de pontuação] [ score-model] módulo onde ele está marcado. Os resultados são então saídos do serviço da Web por meio de **saída do serviço Web**.

**Testar um novo Web service**

No portal de serviços Web de aprendizado de máquina do Azure, clique em **teste** na parte superior da página. A página de **teste** é aberta e pode ser inserido dados para o serviço. Os campos de entrada exibidos correspondem às colunas que aparecia no conjunto de dados de risco de crédito alemão original. 

Insira um conjunto de dados e clique em **Solicitação-resposta de teste**.

Os resultados do teste serão exibida na parte lateral direita da página na coluna de saída. 

Ao testar no portal de serviços da Web de aprendizagem do Windows Azure máquina, você pode habilitar dados de amostra que você pode usar para testar o serviço de solicitação de resposta. Se você criou o serviço Web no Studio de aprendizado de máquina, os dados de amostra são obtidos os dados sua usada para treinar seu modelo.

> [AZURE.TIP] A maneira que temos o experimento previsão configurado, todo o resultados do [Modelo de pontuação] [ score-model] módulo são retornados. Isso inclui o valor de risco de crédito além de todos os dados de entrada e a probabilidade de pontuação. Se você quisesse retornar algo diferente - por exemplo, apenas o crédito risco valor - e em seguida, você pode inserir um [Projeto colunas] [ project-columns] módulo entre [O modelo de pontuação] [ score-model] e a **saída do serviço Web** para eliminar as colunas que você não deseja que o serviço Web para retornar. 

## <a name="manage-the-web-service"></a>Gerenciar o serviço Web

**Gerenciar um serviço Web clássico**

Depois que você implantou o serviço Web clássico, você pode gerenciá-lo a partir do [portal clássico do Windows Azure](https://manage.windowsazure.com).

1. Entrar no [portal clássico do Windows Azure](https://manage.windowsazure.com).
2. No painel de serviços do Microsoft Azure, clique em **APRENDIZADO de máquina**.
3. Clique em seu espaço de trabalho.
4. Clique na guia **serviços da Web** .
5. Clique no serviço Web que criamos.
6. Clique no ponto de extremidade "padrão".

A partir daqui, você pode fazer coisas como monitorar como o serviço da Web está sendo realizada e fazer ajustes de desempenho, alterando simultânea quantos chama o serviço podem manipular.
Você ainda pode publicar seu serviço da Web no Azure Marketplace.

Para obter mais detalhes, consulte:

- [Criando pontos de extremidade](machine-learning-create-endpoint.md)
- [Serviço Web de dimensionamento](machine-learning-scaling-webservice.md)
- [Publicar o serviço Web de aprendizagem do Windows Azure máquina no Azure Marketplace](machine-learning-publish-web-service-to-azure-marketplace.md)

**Gerenciar um serviço Web no portal de serviços da Web de aprendizagem do Windows Azure máquina**

Depois que você implantou o serviço Web, clássico ou novo, você pode gerenciá-lo a partir do [portal de serviços da Web de aprendizado de máquina do Azure](https://servics.azureml.net).

Para monitorar o desempenho do seu serviço da Web:

1. Entrar no [portal de serviços da Web de aprendizado de máquina do Azure](https://servics.azureml.net).
2. Clique em **serviços Web**.
3. Clique em seu serviço da Web.
4. Clique em **Painel de controle**.

----------

**Próximo: [o serviço Web do Access](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/