<properties
   pageTitle="Carregar dados do SQL Server em um depósito de dados do SQL Azure (bcp) | Microsoft Azure"
   description="Para um tamanho de dados pequeno, usa bcp para exportar dados do SQL Server para arquivos simples e importar os dados diretamente no depósito de dados do SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-flat-files"></a>Carregar dados do SQL Server em um depósito de dados do SQL Azure (arquivos simples)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [BCP](sql-data-warehouse-load-from-sql-server-with-bcp.md)

Para pequenos conjuntos de dados, você pode usar o utilitário de linha de comando bcp para exportar dados do SQL Server e, em seguida, carregue-o diretamente ao depósito de dados do SQL Azure.

Neste tutorial, você usará bcp para:

- Exportar uma tabela a partir do SQL Server usando o bcp comando (ou crie um arquivo de exemplo simples)
- Importe a tabela de um arquivo simples depósito de dados do SQL.
- Crie estatísticas sobre os dados carregados.

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## <a name="before-you-begin"></a>Antes de começar

### <a name="prerequisites"></a>Pré-requisitos

Para percorrer neste tutorial, você precisa:

- Um banco de dados do SQL Data Warehouse
- O utilitário de linha de comando bcp instalado
- O utilitário de linha de comando sqlcmd instalado

Você pode baixar os utilitários bcp e sqlcmd no [Centro de Download da Microsoft][].

### <a name="data-in-ascii-or-utf-16-format"></a>Dados em formato ASCII ou UTF-16

Se você estiver tentando este tutorial com seus próprios dados, seus dados precisam usar a ASCII ou UTF-16 codificação como bcp não oferece suporte a UTF-8. 

PolyBase suporta UTF-8, mas ainda não dá suporte a UTF-16. Observe que, se você quiser combinar bcp com PolyBase você precisará transformar os dados UTF-8 depois que ele é exportado do SQL Server. 


## <a name="1-create-a-destination-table"></a>1. criar uma tabela de destino

Defina uma tabela em SQL Data Warehouse que será a tabela de destino para o carregamento. As colunas na tabela devem corresponder aos dados de cada linha de seu arquivo de dados.

Para criar uma tabela, abra um prompt de comando e usar sqlcmd.exe para executar o comando a seguir:


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


## <a name="2-create-a-source-data-file"></a>2. criar um arquivo de dados de origem

Abra o bloco de notas e copie as seguintes linhas de dados para um novo arquivo de texto e, em seguida, salve esse arquivo em seu diretório temp local, C:\Temp\DimDate2.txt. Esses dados estão no formato ASCII.

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

(Opcional) Para exportar seus próprios dados de um banco de dados do SQL Server, abra um prompt de comando e execute o comando a seguir. Substitua o nome de tabela, nomedoservidor, DatabaseName, nome de usuário e senha por suas próprias informações.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```



## <a name="3-load-the-data"></a>3. carregar os dados
Para carregar os dados, abra um prompt de comando e execute o seguinte comando, substituindo os valores para o nome do servidor, nome do banco de dados, nome de usuário e senha com suas próprias informações.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

Use este comando para verificar se que os dados foram carregados corretamente

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Os resultados devem ter esta aparência:

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

## <a name="4-create-statistics"></a>4. criar estatísticas

SQL Data Warehouse faz ainda não suporte criação automática ou estatísticas a atualização automática. Para obter o melhor desempenho de consulta, é importante criar estatísticas sobre todas as colunas de todas as tabelas após a primeira carga ou após ocorrerem substanciais alterações nos dados. Para obter uma explicação detalhada das estatísticas, consulte [estatísticas][]. 

Execute o seguinte comando para criar estatísticas na sua tabela recentemente carregada.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="5-export-data-from-sql-data-warehouse"></a>5. exportar dados do SQL Data Warehouse
Por diversão, você pode exportar os dados que você carregou apenas sair do SQL Data Warehouse.  O comando Exportar é exatamente o mesmo que a exportação do SQL Server.

No entanto, há uma diferença nos resultados. Como os dados são armazenados em locais distribuídos dentro SQL Data Warehouse, quando você exporta dados cada nó de computação grava dados para o arquivo de saída. A ordem dos dados no arquivo de saída é provavelmente será diferente da ordem dos dados no arquivo de entrada.

### <a name="export-a-table-and-compare-exported-results"></a>Exportar uma tabela e comparar resultados exportados

Para ver os dados exportados, abra um prompt de comando e execute este comando usando seus próprio parâmetros. Nomedoservidor é o nome do seu servidor do SQL Azure lógico.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Você pode confirmar que os dados foram exportados corretamente abrindo o novo arquivo. Os dados no arquivo devem coincidir o texto abaixo, mas provavelmente serão classificados em uma ordem diferente:

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

### <a name="export-the-results-of-a-query"></a>Exportar os resultados de uma consulta

Você pode usar a função **queryout** de bcp para exportar os resultados de uma consulta em vez de exportar a tabela inteira. 

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral de carregamento, consulte [carregar dados para SQL Data Warehouse][].
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento de depósito de dados do SQL][].
Consulte [Visão geral sobre Table][] ou [CREATE TABLE sintaxe][] para obter mais informações sobre como criar uma tabela no SQL Data Warehouse.

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
