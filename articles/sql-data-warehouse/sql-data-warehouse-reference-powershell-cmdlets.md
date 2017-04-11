<properties
   pageTitle="Cmdlets do PowerShell para depósito de dados do SQL Azure"
   description="Encontre os cmdlets do PowerShell superiores para depósito de dados do SQL Azure incluindo como pausar e continuar a um banco de dados."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess;mausher"/>

# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Cmdlets do PowerShell e as APIs REST para depósito de dados do SQL

Muitas tarefas de administração de SQL Data Warehouse podem ser gerenciadas usando cmdlets do PowerShell do Azure ou APIs REST.  A seguir alguns exemplos de como usar comandos do PowerShell para automatizar tarefas comuns no SQL Data Warehouse.  Para obter alguns exemplos REST boas, consulte o artigo [escalabilidade de gerenciar com restante][].

> [AZURE.NOTE]  Para poder usar o PowerShell do Azure com o SQL Data Warehouse, você precisa Azure PowerShell versão 1.0.3 ou maior.  Você pode verificar sua versão executando **módulo de Get - ListAvailable-nome do Azure**.  Pode ser instalada a versão mais recente do [Microsoft Web Platform Installer][].  Para obter mais informações sobre como instalar a versão mais recente, veja [como instalar e configurar o Azure PowerShell][].

## <a name="get-started-with-azure-powershell-cmdlets"></a>Começar a usar cmdlets do PowerShell do Azure

1. Abrir o Windows PowerShell. 
2. No prompt do PowerShell, execute esses comandos entrar para o Gerenciador de recursos do Azure e selecione sua assinatura.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Exemplo de depósito de dados do SQL pausar

Pausar um banco de dados denominado "Database02" hospedado em um servidor chamado "Server01."  O servidor está em um grupo de recursos Azure denominado "ResourceGroup1". 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Uma variação, este exemplo pipes recuperados ao objeto [Suspender-AzureRmSqlDatabase][].  Como resultado, o banco de dados está pausado. O comando final mostra os resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Iniciar o exemplo de depósito de dados do SQL

Retomar a operação de banco de dados denominada "Database02" hospedado em um servidor chamado "Server01." O servidor está contido em um grupo de recursos denominado "ResourceGroup1".

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Uma variação, este exemplo recupera um banco de dados denominado "Database02" de um servidor chamado "Server01" contida em um grupo de recursos denominado "ResourceGroup1". Ele pipes recuperados ao objeto [AzureRmSqlDatabase de currículo][].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] Observe que se o servidor está foo.database.windows.net, use "foo" como o nome do servidor - em cmdlets do PowerShell.

## <a name="frequently-used-powershell-cmdlets"></a>Cmdlets do PowerShell usado frequentemente

Esses cmdlets do PowerShell são frequentemente usados com depósito de dados do SQL Azure.

- [Get-AzureRmSqlDatabase][]
- [Get-AzureRmSqlDeletedDatabaseBackup][]
- [Get-AzureRmSqlDatabaseRestorePoints][]
- [Novo AzureRmSqlDatabase][]
- [Remover AzureRmSqlDatabase][]
- [Restaurar-AzureRmSqlDatabase][] 
- [Currículo-AzureRmSqlDatabase][]
- [Selecione AzureRmSubscription][]
- [Set-AzureRmSqlDatabase][]
- [Suspender-AzureRmSqlDatabase][]

## <a name="next-steps"></a>Próximas etapas
Para obter mais exemplos do PowerShell, consulte:

- [Criar um depósito de dados do SQL usando o PowerShell][]
- [Restauração do banco de dados][]

Para obter uma lista de todas as tarefas que pode ser automatizada com PowerShell, consulte [Cmdlets de banco de dados do SQL Azure][].  Para obter uma lista de tarefas que pode ser automatizada com restante, consulte [operações para bancos de dados do SQL Azure][].

<!--Image references-->

<!--Article references-->
[Como instalar e configurar o PowerShell do Azure]: ./powershell-install-configure.md
[Criar um depósito de dados do SQL usando o PowerShell]: ./sql-data-warehouse-get-started-provision-powershell.md
[Restauração do banco de dados]: ./sql-data-warehouse-restore-database-powershell.md
[Gerenciar escalabilidade com restante]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Cmdlets de banco de dados do SQL Azure]: https://msdn.microsoft.com/library/mt574084.aspx
[Operações para bancos de dados do SQL Azure]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[Novo AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remover AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restaurar-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Currículo-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Selecione AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspender-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
