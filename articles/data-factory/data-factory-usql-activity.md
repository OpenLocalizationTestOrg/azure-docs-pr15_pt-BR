<properties 
    pageTitle="Executar o script U-SQL no Azure dados Lucerne análise da fábrica de dados do Azure" 
    description="Saiba como processar dados executando scripts U-SQL no serviço de computação de análise de Lucerne de dados do Azure." 
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
    ms.date="09/12/2016" 
    ms.author="spelluru"/>

# <a name="run-u-sql-script-on-azure-data-lake-analytics-from-azure-data-factory"></a>Executar o script U-SQL no Azure dados Lucerne análise da fábrica de dados do Azure
> [AZURE.SELECTOR]
[Seção](data-factory-hive-activity.md)  
[Porco](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Fluxo de Hadoop](data-factory-hadoop-streaming-activity.md)
[Aprendizado de máquina](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimento armazenado](data-factory-stored-proc-activity.md)
[Dados Lucerne Analytics U-SQL](data-factory-usql-activity.md)
[.NET personalizado](data-factory-use-custom-activities.md)
 
Um pipeline em uma fábrica Azure dados processa dados nos serviços do armazenamento vinculado usando os serviços de computação vinculadas. Ele contém uma sequência de atividades onde cada atividade executa uma operação de processamento específico. Este artigo descreve a **Atividade de U-SQL de análise de dados Lucerne** que executa um script de **U-SQL** em um serviço de computação vinculado **Azure dados Lucerne Analytics** . 

> [AZURE.NOTE] 
> Crie uma conta do Azure dados Lucerne Analytics antes de criar um pipeline com uma atividade de U-SQL dados Lucerne Analytics. Para saber mais sobre a análise de Lucerne de dados do Azure, consulte [Introdução ao Azure dados Lucerne Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
>  
> Examine a [criar seu primeiro tutorial de pipeline](data-factory-build-your-first-pipeline.md) para obter etapas detalhadas para criar uma fábrica de dados, serviços vinculados, conjuntos de dados e um pipeline. Use trechos JSON com Editor de fábrica de dados ou Visual Studio ou Azure PowerShell para criar entidades de fábrica de dados.

## <a name="azure-data-lake-analytics-linked-service"></a>A análise de dados Azure Lucerne vinculado serviço
Criar um serviço de **Análise de Lucerne do Azure dados** vinculados para vincular a um serviço de computação Azure dados Lucerne Analytics a uma fábrica dados Azure. A atividade de dados Lucerne Analytics U-SQL no pipeline se refere a esse serviço vinculado. 

O exemplo a seguir fornece a definição de JSON para um serviço de análise de Lucerne do Azure dados vinculados. 

    {
        "name": "AzureDataLakeAnalyticsLinkedService",
        "properties": {
            "type": "AzureDataLakeAnalytics",
            "typeProperties": {
                "accountName": "adftestaccount",
                "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
                "authorization": "<authcode>",
                "sessionId": "<session ID>", 
                "subscriptionId": "<subscription id>",
                "resourceGroupName": "<resource group name>"
            }
        }
    }


A tabela a seguir fornece descrições das propriedades usada na definição JSON. 

Propriedade | Descrição | Necessário
-------- | ----------- | --------
Tipo | A propriedade de tipo deve ser definida como: **AzureDataLakeAnalytics**. | Sim
accountName | Nome da conta do Azure dados Lucerne Analytics. | Sim
dataLakeAnalyticsUri | URI de Lucerne análise de dados do Azure. |  Não 
autorização | Código de autorização é automaticamente recuperado depois clicando no botão **Autorizar** no Editor de fábrica de dados e Concluindo login OAuth.  | Sim 
subscriptionId | Id da assinatura Azure | Não (se não especificado, assinatura da fábrica dados é usada). 
resourceGroupName | Nome do grupo de recursos do Azure |  Não (se não especificado, grupo de recursos da fábrica dados é usado).
identificação de sessão | id da sessão da sessão de autorização de OAuth. Cada id de sessão é exclusiva e pode ser usado somente uma vez. A sessão que ID é gerado automaticamente no Editor de fábrica de dados. | Sim

O código de autorização gerado usando o botão **Autorizar** expira depois de um dia. Consulte a tabela a seguir para os tempos de expiração para diferentes tipos de contas de usuário. Você pode ver o seguinte erro mensagem quando a autenticação **token expira**: erro de operação de credencial: invalid_grant - AADSTS70002: erro ao validar credenciais. AADSTS70008: A concessão de acesso fornecido expirou ou foi revogada. ID de rastreamento: ID de correlação d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 carimbo de hora: 2015-12-15 21:09:31Z

 
| Tipo de usuário | Expira após |
| :-------- | :----------- | 
| Contas de usuário não gerenciadas pelo Active Directory do Azure (@hotmail.com, @live.com, etc.) | 12 horas |
| Contas de usuários gerenciadas pelo Azure Active Directory (AAD) | 14 dias após a última fatia executar. <br/><br/>90 dias, se uma fatia com base em serviço vinculado baseado em OAuth é executado pelo menos uma vez a cada 14 dias. |

Para evitar/resolver esse erro, autorize de novo usando o **Autorizar** botão quando o **token expira** e reimplantação o serviço vinculada. Você também pode gerar valores para as propriedades de **identificação de sessão** e **autorização** por programação usando o código na seção a seguir. 

  
### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Para gerar programaticamente valores de ID de sessão e autorização 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Consulte os tópicos [Classes AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)e [AuthorizationSessionGetResponse classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) para obter detalhes sobre as classes de fábrica de dados usadas no código. Adicionar uma referência a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll da classe WindowsFormsWebAuthenticationDialog. 
 
 
## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de U-SQL Lucerne a análise de dados 

O seguinte trecho JSON define um pipeline com uma atividade de U-SQL dados Lucerne Analytics. A definição de atividade tem uma referência para o serviço do Azure dados Lucerne Analytics vinculado que você criou anteriormente.   
  

    {
        "name": "ComputeEventsByRegionPipeline",
        "properties": {
            "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
            "activities": 
            [
                {
                    "type": "DataLakeAnalyticsU-SQL",
                    "typeProperties": {
                        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                        "scriptLinkedService": "StorageLinkedService",
                        "degreeOfParallelism": 3,
                        "priority": 100,
                        "parameters": {
                            "in": "/datalake/input/SearchLog.tsv",
                            "out": "/datalake/output/Result.tsv"
                        }
                    },
                    "inputs": [
                        {
                            "name": "DataLakeTable"
                        }
                    ],
                    "outputs": 
                    [
                        {
                            "name": "EventsByRegionTable"
                        }
                    ],
                    "policy": {
                        "timeout": "06:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "EventsByRegion",
                    "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
                }
            ],
            "start": "2015-08-08T00:00:00Z",
            "end": "2015-08-08T01:00:00Z",
            "isPaused": false
        }
    }


A tabela a seguir descreve os nomes e descrições de propriedades que são específicas a esta atividade. 

Propriedade | Descrição | Necessário
:-------- | :----------- | :--------
tipo | A propriedade type deve ser definida para **DataLakeAnalyticsU-SQL**. | Sim
scriptPath | Caminho para a pasta que contém o script U-SQL. Nome do arquivo diferencia maiusculas de minúsculas. | Não (se você usa o script)
scriptLinkedService | Serviço vinculado que vincule o armazenamento que contém o script para a fábrica de dados | Não (se você usa o script)
script | Especifica script embutido em vez de especificando scriptPath e scriptLinkedService. Por exemplo: "script": "Teste criar banco de dados". | Não (se você usar scriptPath e scriptLinkedService)
degreeOfParallelism | O número máximo de nós simultaneamente usados para executar o trabalho. | Não
prioridade | Determina quais trabalhos fora tudo o que são enfileirados devem estar selecionados para executar primeiro. Quanto menor o número, maior a prioridade. | Não 
parâmetros | Parâmetros do script U-SQL | Não 

Consulte a [Definição de Script SearchLogProcessing.txt](#script-definition) para a definição de script. 

## <a name="sample-input-and-output-datasets"></a>Amostra de entrada e saída conjuntos de dados

### <a name="input-dataset"></a>Dataset de entrada
Neste exemplo, os dados de entrada residem em uma loja de Lucerne de dados do Azure (arquivo SearchLog.tsv na pasta datalake/entrada). 

    {
        "name": "DataLakeTable",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/input/",
                "fileName": "SearchLog.tsv",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }   

### <a name="output-dataset"></a>Conjunto de dados de saída
Neste exemplo, os dados de saída produzidos pelo script U-SQL estão armazenados em um armazenamento de Lucerne de dados do Azure (datalake/saída pasta). 

    {
        "name": "EventsByRegionTable",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/output/"
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="sample-data-lake-store-linked-service"></a>Armazenamento de Lucerne de dados de amostra vinculado serviço
Aqui está a definição da amostra que Azure dados Lucerne Store vinculado serviço usado pelos conjuntos de dados de entrada/saída. 

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

Consulte o artigo [mover dados para e do repositório de Lucerne de dados do Azure](data-factory-azure-datalake-connector.md) para obter descrições das propriedades JSON. 

## <a name="sample-u-sql-script"></a>Exemplo de Script de U-SQL 

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv(nullEscape:"#NULL#");
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start <= DateTime.Parse("2012/02/19");
    
    OUTPUT @rs1   
        TO @out
          USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

Os valores para **@in** e **@out** parâmetros no script U-SQL são passados dinamicamente por AAD usando a seção 'parâmetros'. Consulte a seção 'parâmetros' na definição do pipeline.

Você pode especificar outras propriedades como degreeOfParallelism e prioridade também em sua definição de pipeline para os trabalhos que executam o serviço de análise de Lucerne de dados do Azure.

## <a name="dynamic-parameters"></a>Parâmetros dinâmicos
A definição de pipeline de exemplo, e reduzir parâmetros recebe com valores embutidos. 

    "parameters": {
        "in": "/datalake/input/SearchLog.tsv",
        "out": "/datalake/output/Result.tsv"
    }

É possível usar parâmetros dinâmicos em vez disso. Por exemplo: 

    "parameters": {
        "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
        "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
    }

Nesse caso, arquivos de entrada ainda são recebidos da pasta /datalake/input e arquivos de saída são gerados na pasta /datalake/output. Os nomes de arquivo são dinâmicos com base na hora de início de fatia.  