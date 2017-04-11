<properties
   pageTitle="Carregar os dados de exemplo em SQL Data Warehouse | Microsoft Azure"
   description="Carregar dados de exemplo em um depósito de dados do SQL"
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
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#<a name="load-sample-data-into-sql-data-warehouse"></a>Carregar dados de exemplo em um depósito de dados do SQL

Siga estas etapas simples para carregar e consultar o banco de dados de amostra do Adventure Works. Esses scripts usam sqlcmd para executar SQL que irá criar tabelas e exibições. Depois de tabelas foram criadas, os scripts usará bcp para carregar dados.  Se você ainda não tiver sqlcmd e bcp instalado, siga estes links para [instalar bcp][] e [instalar sqlcmd][].

##<a name="load-sample-data"></a>Carregar dados de amostra

1. Baixe o arquivo zip de [Scripts de exemplo Adventure Works para SQL Data Warehouse][] .

2. Extrai os arquivos zip baixado em uma pasta em seu computador local.

3. Edite o arquivo extraído aw_create.bat e definir as seguintes variáveis encontradas na parte superior do arquivo.  Certifique-se de não deixar nenhum espaço em branco entre o "=" e o parâmetro.  A seguir são exemplos de podem ser a aparência de suas edições.

    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```

4. A partir de um prompt de cmd do Windows, execute o aw_create.bat editado.  Certifique-se de que você está no diretório de onde você salvou sua versão editada do aw_create.bat.
Este script irá...
    * Solte qualquer tabelas do Adventure Works ou modos de exibição que já existem no seu banco de dados
    * Criar as tabelas de Adventure Works e modos de exibição
    * Carregar cada tabela do Adventure Works usando bcp
    * Valide as contagens de linha para cada tabela do Adventure Works
    * Coletar estatísticas em cada coluna para cada tabela do Adventure Works


##<a name="query-sample-data"></a>Dados de exemplo de consulta

Depois que você já carregado alguns dados de exemplo em seu Data Warehouse SQL, você pode rapidamente executar algumas consultas.  Para executar uma consulta, conecte seu banco de dados recém-criado do Adventure Works no DW do SQL Azure usando o Visual Studio e SSDT, conforme descrito no documento [da consulta com Visual Studio][] .

Exemplo de instrução select simple para obter todas as informações dos funcionários:

```sql
SELECT * FROM DimEmployee;
```

Exemplo de uma consulta mais complexa usando construções como agrupar por para examinar o valor total para todas as vendas em cada dia:

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Exemplo de um SELECT com uma cláusula WHERE para filtrar pedidos de antes de uma determinada data:

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

SQL Data Warehouse oferece suporte para quase todas as construções T-SQL que suporta do SQL Server.  Quaisquer diferenças estão documentadas em nossa documentação [migrar código][] .

## <a name="next-steps"></a>Próximas etapas
Agora que você já tinha uma chance de tentar algumas consultas com dados de exemplo, confira como [desenvolver][], [carregar][]ou [migrar][] para o SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[migrar]: sql-data-warehouse-overview-migrate.md
[desenvolver]: sql-data-warehouse-overview-develop.md
[carregar]: sql-data-warehouse-overview-load.md
[consulta com o Visual Studio]: sql-data-warehouse-query-visual-studio.md
[migrar código]: sql-data-warehouse-migrate-code.md
[instalar o bcp]: sql-data-warehouse-load-with-bcp.md
[instalar o sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Scripts de exemplo da Adventure Works para depósito de dados do SQL]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip
