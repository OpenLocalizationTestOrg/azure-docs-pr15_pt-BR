<properties
   pageTitle="Gerenciar power de computação no depósito de dados do SQL Azure (PowerShell) | Microsoft Azure"
   description="Tarefas do PowerShell para gerenciar calcular power. Recursos de computação de escala ajustando DWUs. Ou, pausar e continuar recursos de computação para economizar custos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/13/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>Gerenciar power de computação no depósito de dados do SQL Azure (PowerShell)

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTANTE](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Dimensionar o desempenho dimensionando calcular memória para atender a mudança de sua carga de trabalho e recursos. Economizar custos por recursos back dimensionamento durante horários de pico ou computação pausa completamente. 

Este conjunto de tarefas usa o portal do Azure para:

- Computação de escala
- Computação de pausa
- Computação de currículo

Para saber mais sobre isso, consulte [Visão geral de computação de gerenciar][].


## <a name="before-you-begin"></a>Antes de começar

### <a name="install-the-latest-version-of-azure-powershell"></a>Instalar a versão mais recente do PowerShell do Azure

> [AZURE.NOTE]  Para usar o PowerShell do Azure com SQL Data Warehouse, você precisa Azure PowerShell versão 1.0.3 ou maior.  Para verificar sua versão atual, execute o comando **módulo de Get - ListAvailable-nome do Azure**. Você pode instalar a versão mais recente do [Microsoft Web Platform Installer][].  Para obter mais informações, consulte [como instalar e configurar o PowerShell do Azure][].

### <a name="get-started-with-azure-powershell-cmdlets"></a>Começar a usar cmdlets do PowerShell do Azure

Introdução:

1. Abra o PowerShell Azure. 
2. No prompt do PowerShell, execute esses comandos entrar para o Gerenciador de recursos do Azure e selecione sua assinatura.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Alimentação de computação de escala

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar os DWUs, use o cmdlet do PowerShell [Set-AzureRmSqlDatabase][] . O exemplo a seguir define o objetivo de nível de serviço para DW1000 para o banco de dados MySQLDW que está hospedado no servidor meuservidor. 

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Computação pausa

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar um banco de dados, use o cmdlet [Suspender-AzureRmSqlDatabase][] . O exemplo a seguir pausa um banco de dados denominado Database02 hospedado em um servidor chamado Server01. O servidor está em um grupo de recursos Azure denominado ResourceGroup1. 

> [AZURE.NOTE] Observe que se o servidor está foo.database.windows.net, use "foo" como o nome do servidor - em cmdlets do PowerShell.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
Uma variação, este próximo exemplo recupera o banco de dados para o objeto $database. Ele então pipes o objeto para [Suspender-AzureRmSqlDatabase][]. Os resultados são armazenados em resultDatabase o objeto. O comando final mostra os resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Computação de currículo

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para iniciar um banco de dados, use o cmdlet [Resume-AzureRmSqlDatabase][] . O exemplo a seguir inicia um banco de dados denominado Database02 hospedado em um servidor chamado Server01. O servidor está em um grupo de recursos Azure denominado ResourceGroup1. 

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

Uma variação, este próximo exemplo recupera o banco de dados para o objeto $database. Ele pipes o objeto [AzureRmSqlDatabase de currículo][] e armazena os resultados em $resultDatabase. O comando final mostra os resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Próximas etapas

Para outras tarefas de gerenciamento, consulte [Visão geral de gerenciamento][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Visão geral do gerenciamento]: ./sql-data-warehouse-overview-manage.md
[Como instalar e configurar o PowerShell do Azure]: ./powershell-install-configure.md
[Gerenciar a visão geral de computação]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Currículo-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspender-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/
