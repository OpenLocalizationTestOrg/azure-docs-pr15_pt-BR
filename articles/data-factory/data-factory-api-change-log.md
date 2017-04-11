<properties 
    pageTitle="Dados Factory - Log de alterações de API do .NET | Microsoft Azure" 
    description="Descreve alterações significativas, inclusões de recurso, correções de bugs etc... em uma versão específica de API do .NET para a fábrica de dados do Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="spelluru"/>

# <a name="azure-data-factory---net-api-change-log"></a>Fábrica de dados do Azure - log de alterações de API .NET 
Este artigo fornece informações sobre alterações SDK de fábrica de dados do Azure em uma versão específica. Você pode obter o pacote de NuGet mais recente de fábrica de dados do Azure [aqui](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) 

## <a name="version-4110"></a>Versão 4.11.0
Adições de recursos:

- Os seguintes tipos de serviço vinculada foram adicionados:
    - [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
    - [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
    - [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
- Os seguintes tipos de conjunto de dados foram adicionados: 
    - [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
    - [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
- Os seguintes tipos de fonte de cópia foram adicionados:
    - [MongoDbSource](https://msdn.microsoft.com/en-US/library/mt765123.aspx)

## <a name="version-4100"></a>Versão 4.10.0
- As seguintes propriedades opcionais foram adicionadas ao TextFormat:
    - [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
    - [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
    - [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
- Os seguintes tipos de serviço vinculada foram adicionados:
    - [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
    - [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
- Os seguintes tipos de conjunto de dados foram adicionados:
    - [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
- Os seguintes tipos de fonte de cópia foram adicionados:
    - [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
- Adicionar propriedade de [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) a AzureMLBatchExecutionActivity 
    - Habilitar passando web várias entradas de serviço para uma experiência de aprendizado de máquina do Azure


## <a name="version-491"></a>Versão 4.9.1

### <a name="bug-fix"></a>Correção de bugs

- Reprovar autenticação baseada em WebApi para [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Versão 4.9.0

### <a name="feature-additions"></a>Adições de recursos

- Adicione propriedades [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) e [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) a CopyActivity. Consulte [Copiar em estágios](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre o recurso.


### <a name="bug-fix"></a>Correção de bugs

- Apresente uma sobrecarga do método de [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) , que usa uma instância de [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) .
- Marca [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) e [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) como opcional em CopySink.

## <a name="version-480"></a>Versão 4.8.0

### <a name="feature-additions"></a>Adições de recursos
- As seguintes propriedades opcionais foram adicionadas ao tipo de atividade de cópia habilitar o ajuste de desempenho de cópia:
    - [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
    - [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Versão 4.7.0

### <a name="feature-additions"></a>Adições de recursos
* Adicionados novos StorageFormat tipo [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) para copiar arquivos no formato do linha otimizado coluna (ORC).
* Adicione propriedades [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) e PolyBaseSettings a SqlDWSink.
    * Habilita o uso de PolyBase para copiar dados para o SQL Data Warehouse.

## <a name="version-461"></a>Versão 4.6.1

### <a name="bug-fixes"></a>Correções de bugs
* Corrige a solicitação HTTP para listar windows de atividade.
    * Remove a carga de solicitação o nome do grupo de recursos e o nome de fábrica de dados.

## <a name="version-460"></a>Versão 4.6.0

### <a name="feature-additions"></a>Adições de recursos

- As seguintes propriedades foram adicionadas ao [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
    - [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
    - [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
    - [Conjuntos de dados](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
- As seguintes propriedades foram adicionadas ao [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
    - [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
- Novo adicionado [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) digite o tipo de [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) para definir conjuntos de dados cujos dados estão no formato JSON. 

## <a name="version-450"></a>Versão 4.5.0

### <a name="feature-additions"></a>Adições de recursos
* [Operações de lista para a janela de atividade](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx)de adicionados.
    * Métodos adicionados para recuperar windows de atividade com filtros baseados nos tipos de entidade (ou seja, fábricas de dados, conjuntos de dados, canais e atividades).
* Os seguintes tipos de serviço vinculada foram adicionados: 
    * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Os seguintes tipos de conjunto de dados foram adicionados: 
    * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Os seguintes tipos de fonte de cópia foram adicionados:  
    * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Versão 4.4.0

### <a name="feature-additions"></a>Adições de recursos

- O seguinte tipo de serviço vinculada foi adicionado como fontes de dados e recpetores para atividades de cópia:
    - [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Consulte [Serviço vinculada de SAS de armazenamento do Azure](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) para obter exemplos e informações conceituais. 

## <a name="version-430"></a>Versão 4.3.0

### <a name="feature-additions"></a>Adições de recursos

- O seguintes refúgio de tipos de serviço vinculada foi adicionado como fontes de dados para atividades de cópia:
    - [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Para obter exemplos e informações conceituais, consulte [mover dados do HDFS usando dados fábrica](data-factory-hdfs-connector.md) . 
    - [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Para obter exemplos e informações conceituais, consulte [mover dados do ODBC armazena usando fábrica de dados do Azure](data-factory-odbc-connector.md) . 

## <a name="version-420"></a>Versão 4.2.0

### <a name="feature-additions"></a>Adições de recursos

- O novo tipo de atividade a seguir foi adicionado: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Para obter detalhes sobre a atividade, consulte [modelos de Atualizando Azure ML usando a atividade de recursos de atualização](data-factory-azure-ml-batch-execution-activity.md#updating-azure-ml-models-using-the-update-resource-activity).
- Um novo de propriedade opcional [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) foi adicionado à [classe AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx). 
- Propriedades [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) e [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) foram adicionadas à classe [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) . 
- Permitir a configuração dos tempos limite para chamadas de cliente para o serviço de dados fábrica. 


## <a name="version-410"></a>Versão versão 4.1.0

### <a name="feature-additions"></a>Adições de recursos
* Os seguintes tipos de serviço vinculada foram adicionados: 
    * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
    * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Os seguintes tipos de atividade foram adicionados: 
    * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Os seguintes tipos de conjunto de dados foram adicionados: 
    * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Os seguintes tipos de origem e receptor para atividade de cópia foram adicionados:
    * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
    * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)


## <a name="version-401"></a>Versão 4.0.1

### <a name="breaking-changes"></a>Alterações significativas
As classes a seguir foram renomeadas. Os novos nomes estavam os nomes originais de classes antes de liberar 4.0.0. 
 
Nome na 4.0.0 | Nome na 4.0.1
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## <a name="version-400"></a>Versão 4.0.0

### <a name="breaking-changes"></a>Alterações significativas



- As seguintes classes/interfaces foram renomeadas.

| Nome antigo | Novo nome |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| Tabela | [Conjunto de dados](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    

- Os métodos de **lista** retornam resultados pagináveis agora. Se a resposta contém uma propriedade de **NextLink** não vazio, o aplicativo cliente deve continuar a busca a próxima página até que todas as páginas são retornadas.  Aqui está um exemplo: 

        PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
        var pipelines = new List<Pipeline>(response.Pipelines);
    
        string nextLink = response.NextLink;
        while (string.IsNullOrEmpty(response.NextLink))
        {
            PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
            pipelines.AddRange(nextResponse.Pipelines);
    
            nextLink = nextResponse.NextLink;
        }
    
- Pipeline de **lista** API retorna apenas o resumo de um pipeline em vez de detalhes completos. Por exemplo, atividades em um resumo de pipeline só contenham nome e tipo.

### <a name="feature-additions"></a>Adições de recursos
- A classe [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) suporta duas novas propriedades, **SliceIdentifierColumnName** e **SqlWriterCleanupScript**, para oferecer suporte cópia idempotente depósito de dados do SQL Azure. Consulte o artigo de [Depósito de dados do SQL Azure](data-factory-azure-sql-data-warehouse-connector.md) , especificamente, o [mecanismo 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) e o [mecanismo 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2) seções, para obter detalhes sobre essas propriedades.

- Agora, oferecemos suporte executando procedimento armazenado em fontes Azure SQL Database e depósito de dados do SQL Azure como parte da atividade de cópia. As classes [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) e [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) têm as seguintes propriedades: **SqlReaderStoredProcedureName** e **StoredProcedureParameters**. Consulte os artigos de [Banco de dados do SQL Azure](data-factory-azure-sql-connector.md#sqlsource) e [Depósito de dados do SQL Azure](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) em Azure.com para obter detalhes sobre essas propriedades.  