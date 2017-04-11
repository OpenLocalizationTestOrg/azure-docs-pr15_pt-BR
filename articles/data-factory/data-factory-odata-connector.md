<properties 
    pageTitle="Mover dados de fontes de OData | Fábrica de dados do Azure" 
    description="Saiba mais sobre como mover dados de fontes de OData usando fábrica de dados do Azure." 
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
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-a-odata-source-using-azure-data-factory"></a>Mover dados do OData uma origem usando fábrica de dados do Azure
Este artigo descreve como você pode usar a atividade de cópia em uma fábrica de dados do Azure para mover dados de uma fonte de OData para outro armazenamento de dados. Este artigo se baseia o artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com atividade de cópia e combinações de repositório de dados com suporte.

> [AZURE.NOTE] Esse suporte de conector OData copiando dados de ambas as nuvem OData e fontes de OData locais. Para o último, você precisa instalar o Gateway de gerenciamento de dados. Consulte o artigo [mover dados entre locais e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre o Gateway de gerenciamento de dados.

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil para criar um pipeline que copia dados de uma fonte de OData é usar o Assistente de dados de cópia. Consulte [Tutorial: criar um pipeline usando o Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para um rápida passo a passo sobre como criar um pipeline usando o Assistente de dados de cópia. 

Os exemplos a seguir fornecem definições de JSON de exemplo que você pode usar para criar um pipeline usando o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de uma fonte de OData para um armazenamento de Blob do Azure. No entanto, os dados podem ser copiados para qualquer um dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.


## <a name="sample-copy-data-from-odata-source-to-azure-blob"></a>Exemplo: Copiar dados de origem de OData para Azure Blob

Este exemplo mostra como copiar dados de uma fonte de OData para o armazenamento de Blob do Azure. No entanto, dados podem ser copiados **diretamente** a qualquer uma dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.  
 
O exemplo tem as seguintes entidades de fábrica de dados:

1.  Um serviço vinculado do tipo [OData](#odata-linked-service-properties).
2.  Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Uma entrada [dataset](data-factory-create-datasets.md) do tipo [ODataResource](#odata-dataset-type-properties).
4.  Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](#odata-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados consultando uma fonte de OData para um Azure blob cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras. 

**OData vinculado serviço** Este exemplo usa a autenticação básica. Consulte a seção de [OData vinculado serviço](#odata-linked-service-properties) para diferentes tipos de autenticação, você pode usar. 

    {
        "name": "ODataLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
                "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
            }
        }
    }


**Serviço de armazenamento vinculado do Azure**

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
              "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }

**Dataset de entrada de OData**

Configuração "externo": "true" informa o serviço de fábrica de dados que o conjunto de dados externo à fábrica dados e não é produzido por uma atividade na fábrica dados.
    
    {
        "name": "ODataDataset",
        "properties": 
        {
            "type": "ODataResource",
            "typeProperties": 
            {
                "path": "Products" 
            },
            "linkedServiceName": "ODataLinkedService",
            "structure": [],
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3               
            }
        }
    }

Especificando o **caminho** no dataset definição é opcional. 


**Conjunto de dados de saída do Azure Blob**

Dados são gravados em um novo blob cada hora (frequência: horas, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa ano, mês, dia e partes de horas de início.

    {
        "name": "AzureBlobODataDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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



**Pipeline com atividade de cópia**

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendado para ser executado a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **RelationalSource** e **receptor** tipo está definido como **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os últimos dados (mais recentes) da fonte de OData.
    
    {
        "name": "CopyODataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "?$select=Name, Description&$top=5",
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "ODataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobODataDataSet"
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
                    "name": "ODataToBlob"
                }
            ],
            "start": "2016-02-01T18:00:00Z",
            "end": "2016-02-03T19:00:00Z"
        }
    }


Especifica a **consulta** na definição de pipeline é opcional. A **URL** que usa o serviço de fábrica de dados para recuperar dados é: URL especificada no serviço vinculado (obrigatório) + caminho especificado no conjunto de dados (opcional) + consulta no pipeline (opcional). 

## <a name="odata-linked-service-properties"></a>OData vinculado propriedades do serviço

A tabela a seguir fornece descrição para elementos JSON específicas para o serviço OData vinculado.

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- | 
| tipo | A propriedade de tipo deve ser definida como: **OData** | Sim |
| URL| URL do serviço OData. | Sim |
| authenticationType | Tipo de autenticação usado para conectar à fonte de OData. <br/><br/> Para a nuvem OData, os valores possíveis são anônimo e Basic. Para locais de OData, os valores possíveis são anônimo, Basic e Windows. | Sim | 
| nome de usuário | Especifique o nome de usuário se você estiver usando a autenticação básica. | Sim (somente se você estiver usando a autenticação básica) | 
| senha | Especifica a senha da conta de usuário que você especificou para o nome de usuário. | Sim (somente se você estiver usando a autenticação básica) | 
| gatewayName | Nome do gateway que o serviço de dados fábrica deve usar para se conectar ao serviço OData local. Especifique somente se você estiver copiando dados de origem de OData de local. | Não |

### <a name="using-basic-authentication"></a>Usando a autenticação básica

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Basic",
                "username": "username",
               "password": "password"
           }
       }
    }

### <a name="using-anonymous-authentication"></a>Usando a autenticação anônima
    
    {
        "name": "ODataLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           }
       }
    }

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Usando a autenticação do Windows acessar local de origem de OData

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
               "authenticationType": "Windows",
                "username": "domain\\user",
               "password": "password",
               "gatewayName": "mygateway"
           }
       }
    }



## <a name="odata-dataset-type-properties"></a>Propriedades de tipo de conjunto de dados OData

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo de [conjuntos de dados criando](data-factory-create-datasets.md) . Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOB do Microsoft Azure, tabela do Microsoft Azure, etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção typeProperties de conjunto de dados do tipo **ODataResource** (que inclui o conjunto de dados OData) tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| caminho | Caminho para o recurso de OData | Não | 

## <a name="odata-copy-activity-type-properties"></a>Propriedades de tipo de atividade de cópia de OData

Para obter uma lista completa das seções & propriedades disponíveis para a definição de atividades, consulte o artigo [Criar canais](data-factory-create-pipelines.md) . Propriedades como nome, descrição, entrada e saída tabelas e política estão disponíveis para todos os tipos de atividades. 

Por outro lado, propriedades disponíveis na seção typeProperties da atividade variam com cada tipo de atividade. Atividade de cópia, eles variam dependendo dos tipos de fontes e receptores.

Quando origem for do tipo **RelationalSource** (que inclui OData) as seguintes propriedades estão disponíveis na seção de typeProperties:

| Propriedade | Descrição | Exemplo | Necessário |
| -------- | ----------- | -------------- | -------- |
| consulta | Use a consulta personalizada para ler dados. | "? $select = nome, descrição e $top = 5" | Não | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-odata"></a>Digite o mapeamento para OData

Conforme mencionado no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , atividade de cópia executa conversões de tipo automática de tipos de fonte para coletar tipos com a abordagem de 2 etapas a seguir:

1. Converter de tipos de fonte nativo para tipo .NET
2. Converter do tipo .NET para o tipo de receptor nativo

Quando armazena de movimentação de dados de dados OData, tipos de dados OData são mapeados para tipos .NET.


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Desempenho e ajuste  
Consulte o [guia de ajuste e desempenho de atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre principais fatores que desempenho de impacto de movimentação de dados (cópia atividade) em fábrica de dados do Azure e várias maneiras de otimizá-lo.

