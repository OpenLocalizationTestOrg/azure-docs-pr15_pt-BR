<properties
   pageTitle="Consultar depósito de dados do SQL Azure (sqlcmd) | Microsoft Azure"
   description="Consultando depósito de dados do SQL Azure com o sqlcmd utilitário de linha de comando."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/06/2016"
   ms.author="barbkess;sonyama"/>

# <a name="query-azure-sql-data-warehouse-sqlcmd"></a>Depósito de dados do SQL Azure (sqlcmd) de consulta

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Aprendizado de máquina Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [O Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Este passo a passo usa o utilitário de linha de comando [sqlcmd][] para consultar um depósito de dados do SQL Azure.  

## <a name="1-connect"></a>1. Conecte

Para começar a usar o [sqlcmd][], abra o prompt de comando e insira **sqlcmd** seguido a cadeia de conexão do banco de dados SQL Data Warehouse. A cadeia de conexão requer os parâmetros a seguir:

+ **Server (-S):** Servidor do formulário `<`o nome do servidor`>`. database.windows.net
+ **Banco de dados (-d):** Nome do banco de dados.
+ **Habilitar identificadores entre aspas (-eu):** Identificadores entre aspas devem estar habilitados para se conectar a uma instância do SQL Data Warehouse.

Para usar a autenticação do SQL Server, você precisa adicionar os parâmetros de nome de usuário/senha:

+ **Usuário (-U):** Usuário do servidor no formulário `<`usuário`>`
+ **Senha (-P):** Senha associada ao usuário.

Por exemplo, a cadeia de caracteres de conexão pode parecer com o seguinte:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Para usar autenticação integrado do Active Directory do Azure, você precisa adicionar os parâmetros do Azure Active Directory:

+ **Autenticação do azure Active Directory (-G):** usar Azure Active Directory para autenticação

Por exemplo, a cadeia de caracteres de conexão pode parecer com o seguinte:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [AZURE.NOTE] Você precisa habilitar a [Autenticação do Azure Active Directory](sql-data-warehouse-authentication.md) autenticar usando o Active Directory.

## <a name="2-query"></a>2. consulta de

Após a conexão, você pode executar qualquer instruções Transact-SQL compatíveis com a instância.  Neste exemplo, as consultas são enviadas no modo interativo.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Esses exemplos próximos mostram como você pode executar as consultas no modo de lotes usando a opção -Q ou tubulação seu SQL para sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Próximas etapas

Consulte a [documentação de sqlcmd][sqlcmd] para saber mais sobre detalhes sobre as opções disponíveis no sqlcmd.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[Sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
