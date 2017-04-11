<properties 
    pageTitle="Mover dados do Teradata | Fábrica de dados do Azure" 
    description="Saiba mais sobre Teradata conector para o serviço de dados fábrica que permite mover dados do banco de dados Teradata" 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-teradata-using-azure-data-factory"></a>Mover dados do Teradata usando fábrica de dados do Azure

Este artigo descreve como você pode usar a atividade de cópia em uma fábrica de dados do Azure para mover dados do Teradata aos dados de outra armazenar. Este artigo se baseia o artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com atividade de cópia e combinações de repositório de dados com suporte.

Fábrica de dados suporta a conexão a fontes de Teradata locais por meio do Gateway de gerenciamento de dados. Consulte o artigo de [mover dados entre locais de locais e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de gerenciamento de dados e instruções passo a passo sobre como configurar o gateway. 

> [AZURE.NOTE]
Gateway é necessária, mesmo se o Teradata está hospedado em um máquina virtual IaaS do Azure. Você pode instalar o gateway na mesma VM IaaS como o armazenamento de dados ou em uma máquina virtual diferente, desde que o gateway pode se conectar ao banco de dados. 

Fábrica de dados suporta somente mover dados do Teradata outros armazenamentos de dados e não do outros armazenamentos de dados para Teradata.

## <a name="installation"></a>Instalação 

Para o Gateway de gerenciamento de dados para se conectar ao banco de dados Teradata, é necessário instalar o [Provedor de dados .NET para Teradata](http://go.microsoft.com/fwlink/?LinkId=278886) no mesmo sistema do Gateway de gerenciamento de dados.

> [AZURE.NOTE] Consulte o [gateway de solucionar problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para obter dicas sobre solução de problemas de conexão/gateway problemas relacionados. 

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil para criar um pipeline que copia dados de Teradata para qualquer um dos armazenamentos de dados com suporte receptor é usar o Assistente de dados de cópia. Consulte [Tutorial: criar um pipeline usando o Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para um rápida passo a passo sobre como criar um pipeline usando o Assistente de dados de cópia. 

O exemplo a seguir fornece definições de JSON de exemplo que você pode usar para criar um pipeline usando o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de Teradata ao armazenamento de Blob do Azure. No entanto, os dados podem ser copiados para qualquer um dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.   

### <a name="sample-copy-data-from-teradata-to-azure-blob"></a>Exemplo: Copiar dados de Teradata ao Azure Blob

Este exemplo mostra como copiar dados de um banco de dados Teradata para um armazenamento de Blob do Azure. No entanto, dados podem ser copiados **diretamente** a qualquer uma dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.  
 
O exemplo tem as seguintes entidades de fábrica de dados:

1.  Um serviço vinculado do tipo [OnPremisesTeradata](data-factory-onprem-teradata-connector.md#teradata-linked-service-properties).
2.  Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Uma entrada [dataset](data-factory-create-datasets.md) do tipo [RelationalTable](data-factory-onprem-teradata-connector.md#teradata-dataset-type-properties).
4.  Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
4.  O [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](data-factory-onprem-teradata-connector.md#teradata-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de um resultado de consulta no banco de dados Teradata para um blob cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras. 

A primeira etapa, configure o gateway de gerenciamento de dados. As instruções são no artigo [mover dados entre locais de local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço de Teradata vinculado:**

    {
        "name": "OnPremTeradataLinkedService",
        "properties": {
            "type": "OnPremisesTeradata",
            "typeProperties": {
                "server": "<server>",
                "authenticationType": "<authentication type>",
                "username": "<username>",
                "password": "<password>",
                "gatewayName": "<gatewayName>"
            }
        }
    }

**Armazenamento de Blob do Azure vinculado serviço:**

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorageLinkedService",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
            }
        }
    }


**Teradata dataset de entrada:**

O exemplo supõe que você criou uma tabela "MinhaTabela" na Teradata e contém uma coluna chamada "carimbo de hora" para os dados da série de tempo.

Configuração "externo": verdadeiro informa o serviço de fábrica de dados que a tabela é externa à fábrica dados e não é produzida por uma atividade na fábrica dados.

    {
        "name": "TeradataDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremTeradataLinkedService",
            "typeProperties": {
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }


**Azure Blob dataset de saída:**

Dados são gravados em um novo blob cada hora (frequência: horas, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa ano, mês, dia e partes de horas de início.

    {
        "name": "AzureBlobTeradataDataSet",
        "properties": {
            "published": false,
            "location": {
                "type": "AzureBlobLocation",
                "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ],
                "linkedServiceName": "AzureStorageLinkedService"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**Pipeline com atividade de cópia:**

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendada para executar a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **RelationalSource** e **receptor** tipo está definido como **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados na última hora para copiar.

    {
        "name": "CopyTeradataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "TeradataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobTeradataDataSet"
                        }
                    ],                  
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "TeradataToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z",
            "isPaused": false
        }
    }


## <a name="teradata-linked-service-properties"></a>Propriedades de serviço de Teradata vinculado

A tabela a seguir fornece descrição para elementos JSON específicos ao serviço Teradata vinculado. 

Propriedade | Descrição | Necessário
-------- | ----------- | --------
tipo | A propriedade type deve ser definida: **OnPremisesTeradata** | Sim
servidor | Nome do servidor Teradata. | Sim
authenticationType | Tipo de autenticação usado para conectar ao banco de dados Teradata. Valores possíveis são: anônima, básica e Windows. | Sim
nome de usuário | Especifique o nome de usuário se você estiver usando a autenticação básica ou Windows. | Não 
senha | Especifica a senha da conta de usuário que você especificou para o nome de usuário. | Não 
gatewayName | Nome do gateway que o serviço de dados fábrica deve usar para se conectar ao banco de dados Teradata local. | Sim

Consulte [definir credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para obter detalhes sobre como definir credenciais para uma fonte de dados Teradata local.

## <a name="teradata-dataset-type-properties"></a>Propriedades de tipo de conjunto de dados Teradata

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo de [conjuntos de dados criando](data-factory-create-datasets.md) . Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOB do Microsoft Azure, tabela do Microsoft Azure, etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. Atualmente, não há nenhuma propriedade de tipo com suporte para o conjunto de dados Teradata. 


## <a name="teradata-copy-activity-type-properties"></a>Propriedades de tipo de atividade de cópia Teradata

Para obter uma lista completa das seções & propriedades disponíveis para a definição de atividades, consulte o artigo [Criar canais](data-factory-create-pipelines.md) . Propriedades como nome, descrição, entrada e saída tabelas e políticas estão disponíveis para todos os tipos de atividades. 

Por outro lado, propriedades disponíveis na seção typeProperties da atividade variam com cada tipo de atividade. Atividade de cópia, eles variam dependendo dos tipos de fontes e receptores.

Quando origem for do tipo **RelationalSource** (que inclui Teradata) as seguintes propriedades estão disponíveis na seção de **typeProperties** :

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
consulta | Use a consulta personalizada para ler dados. | Cadeia de caracteres de consulta SQL. Por exemplo: selecionar * de MyTable. | Sim

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-teradata"></a>Digite o mapeamento para Teradata

Conforme mencionado no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões de tipo automática de tipos de fonte para coletar tipos com a abordagem de 2 etapas a seguir:

1. Converter de tipos de fonte nativo para tipo .NET
2. Converter do tipo .NET para o tipo de receptor nativo

Ao mover dados para Teradata, os seguintes mapeamentos são usados Teradata tipo para o tipo de .NET.

Tipo de banco de dados Teradata | Tipo .NET framework
----------------- | ---------------------------
CARACT | Cadeia de caracteres
CLOB | Cadeia de caracteres
Elemento gráfico | Cadeia de caracteres
VarChar | Cadeia de caracteres
VarGraphic | Cadeia de caracteres
Blob | Byte]
Byte | Byte]
VarByte | Byte]
BigInt | Int64
ByteInt | Int16
Decimal | Decimal
Duplo | Duplo
Número inteiro | Int32
Número | Duplo
SmallInt | Int16
Data | DateTime
Tempo | Período de tempo
Tempo com fuso horário | Cadeia de caracteres
Carimbo de hora | DateTime
Carimbo de hora com fuso horário | DateTimeOffset
Dia de intervalo | Período de tempo
Dia do intervalo para hora | Período de tempo
Dia do intervalo em minuto | Período de tempo
Dia do intervalo em segundo | Período de tempo
Hora de intervalo | Período de tempo
Hora de intervalo para minuto | Período de tempo
Hora de intervalo em segundo | Período de tempo
Minuto de intervalo | Período de tempo
Minuto em segundo intervalo | Período de tempo
Intervalo segundo | Período de tempo
Ano de intervalo | Cadeia de caracteres
Ano de intervalo para o mês | Cadeia de caracteres
Mês de intervalo | Cadeia de caracteres
Period(date) | Cadeia de caracteres
Period(time) | Cadeia de caracteres
Período (com fuso horário) | Cadeia de caracteres
Period(timestamp) | Cadeia de caracteres
Período (carimbo de hora com fuso horário) | Cadeia de caracteres
XML | Cadeia de caracteres

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Desempenho e ajuste  
Consulte o [guia de ajuste e desempenho de atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre principais fatores que desempenho de impacto de movimentação de dados (cópia atividade) em fábrica de dados do Azure e várias maneiras de otimizá-lo.