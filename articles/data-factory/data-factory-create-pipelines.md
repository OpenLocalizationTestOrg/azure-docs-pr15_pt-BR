<properties 
    pageTitle="Criar/agenda canais, a cadeia de atividades em fábrica de dados | Microsoft Azure" 
    description="Saiba como criar um pipeline de dados em fábrica de dados do Azure para mover e transformar dados. Crie um fluxo de trabalho orientados a dados para produzir pronta para usar as informações." 
    keywords="pipeline de dados, dados controlado pelo fluxo de trabalho"
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article"
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="pipelines-and-activities-in-azure-data-factory"></a>Canais e atividades em fábrica de dados do Azure
Este artigo ajuda você a compreender canais e atividades em fábrica de dados do Azure e usá-los para construir ponta a ponta fluxos de trabalho orientados a dados para a movimentação de dados e cenários de processamento de dados.  

> [AZURE.NOTE] Este artigo pressupõe que você passou entre [Introdução ao Azure dados Factory](data-factory-introduction.md). Se você não tiver práticos-on-experiência com a criação de dados fábricas, indo tutorial de [criar sua primeira fábrica de dados](data-factory-build-your-first-pipeline.md) ajuda você entender este artigo melhor.  

## <a name="what-is-a-data-pipeline"></a>O que é um pipeline de dados?
**Pipeline** é um agrupamento de **atividades**de logicamente relacionadas. Ele é usado para atividades de grupo em uma unidade que executa uma tarefa. Para compreender melhor canais, você precisa compreender uma atividade primeiro. 

## <a name="what-is-an-activity"></a>O que é uma atividade?
Atividades definem as ações a serem executadas em seus dados. Cada atividade leva zero ou mais [conjuntos de dados](data-factory-create-datasets.md) como entradas e produz um ou mais conjuntos de dados como saída. 

Por exemplo, você pode usar uma atividade de cópia para coordenar copiando dados de armazenamento de dados para outro armazenamento de dados. Da mesma forma, você pode usar uma atividade de HDInsight seção para executar uma consulta de seção em um cluster de Azurehdinsight para transformar os dados. Azure Data Factory fornece uma ampla variedade de [transformação de dados](data-factory-data-transformation-activities.md)e atividades de [movimentação de dados](data-factory-data-movement-activities.md) . Você também pode optar por criar uma atividade personalizada do .NET para executar seu próprio código. 

## <a name="sample-copy-pipeline"></a>Pipeline de cópia de amostra
No pipeline de exemplo abaixo, há uma atividade do tipo **cópia** da seção de **atividades** . Neste exemplo, a [atividade de copiar](data-factory-data-movement-activities.md) copia dados de um armazenamento de Blob do Azure um banco de dados do SQL Azure. 

    {
      "name": "CopyPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2016-07-12T00:00:00Z",
        "end": "2016-07-13T00:00:00Z"
      }
    } 

Observe os seguintes pontos:

- Na seção atividades, há apenas uma atividade cujo **tipo** está definido como **Copiar**.
- Entrada da atividade está definida como **InputDataset** e saída para a atividade está definida como **OutputDataset**.
- Na seção **typeProperties** , **BlobSource** especificado como o tipo de fonte e **SqlSink** é especificado como o tipo de receptor.

Para uma explicação completa de criar esse pipeline, consulte [Tutorial: copiar dados de armazenamento de Blob do banco de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

## <a name="sample-transformation-pipeline"></a>Pipeline de transformação de amostra
No pipeline de exemplo abaixo, há uma atividade do tipo **HDInsightHive** na seção de **atividades** . Neste exemplo, a [atividade de HDInsight seção](data-factory-hive-activity.md) transforma os dados de um armazenamento de Blob do Azure executando um arquivo de script de seção em um cluster de Azure HDInsight Hadoop. 

    {
        "name": "TransformPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }

Observe os seguintes pontos: 

- Na seção atividades, há apenas uma atividade cujo **tipo** está definido como **HDInsightHive**.
- O arquivo de script da seção, **partitionweblogs.hql**, está armazenado na conta de armazenamento do Azure (especificado por scriptLinkedService, chamado **AzureStorageLinkedService**) e na pasta de **script** no contêiner **adfgetstarted**.
- A seção **define** é usada para especificar as configurações de tempo de execução que são passadas para o script de seção como valores de configuração de seção (por exemplo ${hiveconf: inputtable}, ${hiveconf:partitionedtable}).

Para uma explicação completa de criar esse pipeline, consulte [Tutorial: criar sua primeira pipeline para processar dados usando cluster Hadoop](data-factory-build-your-first-pipeline.md). 

## <a name="chaining-activities"></a>Atividades de encadeamento
Se você tiver várias atividades em um pipeline e saída de uma atividade não é uma entrada de outra atividade, as atividades podem executar em paralelo se fatias de dados de entrada para as atividades estiver prontas. 

Você pode cadeia duas atividades fazendo com que o conjunto de dados de saída de uma atividade como do dataset de entrada da outra atividade. As atividades podem estar no mesmo pipeline ou em canais diferentes. A segunda atividade executa somente quando primeiro for concluída com êxito. 

Por exemplo, considere o seguinte:
 
1.  Pipeline P1 tem A1 atividade que requer dataset de entrada externo D1 e produzir **saída** dataset **D2**.
2.  Pipeline P2 tem A2 de atividade que requer **entrada** do dataset **D2**e produz saída dataset D3.
 
Neste cenário, a atividade A1 é executado quando os dados externos estão disponíveis e a frequência de disponibilidade do agendamento é alcançada.  A atividade A2 é executada quando as fatias agendadas de D2 se tornam disponíveis e a frequência de disponibilidade do agendamento é alcançada. Se houver um erro em uma das fatias em dataset D2, A2 não são executadas para essa fatia até que ele fique disponível.

Modo de exibição de diagrama:

![Atividades do encadeamento dois canais](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Modo de exibição de diagrama com ambas as atividades no mesmo pipeline: 

![Atividades de encadeamento no mesmo pipeline](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

Para obter mais informações, consulte [programação e a execução](#chaining-activities). 

## <a name="scheduling-and-execution"></a>Planejamento e à execução
Até você ter compreendido o que são canais e atividades. Você também tiver procurou como eles são definidos e um alto nível exibição das atividades em fábrica de dados do Azure. Agora vamos Observe como eles são executados. 

Um pipeline está ativo somente entre seu horário de início e a hora de término. Ele não é executado antes da hora de início ou após o horário de término. Se o pipeline for pausado, ele não é executado independentemente sua hora de início e término. Para um pipeline executar, ele deve não estar pausado. Na verdade, não é o pipeline que seja executado. É as atividades no pipeline que são executadas. No entanto eles fazer isso no contexto geral do pipeline. 

Consulte [programação e execução](data-factory-scheduling-and-execution.md) para entender como o agendamento e a execução funciona em fábrica de dados do Azure.

## <a name="create-pipelines"></a>Criar canais
Azure Data Factory fornece vários mecanismos para criar e implantar canais (que por sua vez contêm uma ou mais atividades nele). 

### <a name="using-azure-portal"></a>Usando o portal do Azure
Você pode usar o editor de fábrica de dados no portal do Azure para criar um pipeline. Consulte [Introdução ao Azure Data Factory (Editor de fábrica de dados)](data-factory-build-your-first-pipeline-using-editor.md) para uma explicação de ponta a ponta. 

### <a name="using-visual-studio"></a>Usando o Visual Studio 
Você pode usar Visual Studio para criar e implantar canais em fábrica de dados do Azure. Consulte [Introdução ao Azure Data Factory (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) para uma explicação de ponta a ponta. 

### <a name="using-azure-powershell"></a>Usando o PowerShell Azure
Você pode usar o PowerShell do Azure para criar canais em fábrica de dados do Azure. Dizer que você definiu o pipeline de JSON em um arquivo no c:\DPWikisample.json. Você poderá carregá-lo à sua instância de fábrica de dados do Azure, conforme mostrado no exemplo a seguir:

    New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

Consulte [Introdução ao Azure Data Factory (Azure PowerShell)](data-factory-build-your-first-pipeline-using-powershell.md) para uma explicação de ponta a ponta para a criação de uma fábrica de dados com um pipeline. 

### <a name="using-net-sdk"></a>Usando o SDK do .NET
Você pode criar e implantar o pipeline via .NET SDK muito. Esse mecanismo pode ser usado para criar canais programaticamente. Para obter mais informações, consulte [criar, gerenciar e monitorar fábricas de dados por programação](data-factory-create-data-factories-programmatically.md). 


### <a name="using-azure-resource-manager-template"></a>Usando o modelo do Gerenciador de recursos do Azure
Você pode criar e implantar o pipeline usando um modelo do Gerenciador de recursos do Azure. Para obter mais informações, consulte [Introdução ao Azure Data Factory (Azure Resource Manager)](data-factory-build-your-first-pipeline-using-arm.md). 

### <a name="using-rest-api"></a>Usando a API REST
Você pode criar e implantar o pipeline usando APIs REST muito. Esse mecanismo pode ser usado para criar canais programaticamente. Para obter mais informações, consulte [criar ou atualizar um Pipeline](https://msdn.microsoft.com/library/azure/dn906741.aspx). 


## <a name="monitor-and-manage-pipelines"></a>Monitorar e gerenciar canais  
Quando um pipeline é implantado, você pode gerenciar e monitorar seus canais, fatias e é executado. Leia mais sobre ele aqui: [Monitor e gerenciar canais](data-factory-monitor-manage-pipelines.md).


## <a name="pipeline-json"></a>Pipeline JSON   
Vamos dar uma olhada mais atenta na como um pipeline é definido no formato JSON. A estrutura genérica para um pipeline tem a seguinte aparência:

    {
        "name": "PipelineName",
        "properties": 
        {
            "description" : "pipeline description",
            "activities":
            [
    
            ],
            "start": "<start date-time>",
            "end": "<end date-time>"
        }
    }

A seção **atividades** pode ter uma ou mais atividades definidas dentro dele. Cada atividade tem a seguinte estrutura de nível superior:

    {
        "name": "ActivityName",
        "description": "description", 
        "type": "<ActivityType>",
        "inputs":  "[]",
        "outputs":  "[]",
        "linkedServiceName": "MyLinkedService",
        "typeProperties":
        {
    
        },
        "policy":
        {
        }
        "scheduler":
        {
        }
    }

Tabela a seguir descrevem as propriedades dentro as definições de JSON atividade e pipeline:

Marca | Descrição | Necessário
--- | ----------- | --------
nome | Nome da atividade ou o pipeline. Especifique um nome que representa a ação que a atividade ou pipeline está configurado para executar<br/><ul><li>Número máximo de caracteres: 260</li><li>Deve começar com um número de letra ou um sublinhado (_)</li><li>Caracteres seguintes não são permitidas: ".", "+", "?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> | Sim
Descrição | Texto que descreve a atividade ou pipeline para que serve | Sim
tipo | Especifica o tipo da atividade. Consulte os artigos de [Atividades de movimentação de dados](data-factory-data-movement-activities.md) e [Atividades de transformação de dados](data-factory-data-transformation-activities.md) para diferentes tipos de atividades. | Sim
entradas | Tabelas de entrada usadas pela atividade<br/><br/>uma tabela de entrada<br/>"entradas": [{"nome": "inputtable1"}],<br/><br/>duas tabelas de entrada <br/>"entradas": [{"nome": "inputtable1"}, {"nome": "inputtable2"}], | Sim
saídas | Tabelas de saída usadas pelo activity.// uma tabela de saída<br/>"saídas": [{"nome": "outputtable1"}],<br/><br/>duas tabelas de saída<br/>"saídas": [{"nome": "outputtable1"}, {"nome": "outputtable2"}], | Sim
linkedServiceName | Nome do serviço vinculado usado pela atividade. <br/><br/>Uma atividade pode exigir que você especifique o serviço vinculado links para o ambiente de computação necessários. | Sim para atividade de HDInsight e lote pontuação atividade de aprendizado de máquina Azure <br/><br/>Não para todos os outros
typeProperties | Propriedades da seção typeProperties dependem do tipo da atividade. | Não
política | Políticas que afetam o comportamento de tempo de execução da atividade. Se não for especificada, políticas padrão são usadas. | Não
Iniciar | Data-hora de início para o pipeline. Deve estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. <br/><br/>É possível especificar um horário local, por exemplo, uma hora de EST. Aqui está um exemplo: "2016-02-27T06:00:00**-05:00**", que é 6 AM EST.<br/><br/>As propriedades de início e término juntos especificam período ativo para o pipeline. Fatias de saída só são produzidas neste período ativo. | Não<br/><br/>Se você especificar um valor para a propriedade end, especifique o valor da propriedade de início.<br/><br/>As horas de início e término podem estar vazias para criar um pipeline. Você deve especificar os dois valores para definir um período de ativo para o pipeline executar. Se você não especificar horários de início e término ao criar um pipeline, você pode defini-los usando o cmdlet Set-AzureRmDataFactoryPipelineActivePeriod mais tarde.
final | Data-hora de término para o pipeline. Se especificado deve estar no formato ISO. Por exemplo: 2014-10-14T17:32:41Z <br/><br/>É possível especificar um horário local, por exemplo, uma hora de EST. Aqui está um exemplo: "2016-02-27T06:00:00**-05:00**", que é 6 AM EST.<br/><br/>Para executar o pipeline indefinidamente, especifique 9999-09-09 como o valor da propriedade de fim. | Não <br/><br/>Se você especificar um valor para a propriedade de início, especifique o valor da propriedade de fim.<br/><br/>Consulte anotações para a propriedade **Iniciar** .
isPaused | Se definido como verdadeiro o pipeline não é executado. Valor padrão = false. Você pode usar esta propriedade para habilitar ou desabilitar. | Não 
Agendador | propriedade de "Agendador" é usada para definir o agendamento desejado para a atividade. Suas sub-propriedades são os mesmos que aqueles na [propriedade de disponibilidade em um conjunto de dados](data-factory-create-datasets.md#Availability). | Não |   
| pipelineMode | O método de agendamento é executado para o pipeline. Valores permitidos são: agendado (padrão), única.<br/><br/>'Agendado' indica que o pipeline é executado em um intervalo de tempo especificado de acordo com o seu período ativo (hora de início e de término). 'Única' indica que o pipeline é executado apenas uma vez. Canais única uma vez criados não podem ser modificada/atualizados no momento. Consulte [Onetime pipeline](data-factory-scheduling-and-execution.md#onetime-pipeline) para obter detalhes sobre a configuração única. | Não | 
| expirationTime | Duração de tempo após a criação para o qual o pipeline é válida e deve permanecer provisionado. Se não ter qualquer ativo, falha ou pendente é executado, o pipeline é excluído automaticamente quando atingem o tempo de expiração. | Não | 
| conjuntos de dados | Lista de conjuntos de dados a ser usado por atividades definidas no pipeline. Esta propriedade pode ser usada para definir conjuntos de dados que são específicos para esse pipeline e não definido dentro da fábrica de dados. Conjuntos de dados definidos dentro esse pipeline só podem ser usados por esse pipeline e não podem ser compartilhados. Consulte [conjuntos de dados do escopo](data-factory-create-datasets.md#scoped-datasets) para obter detalhes.| Não |  
 

### <a name="policies"></a>Políticas
Diretivas afetam o comportamento de tempo de execução de uma atividade, especificamente quando a fatia de uma tabela é processada. A tabela a seguir fornece detalhes.

Propriedade | Valores permitidos | Valor padrão | Descrição
-------- | ----------- | -------------- | ---------------
concorrência | Número inteiro <br/><br/>Valor máximo: 10 | 1 | Número de execuções simultâneas da atividade.<br/><br/>Ele determina o número de execuções de atividades paralelas que podem ocorrer em diferentes faixas. Por exemplo, se uma atividade precisa passar um grande conjunto de dados disponíveis, tendo um valor maior de concorrência acelera o processamento de dados. 
executionPriorityOrder | NewestFirst<br/><br/>OldestFirst | OldestFirst | Determina a ordenação de fatias de dados que estão sendo processadas.<br/><br/>Por exemplo, se você tiver 2 fatias (um ocorra em 4 pm e outro em 5 pm) e ambas são pendentes execução. Se você definir o executionPriorityOrder seja NewestFirst, a fatia em 5 PM é processada primeiro. Da mesma forma se você definir o executionPriorityORder seja OldestFIrst, a fatia em 4 PM é processada. 
Repetir | Número inteiro<br/><br/>Valor max pode ser 10 | 3 | Número de tentativas antes do processamento de dados da fatia está marcado como falha. Execução de atividade de uma fatia de dados está repetida até a contagem de repetição especificado. A tentativa é feita assim que possível após a falha.
tempo limite | Período de tempo | 00:00:00 | Tempo limite para a atividade. Exemplo: 00:10:00 (indica o tempo limite de 10 minutos)<br/><br/>Se um valor não for especificado ou for 0, o tempo limite é infinito.<br/><br/>Se o tempo de processamento de dados em uma fatia exceder o valor de tempo limite, ela será cancelada e o sistema tenta repetir o processamento. O número de tentativas depende da propriedade de repetição. Quando tempo limite ocorre, o status está definido como TimedOut.
atraso | Período de tempo | 00:00:00 | Especifica o atraso antes do processamento de dados dos inícios na fatia.<br/><br/>A execução da atividade de uma fatia de dados for iniciada após o atraso é passado o tempo de execução esperado.<br/><br/>Exemplo: 00:10:00 (indica o atraso de 10 minutos)
longRetry | Número inteiro<br/><br/>Valor máximo: 10 | 1 | O número de tentativas de repetição longa antes de que é Falha na execução fatia.<br/><br/>longRetry tentativas são espaçadas por longRetryInterval. Portanto, se for necessário especificar um tempo entre tentativas, use longRetry. Se repetir e longRetry forem especificados, cada tentativa de longRetry inclui tentativas de repetição e o número máximo de tentativas é repetir * longRetry.<br/><br/>Por exemplo, se temos as seguintes configurações na diretiva de atividade:<br/>Repetir: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Suponha que não há apenas uma fatia para executar (status está aguardando) e a execução da atividade falha sempre. Inicialmente deve haver 3 tentativas de execução consecutivas. Após cada tentativa, o status de fatia seria repetir. Depois de 3 primeiras tentativas excedam, o status de fatia seria LongRetry.<br/><br/>Após uma hora (ou seja, valor do longRetryInteval), deve haver outro conjunto de 3 tentativas de execução consecutivas. Depois disso, o status de fatia poderia ser falhou e não tentativa de nenhuma mais tentativas. Portanto, geral 6 foram feitas tentativas.<br/><br/>Se qualquer execução for bem-sucedida, o status de fatia seria pronto e não há mais tentativas são aplicadas.<br/><br/>longRetry pode ser usado em situações em que dados dependentes chegam em momentos determinística ou o ambiente geral é falhas em qual processamento de dados ocorre. Nesses casos, isso tentativas após o outro não podem ajudar e fazê-lo após um intervalo de tempo resulta na saída desejada.<br/><br/>Palavra de precaução: não definir valores altos para longRetry ou longRetryInterval. Normalmente, os valores mais altos sugerem outros problemas sistemático. 
longRetryInterval | Período de tempo | 00:00:00 | O atraso entre tentativas de repetição longa 


## <a name="next-steps"></a>Próximas etapas

- Compreenda o [agendamento e em execução no Azure dados fábrica](data-factory-scheduling-and-execution.md).  
- Leia sobre os [recursos de transformação de dados](data-factory-data-transformation-activities.md) em fábrica de dados do Azure e [movimentação de dados](data-factory-data-movement-activities.md)
- Compreenda [o gerenciamento e o monitoramento de fábrica de dados do Azure](data-factory-monitor-manage-pipelines.md).
- [Construa e implante o pipeline de primeira](data-factory-build-your-first-pipeline.md). 
