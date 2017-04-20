<properties
    pageTitle="Implantar um serviço web de aprendizado de máquina | Microsoft Azure"
    description="Como converter um experimento de treinamento em um experimento previsão, prepará-lo para a implantação e depois implantá-lo como um serviço web de aprendizado de máquina do Azure."
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
    ms.date="10/04/2016"
    ms.author="garye"/>

# <a name="deploy-an-azure-machine-learning-web-service"></a>Implantar um serviço web de aprendizado de máquina do Windows Azure

Aprendizado de máquina Azure permite criar, testar e implantar soluções analíticas previsão.

A partir de um alto nível ponto de Vista, isso é feito em três etapas:

- **[Criar um experimento treinamento]** - Studio de aprendizado de máquina do Windows Azure é um ambiente de desenvolvimento visual colaborativo que você usa para treinar e testar um modelo de análise de previsão utilizando dados de treinamento que você fornecer.
- **[Convertê-lo para um experimento previsão]** - uma vez seu modelo foi treinado com dados existentes e você está pronto para usá-lo para novos dados de pontuação, preparar e simplificar seu experimento para previsões.
- **Implantá-lo como um serviço web** - você pode implantar o seu experimento previsão como um serviço da web Azure [novos] ou [clássico] . Os usuários podem enviar dados para o seu modelo e receber previsões do seu modelo.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>Criar um experimento de treinamento

Para treinar um modelo de análise de previsão, você use Azure máquina aprendizagem Studio para criar um experimento treinamento onde você pode incluir vários módulos para carregar dados do treinamento, prepare os dados conforme o necessário, aplicar algoritmos de aprendizado de máquina e avaliar os resultados. Você pode iterar em um experimento e algoritmos de aprendizado de máquina diferentes para comparar e avaliar os resultados de teste.

O processo de criação e experiências de treinamento de gerenciamento é abordado mais detalhadamente em outros lugares. Para obter mais informações, consulte estes artigos:

- [Criar um experimento simple no Azure máquina Learning Studio](machine-learning-create-experiment.md)
- [Desenvolver uma solução de previsão com o aprendizado de máquina do Windows Azure](machine-learning-walkthrough-develop-predictive-solution.md)
- [Importe os dados de treinamento para o Studio de aprendizado de máquina do Azure](machine-learning-data-science-import-data.md)
- [Gerenciar iterações experimento no Azure máquina Learning Studio](machine-learning-manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter o experimento treinamento em um experimento previsão

Depois que você tiver treinado seu modelo, você está pronto para converter sua experimento treinamento em um experimento previsão para pontuação novos dados.

Convertendo um experimento previsão, você está chegando seu modelo treinado pronto para ser implantado como um serviço web de pontuação. Usuários do serviço web podem enviar dados de entrada ao seu modelo e seu modelo enviará novamente os resultados de previsão. Como converter em um experimento previsão, tenha em mente como você espera que seu modelo a ser usado por outros usuários.

Para converter um experimento previsão seu experimento de treinamento, clique em **Executar** na parte inferior da tela de experimento, **Set Up Web Service**e selecione **Previsão de indisponibilidade do Web Service**.

![Converter em pontuação experimento](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

Para obter mais informações sobre como executar esta conversão, consulte [converter um experimento de treinamento de aprendizado de máquina para um experimento previsão](machine-learning-convert-training-experiment-to-scoring-experiment.md).

As seguintes etapas descrevem como implantar um experimento previsão como um novo web service. Você também pode implantar o experimento como o serviço da web clássico.

## <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>Implantar o experimento previsão como um novo web service

Agora que a previsão experimento foi preparado, você pode implantá-lo como um serviço web do Windows Azure. Usando o serviço web, os usuários podem enviar dados para o seu modelo e o modelo e exibir retornará de previsões.

Para implantar seu experimento previsão, clique em **Executar** na parte inferior da tela de experimento. Depois que o experimento execução for concluída, clique em **Implantar o serviço da Web** e selecione **implantar o serviço da Web [New]**.  A página de implantação do portal do serviço de Web de aprendizado de máquina é aberto.

### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Portal de aprendizado Web Service de máquina Deploy experimento página

Na página implantar experimento, insira um nome para o serviço web.
Selecione o plano de preço. Se você tiver um plano de preço existente, que você poderá selecioná-lo, caso contrário, você deve criar um novo plano de preço para o serviço.

1.  No **Plano de preço** lista suspensa, selecione um plano existente ou selecione a opção de **Selecionar o novo plano** .
2.  Em **Nome do plano**, digite um nome que identificará o plano na sua conta.
3.  Selecione uma das **Camadas de planejar mensal**. O padrão de camadas de plano para os planos para sua região padrão e seu serviço da web é implantado para essa região.

Clique em **Deploy** e a página de **início rápido** para seu serviço de web é aberto.

A página de início rápido do serviço web oferece acesso e orientações sobre as tarefas mais comuns que você realizará após a criação de um serviço web. Aqui, você pode acessar a página de teste e a página Consume facilmente.

<!-- ![Deploy the web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-web-service"></a>Testar seu serviço da web

Para testar seu novo serviço da web, clique em **Testar serviço web** em tarefas comuns. Na página de teste, você pode testar o serviço web como uma resposta de solicitação de serviço (RR) ou um serviço de execução de lote (BES).

A página de teste de RR exibe as entradas, saídas e quaisquer parâmetros globais que você definiu para o experimento. Para testar o serviço web, você pode inserir valores apropriados para as entradas manualmente ou fornecer um arquivo formatado do valor (CSV) separada por vírgulas contendo os valores de teste.

Para testar usando RR, a partir do modo de exibição de lista, insira valores apropriados para as entradas e clique em **Testar solicitação-resposta**. Os resultados de previsão exibem na coluna de saída para a esquerda.

![Implantar o serviço web](./media/machine-learning-publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Para testar sua BES, clique em **lote**. Na página de teste de lote, clique em Procurar em sua opinião e selecione um arquivo CSV que contém os valores de amostra apropriado. Se você não possui um arquivo CSV e você criou o seu experimento previsão usando o Studio de aprendizado de máquina, você pode baixar o conjunto de dados para o seu experimento previsão e usá-lo.

Para baixar o conjunto de dados, abra Studio de aprendizado de máquina. Abra o seu experimento previsão e clique com botão direito a entrada para seu experimento. No menu de contexto, selecione o **conjunto de dados** e selecione **Baixar**.

![Implantar o serviço web](./media/machine-learning-publish-a-machine-learning-web-service/figure-7-mls-download.png)

Clique em **Testar**. Exibe o status do seu trabalho de execução em lotes direita em **Trabalhos em lote de teste**.

![Implantar o serviço web](./media/machine-learning-publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)-->

Na página de **configuração** , você pode alterar a descrição, título, atualize a chave de conta de armazenamento e permitir que os dados de amostra para o serviço web.

![Configurar o serviço web](./media/machine-learning-publish-a-machine-learning-web-service/figure-8-arm-configure.png)

Depois que você implantou o serviço web, você pode:

- **Access** -lo por meio da API do serviço web.
- **Gerenciá** -lo por meio do portal de serviços da web de aprendizado de máquina do Windows Azure ou portal clássico do Windows Azure.
- **Update** -la, caso seu modelo é alterado.

### <a name="access-the-web-service"></a>Acessar o serviço web

Depois de implantar o serviço web de máquina Learning Studio, você pode enviar dados para o serviço e recebem respostas programaticamente.

A página de **Consume** fornece todas as informações necessárias acessar o serviço web. Por exemplo, a chave de API é fornecida para permitir o acesso autorizado ao serviço.

Para obter mais informações sobre como acessar um serviço web de aprendizado de máquina, consulte [como consumir um serviço web de aprendizado de máquina do Azure implantado](machine-learning-consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Gerenciar seu novo serviço da web

Você pode gerenciar seu portal de serviços de Web de aprendizado de máquina de serviços da web clássico. Na [página do portal principal](https://services.azureml-test.net/), clique em **Serviços Web**. Na página serviços web, você pode excluir ou copiar um serviço. Para monitorar um serviço específico, clique no serviço e, em seguida, clique em **Painel de controle**. Para monitorar trabalhos em lote associados ao serviço da web, clique em **Log de solicitações de lote**.

## <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>Implantar o experimento previsão como um serviço da web clássico

Agora que o experimento previsão suficientemente foi preparado, você pode implantá-lo como um serviço web do Windows Azure. Usando o serviço web, os usuários podem enviar dados para o seu modelo e o modelo e exibir retornará de previsões.

Para implantar seu experimento previsão, clique em **Executar** na parte inferior da tela de experimento e clique em **Implantar o serviço da Web**. O serviço da web está configurado e você é colocados no painel de serviço da web.

![Implantar o serviço web](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

Você pode testar o serviço da web no portal de serviços de Web de aprendizado de máquina ou Studio de aprendizado de máquina.

Para testar o serviço web de resposta à solicitação, clique no botão **Testar** no painel de controle de serviço da web. Uma caixa de diálogo é exibida para pedir os dados de entrada para o serviço. Estas são as colunas esperadas pelo pontuação experimento. Insira um conjunto de dados e clique em **Okey**. Os resultados gerados pelo serviço da web são exibidos na parte inferior do painel.

Você pode clicar no link de visualização **Testar** para testar seu serviço no portal de serviços da Web de aprendizagem do Windows Azure máquina, conforme mostrado anteriormente na seção novo serviço web.

Para testar o serviço de execução de lote, clique no link de visualização de **teste** . Na página de teste de lote, clique em Procurar em sua opinião e selecione um arquivo CSV que contém os valores de amostra apropriado. Se você não possui um arquivo CSV e você criou o seu experimento previsão usando o Studio de aprendizado de máquina, você pode baixar o conjunto de dados para o seu experimento previsão e usá-lo.

![Testar o serviço web](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

Na página de **configuração** , você pode alterar o nome de exibição do serviço e dê a ela uma descrição. O nome e a descrição é exibida no [Azure portal clássico](http://manage.windowsazure.com/) de onde você gerencia seus serviços da web.

Você pode fornecer uma descrição para seus dados de entrada, dados de saída e os parâmetros de serviço web digitando uma cadeia de caracteres para cada coluna em **Esquema de entrada**, o **Esquema de saída**e o **Parâmetro do serviço Web**. Essas descrições são usadas na documentação de código de exemplo fornecida para o serviço web.

É possível habilitar o registro em log diagnosticar todas as falhas que você está vendo quando seu serviço da web é acessado. Para obter mais informações, consulte [Habilitar o log para serviços da web de aprendizado de máquina](machine-learning-web-services-logging.md).

![Configurar o serviço web](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)

Você também pode configurar os pontos de extremidade do serviço web no portal de serviços da Web de aprendizagem do Windows Azure máquina semelhante ao procedimento mostrado anteriormente na seção novo serviço web. As opções são diferentes, é possível adicionar ou alterar a descrição de serviços, habilitar logs e habilitar dados de amostra de teste.

### <a name="access-the-web-service"></a>Acessar o serviço web

Depois de implantar o serviço web de máquina Learning Studio, você pode enviar dados para o serviço e recebem respostas programaticamente.

O painel fornece todas as informações necessárias acessar o serviço web. Por exemplo, a chave de API é fornecida para permitir o acesso autorizado para o serviço e as páginas de ajuda de API são fornecidas para ajudar você a começar a escrever seu código.

Para obter mais informações sobre como acessar um serviço web de aprendizado de máquina, consulte [como consumir um serviço web de aprendizado de máquina do Azure implantado](machine-learning-consume-web-services.md).

### <a name="manage-the-web-service"></a>Gerenciar o serviço web

Há várias das ações que você pode executar para monitorar um serviço web. Você pode atualizá-la e excluí-lo. Você também pode adicionar mais pontos de extremidade em um serviço da web clássico além do ponto de extremidade padrão que é criado quando você implantá-lo.

Para obter mais informações, consulte [Gerenciar um espaço de trabalho de aprendizado de máquina do Windows Azure](machine-learning-manage-workspace.md) e [Gerenciar um web service usando o portal de serviços da Web de aprendizagem do Windows Azure máquina](machine-learning-manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

## <a name="update-the-web-service"></a>O serviço web de atualização

Você pode fazer alterações em seu serviço da web, como atualizar o modelo com dados de treinamento adicional e implante novamente, substituindo o serviço da web original.

Para atualizar o serviço web, abra o original experimento previsão que você usou para implantar o serviço da web e criar uma cópia editável clicando em **Salvar como**. Faça as alterações desejadas e clique em **Implantar o serviço da Web**.

Porque você implantou nesse experimento antes, você será solicitado que se você deseja atualizar (novo web service) o serviço existente ou substituí-la (serviço da Web clássico). Clicar em **Sim** ou **Update** para o serviço da web existente e implanta o novo experimento previsão é implantado em seu lugar.

> [AZURE.NOTE] Se você fez alterações de configuração no serviço da web original, por exemplo, inserindo um novo nome para exibição ou a descrição, você precisará digitação desses valores novamente.

Uma opção para atualizar seu serviço da web é treinar novamente o modelo e exibir programaticamente. Para obter mais informações, consulte [modelos de aprendizado de máquina treinar novamente programaticamente](machine-learning-retrain-models-programmatically.md).


<!-- internal links -->
[Criar um experimento de treinamento]: #create-a-training-experiment
[Convertê-lo em um experimento previsão]: #convert-the-training-experiment-to-a-predictive-experiment
[Novo]: #deploy-the-predictive-experiment-as-a-new-Web-service
[clássico]: #deploy-the-predictive-experiment-as-a-new-Web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
