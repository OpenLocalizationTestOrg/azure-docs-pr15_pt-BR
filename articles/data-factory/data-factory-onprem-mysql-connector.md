<properties 
    pageTitle="Mover dados do MySQL | Fábrica de dados do Azure" 
    description="Saiba mais sobre como mover dados do banco de dados MySQL usando fábrica de dados do Azure." 
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

# <a name="move-data-from-mysql-using-azure-data-factory"></a>Mover dados do MySQL usando fábrica de dados do Azure

Este artigo descreve como você pode usar a atividade de cópia em uma fábrica de dados do Azure para mover dados do MySQL aos dados de outra armazenar. Este artigo se baseia o artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com atividade de cópia e combinações de repositório de dados com suporte.

Serviço de fábrica de dados suporta a conexão a fontes de MySQL locais usando o Gateway de gerenciamento de dados. Consulte o artigo de [mover dados entre locais de locais e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de gerenciamento de dados e instruções passo a passo sobre como configurar o gateway. 

> [AZURE.NOTE] Gateway é necessária, mesmo se o banco de dados MySQL estiver hospedado em uma máquina virtual do Azure IaaS (máquina virtual). Você pode instalar o gateway na mesma VM como o armazenamento de dados ou em uma máquina virtual diferente, desde que o gateway pode se conectar ao banco de dados.  

Fábrica de dados atualmente suporta somente movimentação de dados de MySQL para outros armazenamentos de dados, mas não para mover dados de outras fontes de dados para MySQL.

## <a name="installation"></a>Instalação 
Para o Gateway de gerenciamento de dados para se conectar ao banco de dados MySQL, você precisa instalar o [MySQL Connector/líquido 6.6.5 para o Microsoft Windows](http://go.microsoft.com/fwlink/?LinkId=278885) no mesmo sistema como o Gateway de gerenciamento de dados.

> [AZURE.NOTE] Consulte o [gateway de solucionar problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para obter dicas sobre solução de problemas de conexão/gateway problemas relacionados. 

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil para criar um pipeline que copia dados de um banco de dados MySQL para qualquer um dos armazenamentos de dados com suporte receptor é usar o Assistente de dados de cópia. Consulte [Tutorial: criar um pipeline usando o Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para um rápida passo a passo sobre como criar um pipeline usando o Assistente de dados de cópia. 

O exemplo a seguir fornece definições de JSON de exemplo que você pode usar para criar um pipeline. Para obter uma explicação completa com instruções passo a passo, consulte o artigo de [mover dados entre locais de local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) . Dados podem ser copiados para qualquer um dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.   

## <a name="sample-copy-data-from-mysql-to-azure-blob"></a>Exemplo: Copiar dados de MySQL para Azure Blob
Este exemplo mostra como copiar dados de um banco de dados MySQL local para um armazenamento de Blob do Azure. No entanto, dados podem ser copiados **diretamente** a qualquer uma dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.  

> [AZURE.IMPORTANT] Este exemplo fornece trechos JSON. Ele não inclui instruções passo a passo para criar a fábrica de dados. Consulte o artigo de [mover dados entre locais de locais e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo. 
 
O exemplo tem as seguintes entidades de fábrica de dados:

1.  Um serviço vinculado do tipo [OnPremisesMySql](data-factory-onprem-mysql-connector.md#mysql-linked-service-properties).
2.  Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Uma entrada [dataset](data-factory-create-datasets.md) do tipo [RelationalTable](data-factory-onprem-mysql-connector.md#mysql-dataset-type-properties).
4.  Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](data-factory-onprem-mysql-connector.md#mysql-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de um resultado de consulta no banco de dados MySQL para um blob por hora. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras. 

A primeira etapa, configure o gateway de gerenciamento de dados. As instruções são no artigo [mover dados entre locais de local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) . 

**Serviço MySQL vinculado**

    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
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

**Conjunto de dados de entrada do MySQL**

O exemplo supõe que você criou uma tabela "MinhaTabela" na MySQL e contém uma coluna chamada "timestampcolumn" para os dados da série de tempo.

Configuração "externo": "true" informa o serviço de fábrica de dados que a tabela é externa à fábrica dados e não é produzida por uma atividade na fábrica dados.
    
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
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



**Conjunto de dados de saída do Azure Blob**

Dados são gravados em um novo blob cada hora (frequência: horas, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa ano, mês, dia e partes de horas de início.

    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendado para ser executado a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **RelationalSource** e **receptor** tipo está definido como **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados na última hora para copiar.
    
    {
        "name": "CopyMySqlToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
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
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="mysql-linked-service-properties"></a>Propriedades de serviço de MySQL vinculado

A tabela a seguir fornece descrição para elementos JSON específicos para serviço MySQL vinculado.

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- | 
| tipo | A propriedade type deve ser definida: **OnPremisesMySql** | Sim |
| servidor | Nome do servidor MySQL. | Sim |
| banco de dados | Nome do banco de dados MySQL. | Sim | 
| esquema  | Nome do esquema no banco de dados. | Não | 
| authenticationType | Tipo de autenticação usado para conectar ao banco de dados MySQL. Valores possíveis são: anônima, básica e Windows. | Sim | 
| nome de usuário | Especifique o nome de usuário se você estiver usando a autenticação básica ou Windows. | Não | 
| senha | Especifica a senha da conta de usuário que você especificou para o nome de usuário. | Não | 
| gatewayName | Nome do gateway que o serviço de dados fábrica deve usar para se conectar ao banco de dados MySQL local. | Sim |

Consulte [definir credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para obter detalhes sobre como definir credenciais para uma fonte de dados MySQL local.

## <a name="mysql-dataset-type-properties"></a>Propriedades de tipo de conjunto de dados MySQL

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo de [conjuntos de dados criando](data-factory-create-datasets.md) . Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOB do Microsoft Azure, tabela do Microsoft Azure, etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção typeProperties de conjunto de dados do tipo **RelationalTable** (que inclui o conjunto de dados MySQL) tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| nome de tabela | Nome da tabela na instância do banco de dados MySQL vinculado serviço se refere. | Não (se a **consulta** de **RelationalSource** for especificada) | 

## <a name="mysql-copy-activity-type-properties"></a>Propriedades de tipo de atividade de cópia MySQL

Para obter uma lista completa das seções & propriedades disponíveis para a definição de atividades, consulte o artigo [Criar canais](data-factory-create-pipelines.md) . Propriedades como nome, descrição e tabelas de entrada e saídas, são diretivas estão disponíveis para todos os tipos de atividades. 

Por outro lado, propriedades disponíveis na seção **typeProperties** da atividade variam com cada tipo de atividade. Atividade de cópia, eles variam dependendo dos tipos de fontes e receptores.

Quando fonte na atividade de cópia é do tipo **RelationalSource** (que inclui MySQL) as seguintes propriedades estão disponíveis na seção de typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- |
| consulta | Use a consulta personalizada para ler dados. | Cadeia de caracteres de consulta SQL. Por exemplo: selecionar * de MyTable. | Não (se o **nome de tabela** de **dataset** for especificado) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-mysql"></a>Digite o mapeamento para MySQL

Conforme mencionado no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , atividade de cópia executa conversões de tipo automática de tipos de fonte para coletar tipos com a abordagem de duas etapas a seguir:

1. Converter de tipos de fonte nativo para tipo .NET
2. Converter do tipo .NET para o tipo de receptor nativo

Ao mover dados para MySQL, os seguintes mapeamentos são usados de tipos de MySQL para tipos .NET.

| Tipo de banco de dados MySQL | Tipo .NET framework |
| ------------------- | ------------------- | 
| bigint não assinado | Decimal |
| bigint | Int64 |
| bit | Decimal |
| blob | Byte] |
| bool |  Booliano | 
| CARACT | Cadeia de caracteres |
| Data | DateTime |
| DateTime | DateTime |
| decimal | Decimal |
| precisão dupla | Duplo |
| duplo | Duplo |
| enumeração | Cadeia de caracteres |
| flutuação | Único |
| int não assinado | Int64 |
| int | Int32 |
| inteiro não assinado | Int64 |
| número inteiro | Int32 | 
| varbinary longo | Byte] |
| varchar longo | Cadeia de caracteres |
| longblob | Byte] |
| LONGTEXT | Cadeia de caracteres | 
| mediumblob |  Byte] | 
| mediumint não assinado | Int64 |
| mediumint | Int32 | 
| mediumtext | Cadeia de caracteres |
| numérico | Decimal |
| real |  Duplo |
| definir | Cadeia de caracteres |
| smallint não assinado | Int32 |
| smallint | Int16 |
| texto | Cadeia de caracteres |
| tempo | Período de tempo |
| carimbo de hora | DateTime |
| tinyblob | Byte] |
| tinyint não assinado |  Int16 | 
| tinyint | Int16 |
| tinytext | Cadeia de caracteres | 
| varchar | Cadeia de caracteres | 
| ano | Int | 


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Desempenho e ajuste  
Consulte o [guia de ajuste e desempenho de atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre principais fatores que desempenho de impacto de movimentação de dados (cópia atividade) em fábrica de dados do Azure e várias maneiras de otimizá-lo.

