<properties
   pageTitle="Criar um depósito de dados do SQL no portal do Azure | Microsoft Azure"
   description="Saiba como criar um depósito de dados do SQL Azure no portal do Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="barbkess;lodipalm;sonyama"/>

# <a name="create-an-azure-sql-data-warehouse"></a>Criar um depósito de dados do SQL Azure

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Neste tutorial usa o portal do Azure para criar um depósito de dados do SQL que contém um banco de dados de exemplo AdventureWorksDW.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa:

- **Conta do Microsoft Azure**: visite [Avaliação gratuita do Azure][] ou [MSDN Azure créditos][] para criar uma conta.
- **Servidor do SQL Azure**: consulte [criar um servidor de banco de dados do SQL Azure lógico com o portal do Azure][] para obter mais detalhes.

> [AZURE.NOTE] Criar um depósito de dados do SQL pode resultar em um novo serviço faturável.  Para obter mais detalhes, consulte [SQL Data Warehouse preços][] .

## <a name="create-a-sql-data-warehouse"></a>Criar um depósito de dados do SQL

1. Entrar no [portal do Azure](https://portal.azure.com).

2. Clique em **+ novo** > **dados + armazenamento** > **depósito de dados do SQL**.

    ![Criar](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. Na lâmina **SQL Data Warehouse** , preencha as informações necessárias e pressione 'Criar' para criar.

    ![Criar banco de dados](./media/sql-data-warehouse-get-started-provision/create-database.png)

    - **Servidor**: Recomendamos que você selecione seu servidor primeiro.  

    - **Nome do banco de dados**: O nome que é usado para fazer referência o depósito de dados do SQL.  Ele deve ser exclusivo no servidor.
    
    - **Desempenho**: Recomendamos que começam com 400 [DWUs][DWU]. Você pode mover o controle deslizante para a esquerda ou direita para ajustar o desempenho do data warehouse ou dimensionar para cima ou para baixo após a criação.  Para saber mais sobre DWUs, consulte nossa documentação em [dimensionamento](./sql-data-warehouse-manage-compute-overview.md) ou nossos [preços página][SQL Data Warehouse preços]. 

    - **Assinatura**: selecione a [assinatura] que fará a cobrança para este depósito de dados do SQL.

    - **Grupo de recursos**: [grupos de recursos] [ Resource group] são contêineres projetados para ajudá-lo a gerenciar um conjunto de recursos Azure. Saiba mais sobre [grupos de recursos](../azure-resource-manager/resource-group-overview.md).

    - **Selecione fonte**: clique em **Selecionar fonte** > **amostra**. Azure preenche automaticamente a opção **Selecione sample** com AdventureWorksDW.

> [AZURE.NOTE] O agrupamento padrão para um depósito de dados do SQL é SQL_Latin1_General_CP1_CI_AS. Se um agrupamento diferente for necessário, [T-SQL][] pode ser usado para criar o banco de dados com um agrupamento diferente.

4. Clique em **criar** para criar seu depósito de dados do SQL.

5. Aguarde alguns minutos. Quando seu data warehouse estiver pronta, você deve ser retornado para o [portal do Azure](https://portal.azure.com). Você pode encontrar seu depósito de dados do SQL no seu painel, listado em seus bancos de dados do SQL ou no grupo de recursos que você usou para criá-lo. 

    ![modo de exibição de portal](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)] 

## <a name="next-steps"></a>Próximas etapas

Agora que você criou um depósito de dados do SQL, você está pronto para [se conectar](./sql-data-warehouse-connect-overview.md) e começar a consulta.

Para carregar dados no depósito de dados do SQL, consulte a [Visão geral de carregamento](./sql-data-warehouse-overview-load.md).

Se você estiver tentando migrar um banco de dados existente para o SQL Data Warehouse, consulte [Visão geral de migração](./sql-data-warehouse-overview-migrate.md) ou use um [Utilitário de migração](./sql-data-warehouse-migrate-migration-utility.md).

Regras de firewall também podem ser configuradas com Transact-SQL. Para obter mais informações, consulte [sp_set_firewall_rule][] e [sp_set_database_firewall_rule][].

Também é uma ótima ideia para examinar as [práticas recomendadas][].

<!--Article references-->
[Criar um servidor de banco de dados do Azure SQL lógico com o portal do Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resource groups]: ../resource-group-template-deploy-portal.md
[Práticas recomendadas]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md#data-warehouse-units
[assinatura]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md
 
<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse preços]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Versão de avaliação gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN créditos Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

