<properties 
    pageTitle="Mover dados do Sybase | Fábrica de dados do Azure" 
    description="Saiba mais sobre como mover dados do banco de dados Sybase usando fábrica de dados do Azure." 
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

# <a name="move-data-from-sybase-using-azure-data-factory"></a>Mover dados do Sybase usando fábrica de dados do Azure 

Este artigo descreve como você pode usar a atividade de cópia em uma fábrica de dados do Azure para mover dados do Sybase para outro armazenamento de dados. Este artigo se baseia o artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com atividade de cópia e combinações de repositório de dados com suporte.

Serviço de fábrica de dados suporta a conexão a fontes de Sybase locais usando o Gateway de gerenciamento de dados. Consulte o artigo de [mover dados entre locais de locais e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de gerenciamento de dados e instruções passo a passo sobre como configurar o gateway. 

> [AZURE.NOTE]
> Gateway é necessária, mesmo se o banco de dados Sybase está hospedado em um máquina virtual IaaS do Azure. Você pode instalar o gateway na mesma VM IaaS como o armazenamento de dados ou em uma máquina virtual diferente, desde que o gateway pode se conectar ao banco de dados. 

Fábrica de dados atualmente suporta apenas mover dados do Sybase para outros armazenamentos de dados e não do outros armazenamentos de dados ao Sybase.

## <a name="installation"></a>Instalação

Para o Gateway de gerenciamento de dados para se conectar ao banco de dados Sybase, você precisa instalar o [provedor de dados para Sybase](http://go.microsoft.com/fwlink/?linkid=324846) no mesmo sistema como o Gateway de gerenciamento de dados.

> [AZURE.NOTE] Consulte o [gateway de solucionar problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para obter dicas sobre solução de problemas de conexão/gateway problemas relacionados. 

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil para criar um pipeline que copia dados de um banco de dados Sybase para qualquer um dos armazenamentos de dados com suporte receptor é usar o Assistente de dados de cópia. Consulte [Tutorial: criar um pipeline usando o Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para um rápida passo a passo sobre como criar um pipeline usando o Assistente de dados de cópia. 

O exemplo a seguir fornece definições de JSON de exemplo que você pode usar para criar um pipeline usando o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de banco de dados Sybase ao armazenamento de Blob do Azure. No entanto, os dados podem ser copiados para qualquer um dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.   

## <a name="sample-copy-data-from-sybase-to-azure-blob"></a>Exemplo: Copiar dados de Sybase ao Azure Blob
Este exemplo mostra como copiar dados de um banco de dados Sybase para um armazenamento de Blob do Azure. No entanto, dados podem ser copiados **diretamente** a qualquer uma dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.  
 
O exemplo tem as seguintes entidades de fábrica de dados:

1.  Um serviço vinculado do tipo [OnPremisesSybase](data-factory-onprem-sybase-connector.md#sybase-linked-service-properties).
2.  Um serviço liked do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Uma entrada [dataset](data-factory-create-datasets.md) do tipo [RelationalTable](data-factory-onprem-sybase-connector.md#sybase-dataset-type-properties).
4.  Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  O [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](data-factory-onprem-sybase-connector.md#sybase-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de um resultado de consulta no banco de dados Sybase para um blob cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras. 

A primeira etapa, configure o gateway de gerenciamento de dados. As instruções são no artigo [mover dados entre locais de local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço de Sybase vinculado:**

    {
        "name": "OnPremSybaseLinkedService",
        "properties": {
            "type": "OnPremisesSybase",
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


**Sybase dataset de entrada:**

O exemplo supõe que você criou uma tabela "MinhaTabela" na Sybase e contém uma coluna chamada "carimbo de hora" para os dados da série de tempo.

Configuração "externo": verdadeiro informa o serviço de dados fábrica que este conjunto de dados externo à fábrica dados e não é produzido por uma atividade na fábrica dados. Observe que o **tipo** de serviço vinculado está definido para: **RelationalTable**. 
    
    {
        "name": "SybaseDataSet",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "OnPremSybaseLinkedService",
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
        "name": "AzureBlobSybaseDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendada para executar a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **RelationalSource** e **receptor** tipo está definido como **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados do DBA. Tabela de pedidos no banco de dados.


    {
        "name": "CopySybaseToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "select * from DBA.Orders"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "inputs": [
                        {
                            "name": "SybaseDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobSybaseDataSet"
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
                    "name": "SybaseToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }


## <a name="sybase-linked-service-properties"></a>Propriedades de serviço de Sybase vinculado

A tabela a seguir fornece descrição para elementos JSON específicos ao serviço Sybase vinculado.

Propriedade | Descrição | Necessário
-------- | ----------- | --------
tipo | A propriedade type deve ser definida: **OnPremisesSybase** | Sim
servidor | Nome do servidor Sybase. | Sim
banco de dados | Nome do banco de dados Sybase. | Sim 
esquema  | Nome do esquema no banco de dados. | Não
authenticationType | Tipo de autenticação usado para conectar ao banco de dados Sybase. Valores possíveis são: anônima, básica e Windows. | Sim
nome de usuário | Especifique o nome de usuário se você estiver usando a autenticação básica ou Windows. | Não
senha | Especifica a senha da conta de usuário que você especificou para o nome de usuário. |  Não
gatewayName | Nome do gateway que o serviço de dados fábrica deve usar para se conectar ao banco de dados Sybase local. | Sim 

Consulte [definir credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para obter detalhes sobre como definir credenciais para uma fonte de dados Sybase local.

## <a name="sybase-dataset-type-properties"></a>Propriedades de tipo de conjunto de dados Sybase

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo de [conjuntos de dados criando](data-factory-create-datasets.md) . Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOB do Microsoft Azure, tabela do Microsoft Azure, etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção **typeProperties** de conjunto de dados do tipo **RelationalTable** (que inclui o conjunto de dados Sybase) tem as seguintes propriedades:

Propriedade | Descrição | Necessário
-------- | ----------- | --------
nome de tabela | Nome da tabela na instância do banco de dados Sybase vinculado serviço se refere. | Não (se a **consulta** de **RelationalSource** for especificada)

## <a name="sybase-copy-activity-type-properties"></a>Propriedades de tipo de atividade de cópia Sybase 

Para obter uma lista completa das seções & propriedades disponíveis para a definição de atividades, consulte o artigo [Criando tubulações](data-factory-create-pipelines.md) . Propriedades como nome, descrição, entrada e saída tabelas e política estão disponíveis para todos os tipos de atividades. 

Por outro lado, propriedades disponíveis na seção typeProperties da atividade variam com cada tipo de atividade. Atividade de cópia, eles variam dependendo dos tipos de fontes e receptores.

Quando origem for do tipo **RelationalSource** (que inclui Sybase) as seguintes propriedades estão disponíveis na seção de **typeProperties** :

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
consulta | Use a consulta personalizada para ler dados. | Cadeia de caracteres de consulta SQL. Por exemplo: selecionar * de MyTable. | Não (se o **nome de tabela** de **dataset** for especificado)

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-sybase"></a>Digite o mapeamento para Sybase

Conforme mencionado no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões de tipo automática de tipos de fonte para coletar tipos com a abordagem de 2 etapas a seguir:

1. Converter de tipos de fonte nativo para tipo .NET
2. Converter do tipo .NET para o tipo de receptor nativo

Sybase suporta T-SQL e tipos de T-SQL. Para uma tabela de mapeamento de tipos de sql para o tipo de .NET, consulte [Conector do SQL Azure](data-factory-azure-sql-connector.md) artigo.

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Desempenho e ajuste  
Consulte o [guia de ajuste e desempenho de atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre principais fatores que desempenho de impacto de movimentação de dados (cópia atividade) em fábrica de dados do Azure e várias maneiras de otimizá-lo.