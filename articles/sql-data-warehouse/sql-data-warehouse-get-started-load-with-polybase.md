<properties
   pageTitle="PolyBase tutorial de depósito de dados do SQL | Microsoft Azure"
   description="Saiba o que é PolyBase e como usá-lo para data warehouse cenários."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/10/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-with-polybase-in-sql-data-warehouse"></a>Carregar os dados com PolyBase no depósito de dados do SQL

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)  
- [Fábrica de dados](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
- [BCP](sql-data-warehouse-load-with-bcp.md)

Este tutorial mostra como carregar dados em SQL Data Warehouse usando AzCopy e PolyBase. Quando terminar, você saberá como:

- Usar AzCopy para copiar dados para o armazenamento de blob do Microsoft Azure
- Criar objetos de banco de dados para definir os dados
- Executar uma consulta de T-SQL para carregar os dados

>[AZURE.VIDEO loading-data-with-polybase-in-azure-sql-data-warehouse]

## <a name="prerequisites"></a>Pré-requisitos

Para percorrer neste tutorial, você precisa de:

- Um banco de dados do SQL Data Warehouse.
- Uma conta de armazenamento do Azure do tipo padrão localmente armazenamento redundante (padrão-LRS), armazenamento de localização geográfica redundante padrão (padrão-GRS) ou armazenamento de localização geográfica redundante de acesso de leitura padrão (padrão-RAGRS).
- Utilitário de linha de comando AzCopy. Baixe e instale a [versão mais recente do AzCopy][] que é instalado com as ferramentas de armazenamento do Microsoft Azure.

    ![Ferramentas de armazenamento do Azure](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)


## <a name="step-1-add-sample-data-to-azure-blob-storage"></a>Etapa 1: Adicionar dados de exemplo para o armazenamento de blob do Microsoft Azure

Para carregar dados, precisamos colocar alguns dados de exemplo em um armazenamento de blob do Microsoft Azure. Nesta etapa, podemos preencher um blob de armazenamento do Azure com dados de exemplo. Posteriormente, usaremos PolyBase para carregar esses dados de exemplo no seu banco de dados do SQL Data Warehouse.

### <a name="a-prepare-a-sample-text-file"></a>R. Preparar um arquivo de texto de exemplo

Para preparar um arquivo de texto de exemplo:

1. Abra o bloco de notas e copie as seguintes linhas de dados para um novo arquivo. Salve este diretório temp local como % temp%\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### <a name="b-find-your-blob-service-endpoint"></a>B. Localizar seu ponto de extremidade do serviço de blob

Para localizar seu ponto de extremidade do serviço blob:

1. No Portal do Azure, selecione **Procurar** > **Contas de armazenamento**.
2. Clique na conta de armazenamento que você deseja usar.
3. Na lâmina de conta de armazenamento, clique em Blobs

    ![Clique em Blobs](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)

1. Salve sua URL de ponto de extremidade do serviço de blob para mais tarde.

    ![Ponto de extremidade de serviço de blob](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### <a name="c-find-your-azure-storage-key"></a>C. Encontrar sua chave de armazenamento do Azure

Para encontrar sua chave de armazenamento do Azure:

1. No Portal do Azure, selecione **Procurar** > **Contas de armazenamento**.
2. Clique na conta de armazenamento que você deseja usar.
3. Selecione **todas as configurações** > **teclas de acesso**.
4. Clique na caixa Copiar para copiar uma das suas chaves de acesso para a área de transferência.

    ![Copiar chave de armazenamento do Azure](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### <a name="d-copy-the-sample-file-to-azure-blob-storage"></a>D. Copie o arquivo de amostra para armazenamento de blob do Microsoft Azure

Para copiar os dados para o armazenamento de blob do Microsoft Azure:

1. Abra um prompt de comando e altere os diretórios para o diretório de instalação AzCopy. Este comando é alterado para a pasta de instalação padrão de um cliente do Windows de 64 bits.

    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```

1. Execute o seguinte comando para carregar o arquivo. Especifique a URL do ponto de extremidade do serviço de blob para <blob service endpoint URL> e sua chave da conta de armazenamento do Azure para < azure_storage_account_key >.

    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

Consulte também [está começando a usar o utilitário de linha de comando AzCopy][].

### <a name="e-explore-your-blob-storage-container"></a>E. Explorar o contêiner de armazenamento de blob

Para ver o arquivo carregado ao armazenamento de blob:

1. Volte para seu lâmina de serviço Blob.
2. Em contêineres, clique duas vezes em **datacontainer**.
3. Para explorar o caminho aos seus dados, clique na pasta **datedimension** e você verá seu arquivo carregado **DimDate2.txt**.
4. Para exibir propriedades, clique em **DimDate2.txt**.
5. Observe que, na lâmina Blob propriedades, você pode baixar ou exclua o arquivo.

    ![Blob de armazenamento do Azure do modo de exibição](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)


## <a name="step-2-create-an-external-table-for-the-sample-data"></a>Etapa 2: Criar uma tabela externa para dados de exemplo

Nesta seção, podemos criar uma tabela externa que define os dados de exemplo.

PolyBase usa tabelas externas para acessar dados em um armazenamento de blob do Microsoft Azure. Como os dados não são armazenados no depósito de dados do SQL, PolyBase controla a autenticação aos dados externos usando uma credencial de escopo do banco de dados.

O exemplo nesta etapa usa estas instruções Transact-SQL para criar uma tabela externa.

- [Criar chave mestre (Transact-SQL)][] para criptografar o segredo do seu banco de dados com escopo credencial.
- [Criar banco de dados com escopo credencial (Transact-SQL)][] para especificar as informações de autenticação para a sua conta de armazenamento do Azure.
- [Criar fonte de dados externos (Transact-SQL)][] para especificar o local do seu armazenamento de blob do Microsoft Azure.
- [Criar arquivo formato externo (Transact-SQL)][] para especificar o formato dos dados.
- [Criar tabela externa (Transact-SQL)][] para especificar a definição de tabela e a localização dos dados.

Execute essa consulta em seu banco de dados do SQL Data Warehouse. Ele criará uma tabela externa denominada DimDate2External no esquema dbo que aponta para os dados de exemplo DimDate2.txt no armazenamento de blob do Microsoft Azure.


```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);


-- D: Create an external file format
-- FORMAT_TYPE: Type of file format in Azure storage (supported: DELIMITEDTEXT, RCFILE, ORC, PARQUET).
-- FORMAT_OPTIONS: Specify field terminator, string delimiter, date format etc. for delimited text files.
-- Specify DATA_COMPRESSION method if data is compressed.

CREATE EXTERNAL FILE FORMAT TextFile
WITH (
    FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify column names and data types. This needs to match the data in the sample file.
-- LOCATION: Specify path to file or directory that contains the data (relative to the blob container).
-- To point to all files under the blob container, use LOCATION='.'

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='/datedimension/',
    DATA_SOURCE=AzureStorage,
    FILE_FORMAT=TextFile
);


-- Run a query on the external table

SELECT count(*) FROM dbo.DimDate2External;

```


No SQL Server Object Explorer no Visual Studio, você pode ver o formato de arquivo externo, fonte de dados externa e a tabela de DimDate2External.

![Tabela externa do modo de exibição](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## <a name="step-3-load-data-into-sql-data-warehouse"></a>Etapa 3: Carregar dados em um depósito de dados do SQL

Depois que a tabela externa é criada, você pode carregar os dados em uma nova tabela ou inseri-lo em uma tabela existente.

- Para carregar os dados em uma nova tabela, execute a instrução [Criar tabela como selecionar (Transact-SQL)][] . A nova tabela terá coluna nomeada na consulta. Os tipos de dados das colunas correspondem os tipos de dados na definição de tabela externa.
- Para carregar os dados em uma tabela existente, use o [INSERT... Selecione (Transact-SQL)][] instrução.

```sql
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT * FROM [dbo].[DimDate2External];
```

## <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Etapa 4: Criar estatísticas em seus dados carregados recentemente

SQL Data Warehouse não criação automática ou estatísticas a atualização automática. Portanto, para atingir o desempenho de consulta alta, é importante criar estatísticas em cada coluna de cada tabela após o carregamento primeiro. Também é importante atualizar estatísticas após alterações substanciais nos dados.

Este exemplo cria estatísticas de coluna única na nova tabela DimDate2.

```sql
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
CREATE STATISTICS [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

Para saber mais, consulte [estatísticas][].  


## <a name="next-steps"></a>Próximas etapas
Consulte o [guia de PolyBase][] para obter mais informações que você deve saber como você desenvolve uma solução que usa PolyBase.

<!--Image references-->


<!--Article references-->
[PolyBase in SQL Data Warehouse Tutorial]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Guia de PolyBase]: ./sql-data-warehouse-load-polybase-guide.md
[Primeiros passos com o utilitário de linha de comando AzCopy]: ../storage/storage-use-azcopy.md
[versão mais recente do AzCopy]: ../storage/storage-use-azcopy.md

<!--External references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[Criar fonte de dados externa (Transact-SQL)]:https://msdn.microsoft.com/library/dn935022.aspx
[CRIAR um formato de arquivo externo (Transact-SQL)]:https://msdn.microsoft.com/library/dn935026.aspx
[Criar tabela externa (Transact-SQL)]:https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/mt130698.aspx

[Criar tabela como selecionar (Transact-SQL)]:https://msdn.microsoft.com/library/mt204041.aspx
[INSERIR... Selecione (Transact-SQL)]:https://msdn.microsoft.com/library/ms174335.aspx
[CRIAR chave mestre (Transact-SQL)]:https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189522.aspx
[Criar CREDENCIAL de escopo do banco de dados (Transact-SQL)]:https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189450.aspx
