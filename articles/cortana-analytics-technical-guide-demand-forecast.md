<properties
    pageTitle="Previsão no guia técnico de energia de demanda | Microsoft Azure"
    description="Um guia técnico para o modelo de solução com inteligência de dados do Microsoft Cortana para previsão de energia."
    services="cortana-analytics"
    documentationCenter=""
    authors="yijichen"
    manager="ilanr9"
    editor="yijichen"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="inqiu;yijichen;ilanr9"/>

# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Guia técnico para o modelo de solução de inteligência de Cortana para previsão de energia

## <a name="overview"></a>**Visão geral**

Modelos de solução foram projetados para acelerar o processo de criação de uma demonstração E2E na parte superior de pacote de inteligência de Cortana. Um modelo implantado será provisionar sua assinatura do componente de inteligência de Cortana necessário e crie as relações entre. Ele também propaga o pipeline de dados com dados de exemplo obtendo gerados a partir de um aplicativo de simulação de dados. Baixar o simulador de dados do link fornecido e instalá-lo em seu computador local, consulte o arquivo Leiame. txt para obter instruções sobre o uso do simulador. Dados gerados simulador serão alimentar do pipeline de dados e iniciar gerando previsão de aprendizado de máquina que depois poderão ser visualizada no painel do Power BI.

O modelo de solução pode ser encontrado [aqui](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1) 

O processo de implantação orientará você várias etapas para configurar suas credenciais de solução. Certificar-se de que você registrar essas credenciais como nome da solução, nome de usuário e senha fornecidos durante a implantação.

O objetivo deste documento é explicar a arquitetura de referência e diferentes componentes provisionados em sua assinatura como parte desse modelo de solução. O documento também fala sobre como substituir os dados de exemplo, com dados reais do seu próprio sejam capazes de ver ideias/previsões você ganha dados. Além disso, a documento trata as partes do modelo de solução que precisa ser modificada se você quiser personalizar a solução com seus próprios dados. Instruções sobre como criar o painel do Power BI para esse modelo de solução são fornecidas no final.

## <a name="big-picture"></a>**Visão geral**

![](media\cortana-analytics-technical-guide-demand-forecast\ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Arquitetura explicada
Quando a solução for implantada, vários serviços Azure Cortana Analytics Suite estão ativados (*isto é,* Hub de evento, a análise de fluxo, HDInsight, Data Factory, aprendizado de máquina, *etc.*). Diagrama de arquitetura acima mostra, em um alto nível, como a demanda de previsão para o modelo de solução de energia é construída de ponta a ponta. Você poderá investigar esses serviços clicando no diagrama de modelo de solução criado com a implantação da solução. As seções a seguir descrevem cada parte.

## <a name="data-source-and-ingestion"></a>**Inclusão e fonte de dados**

### <a name="synthetic-data-source"></a>Fonte de dados sintéticos

Para este modelo de fonte de dados usado é gerada por um aplicativo da área de trabalho que você vai baixar e executar localmente após a implantação bem-sucedida. Você encontrará as instruções para baixar e instalar esse aplicativo na barra de propriedades quando você seleciona o primeiro nó chamado Simulator de dados de previsão de energia no diagrama de modelo de solução. Este aplicativo feeds o serviço de [Hub de evento do Azure](#azure-event-hub) com pontos de dados, ou eventos, que serão usados o restante do fluxo de solução.

O aplicativo de geração de evento preencherá Hub de evento do Azure somente enquanto ele está em execução no computador.

### <a name="azure-event-hub"></a>Hub de evento Azure

O serviço de [Hub de evento do Azure](https://azure.microsoft.com/services/event-hubs/) é o destinatário da entrada fornecida pela fonte de dados sintéticos descrito acima.

## <a name="data-preparation-and-analysis"></a>**Análise e preparação de dados**


### <a name="azure-stream-analytics"></a>Análise de fluxo Azure

O serviço de [Análise de fluxo de Azure](https://azure.microsoft.com/services/stream-analytics/) é usado para oferecer quase em tempo real análise no fluxo de entrada do serviço do [Hub de evento do Azure](#azure-event-hub) e publicar os resultados em um painel do [Power BI](https://powerbi.microsoft.com) , bem como arquivamento de todos os eventos de entrada brutos para o serviço de [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) para processamento posterior pelo serviço [Fábrica de dados do Azure](https://azure.microsoft.com/documentation/services/data-factory/) .

### <a name="hd-insights-custom-aggregation"></a>HD ideias personalizada agregação

O serviço do Azure HD ideias é usado para executar scripts de [seção](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (coordenadas pelo fábrica de dados do Azure) para fornecer agregações sobre os eventos brutos que foram arquivados usando o serviço de análise de fluxo de Azure.

### <a name="azure-machine-learning"></a>Aprendizado de máquina Azure

O serviço de [Aprendizado de máquina do Azure](https://azure.microsoft.com/services/machine-learning/) é usado (coordenadas pelo fábrica de dados do Azure) para fazer a previsão no consumo de energia futuras de uma determinada região fornecida as entradas recebidas.

## <a name="data-publishing"></a>**Publicação de dados**


### <a name="azure-sql-database-service"></a>Serviço de banco de dados do SQL Azure

O serviço de [Banco de dados do SQL Azure](https://azure.microsoft.com/services/sql-database/) é usado para armazenar (gerenciados pelo fábrica de dados do Azure) as previsões recebidas pelo serviço aprendizado de máquina do Azure que será consumido no painel [Do Power BI](https://powerbi.microsoft.com) .

## <a name="data-consumption"></a>**Consumo de dados**

### <a name="power-bi"></a>Power BI

Serviço do [Power BI](https://powerbi.microsoft.com) é usado para mostrar um painel que contém as agregações fornecido pela [Análise de fluxo de Azure](https://azure.microsoft.com/services/stream-analytics/) serviço, bem como demanda previsão resultados armazenados no [Banco de dados do Azure SQL](https://azure.microsoft.com/services/sql-database/) que foram produzidos usando o serviço de [Aprendizado de máquina do Azure](https://azure.microsoft.com/services/machine-learning/) . Para obter instruções sobre como criar o painel do Power BI para esse modelo de solução, consulte a seção abaixo.

## <a name="how-to-bring-in-your-own-data"></a>**Como apresentar seus próprios dados**

Esta seção descreve como trazer seus próprios dados para Azure e áreas que exijam alterações para os dados que você trazer para essa arquitetura.

É provável que qualquer conjunto de dados que você trazer seria coincidir com o conjunto de dados usado para esse modelo de solução. Noções básicas sobre os requisitos e seus dados serão fundamental em como você pode modificar este modelo para trabalhar com seus próprios dados. Se esta for sua primeira exposição para o serviço de aprendizado de máquina do Azure, você pode obter uma introdução-lo usando o exemplo em [como criar seu primeiro experimento](machine-learning\machine-learning-create-experiment.md).

As seções a seguir serão discutir as seções do modelo que exigem modificações quando um novo dataset é introduzido.

### <a name="azure-event-hub"></a>Hub de evento Azure

O serviço de [Hub de evento do Azure](https://azure.microsoft.com/services/event-hubs/) é muito genérico, que dados podem ser publicados no hub no formato CSV ou JSON. Nenhum processamento especial ocorre no Hub de evento do Azure, mas é importante que você compreenda os dados que são colocados.

Este documento não descreve como inclusão seus dados, mas um pode facilmente enviar eventos ou dados a um Hub de evento do Azure, usando a [API do Hub de eventos](event-hubs\event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Análise de fluxo Azure

O serviço de [Análise de fluxo de Azure](https://azure.microsoft.com/services/stream-analytics/) é usado para oferecer quase em tempo real analytics lendo de fluxos de dados e saída de qualquer número de fontes de dados.

Para a demanda de previsão para o modelo de solução de energia, a consulta de análise de fluxo de Azure consiste em duas subconsultas, cada consumindo eventos do serviço do Hub de evento do Azure como entradas e ter saídas de dois locais distintos. Essas saídas consistem em um conjunto de dados do Power BI e um local de armazenamento do Azure.

A consulta de [Análise de fluxo de Azure](https://azure.microsoft.com/services/stream-analytics/) pode ser encontrada por:

-   Fazer login no [portal de gerenciamento do Azure](https://manage.windowsazure.com/)

-   Localizando os trabalhos de análise de fluxo ![](media\cortana-analytics-technical-guide-demand-forecast\icon-stream-analytics.png) que foram gerados quando a implantação da solução. Uma é para enviar dados para blob storage (por exemplo, mytest1streaming432822asablob) e o outro é para enviar dados ao Power BI (por exemplo, mytest1streaming432822asapbi).


-   Selecionando

    -   ***ENTRADAS*** para exibir a entrada de consulta

    -   ***Consulta*** para exibir a consulta em si

    -   ***SAÍDAS*** para exibir saídas diferentes

Informações sobre a construção de consulta de análise de fluxo de Azure podem ser encontradas na [Referência de consulta do fluxo de análise](https://msdn.microsoft.com/library/azure/dn834998.aspx) no MSDN.

Nesta solução, o trabalho de análise de fluxo de Azure que gera dataset com perto informações de análise em tempo real sobre o fluxo de dados de entrada a um painel do Power BI é fornecido como parte desse modelo de solução. Porque não há conhecimento implícito sobre o formato de dados de entrada, essas consultas precisarão ser alteradas com base em seu formato de dados.

O outro trabalho de análise de fluxo de Azure saídas todos os eventos de [Hub de evento](https://azure.microsoft.com/services/event-hubs/) ao [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) e não requer, portanto, nenhuma alteração, independentemente de seu formato de dados, como as informações de evento completo são transmitidas ao armazenamento.

### <a name="azure-data-factory"></a>Fábrica de dados do Azure

O serviço de [Fábrica de dados do Azure](https://azure.microsoft.com/documentation/services/data-factory/) organiza a movimentação e o processamento de dados. Na previsão de demanda para o modelo de solução de energia fábrica dados é composta por doze [canais](data-factory\data-factory-create-pipelines.md) mover e os dados usando várias tecnologias de processo.

  Você pode acessar sua fábrica dados abrindo o nó de fábrica de dados na parte inferior do diagrama de modelo de solução criado com a implantação da solução. Isso levará você à fábrica dados em seu portal de gerenciamento do Azure. Se você vir erros em seus conjuntos de dados, você pode ignorar aqueles como eles estão devido a fábrica de dados sendo implantada antes do gerador de dados foi iniciado. Esses erros não impedir que sua fábrica de dados funcione.

Esta seção discute o necessário [canais](data-factory\data-factory-create-pipelines.md) e [atividades](data-factory\data-factory-create-pipelines.md) contidas na [Fábrica de dados do Azure](https://azure.microsoft.com/documentation/services/data-factory/). Abaixo está o modo de exibição de diagrama da solução.

![](media\cortana-analytics-technical-guide-demand-forecast\ADF2.png)

Cinco pipeline de desta fábrica contêm [seção](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts que são usados para partição e agregar os dados. Quando observado, os scripts estará localizados na conta de [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) criada durante a instalação. Sua localização será: demandforecasting\\\\script\\\\seção\\ \\ (ou https://[Your solução name].blob.core.windows.net/demandforecasting).

Tem os scripts [seção](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) semelhante para as consultas de [Análise de fluxo de Azure](#azure-stream-analytics-1) , conhecimento implícito sobre o formato de dados de entrada, essas consultas precisarão ser alteradas com base nas suas necessidades de [engenharia do recurso](machine-learning\machine-learning-feature-selection-and-engineering.md) e formato de dados.

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*

Esse pipeline de [pipeline](data-factory\data-factory-create-pipelines.md) contém uma única atividade - uma atividade de [HDInsightHive](data-factory\data-factory-hive-activity.md) usando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script [seção](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) agregar a cada 10 segundos transmitida nos dados de demanda no nível de Subestação para cada hora nível de região e coloca em [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) do trabalho de análise de fluxo de Azure.

O script de [seção](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para essa tarefa partição é ***AggregateDemandRegion1Hr.hql***


#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*

Esse [pipeline](data-factory\data-factory-create-pipelines.md) contém duas atividades:
- Atividade de [HDInsightHive](data-factory\data-factory-hive-activity.md) usando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script de seção para agregar os dados de demanda histórico por hora no nível de Subestação por hora nível da região e colocar no armazenamento do Azure durante o trabalho de análise de fluxo do Azure

- Atividade de [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os dados agregados de blob de armazenamento do Azure para o banco de dados de SQL Azure que foi provisionada como parte da instalação do modelo de solução.

O script de [seção](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para essa tarefa é ***AggregateDemandHistoryRegion.hql***.


#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*

Esses [canais](data-factory\data-factory-create-pipelines.md) conter várias atividades e cujo resultado final é as previsões scored do experimento aprendizado de máquina do Azure associada a este modelo de solução. Eles são quase idênticos exceto cada um deles trata apenas a região diferente que está sendo feito por diferentes RegionID passado no pipeline de AAD e o script de seção de cada região.  
As atividades contidas neste são:
-   Atividade de [HDInsightHive](data-factory\data-factory-hive-activity.md) usando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script de seção para realizar as agregações e engenharia de recurso necessária para a experiência de aprendizado de máquina do Azure. Os scripts de seção para essa tarefa são respectivos ***PrepareMLInputRegionX.hql***.

-   Atividade de [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os resultados da atividade de [HDInsightHive](data-factory\data-factory-hive-activity.md) para um único blob de armazenamento do Azure que pode ser acessado pela atividade [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) .

-   Atividade de [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) que chama o aprendizado de máquina Azure experimentar que resulta nos resultados sendo colocadas em um único armazenamento do Azure blob.

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
Esses [canais](data-factory\data-factory-create-pipelines.md) contêm uma única atividade - uma atividade de [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os resultados da experiência de aprendizado de máquina do Azure das respectivas ***MLScoringRegionXPipeline*** no banco de dados de SQL Azure que foi provisionada como parte da instalação do modelo de solução.

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
Este [canais](data-factory\data-factory-create-pipelines.md) contêm uma única atividade - uma atividade de [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os dados agregados demanda contínua de ***LoadHistoryDemandDataPipeline*** para o banco de dados de SQL Azure que foi provisionada como parte da instalação do modelo de solução.

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
Esses [canais](data-factory\data-factory-create-pipelines.md) contêm uma única atividade - uma atividade de [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os dados de referência da subestação/região/Topologygeo que são carregados para blob de armazenamento do Azure como parte da instalação do modelo de solução no banco de dados de SQL Azure que foi provisionada como parte da instalação do modelo de solução.

### <a name="azure-machine-learning"></a>Aprendizado de máquina Azure
O experimento de [Aprendizado de máquina do Azure](https://azure.microsoft.com/services/machine-learning/) usado para esse modelo de solução fornece a previsão de demanda da região. O experimento é específico para o conjunto de dados consumido e, portanto, exigirá modificação ou substituição específica aos dados que estão trouxe.

## <a name="monitor-progress"></a>**Monitorar o progresso**
Depois que o gerador de dados for iniciado, o pipeline começa a obter alimentado e os diferentes componentes de sua solução iniciar iniciar em procedimentos de ação os comandos emitidos pela fábrica de dados. Há duas maneiras que você pode monitorar o pipeline.

1. Verifique os dados do armazenamento de Blob do Azure.

    Um do trabalho de análise de fluxo grava os dados de entrada brutos ao armazenamento de blob. Se você clicar em componente de **Armazenamento de Blob do Azure** da solução na tela você com êxito implantado a solução e clique em **Abrir** no painel direito, levará você para o [portal de gerenciamento do Azure](https://portal.azure.com). Uma vez lá, clique em **Blobs**. No painel seguinte, você verá uma lista de contêineres. Clique em **"energysadata"**. No painel seguinte, você verá a pasta **"demandongoing"** . Dentro da pasta RELATS, você verá pastas com nomes como data = 2016-01-28 etc. Se você vir estas pastas, indica que os dados processados são com êxito sendo gerado em seu computador e armazenado no armazenamento de blob. Você deve ver os arquivos que devem ter tamanhos finitos em MB nessas pastas.

2. Verificar os dados do Azure SQL Database.

    A última etapa do pipeline é gravar dados (por exemplo, previsões de aprendizado de máquina) no banco de dados SQL. Talvez seja necessário aguardar um máximo de 2 horas para os dados sejam exibidos no banco de dados do SQL. Uma maneira para monitorar a quantidade de dados está disponível no seu banco de dados do SQL é por meio do [portal de gerenciamento do Azure](https://manage.windowsazure.com/). No painel esquerdo, localize bancos de dados do SQL![](media\cortana-analytics-technical-guide-demand-forecast\SQLicon2.png) e clique nele. Localize o banco de dados (ou seja, demo123456db) e clique nela. Na próxima página **"Conectar ao seu banco de dados"** na seção, clique em **"Executar Transact-SQL consultas em seu banco de dados do SQL"**.

    Aqui, você pode clicar em nova consulta e consulta para o número de linhas (por exemplo, "select count(*) de DemandRealHourly)" como cresce de seu banco de dados, o número de linhas na tabela aumentará.)

3. Verificar os dados do painel do Power BI.

    Você pode configurar o painel de controle do Power BI caminho quente para monitorar os dados de entrada brutos. Siga as instruções na seção "Power BI painel".



## <a name="power-bi-dashboard"></a>**Painel do Power BI**

### <a name="overview"></a>Visão geral

Esta seção descreve como configurar o painel de controle do Power BI para visualizar seus dados de tempo real de análise de fluxo Azure (caminho quente), bem como os resultados do Azure (caminho frio) de aprendizado de máquina de previsão.


### <a name="setup-hot-path-dashboard"></a>Painel de caminho quente de configuração

As etapas a seguir guiará como visualizar saída de dados de tempo real de trabalhos de análise de fluxo geradas no momento da implantação da solução. Uma conta do [Power BI online](http://www.powerbi.com/) é necessário para executar as seguintes etapas. Se você não tiver uma conta, você pode [criar uma](https://powerbi.microsoft.com/pricing).

1.  Adicione saída do Power BI no Azure fluxo Analytics (ASA).

    -  Você precisará siga as instruções em [Azure fluxo Analytics & Power BI: um painel de análise em tempo real para visibilidade em tempo real de fluxo de dados](stream-analytics-power-bi-dashboard.md) para configurar a saída do seu trabalho de análise de fluxo de Azure como seu painel do Power BI.

    - Localize o trabalho de análise de fluxo em seu [portal de gerenciamento do Azure](https://manage.windowsazure.com). O nome do trabalho deve ser: YourSolutionName + "streamingjob" + número aleatório + "asapbi" (ou seja, demostreamingjob123456asapbi).

    - Adicione uma saída de PowerBI para o trabalho ASA. Defina o **Alias de saída** como **'PBIoutput'**. Defina seu **Nome de conjunto de dados** e o **nome da tabela** como **'EnergyStreamData'**. Uma vez que você adicionou a saída, clique em **"Começar"** na parte inferior da página para iniciar o trabalho de análise de fluxo. Você deve obter uma mensagem de confirmação (*por exemplo*, "Iniciando fluxo analytics trabalho myteststreamingjob12345asablob bem-sucedida").

2. Efetuar login no [Power BI on-line](http://www.powerbi.com)

    -   No painel esquerdo seção de conjuntos de dados no meu espaço de trabalho, você deve ser capaz de ver um novo conjunto de dados mostrando no painel esquerdo do Power BI. Este é o streaming dados enviados do Azure fluxo Analytics na etapa anterior.

    -   Verifique se o painel de ***visualizações*** está aberto e é mostrado no lado direito da tela.

3. Crie o bloco "Demanda por carimbo de hora":
    -   Clique em dataset **'EnergyStreamData'** no painel à esquerda, seção de conjuntos de dados.

    -   Clique ícone de **"gráfico de linhas"** ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic8.png).

    -   Clique em 'EnergyStreamData' no painel **campos** .

    -   Clique em **"Carimbo de hora"** e verifique se que ele mostra em "Eixo". Clique em **"Carregar"** e verifique se que ele mostra em "Valores".

    -   Clique em **Salvar** na parte superior e nomeie o relatório como "EnergyStreamDataReport". O relatório denominado "EnergyStreamDataReport" será mostrado na seção de relatórios no painel navegador, à esquerda.

    -   Clique em **"Pin Visual"** ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) ícone no canto superior direito deste gráfico de linhas, uma janela de "Pin para Dashboard" pode aparecer para que você escolha um painel. Selecione "EnergyStreamDataReport" e clique em "Fixar".

    -   Passe o mouse sobre esse bloco no painel, clique em "Editar" ícone no canto superior direito para alterar seu título como "Demanda por carimbo de hora"

4.  Crie outros blocos do painel com base em conjuntos de dados apropriados. O modo de exibição do painel final é mostrado abaixo.
        ![](media\cortana-analytics-technical-guide-demand-forecast\PBIFullScreen.png)


### <a name="setup-cold-path-dashboard"></a>Painel de fria caminho de instalação
No pipeline de dados do caminho fria, o objetivo essencial é obter a previsão de demanda de cada região. Power BI se conecta a um banco de dados do SQL Azure como sua fonte de dados, onde os resultados de previsão são armazenados.

> [AZURE.NOTE] 1) leva para coletar resultados previsão suficiente para o painel de algumas horas. Recomendamos que você inicia esse processo 2-3 horas após você almoço o gerador de dados. 2) nesta etapa, o pré-requisito é baixar e instalar o software gratuito [área de trabalho do Power BI](https://powerbi.microsoft.com/desktop).



1.  Obtenha as credenciais de banco de dados.

    Será necessário **o nome de servidor de banco de dados, o nome do banco de dados, o nome de usuário e senha** antes de passar para as próximas etapas. Estas são as etapas para orientá-lo como localizá-las.

    -   Depois de **"Azure SQL Database"** no seu diagrama de modelo de solução ficará verde, clique nele e, em seguida, clique em **"Abrir"**. Você será orientado ao portal de gerenciamento do Azure e página de informações do banco de dados será aberta também.

    -   Na página, você pode encontrar uma seção "Banco de dados". Ele lista o banco de dados que você criou. O nome do seu banco de dados deve ser **"Seu nome de solução número aleatório + 'db'"** (por exemplo, "mytest12345db").

    -   Clique em banco de dados, na novato pop-out de painel, você pode encontrar o nome do seu servidor de banco de dados na parte superior. Seu recomendável de nome de nome de servidor de banco de dados ser **"Seu nome de solução número aleatório + 'database.windows.net,1433'"** (por exemplo, "mytest12345.database.windows.net,1433").

    -   Seu banco de dados de **nome de usuário** e **senha** são o mesmo que o nome de usuário e senha gravou anteriormente durante a implantação da solução.

2.  Atualizar a fonte de dados do arquivo caminho fria Power BI
    -  Verifique se que você instalou a versão mais recente da [área de trabalho do Power BI](https://powerbi.microsoft.com/desktop).

    -   Na pasta **"DemandForecastingDataGeneratorv1.0"** que você baixou, clique duas vezes no arquivo **' Power BI Template\DemandForecastPowerBI.pbix'.** As visualizações iniciais são baseadas em dados fictícios. **Observação:** Se você vir um erro ajustá, verifique se você instalou a versão mais recente do Power BI Desktop.

        Depois de você abri-lo, na parte superior do arquivo, clique em **' Editar consultas '**. No pop-out de janela, clique duas vezes **'Fonte'** no painel da direita.
    ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic1.png)

    -   Na janela pop, substitua **"Servidor"** e **"Banco de dados"** com seus próprios nomes de banco de dados e servidor e clique em **"Okey"**. Nome do servidor, certifique-se de que você especifique a porta 1433 (**YourSolutionName.database.windows.net, 1433**). Ignore as mensagens de aviso que aparecem na tela.

    -   Na próxima pop-out de janela, você verá duas opções no painel esquerdo (**Windows** e **banco de dados**). Clique em **"Banco de dados"**, preencha seu **"Nome"** e **"Senha"** (esse é o nome de usuário e a senha que você inseriu quando você primeiro implantado a solução e criou um banco de dados do SQL Azure). Em ***Selecione quais nível para aplicar essas configurações***, marque a opção de nível de banco de dados. Clique em **"Conectar"**.

    -   Quando você for orientado voltar para a página anterior, feche a janela. Será exibida uma mensagem out - clique em **Aplicar**. Por fim, clique no botão **Salvar** para salvar as alterações. Agora, seu arquivo do Power BI estabeleceu conexão com o servidor. Se suas visualizações estiverem vazias, verifique se que você desmarcar as seleções na visualizações para visualizar todos os dados clicando no ícone de borracha no canto superior direito das legendas. Use o botão Atualizar para refletir os novos dados em visualizações. Inicialmente, você só verá os dados de propagação em suas visualizações conforme a fábrica de dados está agendada para atualizar a cada 3 horas. Após 3 horas, você verá novas previsões refletidas em suas visualizações quando você atualiza os dados.

3. (Opcional) Publica no painel de caminho fria [On-line do Power BI](http://www.powerbi.com/). Observe que esta etapa precisa de uma conta de Power BI (ou a sua conta do Office 365).

    -   Clique em **"Publicar"** e alguns segundos mais tarde, uma janela aparece exibindo "Publicação para Power BI sucesso!" com uma marca de seleção verde. Clique no link abaixo "Demoprediction.pbix aberto no Power BI". Para obter instruções detalhadas, consulte [Publicar da área de trabalho do Power BI](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

    -   Para criar um novo painel: clique o **+** sinal ao lado de seção **painéis** no painel esquerdo. Insira o nome "Demonstração de previsão de demanda" para este novo painel de controle.

    -   Depois que você abre o relatório, clique em ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) para fixar todas as visualizações ao seu painel. Para obter instruções detalhadas, consulte [Fixar um bloco para um painel do Power BI de um relatório](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
        Vá para a página de painel e ajustar o tamanho e a localização de suas visualizações e editar seus títulos. Para obter instruções detalhadas sobre como editar seus blocos, consulte [Editar uma peça - redimensionar, mover, renomear, pin, excluir, Adicionar hiperlink](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Aqui está um painel de exemplo com algumas visualizações de caminho fria fixada para ele.

        ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic7.png)

4. (Opcional) Programar a atualização da fonte de dados.
    -     A atualização programada de dados, passe o mouse sobre o conjunto de dados **EnergyBPI-Final** , clique em ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic3.png) e, em seguida, escolha a **Programação de atualização**.
    **Observação:** Se você vir uma Massagem de aviso, clique em **Editar credenciais** e verifique se suas credenciais de banco de dados são semelhantes aos descrito na etapa 1.

    ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic4.png)

    -   Expanda a seção **Programação de atualização** . Ative "manter os dados atualizados".

    -   Agende a atualização com base nas suas necessidades. Para obter mais informações, consulte a [atualização de dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).


## <a name="how-to-delete-your-solution"></a>**Como excluir sua solução**
Certifique-se de que você parar o gerador de dados quando não ativamente usando a solução como executar o gerador de dados provocará custos mais altos. Exclua a solução se não estiver usando. A exclusão de sua solução excluirá todos os componentes configurados em sua assinatura quando você implantou a solução. Para excluir a solução, clique no seu nome de solução no painel esquerdo do modelo de solução e clique em Excluir.

## <a name="cost-estimation-tools"></a>**Ferramentas de estimativa de custo**

As duas ferramentas a seguir estão disponíveis para ajudá-lo a entender melhor os custos totais envolvidos na execução a demanda de previsão para o modelo de solução de energia em sua assinatura:

-   [Ferramenta de estimador do Microsoft Azure custo (online)](https://azure.microsoft.com/pricing/calculator/)

-   [Ferramenta de avaliador de custo do Microsoft Azure (desktop)](http://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**Confirmações**
Este artigo é criado por cientista dados Yijing Chen e engenharia de software Min Qiu na Microsoft.
