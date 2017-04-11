<properties 
    pageTitle="Mover dados do Cassandra usando dados Factory | Microsoft Azure" 
    description="Saiba mais sobre como mover dados de um banco de dados local Cassandra usando fábrica de dados do Azure." 
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
    ms.date="09/07/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Mover dados de um banco de dados local Cassandra usando fábrica de dados do Azure
Este artigo descreve como você pode usar a atividade de cópia em uma fábrica de dados do Azure para copiar dados de um banco de dados local Cassandra para qualquer armazenamento de dados listado na coluna receptor na seção [fontes de suporte e receptores](data-factory-data-movement-activities.md#supported-data-stores) . Este artigo se baseia o artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral de movimentação de dados com atividade de cópia e combinações de repositório de dados com suporte.

Fábrica de dados atualmente suporta somente movimentação de dados de um banco de dados de Cassandra suportados [receptor armazenamentos de dados](data-factory-data-movement-activities.md#supported-data-stores), mas não mover os dados de outras fontes de dados para um banco de dados de Cassandra.

## <a name="prerequisites"></a>Pré-requisitos
Para o serviço de fábrica de dados do Azure sejam capazes de se conectar ao seu banco de dados local Cassandra, você deve instalar o seguinte: 

- Gateway de gerenciamento de dados 2.0 ou acima na mesma máquina que hospeda o banco de dados ou em uma máquina separada para evitar que competem para recursos com o banco de dados. Gateway de gerenciamento de dados é um software que se conecta a fontes de dados locais aos serviços de nuvem de maneira segura e gerenciada. Consulte o artigo [mover dados entre locais e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre o Gateway de gerenciamento de dados.
  
    Quando você instala o gateway, ele instala automaticamente um driver ODBC do Microsoft Cassandra usado para conectar ao banco de dados de Cassandra. 

> [AZURE.NOTE] Consulte o [gateway de solucionar problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para obter dicas sobre solução de problemas de conexão/gateway problemas relacionados. 

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil para criar um pipeline que copia dados de um banco de dados de Cassandra para qualquer um dos armazenamentos de dados com suporte receptor é usar o Assistente de dados de cópia. Consulte [Tutorial: criar um pipeline usando o Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para um rápida passo a passo sobre como criar um pipeline usando o Assistente de dados de cópia. 

O exemplo a seguir fornece definições de JSON de exemplo que você pode usar para criar um pipeline usando o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de banco de dados de Cassandra ao armazenamento de Blob do Azure. No entanto, os dados podem ser copiados para qualquer um dos receptores declarado [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia em fábrica de dados do Azure.   


## <a name="sample-copy-data-from-cassandra-to-blob"></a>Exemplo: Copiar dados de Cassandra para Blob
O exemplo copia dados de um banco de dados de Cassandra para um Azure blob cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções seguindo as amostras. Dados podem ser copiados diretamente para qualquer um dos receptores de consta no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia no Azure dados fábrica. 

- Um serviço vinculado do tipo [OnPremisesCassandra](#onpremisescassandra-linked-service-properties).
- Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Uma entrada [dataset](data-factory-create-datasets.md) do tipo [CassandraTable](#cassandratable-properties).
- Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [CassandraSource](#cassandrasource-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

**Serviço de Cassandra vinculado**

Este exemplo usa o serviço de **Cassandra** vinculado. Consulte a seção [Cassandra vinculado serviço](#onpremisescassandra-linked-service-properties) para as propriedades suportadas por esse serviço vinculado.  

    {
        "name": "CassandraLinkedService",
        "properties":
        {
            "type": "OnPremisesCassandra",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "host": "mycassandraserver",
                "port": 9042,
                "username": "user",
                "password": "password",
                "gatewayName": "mygateway"
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

**Cassandra dataset de entrada**

    {
        "name": "CassandraInput",
        "properties": {
            "linkedServiceName": "CassandraLinkedService",
            "type": "CassandraTable",
            "typeProperties": {
                "tableName": "mytable",
                "keySpace": "mykeyspace" 
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

Configuração **externos** como **true** informa o serviço de fábrica de dados que o conjunto de dados externo à fábrica dados e não é produzido por uma atividade na fábrica dados.

**Conjunto de dados de saída do Azure Blob**

Dados são gravados em um novo blob cada hora (frequência: horas, intervalo: 1). 

    {
        "name": "AzureBlobOutput",
        "properties":
        {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties":
            {
                "folderPath": "adfgetstarted/fromcassandra"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**Pipeline com atividade de cópia**

O pipeline contém uma atividade de cópia que está configurado para usar os conjuntos de dados de entrada e saídos e está agendado para ser executado a cada hora. No pipeline de definição de JSON, o tipo de **fonte** é definido como **CassandraSource** e **receptor** tipo está definido como **BlobSink**. 

Consulte [Propriedades de tipo de RelationalSource](#cassandrasource-type-properties) para a lista de propriedades compatíveis com o RelationalSource. 
    
    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2016-06-01T18:00:00",
            "end":"2016-06-01T19:00:00",
            "description":"pipeline with copy activity",
            "activities":[  
            {
                "name": "CassandraToAzureBlob",
                "description": "Copy from Cassandra to an Azure blob",
                "type": "Copy",
                "inputs": [
                {
                    "name": "CassandraInput"
                }
                ],
                "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
                ],
                "typeProperties": {
                    "source": {
                        "type": "CassandraSource",
                        "query": "select id, firstname, lastname from mykeyspace.mytable"
        
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
## <a name="onpremisescassandra-linked-service-properties"></a>Propriedades do serviço de OnPremisesCassandra vinculado

A tabela a seguir fornece descrição para elementos JSON específicos ao serviço de Cassandra vinculado.

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- | 
| tipo | A propriedade type deve ser definida: **OnPremisesCassandra** | Sim |
| host | Um ou mais endereços IP e nomes de host de servidores de Cassandra.<br/><br/>Especifique uma lista separada por vírgulas de endereços IP ou nomes de host para conectar-se a todos os servidores simultaneamente. | Sim | 
| porta | A porta TCP que o servidor de Cassandra usa para escutar conexões de cliente. | Não, valor padrão: 9042 |
| authenticationType | Básica ou anônima | Sim |
| nome de usuário |Especifique o nome de usuário da conta de usuário. | Sim, se authenticationType está definido como Basic. |
| senha | Especifica a senha da conta de usuário.  | Sim, se authenticationType está definido como Basic. |
| gatewayName | O nome do gateway que é usado para conectar ao banco de dados Cassandra local. | Sim |
| encryptedCredential | Credencial criptografada pelo gateway. | Não | 

## <a name="cassandratable-properties"></a>Propriedades de CassandraTable

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo de [conjuntos de dados criando](data-factory-create-datasets.md) . Seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOB do Microsoft Azure, tabela do Microsoft Azure, etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção typeProperties de conjunto de dados do tipo **CassandraTable** tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| keyspace | Nome do esquema de banco de dados de Cassandra ou keyspace. | Sim (se a **consulta** para **CassandraSource** não está definida). |
| nome de tabela | Nome da tabela no banco de dados de Cassandra. | Sim (se a **consulta** para **CassandraSource** não está definida). |


## <a name="cassandrasource-type-properties"></a>Propriedades de tipo de CassandraSource
Para obter uma lista completa das seções & propriedades disponíveis para a definição de atividades, consulte o artigo [Criar canais](data-factory-create-pipelines.md) . Propriedades como nome, descrição, entrada e saída tabelas e política estão disponíveis para todos os tipos de atividades. 

Por outro lado, propriedades disponíveis na seção typeProperties da atividade variam com cada tipo de atividade. Atividade de cópia, eles variam dependendo dos tipos de fontes e receptores.

Quando a origem for do tipo **CassandraSource**, as seguintes propriedades estão disponíveis na seção de typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- |
| consulta | Use a consulta personalizada para ler dados. | Consulta SQL-92 ou uma consulta CQL. Consulte a [referência CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao usar a consulta SQL, especifique **keyspace name.table nome** para representar a tabela que você deseja consultar. | Não (se o nome de tabela e keyspace no conjunto de dados são definidos).  |
| consistencyLevel | O nível de consistência Especifica quantos réplicas devem responder a uma solicitação de leitura antes de retornar dados para o aplicativo cliente. Cassandra verifica o número especificado de réplicas para dados para atender a solicitação de leitura. | UM, DOIS, TRÊS, QUORUM, TODOS, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Consulte [Configurando a consistência de dados](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) para obter detalhes. | Não. Valor padrão é um. |  


### <a name="type-mapping-for-cassandra"></a>Digite o mapeamento para Cassandra
Tipo de Cassandra | .NET com base em tipo
--------------- | ---------------
ASCII | Cadeia de caracteres 
BIGINT | Int64
BLOB | Byte]
BOOLIANO | Booliano
DECIMAL | Decimal
DUPLO | Duplo
SOBREPOR | Único
INET | Cadeia de caracteres
INT | Int32
TEXTO | Cadeia de caracteres
CARIMBO DE HORA | DateTime
TIMEUUID | GUID
UUID | GUID
VARCHAR | Cadeia de caracteres
VARINT | Decimal

> [AZURE.NOTE]  
> Conjunto de tipos (mapa, conjunto, lista, etc.), consultem a seção de [trabalhar com tipos de coleção de Cassandra usando tabela virtual](#work-with-collections-using-virtual-table) . 
> 
> Tipos definidos pelo usuário não são suportados.
> 
> O comprimento de comprimentos de coluna binária e cadeia de caracteres não pode ser maior do que 4000. 

## <a name="work-with-collections-using-virtual-table"></a>Trabalhar com conjuntos usando tabela virtual
Azure Data Factory usa um driver ODBC interno conectem e copie os dados de seu banco de dados de Cassandra. Para tipos de coleção incluindo map, definir e lista, o driver novamente normalizará os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver quaisquer colunas de conjunto, o driver gera virtuais tabelas a seguir:

-   Uma **tabela base**, que contém os mesmos dados como a tabela real, exceto para as colunas do conjunto. A tabela base usa o mesmo nome como a tabela real que ele representa.
-   Uma **tabela virtual** para cada coluna conjunto, que se expande os dados aninhados. As tabelas virtuais que representam coleções são nomeadas usando o nome da tabela real, um separador "_vt_" e o nome da coluna.

Tabelas virtuais referem-se aos dados na tabela real, ativando o driver acessar os dados desordenados. Consulte a seção de exemplo para obter detalhes. Você pode acessar o conteúdo dos conjuntos de Cassandra consultando e associar as tabelas virtuais.

Você pode aproveitar o [Assistente de cópia](data-factory-data-movement-activities.md#data-factory-copy-wizard) para intuitiva exibir a lista de tabelas no banco de dados de Cassandra incluindo as tabelas virtuais e visualizar os dados dentro. Você também pode criar uma consulta no Assistente de cópia e validar para ver o resultado.

### <a name="example"></a>Exemplo
Por exemplo, a seguinte "ExampleTable" é uma tabela de banco de dados de Cassandra que contém uma coluna de chave primária da inteiro denominada "pk_int", uma coluna de texto valor nomeado, uma coluna de lista, uma coluna de mapa e uma coluna de conjunto (chamado de "StringSet").

pk_int | Valor | Lista | Mapa |   StringSet
------ | ----- | ---- | --- | --------
1 | "valor de amostra 1" | ["1", "2", "3"]  | {"S1": "a", "S2": "b"} | {"A", "B", "C"}
3 | "valor de exemplo 3" | ["100", "101", "102", "105"] | {"S1": "t"} | {"A", "E"}

O driver seria gerar várias tabelas virtuais para representar esta tabela única. As colunas de chave estrangeiras nas tabelas virtuais fazem referência as colunas de chave primária na tabela real e indicam qual linha de tabela real da linha da tabela virtual corresponde ao. 

A primeira tabela virtual é a tabela base chamada "ExampleTable" é mostrada na tabela a seguir. A tabela base contém os mesmos dados da tabela de banco de dados original, exceto para as coleções de arquivos, que são omitidos nesta tabela e expandido em outras tabelas virtuais.

pk_int | Valor
------ | -----
1 | "valor de amostra 1"
3 | "valor de exemplo 3"

As tabelas a seguir mostram as tabelas virtuais que renormalize os dados das colunas de lista, mapa e StringSet. As colunas com nomes que terminam com index"ou"_key"indicam a posição dos dados dentro da lista ou mapa original. As colunas com nomes que terminam com Value"contêm os dados expandidos da coleção.

#### <a name="table-exampletablevtlist"></a>Tabela "ExampleTable_vt_List":
pk_int | List_index | List_value
------ | ---------- | ----------
1 | 0 | 1
1 | 1 | 2
1 | 2 | 3
3 | 0 | 100
3 | 1 | 101
3 | 2 | 102
3 | 3 | 103

#### <a name="table-exampletablevtmap"></a>Tabela "ExampleTable_vt_Map":
pk_int | Map_key | Map_value
------ | ------- | ---------
1 | S1 | R
1 | S2 | b
3 | S1 | t

#### <a name="table-exampletablevtstringset"></a>Tabela "ExampleTable_vt_StringSet":
pk_int | StringSet_value
------ | ---------------
1 | R
1 | B
1 | C
3 | R
3 | E





[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Desempenho e ajuste  
Consulte o [guia de ajuste e desempenho de atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre principais fatores que desempenho de impacto de movimentação de dados (cópia atividade) em fábrica de dados do Azure e várias maneiras de otimizá-lo.
