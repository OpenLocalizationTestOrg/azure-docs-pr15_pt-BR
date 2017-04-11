<properties
    pageTitle="Implantando serviços web do Azure ML que usam módulos dados importação e exportação de dados | Microsoft Azure"
    description="Saiba como usar os módulos dados de importar e exportar dados para enviar e receber dados de um serviço web."
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
    ms.date="08/12/2016"
    ms.author="v-donglo"/>



# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>Implantando serviços web do Azure ML que usam a importação de dados e módulos de exportação de dados 

Quando você cria um experimento previsão, você normalmente adiciona uma entrada de serviço da web e saída. Quando você implanta o experimento, consumidores podem enviar e receber dados do serviço da web por meio das entradas e saídas. Para alguns aplicativos, os dados de um consumidor podem estar disponíveis a partir de um feed de dados ou já reside em uma fonte de dados externos como armazenamento de Blob do Azure. Nesses casos, eles não precisam ler e gravar dados usando saídas e entradas de serviço web. Em vez disso, eles podem usar o serviço de execução de lote (BES) para ler dados da fonte de dados usando um módulo de importar dados e gravar os resultados de pontuação em um local de dados diferentes usando um módulo de exportar dados.

Os dados de importação e exportação módulos de dados, pode ler e gravar para um número de dados locais como uma URL da Web via HTTP, uma consulta de seção, um banco de dados do SQL Azure, armazenamento de tabela do Azure, armazenamento de Blob do Azure, um Feed de dados fornecem ou um banco de dados do SQL no local.

Este tópico usa o "exemplo 5: avaliar trem, teste, para classificação binária: Dataset adultos" amostra e assume o dataset já foi carregado para uma tabela do SQL Azure denominada censusdata.

## <a name="create-the-training-experiment"></a>Criar a experiência de treinamento 
 
Quando você abre o "exemplo 5: avaliar trem, teste, para classificação binária: Dataset adultos" exemplo usa o conjunto de dados do exemplo adultos classificação binária Renda de censo. E o experimento na tela terá uma aparência semelhante a esta imagem.

![Configuração inicial da experiência.](./media/machine-learning-web-services-that-use-import-export-modules/initial-look-of-experiment.png)
  

Para ler os dados da tabela do SQL Azure:

1.  Exclua o módulo do conjunto de dados.
2.  Na caixa de pesquisa componentes, digite importação.
3.  Na lista de resultados, adicione um módulo de *Importar dados* para a tela experimento.
4.  Conecte saída do módulo *Importar dados* a entrada do módulo *Limpar dados ausentes* .
5.  No painel Propriedades, selecione **o banco de dados do Azure SQL** no menu suspenso de **Fonte de dados** .
6.  No **nome do servidor de banco de dados**, o **nome do banco de dados**, **nome de usuário**e **senha** campos, insira as informações apropriadas para seu banco de dados.
7.  No campo de consulta de banco de dados, insira a seguinte consulta.

        select [age],
           [workclass],
           [fnlwgt],
           [education],
           [education-num],
           [marital-status],
           [occupation],
           [relationship],
           [race],
           [sex],
           [capital-gain],
           [capital-loss],
           [hours-per-week],
           [native-country],
           [income]
        from dbo.censusdata;

8.  Na parte inferior da experiência da tela, clique em **Executar**.

## <a name="create-the-predictive-experiment"></a>Criar o experimento previsão

Em seguida você configurar o experimento previsão do qual você implantará seu serviço da web.

1.  Na parte inferior da tela experimento, clique em **Configurar o serviço Web** e selecione **Previsão serviço Web [recomendável]**.
2.  Remova o experimento previsão a *Entrada de serviço da Web* e *módulos de saída de serviço Web* . 
3.  Na caixa de pesquisa componentes, digite exportação.
4.  Na lista de resultados, adicione um módulo de *Exportar dados* para a tela experimento.
5.  Conecte saída do *Modelo de pontuação* módulo a entrada do módulo *Exportar dados* . 
6.  No painel Propriedades, selecione **o banco de dados do Azure SQL** na lista suspensa de destino de dados.
7.  No **nome do servidor de banco de dados**, o **nome do banco de dados**, **nome de conta de usuário do servidor**e campos de **senha de conta de usuário do servidor** , insira as informações apropriadas para seu banco de dados.
8.  No campo da **lista de colunas a serem salvos separada por vírgulas** , digite marcou rótulos.
9.  No **campo nome da tabela de dados**, digite dbo. ScoredLabels. Se a tabela não existir, ele é criado quando o experimento é executado ou o serviço web é chamado.
10. No campo da **lista de colunas de tabela de dados separados por vírgula** , digite ScoredLabels.

Quando você escreve um aplicativo que chama o serviço web final, talvez você queira especificar uma consulta de entrada diferentes ou tabela de destino em tempo de execução. Para configurar essas entradas e saídas, você pode usar o recurso de parâmetros de serviço Web para definir a propriedade de *fonte de dados* do módulo de *Importação de dados* e a propriedade de destino de dados de modo de *Exportar dados* .  Para obter mais informações sobre parâmetros de serviço Web, consulte a [entrada de parâmetros de serviço Web AzureML](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) no Cortana inteligência e no Blog de aprendizado de máquina.

Para configurar os parâmetros de serviço Web para a consulta de importação e a tabela de destino:

1.  No painel Propriedades para o módulo de *Importar dados* , clique no ícone na parte superior direita do campo de **consulta de banco de dados** e selecione **Definir como parâmetro de serviço web**.
2.  No painel Propriedades para o módulo de *Exportar dados* , clique no ícone na parte superior direita do campo **nome da tabela de dados** e selecione **Definir como parâmetro de serviço web**.
3.  Na parte inferior do painel de propriedades de módulo *Exportar dados* , na seção de **Parâmetros de serviço Web** , clique em consulta de banco de dados e renomeie-a consulta.
4.  Clique em **nome da tabela de dados** e renomeie-a como **tabela**.

Quando terminar, seu experimento deve ser semelhante a esta imagem.
 
![Aparência final do experimento.](./media/machine-learning-web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Agora você pode implantar o experimento como um serviço da web.

## <a name="deploy-the-web-service"></a>Implantar o serviço da web 
Você pode implantar um novo serviço da web ou clássico.

### <a name="deploy-a-classic-web-service"></a>Implantar um serviço Web clássico

Para implantar como um serviço Web clássico e criar um aplicativo para consumi-la:

1.  Na parte inferior da tela experimento, clique em executar.
2.  Quando a execução for concluída, clique em **Implantar o serviço da Web** e selecione **Implantar o serviço Web [clássico]**.
3.  No painel de serviço web, localize sua chave API. Copie e salve-o para usá-la posteriormente.
4.  Na tabela de **Ponto de extremidade padrão** , clique no link de **Execução de lote** para abrir a página de ajuda de API.
5.  No Visual Studio, crie um aplicativo de console c#.
6.  Na página de Ajuda da API, localize a seção de **Código de exemplo** na parte inferior da página.
7.  Copie e cole o código de amostra c# no arquivo Program.cs e remover todas as referências para o armazenamento de blob.
8.  Atualize o valor da variável *apiKey* com a chave de API salva anteriormente.
9.  Localize a declaração de solicitação e atualizar os valores de parâmetros de serviço Web que são passados para os *Dados de importar* e *Exportar dados* módulos. Nesse caso, você será usar a consulta original, mas definir um novo nome de tabela.

        var request = new BatchExecutionRequest() 
        {   
            GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable2" },
            }
        };

10. Execute o aplicativo. 

Após a conclusão da execução, uma nova tabela é adicionada ao banco de dados contendo os resultados de pontuação.

### <a name="deploy-a-new-web-service"></a>Implantar um novo serviço Web

Para implantar como um novo serviço Web e criar um aplicativo para consumi-la:

1.  Na parte inferior da experiência da tela, clique em **Executar**.
2.  Quando a execução for concluída, clique em **Implantar o serviço da Web** e selecione **Implantar [novo] serviço Web**.
3.  Na página implantar experimento, insira um nome para seu serviço da web, selecione um plano de preços e clique em **implantar**.
4.  Na página de **início rápido** , clique em **Consume**.
5.  Na seção **Exemplo de código** , clique em **lote**.
6.  No Visual Studio, crie um aplicativo de console c#.
7.  Copie e cole o código de amostra c# no arquivo Program.cs.
8.  Atualize o valor da variável *apiKey* com a **Chave primária** localizado na seção **informações de consumo básica** .
9.  Localize a declaração *scoreRequest* e atualizar os valores de parâmetros de serviço Web que são passados para os *Dados de importar* e *Exportar dados* módulos. Nesse caso, você será usar a consulta original, mas definir um novo nome de tabela.

        var scoreRequest = new
        {
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                 { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };

10. Execute o aplicativo. 
 

