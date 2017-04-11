<properties 
    pageTitle="Mover dados do DB2 | Fábrica de dados do Azure" 
    description="Saiba mais sobre como mover dados do banco de dados do DB2 usando fábrica de dados do Azure" 
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
    ms.date="09/06/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-db2-using-azure-data-factory"></a>Mover os dados de DB2 usando fábrica de dados do Azure
Este artigo descreve como você pode usar a atividade de cópia em uma fábrica de dados do Azure para mover dados do DB2 aos dados de outra armazenar. Este artigo se baseia o artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com atividade de cópia e combinações de repositório de dados com suporte.

Fábrica de dados oferece suporte para se conectar a fontes de DB2 locais usando o Gateway de gerenciamento de dados. Consulte o artigo de [mover dados entre locais de locais e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de gerenciamento de dados e instruções passo a passo sobre como configurar o gateway. 

> [AZURE.NOTE]
Use o gateway para se conectar à DB2, mesmo se ele estiver hospedado em Azure IaaS VMs. Se você estiver tentando se conectar a uma instância do DB2 hospedados na nuvem, você também pode instalar a instância de gateway na VM IaaS.

Fábrica de dados atualmente suporta apenas mover dados do DB2 para outros armazenamentos de dados e não do outros armazenamentos de dados para DB2. 

## <a name="installation"></a>Instalação 

O Gateway de gerenciamento de dados fornece um driver DB2 interno que suporta o seguinte: 

- SQLAM 9 / 10 / 11
- DB2 para LUW (Linux, Unix, Windows)
- DB2 para z/OS e DB2 para eu (também conhecidos como como / 400)

Portanto, você não precisa instalar manualmente os drivers ao copiar dados de DB2.

> [AZURE.NOTE] Consulte o [gateway de solucionar problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para obter dicas sobre solução de problemas de conexão/gateway problemas relacionados. 

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil para criar um pipeline que copia dados de um banco de dados DB2 é usar o Assistente de dados de cópia. Consulte [Tutorial: criar um pipeline usando o Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para um rápida passo a passo sobre como criar um pipeline usando o Assistente de dados de cópia. 

Os exemplos a seguir fornecem definições de JSON de exemplo que você pode usar para criar um pipeline usando o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados do banco de dados DB2 e armazenamento de Blob do Azure. No entanto, os dados podem ser copiados para qualquer um dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.

## <a name="sample-copy-data-from-db2-to-azure-blob"></a>Exemplo: Copiar dados de DB2 ao Azure Blob

Este exemplo mostra como copiar dados de um banco de dados do DB2 locais para um armazenamento de Blob do Azure. No entanto, dados podem ser copiados **diretamente** a qualquer uma dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.  
 
O exemplo tem as seguintes entidades de fábrica de dados:

1.  Um serviço vinculado do tipo [OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties).
2.  Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3.  Uma entrada [dataset](data-factory-create-datasets.md) do tipo [RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties).
4.  Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
5.  Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

O exemplo copia dados de um resultado de consulta em um banco de dados DB2 para um Azure blob por hora. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras. 

A primeira etapa, instalar e configurar um gateway de gerenciamento de dados. Instruções estão no artigo [mover dados entre locais de local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço de DB2 vinculada:**

    {
        "name": "OnPremDb2LinkedService",
        "properties": {
            "type": "OnPremisesDb2",
            "typeProperties": {
                "server": "<server>",
                "database": "<database>",
                "schema": "<schema>",
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

**Dataset de entrada do DB2:**

O exemplo supõe que você criou uma tabela "MinhaTabela" na DB2 e contém uma coluna chamada "carimbo de hora" para os dados da série de tempo.

Configuração "externo": verdadeiro informa o serviço de dados fábrica que este conjunto de dados externo à fábrica dados e não é produzido por uma atividade na fábrica dados. Observe que o **tipo** está definido como **RelationalTable**. 

    {
        "name": "Db2DataSet",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "OnPremDb2LinkedService",
            "typeProperties": {},
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
        "name": "AzureBlobDb2DataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

**Pipeline com atividade de cópia:**

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendado para ser executado a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **RelationalSource** e **receptor** tipo está definido como **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados da tabela Pedidos.

    {
        "name": "CopyDb2ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "select * from \"Orders\""
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Db2DataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobDb2DataSet"
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
                    "name": "Db2ToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }


## <a name="db2-linked-service-properties"></a>Propriedades de serviço de DB2 vinculada

A tabela a seguir fornece descrição para elementos JSON específicos ao serviço de DB2 vinculado. 

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- | 
| tipo | A propriedade type deve ser definida: **OnPremisesDB2** | Sim |
| servidor | Nome do servidor DB2. | Sim |
| banco de dados | Nome do banco de dados DB2. | Sim |
| esquema | Nome do esquema no banco de dados. O nome do esquema diferencia maiusculas de minúsculas. | Não |
| authenticationType | Tipo de autenticação usado para conectar ao banco de dados DB2. Valores possíveis são: anônima, básica e Windows. | Sim |
| nome de usuário | Especifique o nome de usuário se você estiver usando a autenticação básica ou Windows. | Não |
| senha | Especifica a senha da conta de usuário que você especificou para o nome de usuário. | Não |
| gatewayName | Nome do gateway que o serviço de dados fábrica deve usar para se conectar ao banco de dados DB2 local. | Sim |

Consulte [definir credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para obter detalhes sobre como definir credenciais para uma fonte de dados de DB2 local. 


## <a name="db2-dataset-type-properties"></a>Propriedades de tipo de conjunto de dados DB2

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo de [conjuntos de dados criando](data-factory-create-datasets.md) . Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOB do Microsoft Azure, tabela do Microsoft Azure, etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção typeProperties de conjunto de dados do tipo RelationalTable (que inclui DB2 dataset) tem as seguintes propriedades.

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- | 
| nome de tabela | Nome da tabela na instância do banco de dados DB2 vinculado serviço se refere. O nome de tabela diferencia maiusculas de minúsculas. | Não (se a **consulta** de **RelationalSource** for especificada) |

## <a name="db2-copy-activity-type-properties"></a>Propriedades de tipo de atividade de cópia DB2

Para obter uma lista completa das seções & propriedades disponíveis para a definição de atividades, consulte o artigo [Criar canais](data-factory-create-pipelines.md) . Propriedades como nome, descrição, entrada e saída tabelas e políticas estão disponíveis para todos os tipos de atividades. 

Por outro lado, propriedades disponíveis na seção typeProperties da atividade variam com cada tipo de atividade. Atividade de cópia, eles variam dependendo dos tipos de fontes e receptores.

Para copiar atividade, quando origem for do tipo **RelationalSource** (que inclui DB2) as seguintes propriedades estão disponíveis na seção typeProperties:


| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------- | -------------- |
| consulta | Use a consulta personalizada para ler dados. | Cadeia de caracteres de consulta SQL. Por exemplo: `"query": "select * from "MySchema"."MyTable""`. | Não (se o **nome de tabela** de **dataset** for especificado)|

> [AZURE.NOTE] Esquema e nomes de tabelas diferenciam maiusculas de minúsculas. Coloque nomes na "" (aspas duplas) na consulta.  

**Exemplo:**

    "query": "select * from "DB2ADMIN"."Customers""


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-db2"></a>Digite o mapeamento para DB2
Conforme mencionado no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões de tipo automática de tipos de fonte para coletar tipos com a abordagem de 2 etapas a seguir:

1. Converter de tipos de fonte nativo para tipo .NET
2. Converter do tipo .NET para o tipo de receptor nativo

Ao mover dados para DB2, os seguintes mapeamentos são usados DB2 tipo para o tipo de .NET.

Tipo de banco de dados DB2 | Tipo .NET framework 
----------------- | ------------------- 
SmallInt | Int16
Número inteiro | Int32
BigInt | Int64
Real | Único
Duplo | Duplo
Sobrepor | Duplo
Decimal | Decimal
DecimalFloat | Decimal
Numérico | Decimal
Data | DateTime
Tempo | Período de tempo
Carimbo de hora | DateTime
XML | Byte]
CARACT | Cadeia de caracteres
VarChar | Cadeia de caracteres
LongVarChar | Cadeia de caracteres
DB2DynArray | Cadeia de caracteres
Binário | Byte]
VarBinary | Byte]
LongVarBinary | Byte]
Elemento gráfico | Cadeia de caracteres
VarGraphic | Cadeia de caracteres
LongVarGraphic | Cadeia de caracteres
CLOB | Cadeia de caracteres
Blob | Byte]
DbClob | Cadeia de caracteres
SmallInt | Int16
Número inteiro | Int32
BigInt | Int64
Real | Único
Duplo | Duplo
Sobrepor | Duplo
Decimal | Decimal
DecimalFloat | Decimal
Numérico | Decimal
Data | DateTime
Tempo | Período de tempo
Carimbo de hora | DateTime
XML | Byte]
CARACT | Cadeia de caracteres


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Desempenho e ajuste  
Consulte o [guia de ajuste e desempenho de atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre principais fatores que desempenho de impacto de movimentação de dados (cópia atividade) em fábrica de dados do Azure e várias maneiras de otimizá-lo.


