<properties 
    pageTitle="Criar conjuntos de dados no Azure dados fábrica | Microsoft Azure" 
    description="Aprenda a criar conjuntos de dados no Azure dados fábrica com exemplos que usam propriedades como deslocamento e anchorDateTime."
    keywords="Criar conjunto de dados, exemplo dataset, exemplo offset"
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
    ms.date="09/13/2016" 
    ms.author="shlo"/>

# <a name="datasets-in-azure-data-factory"></a>Conjuntos de dados em fábrica de dados do Azure
Este artigo descreve os conjuntos de dados em fábrica de dados do Azure e inclui exemplos como deslocamento, anchorDateTime e bancos de dados de deslocamento/estilo.

Quando você cria um conjunto de dados, você está criando um ponteiro para os dados que você deseja processar. Dados são processados (entrada/saída) em uma atividade e uma atividade está contida em um pipeline. Um conjunto de dados de entrada representa a entrada para uma atividade no pipeline e um conjunto de dados de saída representa a saída para a atividade.

Conjuntos de dados identificam dados dentro de armazenamentos de dados diferentes, como tabelas, arquivos, pastas e documentos. Depois de criar um conjunto de dados, você pode usá-lo com atividades em um pipeline. Por exemplo, um conjunto de dados pode ser um conjunto de dados de entrada/saída de uma atividade de cópia ou uma atividade de HDInsightHive. O portal do Azure fornece um layout visual de todas as suas entradas de dados e canais e saídas. Em um relance, você ver todas as relações e dependências de seu pipeline em todas as suas fontes para que você sempre saiba onde os dados são provenientes e onde ele está indo.

Em fábrica de dados do Azure, você pode obter dados de um conjunto de dados, usando a atividade de cópia em um pipeline.

> [AZURE.NOTE] Se você estiver começando a fábrica de dados do Azure, consulte [Introdução ao Azure dados Factory](data-factory-introduction.md) para uma visão geral do serviço de fábrica de dados do Azure. Consulte [criar sua primeira fábrica de dados](data-factory-build-your-first-pipeline.md) para um tutorial para criar sua primeira fábrica de dados. Esses dois artigos fornecem a que você informações necessárias para entender melhor o neste artigo de plano de fundo. 

## <a name="define-datasets"></a>Definir conjuntos de dados
Um conjunto de dados no Azure dados Factory é definido da seguinte maneira: 


    {
        "name": "<name of dataset>",
        "properties": {
            "type": "<type of dataset: AzureBlob, AzureSql etc...>",
            "external": <boolean flag to indicate external data. only for input datasets>,
            "linkedServiceName": "<Name of the linked service that refers to a data store.>",
            "structure": [
                {
                    "name": "<Name of the column>",
                    "type": "<Name of the type>"
                }
            ],
            "typeProperties": {
                "<type specific property>": "<value>",
                "<type specific property 2>": "<value 2>",
            },
            "availability": {
                "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
                "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
            },
           "policy": 
            {      
            }
        }
    }

A tabela a seguir descreve as propriedades no JSON acima:   

| Propriedade | Descrição | Necessário | Padrão |
| -------- | ----------- | -------- | ------- |
| nome | Nome do conjunto de dados. Consulte [Factory de dados do Azure - as regras de nomenclatura](data-factory-naming-rules.md) para regras de nomenclatura. | Sim | DISP |
| tipo | Tipo do conjunto de dados. Especificar um dos tipos de suporte para fábrica de dados do Azure (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Consulte [Tipo de conjunto de dados](#Type) para obter detalhes. | Sim | DISP |
| estrutura | Esquema do conjunto de dados<br/><br/>Para obter detalhes, consulte [Estrutura de Dataset](#Structure) seção. | Não. | DISP |
| typeProperties | Propriedades correspondente ao tipo selecionado. Consulte a seção [Tipo de conjunto de dados](#Type) para obter detalhes sobre os tipos suportados e suas propriedades. | Sim | DISP |
| externo | Sinalizador booliano para especificar se um conjunto de dados é explicitamente produzido por um pipeline de fábrica de dados ou não.  | Não | FALSO | 
| disponibilidade | Define a janela de processamento ou modelo de fatia para a produção de conjunto de dados. <br/><br/>Para obter detalhes, consulte [Dataset disponibilidade](#Availability) seção. <br/><br/>Para obter detalhes sobre o conjunto de dados divisão modelo, consulte o artigo de [agendamento e execução](data-factory-scheduling-and-execution.md) . | Sim | DISP
| política | Define os critérios ou a condição que as fatias do dataset devem atender. <br/><br/>Para obter detalhes, consulte a [Política de conjunto de dados](#Policy) seção. | Não | DISP |

## <a name="dataset-example"></a>Exemplo de conjunto de dados
No exemplo a seguir, o dataset representa uma tabela chamada **MyTable** em um **banco de dados do SQL Azure**. 

    {
        "name": "DatasetSample",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": 
            {
                "tableName": "MyTable"
            },
            "availability": 
            {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

Observe os seguintes pontos: 

- tipo está definido como AzureSqlTable.
- propriedade de tipo de nome de tabela (específica para o tipo de AzureSqlTable) está definida como MyTable.
- linkedServiceName se refere a um serviço vinculado do tipo AzureSqlDatabase. Consulte a definição do seguinte serviço vinculada. 
- frequência de disponibilidade é definida como dia e intervalo é definido como 1, o que significa que a fatia é produzida diariamente.  

AzureSqlLinkedService é definida da seguinte maneira:

    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "",
            "typeProperties": {
                "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
            }
        }
    }

No JSON acima: 

- tipo está definido como AzureSqlDatabase
- propriedade de tipo de connectionString Especifica informações para se conectar a um banco de dados do SQL Azure.  


Como você pode ver, o serviço vinculado define como se conectar a um banco de dados do SQL Azure. O conjunto de dados define qual tabela é usada como uma entrada/saída para a atividade em um pipeline. A seção de atividade no [pipeline](data-factory-create-pipelines.md) JSON Especifica se o conjunto de dados é usado como um conjunto de dados de entrada ou de saída.


> [AZURE.IMPORTANT] A menos que um conjunto de dados é produzido por fábrica de dados do Azure, ele deve ser marcado como **externo**. Esta configuração geralmente se aplica às entradas da primeira atividade em um pipeline.   

## <a name="Type"></a>Tipo de conjunto de dados
As fontes de dados com suporte e tipos de conjunto de dados são alinhados. Consulte os tópicos referenciados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md#supported-data-stores) para obter informações sobre tipos e configurações de conjuntos de dados. Por exemplo, se você estiver usando dados de um banco de dados do SQL Azure, clique em banco de dados do SQL Azure na lista de armazenamentos de dados com suporte para ver informações detalhadas.  

## <a name="Structure"></a>Estrutura do conjunto de dados
A seção **estrutura** define o esquema do dataset. Ele contém uma coleção de nomes e tipos de dados das colunas.  No exemplo a seguir, o dataset tem três colunas slicetimestamp, NomeDoProjeto e pageviews e são do tipo: String, cadeia de caracteres e decimais respectivamente.

    structure:  
    [ 
        { "name": "slicetimestamp", "type": "String"},
        { "name": "projectname", "type": "String"},
        { "name": "pageviews", "type": "Decimal"}
    ]

## <a name="Availability"></a>Disponibilidade de conjunto de dados
A seção de **disponibilidade** em um conjunto de dados define a janela de processamento (por hora, diariamente, semanalmente etc.) ou o modelo fatia do conjunto de dados. Consulte o artigo [agendamento e execução](data-factory-scheduling-and-execution.md) para mais detalhes sobre o modelo de divisão e dependência de conjunto de dados. 

A seção de disponibilidade a seguir especifica que o conjunto de dados de saída está produzidos por hora (ou) entrada dataset está disponível por hora:

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": 1   
    }

A tabela a seguir descreve as propriedades que você pode usar na seção disponibilidade: 

| Propriedade | Descrição | Necessário | Padrão |
| -------- | ----------- | -------- | ------- |
| frequência | Especifica a unidade de tempo para produção de fatia do conjunto de dados.<br/><br/>**Frequência com suporte**: minuto, hora, dia, semana, mês | Sim | DISP |
| intervalo | Especifica um multiplicador para frequência<br/><br/>"Intervalo de frequência x" determina a frequência na fatia é produzida.<br/><br/>Se você precisar dataset a ser dividido em uma base por hora, você definir **frequência** a **hora**e o **intervalo** para **1**.<br/><br/>**Observação:** Se você especificar a frequência de minuto, recomendamos que você defina o intervalo para menor do que 15 | Sim | DISP |
| estilo | Especifica se a fatia deve ser produzida no inicial/final do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se frequência é definida para o mês e estilo for definido como EndOfInterval, a fatia é gerada em do último dia do mês. Se o estilo é definido como StartOfInterval, a fatia é produzida no primeiro dia do mês.<br/><br/>Se frequência é definida como dia e estilo for definido como EndOfInterval, a fatia é produzida na última hora do dia.<br/><br/>Se frequência é definida como hora e estilo for definido como EndOfInterval, a fatia é gerada no final da hora. Por exemplo, para uma fatia PM 1 – 2 PM período, a fatia é produzida em 2 PM. | Não | EndOfInterval |
| anchorDateTime | Define a posição absoluta no tempo usado pelo Agendador para calcular dataset fatia limites. <br/><br/>**Observação:** Se a AnchorDateTime tiver partes de data que são mais granulares que a frequência as partes mais granulares são ignoradas. <br/><br/>Por exemplo, se o **intervalo** for **por hora** (frequência: hora e intervalo: 1) e o **AnchorDateTime** contém **minutos e segundos**, as partes de **minutos e segundos** do AnchorDateTime são ignoradas. | Não | 01/01/0001 |
| deslocamento | Período de tempo pelo qual o início e término de todas as fatias do dataset são adiantadas. <br/><br/>**Observação:** Se anchorDateTime e deslocamento forem especificados, o resultado é o turno combinado. | Não | DISP |

### <a name="offset-example"></a>exemplo de deslocamento

Diária fatias que começam na 6 horas em vez da meia-noite padrão. 

    "availability":
    {
        "frequency": "Day",
        "interval": 1,
        "offset": "06:00:00"
    }

A **frequência** é definida como **dia** e **intervalo** é definido como **1** (uma vez ao dia): se quiser que a fatia ser produzidos em 6 horas, em vez de no momento padrão: 12: 00. Lembre-se de que esse tempo é um horário UTC. 

## <a name="anchordatetime-example"></a>exemplo de anchorDateTime

**Exemplo:** fatias de conjunto de dados 23 horas que iniciar em 2007-04-19T08:00:00

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": 23, 
        "anchorDateTime":"2007-04-19T08:00:00"  
    }

## <a name="offsetstyle-example"></a>Exemplo de deslocamento/estilo

Se você precisa dataset em mensalmente na data e hora específicas (suponha no 3º de cada mês no 8:00 AM), use a marca de **deslocamento** para definir a data e hora ele deve executar. 

    {
      "name": "MyDataset",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "availability": {
          "frequency": "Month",
          "interval": 1,
          "offset": "3.08:10:00",
          "style": "StartOfInterval"
        }
      }
    }


## <a name="Policy"></a>Política de conjunto de dados

A seção de **política** na definição dataset define os critérios ou a condição que as fatias do dataset devem atender.

### <a name="validation-policies"></a>Políticas de validação

| Nome da política | Descrição | Aplicada a | Necessário | Padrão |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Valida que os dados em um **Azure blob** atende aos requisitos do tamanho mínimo (em megabytes). | Blob do Microsoft Azure | Não | DISP |
|minimumRows | Valida que os dados em um **banco de dados do SQL Azure** ou uma **tabela do Microsoft Azure** contém o número mínimo de linhas. | <ul><li>Banco de dados do SQL Azure</li><li>Tabela do Microsoft Azure</li></ul> | Não | DISP

#### <a name="examples"></a>Exemplos

**minimumSizeMB:**

    "policy":
    
    {
        "validation":
        {
            "minimumSizeMB": 10.0
        }
    }

**minimumRows**

    "policy":
    {
        "validation":
        {
            "minimumRows": 100
        }
    }

### <a name="external-datasets"></a>Conjuntos de dados externos

Conjuntos de dados externos são aqueles que não são produzidos por um pipeline em execução na fábrica dados. Se o conjunto de dados está marcado como **externo**, a política de **ExternalData** pode ser definida para influenciar o comportamento da disponibilidade fatia dataset. 

A menos que um conjunto de dados é produzido por fábrica de dados do Azure, ele deve ser marcado como **externo**. Esta configuração geralmente aplica-se para as entradas da primeira atividade em um pipeline, a menos que atividade ou encadeamento de pipeline está sendo usado. 

| Nome | Descrição | Necessário | Valor padrão  |
| ---- | ----------- | -------- | -------------- |
| dataDelay | Tempo de atraso no verificar a disponibilidade dos dados externos da fatia determinado. Por exemplo, se os dados deve para estar disponível por hora, a seleção para ver os dados externos estão disponíveis e a fatia correspondente está pronto pode ser atrasada usando dataDelay.<br/><br/>Se aplica somente a hora de presente.  Por exemplo, se for 1:00 PM agora e esse valor é de 10 minutos, a validação começa em 1:10 PM.<br/><br/>Essa configuração não afeta as fatias no passado (fatias com hora de término de fatia + dataDelay < agora) são processadas sem qualquer atraso.<br/><br/>Tempo maior que 23:59 horas precisam especificado usando o formato de day.hours:minutes:seconds. Por exemplo, para especificar 24 horas, não use 24:00:00; em vez disso, use 1.00:00:00. Se você usar 24:00:00, ele será tratado como 24 dias (24.00:00:00). Para 1 dia e 4 horas, especifique 1:04:00:00. | Não | 0 |
| retryInterval | O tempo de espera entre uma falha e a próxima tentativa de repetição. Aplica-se ao tempo presente; Se anterior experimentar falha, podemos esperar esse tempo após o último experimentar. <br/><br/>Se for 1:00 pm agora, vamos começar primeira tentativa. Se a duração para concluir a verificação de validação primeira for 1 minuto e a operação falhou, a próxima tentativa está no 1:00 + 1 min (duração) + 1min (intervalo de repetição) = 1:02 pm. <br/><br/>Para fatias no passado, não há nenhum atraso. A tentativa acontece imediatamente. | Não | 01:00:00 (1 minuto) | 
| retryTimeout | O tempo limite para cada tentativa de repetição.<br/><br/>Se essa propriedade estiver definida para 10 minutos, a validação deve ser concluído em 10 minutos. Se ele demorar mais de 10 minutos para executar a validação, a tentativa atinge o tempo limite.<br/><br/>Se todas as tentativas para a validação de tempo limite, a fatia está marcada como TimedOut. | Não | 10:00:00 (10 minutos) |
| maximumRetry | Número de vezes para verificar a disponibilidade dos dados externos. O valor máximo permitido é 10. | Não | 3 | 

## <a name="scoped-datasets"></a>Escopo de conjuntos de dados
Você pode criar conjuntos de dados com escopo definido a um pipeline usando a propriedade de **conjuntos de dados** . Esses conjuntos de dados só podem ser usados por atividades dentro esse pipeline mas não por atividades em outros canais. O exemplo a seguir define um pipeline com dois conjuntos de dados - InputDataset-rdc e OutputDataset-rdc - a ser usado no pipeline:  

> [AZURE.IMPORTANT] Escopo de conjuntos de dados são compatíveis apenas com canais avulsa (**pipelineMode** definido como **OneTime**). Consulte [Onetime pipeline](data-factory-scheduling-and-execution.md#onetime-pipeline) para obter detalhes.

    {
        "name": "CopyPipeline-rdc",
        "properties": {
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": false
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "InputDataset-rdc"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "OutputDataset-rdc"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1,
                        "style": "StartOfInterval"
                    },
                    "name": "CopyActivity-0"
                }
            ],
            "start": "2016-02-28T00:00:00Z",
            "end": "2016-02-28T00:00:00Z",
            "isPaused": false,
            "pipelineMode": "OneTime",
            "expirationTime": "15.00:00:00",
            "datasets": [
                {
                    "name": "InputDataset-rdc",
                    "properties": {
                        "type": "AzureBlob",
                        "linkedServiceName": "InputLinkedService-rdc",
                        "typeProperties": {
                            "fileName": "emp.txt",
                            "folderPath": "adftutorial/input",
                            "format": {
                                "type": "TextFormat",
                                "rowDelimiter": "\n",
                                "columnDelimiter": ","
                            }
                        },
                        "availability": {
                            "frequency": "Day",
                            "interval": 1
                        },
                        "external": true,
                        "policy": {}
                    }
                },
                {
                    "name": "OutputDataset-rdc",
                    "properties": {
                        "type": "AzureBlob",
                        "linkedServiceName": "OutputLinkedService-rdc",
                        "typeProperties": {
                            "fileName": "emp.txt",
                            "folderPath": "adftutorial/output",
                            "format": {
                                "type": "TextFormat",
                                "rowDelimiter": "\n",
                                "columnDelimiter": ","
                            }
                        },
                        "availability": {
                            "frequency": "Day",
                            "interval": 1
                        },
                        "external": false,
                        "policy": {}
                    }
                }
            ]
        }
    }