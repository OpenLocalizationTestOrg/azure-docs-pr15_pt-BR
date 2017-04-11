<properties
    pageTitle="Guia técnico para o modelo de solução de inteligência de Cortana para previsão manutenção em aeroespacial e outras empresas | Microsoft Azure"
    description="Um guia técnico para o modelo de solução com inteligência de dados do Microsoft Cortana para previsão manutenção em aeroespacial, utilitários e transporte."
    services="cortana-analytics"
    documentationCenter=""
    authors="fboylu"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="fboylu" />

# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>Guia técnico para o modelo de solução de inteligência de Cortana para previsão manutenção em aeroespacial e de outras empresas

## <a name="acknowledgements"></a>**Confirmações**
Este artigo é criado por cientistas dados Yan Zhang, Gauher Shaheen, Fidan Boylu Uz e engenharia de software Dan Grecoe na Microsoft.

## <a name="overview"></a>**Visão geral**

Modelos de solução foram projetados para acelerar o processo de criação de uma demonstração E2E na parte superior de pacote de inteligência de Cortana. Um modelo implantado será provisionar sua assinatura com componentes de inteligência de Cortana necessários e crie as relações entre elas. Ele também propaga o pipeline de dados com dados de exemplo gerados a partir de um aplicativo de gerador de dados que você serão baixadas e instaladas no computador local após implantar o modelo de solução. Os dados gerados o gerador serão alimentar do pipeline de dados e iniciar gerando previsões de aprendizado de máquina que depois poderão ser visualizadas no painel do Power BI. O processo de implantação orientará você várias etapas para configurar suas credenciais de solução. Certificar-se de que você registrar essas credenciais como nome da solução, nome de usuário e senha fornecidos durante a implantação.  

O objetivo deste documento é explicar a arquitetura de referência e diferentes componentes provisionados em sua assinatura como parte desse modelo de solução. O documento também fala sobre como substituir os dados de exemplo com dados reais do seu próprio sejam capazes de ver ideias e previsões dos seus próprios dados. Além disso, o documento aborda as partes do modelo de solução que precisa ser modificada se quiser personalizar a solução com seus próprios dados. Instruções sobre como criar o painel do Power BI para esse modelo de solução são fornecidas no final.

>[AZURE.TIP] Você pode baixar e imprimir uma [versão PDF deste documento](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).

## <a name="big-picture"></a>**Visão geral**

![](media/cortana-analytics-technical-guide-predictive-maintenance\predictive-maintenance-architecture.png)

Quando a solução for implantada, vários serviços Azure Cortana Analytics Suite estão ativados (*isto é,* Hub de evento, a análise de fluxo, HDInsight, Data Factory, aprendizado de máquina, *etc.*). Diagrama de arquitetura acima mostra, em um alto nível, como a manutenção de previsão para o modelo de solução da é construída de ponta a ponta. Você poderá investigar esses serviços no portal do azure clicando no diagrama de modelo de solução criado com a implantação da solução excepto HDInsight como esse serviço está provisionado sob demanda quando as atividades de pipeline relacionados são necessárias para executar e excluídos posteriormente.
Você pode baixar uma [versão em tamanho normal do diagrama](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

As seções a seguir descrevem cada parte.

## <a name="data-source-and-ingestion"></a>**Inclusão e fonte de dados**

### <a name="synthetic-data-source"></a>Fonte de dados sintéticos

Para este modelo de fonte de dados usado é gerada por um aplicativo da área de trabalho que você vai baixar e executar localmente após a implantação bem-sucedida. Você encontrará as instruções para baixar e instalar esse aplicativo na barra de propriedades quando você seleciona o primeiro nó chamado previsão gerador de dados de manutenção no diagrama de modelo de solução. Este aplicativo feeds o serviço de [Hub de evento do Azure](#azure-event-hub) com pontos de dados, ou eventos, que serão usados o restante do fluxo de solução. Essa fonte de dados é composta ou derivado de dados publicamente disponíveis do [repositório de dados de NASA](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/) usando o [conjunto de dados do Turbofan mecanismo degradação simulação](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan).

O aplicativo de geração de evento preencherá Hub de evento do Azure somente enquanto ele está em execução no computador.

### <a name="azure-event-hub"></a>Hub de evento Azure

O serviço de [Hub de evento do Azure](https://azure.microsoft.com/services/event-hubs/) é o destinatário da entrada fornecida pela fonte de dados sintéticos descrito acima.

## <a name="data-preparation-and-analysis"></a>**Análise e preparação de dados**


### <a name="azure-stream-analytics"></a>Análise de fluxo Azure

O serviço de [Análise de fluxo de Azure](https://azure.microsoft.com/services/stream-analytics/) é usado para oferecer quase em tempo real análise no fluxo de entrada do serviço do [Hub de evento do Azure](#azure-event-hub) e publicar os resultados em um painel do [Power BI](https://powerbi.microsoft.com) , bem como arquivamento de todos os eventos de entrada brutos para o serviço de [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) para processamento posterior pelo serviço [Fábrica de dados do Azure](https://azure.microsoft.com/documentation/services/data-factory/) .

### <a name="hd-insights-custom-aggregation"></a>Agregação personalizada HD ideias

O serviço do Azure HD ideias é usado para executar scripts de [seção](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (coordenadas pelo fábrica de dados do Azure) para fornecer agregações sobre os eventos brutos que foram arquivados usando o serviço de análise de fluxo de Azure.

### <a name="azure-machine-learning"></a>Aprendizado de máquina Azure

O serviço de [Aprendizado de máquina do Azure](https://azure.microsoft.com/services/machine-learning/) é usado (coordenadas pelo fábrica de dados do Azure) para fazer previsões sobre a vida útil (regra) restante de um mecanismo de aviões determinado fornecida as entradas recebidas.

## <a name="data-publishing"></a>**Publicação de dados**


### <a name="azure-sql-database-service"></a>Serviço de banco de dados do SQL Azure

O serviço de [Banco de dados do SQL Azure](https://azure.microsoft.com/services/sql-database/) é usado para armazenar (gerenciados pelo fábrica de dados do Azure) as previsões recebidas pelo serviço aprendizado de máquina do Azure que será consumido no painel [Do Power BI](https://powerbi.microsoft.com) .

## <a name="data-consumption"></a>**Consumo de dados**

### <a name="power-bi"></a>Power BI

Serviço do [Power BI](https://powerbi.microsoft.com) é usado para mostrar um painel que contém as agregações e alertas fornecidas pelo serviço de [Análise de fluxo de Azure](https://azure.microsoft.com/services/stream-analytics/) , bem como previsões regra armazenadas no [Banco de dados do Azure SQL](https://azure.microsoft.com/services/sql-database/) que foram produzidas usando o serviço de [Aprendizado de máquina do Azure](https://azure.microsoft.com/services/machine-learning/) . Para obter instruções sobre como criar o painel do Power BI para esse modelo de solução, consulte a seção abaixo.

## <a name="how-to-bring-in-your-own-data"></a>**Como apresentar seus próprios dados**

Esta seção descreve como trazer seus próprios dados para Azure e áreas que exijam alterações para os dados que você trazer para essa arquitetura.

É provável que qualquer conjunto de dados que você trazer seria coincidir com o conjunto de dados usado pelo [conjunto de dados do Turbofan mecanismo degradação simulação](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) usado para esse modelo de solução. Noções básicas sobre os requisitos e seus dados serão fundamental em como você pode modificar este modelo para trabalhar com seus próprios dados. Se esta for sua primeira exposição para o serviço de aprendizado de máquina do Azure, você pode obter uma introdução-lo usando o exemplo em [como criar seu primeiro experimento](machine-learning-create-experiment.md).

As seções a seguir serão discutir as seções do modelo que exigem modificações quando um novo dataset é introduzido.

### <a name="azure-event-hub"></a>Hub de evento Azure

O serviço de Hub de evento do Azure é muito genérico, que dados podem ser publicados no hub no formato CSV ou JSON. Nenhum processamento especial ocorre no Hub de evento do Azure, mas é importante entender os dados que são colocados.

Este documento não descreve como inclusão seus dados, mas você pode facilmente enviar eventos ou dados a um Hub de evento do Azure usando a API de Hub de evento.

### <a name="azure-stream-analytics"></a>Análise de fluxo Azure

O serviço de análise de fluxo de Azure é usado para oferecer quase em tempo real analytics lendo de fluxos de dados e saída de qualquer número de fontes de dados.

Para a manutenção de previsão para o modelo de solução da, a consulta de análise de fluxo de Azure consiste em quatro subconsultas, cada consumindo eventos do serviço de Hub de evento do Azure e precisar saídas quatro locais distinto. Essas saídas consistem em três conjuntos de dados do Power BI e um local de armazenamento do Azure.

A consulta de análise de fluxo de Azure pode ser encontrada por:

-   Fazer logon no portal do Azure

-   Localizando os trabalhos de análise de fluxo ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-stream-analytics.png) que foram gerados quando a implantação da solução (*por exemplo*, **maintenancesa02asapbi** e **maintenancesa02asablob** para a solução de manutenção de previsão)

-   Selecionando

    -   ***ENTRADAS*** para exibir a entrada de consulta

    -   ***Consulta*** para exibir a consulta em si

    -   ***SAÍDAS*** para exibir saídas diferentes

Informações sobre a construção de consulta de análise de fluxo de Azure podem ser encontradas na [Referência de consulta do fluxo de análise](https://msdn.microsoft.com/library/azure/dn834998.aspx) no MSDN.

Nesta solução, as consultas saída três conjuntos de dados com perto informações de análise em tempo real sobre o fluxo de dados de entrada a um painel de Power BI é fornecido como parte desse modelo de solução. Porque não há conhecimento implícito sobre o formato de dados de entrada, essas consultas precisarão ser alteradas com base em seu formato de dados.

A consulta no segundo fluxo analytics trabalho **maintenancesa02asablob** simplesmente saídas todos os eventos de [Hub de evento](https://azure.microsoft.com/services/event-hubs/) ao [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) e não requer, portanto, nenhuma alteração, independentemente de seu formato de dados, como as informações de evento completo são transmitidas ao armazenamento.

### <a name="azure-data-factory"></a>Fábrica de dados do Azure

O serviço de [Fábrica de dados do Azure](https://azure.microsoft.com/documentation/services/data-factory/) organiza a movimentação e o processamento de dados. Na manutenção de previsão para o modelo de solução da fábrica dados consiste em três [canais](../data-factory/data-factory-create-pipelines.md) que se movem e os dados usando várias tecnologias de processo.  Você pode acessar sua fábrica dados abrindo o nó fábrica de dados na parte inferior do diagrama de modelo de solução criada com a implantação da solução. Isso levará você à fábrica dados em seu portal Azure. Se você vir erros em seus conjuntos de dados, você pode ignorar aqueles como eles estão devido a fábrica de dados sendo implantada antes do gerador de dados foi iniciado. Esses erros não impedir que sua fábrica de dados funcione.

![](media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Esta seção discute o necessário [canais](../data-factory/data-factory-create-pipelines.md) e [atividades](../data-factory/data-factory-create-pipelines.md) contidas na [Fábrica de dados do Azure](https://azure.microsoft.com/documentation/services/data-factory/). Abaixo está o modo de exibição de diagrama da solução.

![](media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Dois pipeline de desta fábrica contêm [seção](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts que são usados para partição e agregar os dados. Quando observado, os scripts estará localizados na conta de [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) criada durante a instalação. Sua localização será: maintenancesascript\\\\script\\\\seção\\ \\ (ou https://[Your solução name].blob.core.windows.net/maintenancesascript).

Tem os scripts [seção](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) semelhante para as consultas de [Análise de fluxo de Azure](#azure-stream-analytics-1) , conhecimento implícito sobre o formato de dados de entrada, essas consultas precisarão ser alteradas com base nas suas necessidades de [engenharia do recurso](machine-learning-feature-selection-and-engineering.md) e formato de dados.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*

Esse [pipeline](../data-factory/data-factory-create-pipelines.md) contém uma única atividade - uma atividade de [HDInsightHive](../data-factory/data-factory-hive-activity.md) usando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script de [seção](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para partição os dados colocados em [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) durante a [Azure a análise de fluxo](https://azure.microsoft.com/services/stream-analytics/) de trabalho.

O script de [seção](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para essa tarefa partição é ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*

Esse [pipeline](../data-factory/data-factory-create-pipelines.md) contém várias atividades e cujo resultado final está que experimentar as previsões scored do [Aprendizado de máquina do Azure](https://azure.microsoft.com/services/machine-learning/) associado com este modelo de solução.

As atividades contidas neste são:

-   Atividade de [HDInsightHive](../data-factory/data-factory-hive-activity.md) usando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script de [seção](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para realizar as agregações e engenharia de recurso necessária para a experiência de [Aprendizado de máquina do Azure](https://azure.microsoft.com/services/machine-learning/) .
    O script de [seção](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para essa tarefa partição é ***PrepareMLInput.hql***.

-   Atividade de [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os resultados da atividade de [HDInsightHive](../data-factory/data-factory-hive-activity.md) para um único blob de [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) que pode ser acessado pela atividade [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) .

-   Atividade de [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) que chama o [Aprendizado de máquina do Azure](https://azure.microsoft.com/services/machine-learning/) experimentar que produz os resultados sendo colocados em um único blob de [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) .

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*

Esse [pipeline](../data-factory/data-factory-create-pipelines.md) contém uma única atividade - uma atividade de [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move que os resultados do [Aprendizado de máquina do Azure](#azure-machine-learning) experimentar o ***MLScoringPipeline*** ao banco de [Banco de dados do Azure SQL](https://azure.microsoft.com/services/sql-database/) que foi provisionada como parte da instalação do modelo de solução.

### <a name="azure-machine-learning"></a>Aprendizado de máquina Azure

O experimento de [Aprendizado de máquina do Azure](https://azure.microsoft.com/services/machine-learning/) usado para esse modelo de solução fornece o restante útil vida (regra) de um mecanismo de aviões. O experimento é específico para o conjunto de dados consumido e, portanto, exigirá modificação ou substituição específica aos dados que estão trouxe.

Para obter informações sobre como a experiência de aprendizado de máquina do Azure foi criada, consulte [manutenção previsão: etapa 1 de 3, preparação de dados e engenharia de recurso](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>**Monitorar o progresso**
 Depois que o gerador de dados for iniciado, o pipeline começa a obter alimentado e os diferentes componentes de sua solução iniciar iniciar em procedimentos de ação os comandos emitidos pela fábrica de dados. Há duas maneiras que você pode monitorar o pipeline.

1. Um do trabalho de análise de fluxo grava os dados de entrada brutos ao armazenamento de blob. Se você clicar em componente de armazenamento de Blob de sua solução na tela você com êxito implantado a solução e clique em Abrir no painel direito, levará você para o [portal de gerenciamento](https://portal.azure.com/). Uma vez lá, clique em Blobs. No painel seguinte, você verá uma lista de contêineres. Clique em **maintenancesadata**. No painel seguinte, você verá a pasta **Relats** . Dentro da pasta RELATS, você verá pastas com nomes como hora = 17, hora = 18 etc. Se você vir estas pastas, indica que os dados processados são com êxito sendo gerado em seu computador e armazenado no armazenamento de blob. Você deve ver os arquivos csv que devem ter tamanhos finitos em MB nessas pastas.

2. A última etapa do pipeline é gravar dados (por exemplo, previsões de aprendizado de máquina) no banco de dados SQL. Talvez você precise esperar o máximo de três horas para os dados sejam exibidos no banco de dados do SQL. Uma maneira para monitorar a quantidade de dados está disponível no seu banco de dados do SQL é por meio do [portal azure](https://manage.windowsazure.com/). No painel esquerdo, localize bancos de dados do SQL ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-SQL-databases.png) e clique nele. Localize seu banco de dados **pmaintenancedb** e clique nela. Na próxima página na parte inferior, clique em gerenciar

    ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-manage.png).

    Aqui, você pode clicar em nova consulta e consulta para o número de linhas (por exemplo, selecione count(*) de PMResult). À medida que seu banco de dados cresce, deve aumentar o número de linhas na tabela.


## <a name="power-bi-dashboard"></a>**Painel do Power BI**

### <a name="overview"></a>Visão geral

Esta seção descreve como configurar o painel de controle do Power BI para visualizar seus dados em tempo real de análise de fluxo Azure (caminho quente), bem como resultados de previsão de lote do Azure (caminho frio) de aprendizado de máquina.

### <a name="setup-cold-path-dashboard"></a>Painel de fria caminho de instalação

No pipeline de dados de caminho fria, o objetivo essencial é obter a regra de previsão (restante vida útil) de cada mecanismo de aviões quando conclui um voo (circular). O resultado de previsão é atualizado a cada 3 horas para prever os mecanismos de aviões que concluiu um voo durante as últimos 3 horas.

Power BI se conecta a um banco de dados do SQL Azure como sua fonte de dados, onde os resultados de previsão são armazenados. Observação: 1) após implantar sua solução, uma previsão real aparecerá no banco de dados dentro de 3 horas.
O arquivo de pbix que veio com o download de gerador contém alguns dados de propagação para que você pode criar no painel do Power BI imediatamente. 2) nesta etapa, o pré-requisito é baixar e instalar o software gratuito [área de trabalho do Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

As etapas a seguir o guiará sobre como conectar o arquivo de pbix ao banco de dados SQL que era lançado no momento da implantação da solução que contém dados (*por exemplo*. resultados de previsão) para visualização.

1.  Obtenha as credenciais de banco de dados.

    Você precisará **nome de servidor de banco de dados, o nome do banco de dados, o nome de usuário e senha** antes de passar para as próximas etapas. Estas são as etapas para orientá-lo como localizá-las.

    -   Depois de **' Azure SQL Database'** no seu diagrama de modelo de solução ficará verde, clique nela e clique em **'Abrir'**.

    -   Você verá uma nova guia/janela do navegador que exibe a página de portal Azure. No painel esquerdo, clique em **'Grupos de recursos'** .

    -   Selecione a assinatura que você estiver usando para implantação da solução e selecione **' YourSolutionName\_ResourceGroup'**.

    -   Na novato pop-out de painel, clique na ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-sql.png) ícone para acessar seu banco de dados. O nome do banco de dados é ao lado de este ícone (*por exemplo*, **'pmaintenancedb'**) e o **nome do servidor de banco de dados** está listado sob a propriedade de nome de servidor e deve ser semelhante ao **YourSoutionName.database.windows.net**.

    -   Seu banco de dados de **nome de usuário** e **senha** são o mesmo que o nome de usuário e senha gravou anteriormente durante a implantação da solução.

2.  Atualize a fonte de dados do arquivo do relatório de caminho fria com Power BI Desktop.

    -   Na pasta no computador onde você baixou e descompactados o arquivo gerador, clique duas vezes na **PowerBI\\PredictiveMaintenanceAerospace.pbix** arquivo. Se você vir as mensagens de aviso ao abrir o arquivo, ignorá-los. Na parte superior do arquivo, clique em **' Editar consultas '**.

        ![](media\cortana-analytics-technical-guide-predictive-maintenance\edit-queries.png)

    -   Você verá duas tabelas, **RemainingUsefulLife** e **PMResult**. Selecione a primeira tabela e clique em ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-query-settings.png) ao lado de **'Fonte'** sob **' etapas aplicadas '** no painel direito **' Configurações de consulta '** . Ignore quaisquer mensagens de aviso que aparecem.

    -   Na janela pop, substitua **'Servidor'** e **'Database'** com seus próprios nomes de banco de dados e servidor e clique em **'Okey'**. Nome do servidor, certifique-se de que você especifique a porta 1433 (**YourSoutionName.database.windows.net, 1433**). Deixe o campo de banco de dados como **pmaintenancedb**. Ignore as mensagens de aviso que aparecem na tela.

    -   Na próxima pop-out de janela, você verá duas opções no painel esquerdo (**Windows** e **banco de dados**). Clique em **'Database'**, preencha seu **'Username'** e **'Senha'** (esse é o nome de usuário e a senha que você inseriu quando você primeiro implantado a solução e criou um banco de dados do SQL Azure). Em ***Selecione quais nível para aplicar essas configurações***, marque a opção de nível de banco de dados. Clique em **'Conectar'**.

    -   Na segunda tabela **PMResult** clique ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-navigation.png) ao lado de **'Fonte'** sob **' etapas aplicadas '** no painel direito **' Configurações de consulta '** e atualizar os nomes de servidor e banco de dados como as etapas acima e clique em Okey.

    -   Quando você for orientado voltar para a página anterior, feche a janela. Será exibida uma mensagem out - clique em **Aplicar**. Por fim, clique no botão **Salvar** para salvar as alterações. Agora, seu arquivo do Power BI estabeleceu conexão com o servidor. Se suas visualizações estiverem vazias, verifique se que você desmarcar as seleções na visualizações para visualizar todos os dados clicando no ícone de borracha no canto superior direito das legendas. Use o botão Atualizar para refletir os novos dados em visualizações. Inicialmente, você só verá os dados de propagação em suas visualizações conforme a fábrica de dados está agendada para atualizar a cada 3 horas. Após 3 horas, você verá novas previsões refletidas em suas visualizações quando você atualiza os dados.

3.  (Opcional) Publica no painel de caminho fria [On-line do Power BI](http://www.powerbi.com/). Observe que esta etapa precisa de uma conta de Power BI (ou a sua conta do Office 365).

    -   Clique em **'Publicar'** e alguns segundos mais tarde, uma janela aparece exibindo "Publicação para Power BI sucesso!" com uma marca de seleção verde. Clique no link abaixo "Abrir PredictiveMaintenanceAerospace.pbix no Power BI". Para obter instruções detalhadas, consulte [Publicar da área de trabalho do Power BI](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

    -   Para criar um novo painel: clique o **+** sinal ao lado de seção **painéis** no painel esquerdo. Insira o nome "Demonstração de manutenção previsão" para este novo painel de controle.

    -   Depois que você abre o relatório, clique em ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-pin.png) para fixar todas as visualizações ao seu painel. Para obter instruções detalhadas, consulte [Fixar um bloco para um painel do Power BI de um relatório](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
    Vá para a página de painel e ajustar o tamanho e a localização de suas visualizações e editar seus títulos. Para obter instruções detalhadas sobre como editar seus blocos, consulte [Editar uma peça - redimensionar, mover, renomear, pin, excluir, Adicionar hiperlink](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Aqui está um painel de exemplo com algumas visualizações de caminho fria fixada para ele.  Dependendo do quanto você executa seu gerador de dados, seus números em visualizações podem ser diferentes.
    <br/>
    ![](media\cortana-analytics-technical-guide-predictive-maintenance\final-view.png)
<br/>
    -   A atualização programada de dados, passe o mouse sobre o conjunto de dados de **PredictiveMaintenanceAerospace** , clique em ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-elipsis.png) e, em seguida, escolha a **Programação de atualização**.
<br/>
        **Observação:** Se você vir uma Massagem de aviso, clique em **Editar credenciais** e verifique se suas credenciais de banco de dados são semelhantes aos descrito na etapa 1.
<br/>
    ![](media\cortana-analytics-technical-guide-predictive-maintenance\schedule-refresh.png)
<br/>
    -   Expanda a seção **Programação de atualização** . Ative "manter os dados atualizados".
    <br/>
    -   Agende a atualização com base nas suas necessidades. Para obter mais informações, consulte a [atualização de dados no Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Painel de alta caminho de instalação

As etapas a seguir guiará como visualizar saída de dados de tempo real de trabalhos de análise de fluxo geradas no momento da implantação da solução. Uma conta do [Power BI online](http://www.powerbi.com/) é necessário para executar as seguintes etapas. Se você não tiver uma conta, você pode [criar uma](https://powerbi.microsoft.com/pricing).

1.  Adicione saída do Power BI no Azure fluxo Analytics (ASA).

    -  Você precisará siga as instruções em [Azure fluxo Analytics & Power BI: um painel de análise em tempo real para visibilidade em tempo real de fluxo de dados](stream-analytics-power-bi-dashboard.md) para configurar a saída do seu trabalho de análise de fluxo de Azure como seu painel do Power BI.
    - A consulta ASA tem três saídas que são **aircraftmonitor**, **aircraftalert**e **flightsbyhour**. Você pode exibir a consulta clicando na guia da consulta. Correspondente a cada uma dessas tabelas, você precisará adicionar uma saída ao ASA. Quando você adiciona a primeira saída (*por exemplo,* **aircraftmonitor**) Verifique se que o **Alias de saída**, o **Nome do conjunto de dados** e o **Nome de tabela** são os mesmos (**aircraftmonitor**). Repita as etapas para adicionar saídas para **aircraftalert**e **flightsbyhour**. Depois de ter adicionado todos os três tabelas de saída e iniciou o trabalho ASA, você deve obter uma mensagem de confirmação (*por exemplo*, "Iniciando fluxo analytics trabalho maintenancesa02asapbi bem-sucedida").

2. Efetuar login no [Power BI on-line](http://www.powerbi.com)

    -   No painel esquerdo seção de conjuntos de dados no meu espaço de trabalho, os nomes **aircraftmonitor**, **aircraftalert**e **flightsbyhour** ***DATASET*** deverão aparecer. Este é o streaming dados enviados do Azure fluxo Analytics na etapa anterior. O conjunto de dados **flightsbyhour** poderá não exibir ao mesmo tempo que outros dois conjuntos de dados devido a natureza da consulta SQL atrás dela. No entanto, ele deverá ser mostrada após uma hora.
    -   Verifique se o painel de ***visualizações*** está aberto e é mostrado no lado direito da tela.

3. Após colocar os dados fluindo no Power BI, você pode começar a visualizando os dados de streaming. Abaixo é um painel de exemplo com algumas visualizações de caminho quente fixado-lo. Você pode criar outros blocos do painel com base em conjuntos de dados apropriados. Dependendo do quanto você executa seu gerador de dados, seus números em visualizações podem ser diferentes.


    ![](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

4. Aqui estão algumas etapas para criar um dos blocos acima – o "frota modo de exibição de Sensor 11 versus limite 48.26" bloco:

    -   Clique em dataset **aircraftmonitor** no painel à esquerda, seção de conjuntos de dados.

    -   Clique no ícone de **Gráfico de linhas** .

    -   Clique **processadas** no painel **campos** para que ele aparece em "Eixo" no painel de **visualizações** .

    -   Clique em "s11" e "s11\_alerta" para que os dois apareçam em "Valores". Clique na pequena seta ao lado de **s11** e **s11\_alerta**, alterar "Soma" para "Média".

    -   Clique em **Salvar** na parte superior e nomeie o relatório "aircraftmonitor". O relatório denominado "aircraftmonitor" será mostrado na seção de **relatórios** no painel **navegador** , à esquerda.

    -   Clique no ícone de **Pino Visual** no canto superior direito deste gráfico de linhas. Uma janela de "Pin para Dashboard" pode aparecer para que você escolha um painel. Selecione "Previsão demonstração de manutenção", clique em "Fixar".

    -   Passe o mouse sobre neste bloco no painel, clique no ícone de "Editar" no canto superior direito para alterar seu título para "Frota modo de exibição de Sensor 11 versus limite 48.26" e subtítulo para "Média entre frota ao longo do tempo".

## <a name="how-to-delete-your-solution"></a>**Como excluir sua solução**
Certifique-se de que você parar o gerador de dados quando não ativamente usando a solução como executar o gerador de dados provocará custos mais altos. Exclua a solução se não estiver usando. A exclusão de sua solução excluirá todos os componentes configurados em sua assinatura quando você implantou a solução. Para excluir a solução, clique no seu nome de solução no painel esquerdo do modelo de solução e clique em Excluir.

## <a name="cost-estimation-tools"></a>**Ferramentas de previsão de custo**

As duas ferramentas a seguir estão disponíveis para ajudá-lo a entender melhor os custos totais envolvidos na execução a manutenção de previsão para o modelo de solução em sua assinatura:

-   [Ferramenta de estimador do Microsoft Azure custo (online)](https://azure.microsoft.com/pricing/calculator/)

-   [Ferramenta de avaliador de custo do Microsoft Azure (desktop)](http://www.microsoft.com/download/details.aspx?id=43376)
