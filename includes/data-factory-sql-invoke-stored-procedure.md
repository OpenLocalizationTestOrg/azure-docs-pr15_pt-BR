## <a name="invoking-stored-procedure-for-sql-sink"></a>Chamar o procedimento armazenado do SQL PIA

Ao copiar dados para o SQL Server ou banco de dados do Azure SQL do SQL Server, um usuário especificado procedimento armazenado pode ser configurado e invocado com parâmetros adicionais. 

Um procedimento armazenado pode ser utilizado quando mecanismos de cópia interno não têm a finalidade. Isso geralmente é utilizado ao processar extra (mesclagem colunas, procurando valores adicionais, inserção em várias tabelas...) precisa ser feito antes de inserção final dos dados de origem na tabela de destino. 

Você pode chamar um procedimento armazenado de escolha. O exemplo a seguir mostra como usar um procedimento armazenado para fazer uma inserção simple em uma tabela no banco de dados. 

**Conjunto de dados de saída**

Neste exemplo, tipo está definido como: SqlServerTable. Configurá-lo para AzureSqlTable para usar com um banco de dados do SQL Azure. 

    {
      "name": "SqlOutput",
      "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlLinkedService",
        "typeProperties": {
          "tableName": "Marketing"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    
Defina a seção de SqlSink de atividade de cópia JSON da seguinte maneira. Para chamar um procedimento armazenado ao inserir dados, propriedades SqlWriterStoredProcedureName e SqlWriterTableType são necessários.

    "sink":
    {
        "type": "SqlSink",
        "SqlWriterTableType": "MarketingType",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
        "storedProcedureParameters":
                {
                    "stringData": 
                    {
                        "value": "str1"     
                    }
                }
    }

No seu banco de dados, defina o procedimento armazenado com o mesmo nome como SqlWriterStoredProcedureName. Ela lida com dados de entrada de sua fonte especificada e inserir na tabela de saída. Observe que o nome do parâmetro do procedimento armazenado deve ser o mesmo que o nome de tabela definido no arquivo de JSON de tabela.

    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
    AS
    BEGIN
        DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
        INSERT [dbo].[Marketing](ProfileID, State)
        SELECT * FROM @Marketing
    END

No seu banco de dados, defina o tipo de tabela com o mesmo nome como SqlWriterTableType. Observe que o esquema do tipo tabela deve ser igual o esquema retornado por seus dados de entrada.

    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL
    )

O recurso de procedimento armazenado tira proveito dos [Parâmetros de Table-Valued](https://msdn.microsoft.com/library/bb675163.aspx).