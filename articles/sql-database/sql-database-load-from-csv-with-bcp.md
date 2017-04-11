<properties
   pageTitle="Carregar dados de arquivo CSV para Databaase de SQL Azure (bcp) | Microsoft Azure"
   description="Para um tamanho de dados pequeno, usa bcp para importar dados para o banco de dados do SQL Azure."
   services="sql-database"
   documentationCenter="NA"
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/13/2016"
   ms.author="carlrab"/>


# <a name="load-data-from-csv-into-azure-sql-data-warehouse-flat-files"></a>Carregar dados de CSV para depósito de dados do SQL Azure (arquivos simples)

Você pode usar o utilitário de linha de comando bcp para importar dados de um arquivo CSV para Azure SQL Database.

## <a name="before-you-begin"></a>Antes de começar

### <a name="prerequisites"></a>Pré-requisitos

Para percorrer neste tutorial, você precisa:

- Um servidor de banco de dados do Azure SQL lógico e o banco de dados
- O utilitário de linha de comando bcp instalado
- O utilitário de linha de comando sqlcmd instalado

Você pode baixar os utilitários bcp e sqlcmd no [Centro de Download da Microsoft][].

### <a name="data-in-ascii-or-utf-16-format"></a>Dados em formato ASCII ou UTF-16

Se você estiver tentando este tutorial com seus próprios dados, seus dados precisam usar a ASCII ou UTF-16 codificação como bcp não oferece suporte a UTF-8. 

## <a name="1-create-a-destination-table"></a>1. criar uma tabela de destino

Defina uma tabela no banco de dados do SQL como a tabela de destino. As colunas na tabela devem corresponder aos dados de cada linha de seu arquivo de dados.

Para criar uma tabela, abra um prompt de comando e usar sqlcmd.exe para executar o comando a seguir:


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
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


## <a name="next-steps"></a>Próximas etapas

Para migrar um banco de dados do SQL Server, consulte [migração de banco de dados do SQL Server](sql-database-cloud-migrate.md).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Centro de Download da Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433
