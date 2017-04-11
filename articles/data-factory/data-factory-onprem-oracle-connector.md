<properties 
    pageTitle="Mover dados para/da Oracle usando dados Factory | Microsoft Azure" 
    description="Saiba como mover dados de/para o banco de dados Oracle que está usando o Azure dados fábrica ao local." 
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

# <a name="move-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Mover dados de/para Oracle de local usando fábrica de dados do Azure 

Este artigo descreve como você pode usar a atividade de cópia de fábrica dados para mover dados de/para Oracle outro dados de/para armazenar. Este artigo se baseia o artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com atividade de cópia e combinações de repositório de dados com suporte.

## <a name="installation"></a>Instalação 
Para o serviço de fábrica de dados do Azure sejam capazes de se conectar ao banco de dados Oracle local, você deve instalar os seguintes componentes: 

- Data Management Gateway na mesma máquina que hospeda o banco de dados ou em uma máquina separada para evitar que competem para recursos com o banco de dados. Gateway de gerenciamento de dados é um agente de cliente que se conecta a fontes de dados locais aos serviços de nuvem de maneira segura e gerenciada. Consulte o artigo [mover dados entre locais e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre o Gateway de gerenciamento de dados. 
- Provedor de dados Oracle para .NET. Este componente está incluído nos [Componentes de acesso de dados Oracle para Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Instale a versão apropriada (32/64 bits) na máquina host onde o gateway está instalado. [12.1 de .NET de provedor de dados Oracle](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) podem acessar ao banco de dados Oracle 10g versão 2 ou posterior.

    Se você escolher "XCopy instalação", siga as etapas o Leiame. htm. Recomendamos que você escolha o instalador com UI (não-XCopy um). 
 
    Depois de instalar o provedor, reinicie o serviço de hospedagem do Gateway de gerenciamento de dados em sua máquina usando serviços miniaplicativo (ou) Gerenciador de configuração do Gateway de gerenciamento de dados.  

> [AZURE.NOTE] Consulte o [gateway de solucionar problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para obter dicas sobre solução de problemas de conexão/gateway problemas relacionados. 

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil para criar um pipeline que copia dados de/para um banco de dados Oracle para qualquer um dos armazenamentos de dados com suporte receptor é usar o Assistente de dados de cópia. Consulte [Tutorial: criar um pipeline usando o Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para um rápida passo a passo sobre como criar um pipeline usando o Assistente de dados de cópia. 

O exemplo a seguir fornece definições de JSON de exemplo que você pode usar para criar um pipeline usando o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de/para um banco de dados Oracle de/para o armazenamento de Blob do Azure. No entanto, os dados podem ser copiados para qualquer um dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.   

## <a name="sample-copy-data-from-oracle-to-azure-blob"></a>Exemplo: Copiar dados do Oracle para Azure Blob
Este exemplo mostra como copiar dados de um banco de dados do Oracle local para um armazenamento de Blob do Azure. No entanto, dados podem ser copiados **diretamente** a qualquer uma dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.  
 
O exemplo tem as seguintes entidades de fábrica de dados:

1.  Um serviço vinculado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2.  Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Uma entrada [dataset](data-factory-create-datasets.md) do tipo [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties). 
4.  Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5.  Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) como origem e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) como receptor.

O exemplo copia dados de uma tabela no banco de dados Oracle local para um blob por hora. Para obter mais informações sobre várias propriedades usado no exemplo, consulte a documentação nas seções a seguir as amostras.

**Serviço de Oracle vinculado:**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Armazenamento de Blob do Azure vinculado serviço:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Oracle dataset de entrada:**

O exemplo supõe que você criou uma tabela "MinhaTabela" na Oracle e contém uma coluna chamada "timestampcolumn" para os dados da série de tempo. 

Configuração "externo": "true" informa o serviço de fábrica de dados que o conjunto de dados externo à fábrica dados e não é produzido por uma atividade na fábrica dados.

    {
        "name": "OracleInput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
               "external": true,
            "availability": {
                "offset": "01:00:00",
                "interval": "1",
                "anchorDateTime": "2014-02-27T12:00:00",
                "frequency": "Hour"
            },
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

Dados são gravados em um novo blob cada hora (frequência: horas, intervalo: 1). O nome de caminho e do arquivo de pasta para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa ano, mês, dia e partes de horas de início.
    
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


**Pipeline com atividade de cópia:**

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendada para executar a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **OracleSource** e **receptor** tipo está definido como **BlobSink**.  A consulta SQL especificada com a propriedade **oracleReaderQuery** seleciona os dados na última hora para copiar.

    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": " OracleInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
              },
              "sink": {
                "type": "BlobSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }


Você precisa ajustar a cadeia de caracteres de consulta com base em como as datas são configuradas em seu banco de dados do Oracle. Se você vir a seguinte mensagem de erro: 

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

Talvez seja necessário alterar a consulta, conforme mostrado no exemplo a seguir (usando a função to_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"

## <a name="sample-copy-data-from-azure-blob-to-oracle"></a>Exemplo: Copiar dados do Azure Blob para Oracle
Este exemplo mostra como copiar dados de um armazenamento de Blob do Azure para um banco de dados do Oracle local. No entanto, dados podem ser copiados **diretamente** de qualquer um das fontes declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.  
 
O exemplo tem as seguintes entidades de fábrica de dados:

1.  Um serviço vinculado do tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2.  Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Uma saída [dataset](data-factory-create-datasets.md) do tipo [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties). 
5.  Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) como [OracleSink](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) como receptor de origem.

O exemplo copia dados de um blob a uma tabela no banco de dados Oracle local cada hora. Para obter mais informações sobre várias propriedades usado no exemplo, consulte a documentação nas seções a seguir as amostras.

**Serviço de Oracle vinculado:**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Armazenamento de Blob do Azure vinculado serviço:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Dataset de entrada Azure Blob**

Dados são obtidos de um novo blob cada hora (frequência: horas, intervalo: 1). O nome de caminho e do arquivo de pasta para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa ano, mês e dia parte da hora de início e nome do arquivo usa a parte de hora de início. "externo": "true" configuração informa o serviço de dados fábrica que essa tabela é externa à fábrica dados e não é produzida por uma atividade na fábrica dados.

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
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
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**Conjunto de dados do Oracle saída:**

O exemplo supõe que você criou uma tabela "MinhaTabela" na Oracle. Crie a tabela no Oracle com o mesmo número de colunas, como você espera arquivo CSV de Blob para conter. Novas linhas são adicionadas à tabela de cada hora.

    {
        "name": "OracleOutput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": "1"
            }
        }
    }


**Pipeline com atividade de cópia:**

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendado para ser executado a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **BlobSource** e o tipo de **receptor** está definido como **OracleSink**.  

    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "OracleOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "OracleSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
          ]
       }
    }


## <a name="oracle-linked-service-properties"></a>Propriedades de serviço Oracle vinculado

A tabela a seguir fornece descrição para elementos JSON específicos ao serviço Oracle vinculado. 

Propriedade | Descrição | Necessário
-------- | ----------- | --------
tipo | A propriedade type deve ser definida: **OnPremisesOracle** | Sim
connectionString | Especifica as informações necessárias para se conectar à instância do banco de dados Oracle para a propriedade connectionString. | Sim 
gatewayName | Nome do gateway que é usado para conectar ao servidor Oracle local | Sim

Consulte [definir credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para obter detalhes sobre como definir credenciais para uma fonte de dados do Oracle local.
## <a name="oracle-dataset-type-properties"></a>Propriedades de tipo de conjunto de dados do Oracle

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo de [conjuntos de dados criando](data-factory-create-datasets.md) . Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Oracle, BLOB do Microsoft Azure, tabela do Microsoft Azure, etc.).
 
A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção de typeProperties para o conjunto de dados do tipo OracleTable tem as seguintes propriedades:

Propriedade | Descrição | Necessário
-------- | ----------- | --------
nome de tabela | Nome da tabela no banco de dados Oracle que o serviço vinculado se refere a. | Não (se **oracleReaderQuery** de **OracleSource** for especificado)

## <a name="oracle-copy-activity-type-properties"></a>Propriedades de tipo de atividade de cópia do Oracle

Para obter uma lista completa das seções & propriedades disponíveis para a definição de atividades, consulte o artigo [Criar canais](data-factory-create-pipelines.md) . Propriedades como nome, descrição, entrada e saída tabelas e política estão disponíveis para todos os tipos de atividades. 

> [AZURE.NOTE]
A atividade de cópia leva apenas uma entrada e produz somente uma saída.

Por outro lado, propriedades disponíveis na seção typeProperties da atividade variam com cada tipo de atividade. Atividade de cópia, eles variam dependendo dos tipos de fontes e receptores.

### <a name="oraclesource"></a>OracleSource
Atividade de cópia, quando a origem for do tipo **OracleSource** as seguintes propriedades estão disponíveis na seção de **typeProperties** :

Propriedade | Descrição |Valores permitidos | Necessário
-------- | ----------- | ------------- | --------
oracleReaderQuery | Use a consulta personalizada para ler dados. | Cadeia de caracteres de consulta SQL. Por exemplo: selecione *de MyTable <br/> <br/>se não especificado, a instrução SQL que é executada: selecione* de MyTable | Não (se o **nome de tabela** de **dataset** for especificado)

### <a name="oraclesink"></a>OracleSink
**OracleSink** compatível com as seguintes propriedades:

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
writeBatchTimeout | Tempo de espera para a operação de inserção do lote seja concluída antes que ela expire. | período de tempo<br/><br/> Exemplo: 00:30:00 (30 minutos). | Não
writeBatchSize | Insere dados na tabela SQL quando o tamanho de buffer atinge writeBatchSize.   | Número inteiro (número de linhas)| Não (padrão: 10000)  
sqlWriterCleanupScript | Especifica uma consulta para atividade de cópia executar, de forma que os dados de uma fatia específica é limpos. | Uma instrução de consulta. | Não
sliceIdentifierColumnName | Especifique o nome de coluna para atividade de cópia preencher com identificador de fatia gerado automaticamente, que é usado para limpar dados de uma fatia específico quando executar novamente. | Nome da coluna de uma coluna com tipo de dados de binary(32). | Não


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-oracle"></a>Digite o mapeamento para Oracle

Conforme mencionado na atividade de cópia do artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) executa conversões de tipo automática de tipos de fonte para coletar tipos com a abordagem de 2 etapas a seguir:

1. Converter de tipos de fonte nativo para tipo .NET
2. Converter do tipo .NET para o tipo de receptor nativo

Ao mover dados do Oracle, os seguintes mapeamentos são usados de tipo de dados do Oracle em tipo .NET e vice-versa.

Tipo de dados do Oracle | Tipo de dados do .NET framework
---------------- | ------------------------
BARQUIVO | Byte]
BLOB | Byte]
CARACT | Cadeia de caracteres
CLOB | Cadeia de caracteres
DATA | DateTime
SOBREPOR | Decimal
NÚMERO INTEIRO | Decimal
ANO DE INTERVALO PARA O MÊS | Int32
DIA DO INTERVALO EM SEGUNDO | Período de tempo
Longas | Cadeia de caracteres
LONGO BRUTA | Byte]
NCHAR | Cadeia de caracteres
NCLOB | Cadeia de caracteres
NÚMERO | Decimal
NVARCHAR2 | Cadeia de caracteres
BRUTA | Byte]
ROWID | Cadeia de caracteres
CARIMBO DE HORA | DateTime
CARIMBO DE HORA COM O FUSO HORÁRIO LOCAL | DateTime
CARIMBO DE HORA COM FUSO HORÁRIO | DateTime
INTEIRO NÃO ASSINADO | Número
VARCHAR2 | Cadeia de caracteres
XML | Cadeia de caracteres

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas

**Problema:** Você veja a seguinte **mensagem de erro**: atividade de cópia atendidas parâmetros inválidos: 'UnknownParameterName', mensagem detalhada: não é possível encontrar solicitada .net Framework provedor de dados. Ele pode não estar instalado".  

**Possíveis causas:**

1. O provedor de dados .NET Framework para Oracle não foi instalado.
2. O provedor de dados .NET Framework para Oracle foi instalado para .NET Framework 2.0 e não foi encontrado nas pastas do .NET Framework 4.0. 

**Resolução/solução alternativa:**

1. Se você ainda não tiver instalado o provedor de .NET para Oracle, [Instale-o](http://www.oracle.com/technetwork/topics/dotnet/downloads/) e tente novamente o cenário. 
2. Se você receber a mensagem de erro mesmo depois de instalar o provedor, siga estas etapas: 
    1. Abra a configuração de máquina do .NET 2.0 da pasta: <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Pesquisa para o **Provedor de dados Oracle para .NET**e você deve ser capaz de localizar uma entrada, como mostra o seguinte unwn de exemplo a seguir amostra under **system.data** -> **DbProviderFactories**:
            “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
2.  Copie essa entrada para o arquivo Machine. config na seguinte pasta v 4.0: <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config e altere a versão 4.xxx.x.x.
3.  Instalar "< caminho de instalado ODP.NET > \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll" para o cache de assembly global (GAC) executando `gacutil /i [provider path]`.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


## <a name="performance-and-tuning"></a>Desempenho e ajuste  
Consulte o [guia de ajuste e desempenho de atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre principais fatores que desempenho de impacto de movimentação de dados (cópia atividade) em fábrica de dados do Azure e várias maneiras de otimizá-lo.
