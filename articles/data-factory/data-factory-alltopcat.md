<properties
    pageTitle="Todos os tópicos de serviço de dados fábrica | Microsoft Azure"
    description="Tabela de todos os tópicos para o serviço Azure denominada fábrica de dados que existe em http://azure.microsoft.com/documentation/articles/, título e descrição."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="data-factory"
    ms.workload="data-factory"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="spelluru"/>


# <a name="all-topics-for-azure-data-factory-service"></a>Todos os tópicos de serviço de fábrica de dados do Azure

Este tópico lista cada tópico que se aplica diretamente para o serviço de **Fábrica de dados** do Azure. Você pode pesquisar esta página da Web para palavras-chave usando **Ctrl + F**, para localizar os tópicos de interesse atual.




## <a name="new"></a>Novo

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 1 | [Mover dados do Amazon Redshift utilizando fábrica de dados do Azure](data-factory-amazon-redshift-connector.md) | Saiba mais sobre como mover dados de Redshift Amazon usando fábrica de dados do Azure. |
| 2 | [Mover dados do Amazon Simple Storage Service usando fábrica de dados do Azure](data-factory-amazon-simple-storage-service-connector.md) | Saiba mais sobre como mover dados do Amazon Simple Storage Service (S3) usando fábrica de dados do Azure. |
| 3 | [Dados Azure Factory - Assistente de cópia](data-factory-azure-copy-wizard.md) | Saiba mais sobre como usar o Assistente de cópia do Azure de fábrica dados para copiar dados de fontes de dados com suporte para receptores. |
| 4 | [Tutorial: Criar sua primeira fábrica de dados Azure usando dados fábrica REST API](data-factory-build-your-first-pipeline-using-rest-api.md) | Neste tutorial, você cria um pipeline de fábrica do Azure dados de amostra usando dados fábrica REST API. |
| 5 | [Tutorial: Criar um pipeline com atividade de cópia usando API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md) | Neste tutorial, você cria um pipeline de fábrica de dados do Azure com uma atividade de cópia usando API .NET. |
| 6 | [Tutorial: Criar um pipeline com atividade de cópia usando API REST](data-factory-copy-activity-tutorial-using-rest-api.md) | Neste tutorial, você cria um pipeline de fábrica de dados do Azure com uma atividade de cópia usando API REST. |
| 7 | [Assistente de cópia de fábrica de dados](data-factory-copy-wizard.md) | Saiba mais sobre como usar o Assistente de cópia de fábrica de dados para copiar dados de fontes de dados com suporte para receptores. |
| 8 | [Gateway de gerenciamento de dados](data-factory-data-management-gateway.md) | Configure um gateway dados para mover dados entre locais e na nuvem. Use o Gateway de gerenciamento de dados da fábrica de dados do Azure para mover seus dados. |
| 9 | [Mover dados de um banco de dados local Cassandra usando fábrica de dados do Azure](data-factory-onprem-cassandra-connector.md) | Saiba mais sobre como mover dados de um banco de dados local Cassandra usando fábrica de dados do Azure. |
| 10 | [Mover dados do MongoDB usando fábrica de dados do Azure](data-factory-on-premises-mongodb-connector.md) | Saiba mais sobre como mover dados do banco de dados do MongoDB usando fábrica de dados do Azure. |
| 11 | [Mover dados do Salesforce usando fábrica de dados do Azure](data-factory-salesforce-connector.md) | Saiba mais sobre como mover dados de vendas usando fábrica de dados do Azure. |


## <a name="updated-articles-data-factory"></a>Artigos atualizados, fábrica de dados

Esta seção lista artigos que foram atualizados recentemente, onde a atualização foi grande ou significativas. Para cada artigo atualizado, um trecho aproximado do texto redução adicionado é exibido. Os artigos foram atualizados dentro do intervalo de data de **2016-08-22** para **2016-10-05**.

| &nbsp; | Artigo | Texto atualizado, o trecho de código | Atualizado quando |
| --: | :-- | :-- | :-- |
| 12 | [Fábrica de dados do Azure - log de alterações de API .NET](data-factory-api-change-log.md) | Este artigo fornece informações sobre alterações SDK de fábrica de dados do Azure em uma versão específica. Você pode obter o pacote de NuGet mais recente de Azure dados fábrica aqui (https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) **versão 4.11.0** recurso adições: / os seguintes tipos de serviço vinculada foram adicionados: - OnPremisesMongoDbLinkedService (https://msdn.microsoft.com/library/mt765129.aspx) - AmazonRedshiftLinkedService (https://msdn.microsoft.com/library/mt765121.aspx) - AwsAccessKeyLinkedService (https://msdn.microsoft.com/library/mt765144.aspx) / os seguintes tipos de conjunto de dados foram adicionados: - MongoDbCollectionDataset (https://msdn.microsoft.com/library/mt765145.aspx) - AmazonS3Dataset (https://msdn.microsoft.com/library/mt765112.aspx) / os seguintes tipos de fonte de cópia foram adicionados :-MongoDbSource (https://msdn.microsoft.com/en-US/library/mt765123.aspx) **versão 4.10.0** / as seguintes propriedades opcionais foram adicionadas ao TextFormat:-esquis | 2016-09-22 |
| 13 | [Mover dados para e do Azure Blob usando fábrica de dados do Azure](data-factory-azure-blob-connector.md) |  / copyBehavior / define o comportamento de copiar quando a fonte é BlobSource ou sistema de arquivos.  / **PreserveHierarchy:** preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem a pasta de origem é idêntico ao caminho relativo do arquivo de destino para pasta de destino.. br /.. br /. **FlattenHierarchy:** todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm nome gerado automaticamente. br /.. br /. **MergeFiles: (padrão)** mescla todos os arquivos da pasta de origem para um arquivo. Se o nome de arquivo/Blob for especificado, o nome de arquivo mesclado seria o nome especificado. Caso contrário, seria o nome de arquivo gerado automaticamente.  / Não / **BlobSource** também oferece suporte a essas duas propriedades para compatibilidade com versões anteriores. / **treatEmptyAsNull**: Especifica se tratar nulo ou sequência vazia como valor nulo. / **skipHeaderLineCount** - Especifica quantas linhas precisam ser ignoradas. Ele é aplicável somente quando dataset de entrada usando o TextFormat. Da mesma forma, **BlobSink** suporta ésimo | 2016-09-28 |
| 14 | [Criar canais de previsão usando atividades de aprendizado de máquina do Azure](data-factory-azure-ml-batch-execution-activity.md) | **Serviço da Web requer várias entradas** Se o serviço da web leva várias entradas, use a propriedade **webServiceInputs** em vez de usar **webServiceInput**. Conjuntos de dados que são referenciados pela **webServiceInputs** também devem ser incluídos na atividade **entradas**. No seu experimento Azure ML, entrada de serviço da web e portas de saída e parâmetros globais têm nomes de padrão ("input1", "input2") que você pode personalizar. Os nomes que você usa para webServiceInputs, webServiceOutputs e configurações de globalParameters devem corresponder exatamente aos nomes nas experiências. Você pode exibir a carga de solicitação de exemplo na página Ajuda de execução de lote de seu ponto de extremidade do Azure ML verificar o mapeamento esperado.    {"nome": "PredictivePipeline", "propriedades": {"Descrição": "usar modelo AzureML", "atividades": {"nome": "MLActivity", "tipo": "AzureMLBatchExecution", "Descrição": "análise de previsão no lote de entrada", "entradas": {"nome": "inputDataset1"}, {"nome": "inputDatase | 2016-09-13 |
| 15 | [Copiar guia ajuste e desempenho de atividade](data-factory-copy-activity-performance.md) | 1. **estabelecer uma linha de base**. Durante a fase de desenvolvimento, teste seu pipeline usando atividade de cópia em relação a uma amostra de dados representante. Você pode usar fábrica dados divisão modelo (time-series-datasets-and-data-slices de dados-factory-agendamento-e-execution.md) para limitar a quantidade de dados que você trabalha.  Colete características de desempenho e tempo de execução usando o **monitoramento e gerenciamento de aplicativo**. Escolha **Monitor e gerenciar** na sua home page de fábrica de dados. No modo de exibição de árvore, escolha o **conjunto de dados de saída**. Na lista de **Atividade Windows** , escolha a atividade de cópia executar. **Atividade Windows** lista a duração de atividade de cópia e o tamanho dos dados que são copiados. A taxa de transferência é listada na **Atividade de uma janela do Explorer**. Para saber mais sobre o aplicativo, consulte Monitor e gerenciar canais de fábrica de dados do Azure usando o monitoramento e gerenciamento de aplicativo (dados-factory-monitor-gerenciar-app.md).  ! Executar detalhes (./media/data-factory-copy-activity-performance/mmapp-activity-run-details.pn de atividade | 2016-09-27 |
| 16 | [Tutorial: Criar um pipeline com atividade de cópia usando o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) |   Observe os seguintes pontos:-dataset **tipo** está definido como **AzureBlob**.     - **linkedServiceName** está definido como **AzureStorageLinkedService**. Você criou esse serviço vinculado na etapa 2.     - **folderPath** está definido como o contêiner de **adftutorial** . Você também pode especificar o nome de um blob dentro da pasta usando a propriedade **nome do arquivo** . Como você não está especificando o nome do blob, dados de todos os blobs no contêiner são considerados como uma entrada de dados.    -formato **tipo** está definido como **TextFormat** - existem dois campos no texto arquivo ΓÇô **nome** e **Sobrenome** ΓÇô separado por um caractere de vírgula (**columnDelimiter**) - a **disponibilidade** é definida como **por hora** (**frequência** é definido como **hora** e o **intervalo** é definido como **1**). Portanto, Data Factory procura dados de entrada cada hora na pasta raiz do contêiner de blob (**adftutorial**) que você especificou.  Se você não especificar um **nome de arquivo** de um conjunto de dados de **entrada** , todos os arquivos/blobs da pasta entrada (**folderPath**) são considerado | 2016-09-29 |
| 17 | [Criar, monitorar e gerenciar fábricas dados Azure usando o SDK do .NET de fábrica de dados](data-factory-create-data-factories-programmatically.md) | Anote a ID do aplicativo e a senha (secreta do cliente) e usá-lo no passo a passo. **Assinatura de obter Azure e locatário IDs** Se você não tiver a versão mais recente do PowerShell instalado no computador, siga as instruções em como instalar e configurar o Azure PowerShell (... / powershell-Instalação-configure.md) artigo para instalá-lo. 1. Iniciar o PowerShell do Azure e execute o seguinte comando 2. Execute o seguinte comando e insira o nome de usuário e senha que você usa para entrar no portal do Azure.         AzureRmAccount de login se que você tiver apenas uma assinatura Azure associada a essa conta, você não precisa executar as próximas duas etapas. 3. Execute o seguinte comando para exibir todas as assinaturas para essa conta.       Get-AzureRmSubscription 4. Execute o seguinte comando para selecionar a assinatura à qual você deseja trabalhar. Substitua **NameOfAzureSubscription** com o nome da sua assinatura do Azure.       Get-AzureRmSubscription - o nome de inscrição NameOfAzureSubscription / Set-AzureRmCo | 2016-09-14 |
| 18 | [Canais e atividades em fábrica de dados do Azure](data-factory-create-pipelines.md) |       "começar": "2016-07-12T00:00:00Z", "final": "2016-07-13T00:00:00Z"}} Observe os seguintes pontos: / na seção atividades, há apenas uma atividade cujo **tipo** está definido como **Copiar**. / Entrada para a atividade está definida como **InputDataset** e saída para a atividade está definida como **OutputDataset**. / Na seção **typeProperties** , **BlobSource** especificado como o tipo de fonte e **SqlSink** é especificado como o tipo de receptor. Para uma explicação completa de criar esse pipeline, consulte Tutorial: copiar dados de armazenamento de Blob do banco de dados do SQL (data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). **Pipeline de transformação de amostra** No pipeline de exemplo abaixo, há uma atividade do tipo **HDInsightHive** na seção de **atividades** . Neste exemplo, a atividade de HDInsight seção (dados-factory-seção-activity.md) transforma os dados de um armazenamento de Blob do Azure executando um arquivo de script de seção em um cluster de Azure HDInsight Hadoop.  {"nome": "TransformPipeline", "p | 2016-09-27 |
| 19 | [Transformar dados em fábrica de dados do Azure](data-factory-data-transformation-activities.md) | Fábrica de dados compatível com as seguintes atividades de transformação de dados que podem ser adicionadas a tubulações (dados-factory-criar-pipelines.md) ou individualmente ou encadeadas com outra atividade. .  AZURE. Observação para obter instruções passo a passo com instruções passo a passo, consulte Criar um pipeline com artigo de transformação (data-factory-build-your-first-pipeline.md) de seção. **Atividade de uma seção de HDInsight** A atividade de HDInsight seção em um pipeline de dados fábrica executa consultas de seção no seu próprio ou cluster de baseado no Windows/Linux HDInsight sob demanda. Consulte o artigo de (dados-factory-seção-activity.md) de atividade de seção para obter detalhes sobre esta atividade. **Atividade de HDInsight porco** A atividade de HDInsight porco em um pipeline de dados fábrica executa consultas de porco própria ou cluster de baseado no Windows/Linux HDInsight sob demanda. Consulte o artigo do porco atividade (dados-factory-porco-activity.md) para obter detalhes sobre esta atividade. **Atividade de HDInsight MapReduce** A atividade de HDInsight MapReduce em um pipeline de dados fábrica executa programas de MapReduce em y | 2016-09-26 |
| 20 | [Agendamento de fábrica de dados e a execução](data-factory-scheduling-and-execution.md) | CopyActivity2 seria executado somente se a CopyActivity1 foi executado com êxito e Dataset2 está disponível. Aqui está o pipeline de amostra JSON: {"nome": "ChainActivities", "propriedades": {"Descrição": "Executar atividades em sequência", "atividades": {"tipo": "Copiar", "typeProperties": {"fonte": {"tipo": "BlobSource"}, "receptor": {"tipo": "BlobSink", "copyBehavior": "PreserveHierarchy", "writeBatchSize": 0, "writeBatchTimeout": "00: 00:00"}}, "entradas": {"nome": "Dataset1"}, "saídas": {"nome": "Dataset2"}, "política": {"tempo limite": "01: 00:00"}, "Agendador": {"frequência": "Hora", "intervalo": 1}, "nome": "CopyFromBlob1ToBlob2", "Descrição": "Copiar dados de um blob para outro"}, {"tipo": "Copiar", "typeProperties": {"fonte": {"tipo": "BlobSource"}, "receptor" : {"tipo": "BlobSink", "writeBatchSize": 0, "writeBatchTimeout": "00: 00:00"}}, "em | 2016-09-28 |





## <a name="tutorials"></a>Tutoriais

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 21 | [Tutorial: Criar sua primeira pipeline para processar dados usando Hadoop cluster](data-factory-build-your-first-pipeline.md) | Este tutorial de fábrica de dados do Azure mostra como criar e agendar uma fábrica de dados que processa dados usando o script de seção em um cluster de Hadoop. |
| 22 | [Tutorial: Criar sua primeira fábrica de dados Azure usando o modelo do Gerenciador de recursos do Azure](data-factory-build-your-first-pipeline-using-arm.md) | Neste tutorial, você cria um pipeline de fábrica do Azure dados de amostra usando um modelo do Gerenciador de recursos do Azure. |
| 23 | [Tutorial: Criar sua primeira fábrica de dados Azure usando o portal do Azure](data-factory-build-your-first-pipeline-using-editor.md) | Neste tutorial, você cria um pipeline de fábrica do Azure dados de amostra usando o Editor de fábrica de dados no portal do Azure. |
| 24 | [Tutorial: Criar sua primeira fábrica de dados Azure usando o PowerShell do Azure](data-factory-build-your-first-pipeline-using-powershell.md) | Neste tutorial, você cria um pipeline de fábrica do Azure dados de amostra usando o PowerShell do Azure. |
| 25 | [Tutorial: Criar seu primeiro Azure dados fábrica, usando o Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) | Neste tutorial, você cria um pipeline de fábrica do Azure dados de amostra usando o Visual Studio. |
| 26 | [Tutorial: Criar um pipeline com atividade de cópia usando o portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) | Neste tutorial, você cria um pipeline de fábrica de dados do Azure com uma atividade de cópia usando o Editor de fábrica de dados no portal do Azure. |
| 27 | [Tutorial: Criar um pipeline com atividade de cópia usando o PowerShell do Azure](data-factory-copy-activity-tutorial-using-powershell.md) | Neste tutorial, você criar um pipeline de fábrica de dados do Azure com uma atividade de cópia usando o PowerShell do Azure. |
| 28 | [Tutorial: Criar um pipeline com atividade de cópia usando o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) | Neste tutorial, você cria um pipeline de fábrica de dados do Azure com uma atividade de cópia usando o Visual Studio. |
| 29 | [Copiar dados do armazenamento de Blob usando fábrica de dados do banco de dados do SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | Este tutorial mostra como usar a atividade de cópia em um pipeline de fábrica de dados do Azure para copiar dados de armazenamento de Blob do banco de dados SQL. |
| 30 | [Tutorial: Criar um pipeline com atividade de cópia usando o Assistente de cópia de fábrica de dados](data-factory-copy-data-wizard-tutorial.md) | Neste tutorial, você criar um pipeline de fábrica de dados do Azure com uma atividade de cópia usando o Assistente de cópia suportados pelo fábrica de dados |



## <a name="data-movement"></a>Movimentação de dados

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 31 | [Mover dados para e do Azure Blob usando fábrica de dados do Azure](data-factory-azure-blob-connector.md) | Saiba como copiar dados de blob em fábrica de dados do Azure. Use nosso exemplo: como copiar dados entre o armazenamento de Blob do Azure e Azure SQL Database. |
| 32 | [Mover de dados e armazenamento de Lucerne de dados do Azure usando fábrica de dados do Azure](data-factory-azure-datalake-connector.md) | Saiba como mover dados de/para armazenamento de Lucerne de dados do Azure usando fábrica de dados do Azure |
| 33 | [Mover de dados e DocumentDB usando fábrica de dados do Azure](data-factory-azure-documentdb-connector.md) | Saiba como mover dados para/da coleção DocumentDB do Azure usando fábrica de dados do Azure |
| 34 | [Mover dados para e do banco de dados do SQL Azure usando fábrica de dados do Azure](data-factory-azure-sql-connector.md) | Saiba como mover dados de/para o banco de dados do SQL Azure usando fábrica de dados do Azure. |
| 35 | [Mover de dados e depósito de dados do SQL Azure usando fábrica de dados do Azure](data-factory-azure-sql-data-warehouse-connector.md) | Saiba como mover dados de/para o depósito de dados do SQL Azure usando fábrica de dados do Azure |
| 36 | [Mover de dados e tabela de Azure usando fábrica de dados do Azure](data-factory-azure-table-connector.md) | Saiba como mover dados de/para o armazenamento de tabela do Azure usando fábrica de dados do Azure. |
| 37 | [Copiar guia ajuste e desempenho de atividade](data-factory-copy-activity-performance.md) | Saiba mais sobre os principais fatores que afetam o desempenho de movimentação de dados em fábrica de dados do Azure, quando você usa a atividade de cópia. |
| 38 | [Mover dados usando Copiar atividade](data-factory-data-movement-activities.md) | Saiba mais sobre a movimentação de dados em canais de fábrica de dados: migração de dados entre os armazenamentos de nuvem e entre um armazenamento local e um armazenamento de nuvem. Use atividade de cópia. |
| 39 | [Notas de versão do Gateway de gerenciamento de dados](data-factory-gateway-release-notes.md) | Notas de versão do tório de Gateway de gerenciamento de dados |
| 40 | [Mover dados do local HDFS usando fábrica de dados do Azure](data-factory-hdfs-connector.md) | Saiba mais sobre como mover dados de HDFS locais usando o Azure dados fábrica. |
| 41 | [Monitorar e gerenciar o pipeline de fábrica de dados do Azure usando o novo monitoramento e gerenciamento de aplicativo](data-factory-monitor-manage-app.md) | Saiba como usar o monitoramento e gerenciamento de aplicativo para monitorar e gerenciar canais e fábricas de dados do Azure. |
| 42 | [Mover dados entre fontes locais e na nuvem com o Gateway de gerenciamento de dados](data-factory-move-data-between-onprem-and-cloud.md) | Configure um gateway dados para mover dados entre locais e na nuvem. Use o Gateway de gerenciamento de dados da fábrica de dados do Azure para mover seus dados. |
| 43 | [Mover dados do OData uma origem usando fábrica de dados do Azure](data-factory-odata-connector.md) | Saiba mais sobre como mover dados de fontes de OData usando fábrica de dados do Azure. |
| 44 | [Mover armazenamentos de dados ODBC de dados usando fábrica de dados do Azure](data-factory-odbc-connector.md) | Saiba mais sobre como mover dados de fontes de dados ODBC usando fábrica de dados do Azure. |
| 45 | [Mover os dados de DB2 usando fábrica de dados do Azure](data-factory-onprem-db2-connector.md) | Saiba mais sobre como mover dados do banco de dados do DB2 usando fábrica de dados do Azure |
| 46 | [Mover dados de e para um sistema de arquivos local usando fábrica de dados do Azure](data-factory-onprem-file-system-connector.md) | Saiba como mover dados de e para um sistema de arquivos local usando fábrica de dados do Azure. |
| 47 | [Mover dados do MySQL usando fábrica de dados do Azure](data-factory-onprem-mysql-connector.md) | Saiba mais sobre como mover dados do banco de dados MySQL usando fábrica de dados do Azure. |
| 48 | [Mover dados de/para Oracle de local usando fábrica de dados do Azure](data-factory-onprem-oracle-connector.md) | Saiba como mover dados de/para o banco de dados Oracle que está usando o Azure dados fábrica ao local. |
| 49 | [Mover dados do PostgreSQL usando fábrica de dados do Azure](data-factory-onprem-postgresql-connector.md) | Saiba mais sobre como mover dados do banco de dados PostgreSQL usando fábrica de dados do Azure. |
| 50 | [Mover dados do Sybase usando fábrica de dados do Azure](data-factory-onprem-sybase-connector.md) | Saiba mais sobre como mover dados do banco de dados Sybase usando fábrica de dados do Azure. |
| 51 | [Mover dados do Teradata usando fábrica de dados do Azure](data-factory-onprem-teradata-connector.md) | Saiba mais sobre Teradata conector para o serviço de dados fábrica que permite mover dados do banco de dados Teradata |
| 52 | [Mover os dados para e do SQL Server local ou na IaaS (Azure máquina virtual) usando fábrica de dados do Azure](data-factory-sqlserver-connector.md) | Saiba mais sobre como mover dados de/para o banco de dados do SQL Server local ou em uma VM Azure usando fábrica de dados do Azure. |
| 53 | [Mover dados de uma fonte de tabela da Web usando fábrica de dados do Azure](data-factory-web-table-connector.md) | Saiba mais sobre como mover dados do local uma tabela em uma página da Web usando fábrica de dados do Azure. |



## <a name="data-transformation"></a>Transformação de dados

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 54 | [Criar canais de previsão usando atividades de aprendizado de máquina do Azure](data-factory-azure-ml-batch-execution-activity.md) | Descreve como criar criar canais de previsão usando fábrica de dados do Azure e aprendizado de máquina do Azure |
| 55 | [Calcular serviços vinculados](data-factory-compute-linked-services.md) | Aprenda sobre computação enviornments que você pode usar em canais de fábrica de dados do Azure para transformação/processar dados. |
| 56 | [Processos conjuntos de dados em grande escala usando fábrica de dados e em lotes](data-factory-data-processing-using-batch.md) | Descreve como processar grandes quantidades de dados em um pipeline de fábrica de dados do Azure usando o recurso de processamento paralelo do lote do Azure. |
| 57 | [Transformar dados em fábrica de dados do Azure](data-factory-data-transformation-activities.md) | Saiba como transformar dados ou dados de processo de fábrica de dados do Azure usando Hadoop, aprendizado de máquina ou Azure dados Lucerne Analytics. |
| 58 | [Atividade de Streaming de Hadoop](data-factory-hadoop-streaming-activity.md) | Saiba como você pode usar a atividade de Streaming de Hadoop em uma fábrica de dados do Azure para executar programas Hadoop Streaming em um cluster de HDInsight próprio em – demanda/sua. |
| 59 | [Atividade de seção](data-factory-hive-activity.md) | Saiba como você pode usar a atividade de seção em uma fábrica de dados do Azure para executar consultas de seção em um cluster de HDInsight próprio em – demanda/sua. |
| 60 | [Chamar MapReduce programas da fábrica de dados](data-factory-map-reduce.md) | Saiba como processar dados executando programas de MapReduce em um cluster de Azurehdinsight de uma fábrica de dados do Azure. |
| 61 | [Atividade de porco](data-factory-pig-activity.md) | Saiba como você pode usar a atividade de porco em uma fábrica de dados do Azure para executar scripts de porco em um cluster de HDInsight próprio em – demanda/sua. |
| 62 | [Chamar Spark programas da fábrica de dados](data-factory-spark.md) | Aprenda a chamar Spark programas de uma fábrica de dados Azure usando a atividade de MapReduce. |
| 63 | [Atividade de procedimento armazenado do SQL Server](data-factory-stored-proc-activity.md) | Saiba como você pode usar a atividade de procedimento armazenado do SQL Server para chamar um procedimento armazenado em um banco de dados do Azure SQL ou um depósito de dados do SQL Azure de um pipeline de fábrica de dados. |
| 64 | [Usar atividades personalizadas em um pipeline de fábrica de dados do Azure](data-factory-use-custom-activities.md) | Aprenda a criar atividades personalizadas e usá-los em um pipeline de fábrica de dados do Azure. |
| 65 | [Executar o script U-SQL no Azure dados Lucerne análise da fábrica de dados do Azure](data-factory-usql-activity.md) | Saiba como processar dados executando scripts U-SQL no serviço de computação de análise de Lucerne de dados do Azure. |



## <a name="samples"></a>Amostras

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 66 | [Dados Azure Factory - exemplos](data-factory-samples.md) | Fornece detalhes sobre exemplos que vêm com o serviço de fábrica de dados do Azure. |



## <a name="use-cases"></a>Casos de uso

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 67 | [Estudos de caso de cliente](data-factory-customer-case-studies.md) | Saiba mais sobre como alguns de nossos clientes estavam usando fábrica de dados do Azure. |
| 68 | [Usar caso - perfil de cliente](data-factory-customer-profiling-usecase.md) | Saiba como fábrica de dados do Azure é usada para criar um orientadas a dados fluxo de trabalho (pipeline) para clientes de jogos do perfil. |
| 69 | [Use maiusculas e minúsculas - recomendações de produto](data-factory-product-reco-usecase.md) | Saiba mais sobre um caso de uso implementado usando fábrica de dados do Azure juntamente com outros serviços. |



## <a name="monitor-and-manage"></a>Monitorar e gerenciar

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 70 | [Monitorar e gerenciar o pipeline de fábrica de dados do Azure](data-factory-monitor-manage-pipelines.md) | Saiba como usar o Portal do Azure e PowerShell do Azure para monitorar e gerenciar fábricas de dados do Azure e canais que você criou. |



## <a name="sdk"></a>SDK

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 71 | [Fábrica de dados do Azure - log de alterações de API .NET](data-factory-api-change-log.md) | Descreve alterações significativas, inclusões de recurso, correções de bugs etc... em uma versão específica de API do .NET para a fábrica de dados do Azure. |
| 72 | [Criar, monitorar e gerenciar fábricas dados Azure usando o SDK do .NET de fábrica de dados](data-factory-create-data-factories-programmatically.md) | Aprenda a criar, monitorar e gerenciar fábricas de dados Azure usando dados fábrica SDK programaticamente. |
| 73 | [Referência do desenvolvedor do Azure dados fábrica](data-factory-sdks.md) | Saiba mais sobre as diferentes maneiras de criar, monitorar e gerenciar fábricas de dados do Azure |



## <a name="miscellaneous"></a>Diversos

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 74 | [Dados Azure Factory - perguntas frequentes](data-factory-faq.md) | Perguntas frequentes sobre fábrica de dados do Azure. |
| 75 | [Dados Azure Factory - funções e variáveis de sistema](data-factory-functions-variables.md) | Fornece uma lista de funções de fábrica de dados do Azure e variáveis de sistema |
| 76 | [Dados Azure Factory - as regras de nomenclatura](data-factory-naming-rules.md) | Descreve as regras de nomenclatura para entidades de fábrica de dados. |
| 77 | [Solucionar problemas de fábrica de dados](data-factory-troubleshoot.md) | Saiba como solucionar problemas com o uso de fábrica de dados do Azure. |

