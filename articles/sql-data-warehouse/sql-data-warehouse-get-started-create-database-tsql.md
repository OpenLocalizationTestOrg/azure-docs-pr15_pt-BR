<properties
   pageTitle="Criar um depósito de dados do SQL com TSQL | Microsoft Azure"
   description="Saiba como criar um depósito de dados do SQL Azure com TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>Criar um banco de dados do SQL Data Warehouse usando Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Este artigo mostra como criar um depósito de dados do SQL usando T-SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa: 

- **Conta do Microsoft Azure**: visite [Avaliação gratuita do Azure][] ou [MSDN Azure créditos][] para criar uma conta.
- **Servidor do SQL Azure**: consulte [criar um servidor de banco de dados do SQL Azure lógico com o Portal do Azure][] ou [criar um servidor de banco de dados do SQL Azure lógico com o PowerShell][] para obter mais detalhes.
- **Grupo de recursos**: use o mesmo grupo de recursos como o seu servidor do SQL Azure ou veja [como criar um grupo de recursos][].
- **Ambiente para executar T-SQL**: você pode usar o [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][]ou [SSMS][] para executar T-SQL.

> [AZURE.NOTE] Criar um depósito de dados do SQL pode resultar em um novo serviço faturável.  Consulte [SQL Data Warehouse preços][] para mais detalhes sobre preços.

## <a name="create-a-database-with-visual-studio"></a>Criar um banco de dados com o Visual Studio

Se você é novo no Visual Studio, consulte o artigo [Consulta Azure SQL Data Warehouse (Visual Studio)][].  Para começar, abra o Explorador de objeto do SQL Server no Visual Studio e conectar ao servidor que irá hospedar seu banco de dados do SQL Data Warehouse.  Uma vez conectado, você pode criar um depósito de dados do SQL, execute o seguinte comando SQL no banco de dados **mestre** .  Esse comando cria o banco de dados MySqlDwDb com um objetivo de serviço de DW400 e permitir que o banco de dados aumente até um tamanho máximo de 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Criar um banco de dados com sqlcmd

Como alternativa, você pode executar o mesmo comando com sqlcmd executando o seguinte no prompt de comando.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

O agrupamento padrão quando não especificado é SQL_Latin1_General_CP1_CI_AS agrupar.  O `MAXSIZE` pode estar entre 250 GB e 240 TB.  O `SERVICE_OBJECTIVE` pode estar entre DW100 e DW2000 [DWU][].  Para obter uma lista de todos os valores válidos, consulte a documentação do MSDN para [Criar o banco de dados][].  MAXSIZE tanto o SERVICE_OBJECTIVE podem ser alterado com um comando de T-SQL [ALTER DATABASE][] .  O agrupamento de um banco de dados não pode ser alterado após a criação.   Cuidado deve ser usado quando alterar o SERVICE_OBJECTIVE como alteração DWU causa uma reinicialização de serviços, que cancela todas as consultas em trânsito.  Alterar MAXSIZE não reiniciar o serviços, pois é apenas uma operação de metadados simples.

## <a name="next-steps"></a>Próximas etapas

Após o SQL Data Warehouse provisionamento você pode [carregar dados de amostra][] ou fazer check-out como [desenvolver][], [carregar][]ou [migrar][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Consulta SQL Azure Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrar]: sql-data-warehouse-overview-migrate.md
[desenvolver]: sql-data-warehouse-overview-develop.md
[carregar]: sql-data-warehouse-overview-load.md
[carregar dados de amostra]: sql-data-warehouse-load-sample-databases.md
[Criar um servidor de banco de dados do Azure SQL lógico com o Portal do Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Criar um servidor de banco de dados do Azure SQL lógico com PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[como criar um grupo de recursos]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[Sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CRIAR BANCO DE DADOS]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTERAR BANCO DE DADOS]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse preços]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Versão de avaliação gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN créditos Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
