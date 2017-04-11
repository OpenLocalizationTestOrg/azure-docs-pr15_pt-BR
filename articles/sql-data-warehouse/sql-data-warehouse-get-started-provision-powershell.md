<properties
   pageTitle="Criar SQL Data Warehouse usando o PowerShell | Microsoft Azure"
   description="Criar SQL Data Warehouse usando o PowerShell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="create-sql-data-warehouse-using-powershell"></a>Criar depósito de dados do SQL usando o PowerShell

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Este artigo mostra como criar um depósito de dados do SQL usando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa:

- **Conta do Microsoft Azure**: visite [Avaliação gratuita do Azure][] ou [MSDN Azure créditos][] para criar uma conta.
- **Servidor do SQL Azure**: consulte [criar um servidor de banco de dados do SQL Azure lógico com o Portal do Azure][] ou [criar um servidor de banco de dados do SQL Azure lógico com o PowerShell][] para obter mais detalhes.
- **Grupo de recursos**: use o mesmo grupo de recursos como o seu servidor do SQL Azure ou veja [como criar um grupo de recursos][].
- **PowerShell versão 1.0.3 ou maior**: você pode verificar sua versão executando **módulo de Get - ListAvailable-nome do Azure**.  Pode ser instalada a versão mais recente do [Microsoft Web Platform Installer][].  Para obter mais informações sobre como instalar a versão mais recente, veja [como instalar e configurar o Azure PowerShell][].

> [AZURE.NOTE] Criar um depósito de dados do SQL pode resultar em um novo serviço faturável.  Consulte [SQL Data Warehouse preços][] para mais detalhes sobre preços.

## <a name="create-a-sql-data-warehouse"></a>Criar um depósito de dados do SQL

1. Abrir o Windows PowerShell.
2. Execute este cmdlet para efetuar login ao Gerenciador de recursos do Azure.

    ```Powershell
    Login-AzureRmAccount
    ```
    
3. Selecione a assinatura que você deseja usar para sua sessão atual.

    ```Powershell
    Get-AzureRmSubscription -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```

4.  Crie o banco de dados. Este exemplo cria um banco de dados denominado "mynewsqldw", com o serviço objetivo nível de "DW400", o servidor chamado "sqldwserver1", que está no grupo de recursos denominado "mywesteuroperesgp1".

    ```Powershell
    New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
    ```

Parâmetros necessários são:

- **RequestedServiceObjectiveName**: A quantidade de [DWU][] você está solicitando.  Valores com suporte são: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 e DW6000.
- **DatabaseName**: O nome do SQL Data Warehouse que você está criando.
- **Nomedoservidor**: O nome do servidor que você está usando para criação (deve ser V12).
- **ResourceGroupName**: grupo de recursos que você está usando.  Para localizar recursos disponíveis grupos em sua assinatura usam Get-AzureResource.
- **Edição**: deve ser "Data warehouse" para criar um depósito de dados do SQL.

Parâmetros opcionais são:

- **CollationName**: O agrupamento de padrão se não especificado é SQL_Latin1_General_CP1_CI_AS.  Agrupamento não pode ser alterado em um banco de dados.
- **MaxSizeBytes**: O tamanho máximo padrão de um banco de dados é 10 GB.


Para obter mais detalhes sobre as opções de parâmetro, consulte [AzureRmSqlDatabase de novo][] e [Criar banco de dados (depósito de dados do SQL Azure)][].

## <a name="next-steps"></a>Próximas etapas

Após o SQL Data Warehouse provisionamento talvez você queira tentar [carregar dados de exemplo][] ou fazer check-out como [desenvolver][], [carregar][]ou [migrar][].

Se você estiver interessado em obter mais informações sobre como gerenciar o SQL Data Warehouse programaticamente, consulte nosso artigo sobre como usar [cmdlets do PowerShell e APIs REST][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desenvolver]: ./sql-data-warehouse-overview-develop.md
[carregar]: ./sql-data-warehouse-load-with-bcp.md
[carregar dados de amostra]: ./sql-data-warehouse-load-sample-databases.md
[Cmdlets do PowerShell e APIs REST]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[Como instalar e configurar o PowerShell do Azure]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Criar um servidor de banco de dados do Azure SQL lógico com o Portal do Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Criar um servidor de banco de dados do Azure SQL lógico com PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[como criar um grupo de recursos]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[Novo AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Criar banco de dados (depósito de dados do SQL Azure)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL Data Warehouse preços]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Versão de avaliação gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN créditos Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
