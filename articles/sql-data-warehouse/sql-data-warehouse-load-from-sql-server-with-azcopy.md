<properties
   pageTitle="Carregar dados do SQL Server em um depósito de dados do SQL Azure (PolyBase) | Microsoft Azure"
   description="Usa bcp para exportar dados do SQL Server para arquivos simples, AZCopy para importar dados para o armazenamento de blob do Microsoft Azure e PolyBase para receber os dados em depósito de dados do SQL Azure."
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
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-azcopy"></a>Carregar dados do SQL Server em um depósito de dados do SQL Azure (AZCopy)

Use bcp e utilitários de linha de comando AZCopy para carregar dados do SQL Server para armazenamento de blob do Microsoft Azure. Use PolyBase ou fábrica de dados do Azure para carregar os dados em um depósito de dados do SQL Azure. 


## <a name="prerequisites"></a>Pré-requisitos

Para percorrer neste tutorial, você precisa:

- Um banco de dados do SQL Data Warehouse
- O utilitário de linha de comando bcp instalado
- O utilitário de linha de comando SQLCMD instalado

>[AZURE.NOTE] Você pode baixar os utilitários bcp e sqlcmd no [Centro de Download da Microsoft][].

## <a name="import-data-into-sql-data-warehouse"></a>Importar dados para o SQL Data Warehouse

Neste tutorial, você irá criar uma tabela no depósito de dados do SQL Azure e importar dados para a tabela.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Etapa 1: Criar uma tabela no depósito de dados do SQL Azure

Em um prompt de comando, use sqlcmd para executar a consulta a seguir para criar uma tabela em sua instância:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

>[AZURE.NOTE] Consulte [Visão geral sobre Table][] ou [CREATE TABLE sintaxe][] para obter mais informações sobre como criar uma tabela no SQL Data Warehouse e as opções disponíveis na cláusula WITH.

### <a name="step-2-create-a-source-data-file"></a>Etapa 2: Criar um arquivo de dados de origem

Abra o bloco de notas e copie as seguintes linhas de dados para um novo arquivo de texto e, em seguida, salve esse arquivo em seu diretório temp local, C:\Temp\DimDate2.txt.

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

> [AZURE.NOTE] É importante lembrar que bcp.exe não oferece suporte a codificação UTF-8 do arquivo. Use arquivos ASCII ou UTF-16 codificado ao usar bcp.exe.

### <a name="step-3-connect-and-import-the-data"></a>Etapa 3: Conectar e importar os dados
Usando o bcp, você pode conectar e importar os dados usando o seguinte comando substituindo os valores conforme apropriado:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Você pode verificar que os dados foi carregados executando a seguinte consulta usando sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Isso deve retornar os seguintes resultados:

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Etapa 4: Criar estatísticas em seus dados carregados recentemente

Depósito de dados SQL Azure faz ainda não suporte automático criar ou automático estatísticas de atualização. Para obter o melhor desempenho de suas consultas, é importante que estatísticas de ser criada sobre todas as colunas de todas as tabelas após o carregamento primeiro ou quaisquer alterações substanciais ocorrerem nos dados. Para obter uma explicação detalhada das estatísticas, consulte o tópico de [estatísticas][] no grupo desenvolver de tópicos. Abaixo está um exemplo de como criar estatísticas sobre o em tabelas carregado neste exemplo rápido

Execute as seguintes instruções de criar estatísticas de um prompt de sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Exportar dados do SQL Data Warehouse
Neste tutorial, você criará um arquivo de dados de uma tabela no SQL Data Warehouse. Podemos irá exportar os dados que criamos acima para um novo arquivo de dados chamado DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Etapa 1: Exportar os dados

Usando o utilitário bcp, você pode se conectar e exportar dados usando o seguinte comando substituindo os valores conforme apropriado:

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Você pode confirmar que os dados foram exportados corretamente abrindo o novo arquivo. Os dados no arquivo devem coincidir o texto abaixo:

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

>[AZURE.NOTE] Devido à natureza dos sistemas distribuídos, a ordem de dados pode não estar o mesmo em bancos de dados do SQL Data Warehouse. Outra opção é usar a função de **queryout** de bcp para gravar extrair uma consulta em vez de exportar a tabela inteira.

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral de carregamento, consulte [carregar dados para SQL Data Warehouse][].
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento de depósito de dados do SQL][].

<!--Image references-->

<!--Article references-->

[Carregar dados em um depósito de dados do SQL]: ./sql-data-warehouse-overview-load.md
[Visão geral de desenvolvimento de depósito de dados do SQL]: ./sql-data-warehouse-overview-develop.md
[Visão geral de tabela]: ./sql-data-warehouse-tables-overview.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[Sintaxe de criar tabela]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Centro de Download da Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433
