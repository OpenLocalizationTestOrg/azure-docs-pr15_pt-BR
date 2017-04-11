### <a name="type-conversion-sample"></a>Exemplo de conversão de tipo
O exemplo a seguir é para copiar dados de um Blob para SQL Azure com conversões de tipo.

Suponha que o conjunto de dados Blob está no formato CSV e contém 3 colunas. Uma delas é uma coluna de datetime com um formato de datetime personalizadas usando nomes abreviados francês para o dia da semana.

Você irá definir o conjunto de dados de origem de Blob da seguinte maneira, juntamente com as definições de tipo para as colunas.

    {
        "name": "AzureBlobTypeSystemInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability":
            {
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

Determinado o SQL tipo para tabela de mapeamento de tipo de .NET acima você define a tabela do SQL Azure com o esquema a seguir.

| Nome da coluna | Tipo de SQL |
| ----------- | -------- |
| ID de usuário | bigint |
| nome | texto |
| lastlogindate | DateTime |

Em seguida você irá definir o conjunto de dados do SQL Azure da seguinte maneira. Observação: Você não é necessário especificar seção de "estrutura" com informações de tipo, desde que o tipo de informação já está especificado no armazenamento de dados subjacente.

    {
        "name": "AzureSQLOutput",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

Nesse caso fábrica dados fará automaticamente o tipo de conversões incluindo o campo Datetime com o datetime personalizado Formatar usando a cultura fr-fr ao mover dados do Blob para SQL Azure.


