<properties
    pageTitle="Desabilitar o banco de dados de alongar e trazer dados remotos de volta | Microsoft Azure"
    description="Saiba como desativar Alongar banco de dados para uma tabela e opcionalmente retornem dados remotos."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="disable-stretch-database-and-bring-back-remote-data"></a>Desabilitar o banco de dados de alongar e trazer dados remotos de volta

Para desabilitar Alongar banco de dados de uma tabela, selecione **Alongar** para uma tabela no SQL Server Management Studio. Selecione uma das opções a seguir.

-   Desativar **| Trazer dados de volta do Azure**. Copiar os dados remotos para a tabela do Azure de volta para o SQL Server, desabilite Alongar banco de dados para a tabela. Essa operação provoca os custos de transferência de dados e ele não pode ser cancelado.

-   Desativar **| Deixar dados no Azure**. Desabilite o banco de dados de Alongar para a tabela.  Abandone os dados remotos para a tabela no Azure.

Você também pode usar Transact\-SQL para desabilitar Alongar banco de dados para uma tabela ou um banco de dados.

Após desativar Alongar banco de dados para uma tabela, paradas de migração de dados e os resultados da consulta não incluem mais os resultados da tabela de dados remoto.

Se você deseja simplesmente pausar a migração de dados, consulte [Pausar e continuar Alongar banco de dados](sql-server-stretch-database-pause.md).

>   [AZURE.NOTE] Desabilitando o banco de dados de Alongar para uma tabela ou um banco de dados não exclui o objeto remoto. Se você quiser excluir tabela remota ou banco de dados remoto, você precisa soltá-lo usando o portal de gerenciamento do Azure. Objetos remotos continuam provoca custos Azure até você excluí-las. Para obter mais informações, consulte [Preços de banco de dados do SQL Server Alongar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="disable-stretch-database-for-a-table"></a>Desabilitar o banco de dados de Alongar para uma tabela

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-table"></a>Usar o SQL Server Management Studio para desabilitar Alongar banco de dados para uma tabela

1.  No SQL Server Management Studio, no Pesquisador de objetos, selecione a tabela para a qual você deseja desabilitar Alongar banco de dados.

2.  Direita\-clique **Alongar**e selecione, selecione uma das opções a seguir.

    -   Desativar **| Trazer dados de volta do Azure**. Copiar os dados remotos para a tabela do Azure de volta para o SQL Server, desabilite Alongar banco de dados para a tabela. Este comando não pode ser cancelado.

        >   [AZURE.NOTE] Copiar os dados remotos para a tabela do Azure voltar para o SQL Server gera os custos de transferência de dados. Para obter mais informações, consulte [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

        Depois que todos os dados remotos foi copiados do Azure de volta para o SQL Server, Alongar é desabilitado para a tabela.

    -   Desativar **| Deixar dados no Azure**. Desabilite o banco de dados de Alongar para a tabela.  Abandone os dados remotos para a tabela no Azure.

    >   [AZURE.NOTE] Desabilitando o banco de dados de Alongar para uma tabela não exclui os dados remotos ou tabela remota. Se você quiser excluir a tabela remota, você precisa soltá-lo usando o portal de gerenciamento do Azure. Tabela remota continua provoca custos Azure até você excluí-lo. Para obter mais informações, consulte [Preços de banco de dados do SQL Server Alongar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="use-transact-sql-to-disable-stretch-database-for-a-table"></a>Use Transact\-SQL para desabilitar o banco de dados de Alongar para uma tabela

-   Para desabilitar Alongar para uma tabela e copiar que os dados remotos para a tabela do Azure de volta para o SQL Server, execute o seguinte comando. Depois que todos os dados remotos foi copiados do Azure de volta para o SQL Server, Alongar é desabilitado para a tabela.

    Este comando não pode ser cancelado.

    ```tsql
    USE <Stretch-enabled database name>;
    GO
    ALTER TABLE <Stretch-enabled table name>  
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
    GO
    ```
    >   [AZURE.NOTE] Copiar os dados remotos para a tabela do Azure voltar para o SQL Server gera os custos de transferência de dados. Para obter mais informações, consulte [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

-   Para desativar Alongar para uma tabela e abandonar os dados remotos, execute o seguinte comando.

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```

>   [AZURE.NOTE] Desabilitando o banco de dados de Alongar para uma tabela não exclui os dados remotos ou tabela remota. Se você quiser excluir a tabela remota, você precisa soltá-lo usando o portal de gerenciamento do Azure. Tabela remota continua provoca custos Azure até você excluí-lo. Para obter mais informações, consulte [Preços de banco de dados do SQL Server Alongar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="disable-stretch-database-for-a-database"></a>Desabilitar Alongar banco de dados para um banco de dados
Antes que você pode desabilitar Alongar banco de dados para um banco de dados, você tem que desabilitar o banco de dados de Alongar na Alongar individual\-habilitado para tabelas no banco de dados.

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-database"></a>Usar o SQL Server Management Studio para desabilitar Alongar banco de dados para um banco de dados

1.  No SQL Server Management Studio, no Pesquisador de objetos, selecione o banco de dados para o qual você deseja desabilitar Alongar banco de dados.

2.  Direita\-clique em **tarefas**e em seguida, selecione **Alongar**e selecione, em seguida, selecione **Desabilitar**.

>   [AZURE.NOTE] Desabilitando o banco de dados de Alongar para um banco de dados não exclui o banco de dados remoto. Se você quiser excluir o banco de dados remoto, você precisa soltá-lo usando o portal de gerenciamento do Azure. Banco de dados remoto continua provoca custos Azure até você excluí-lo. Para obter mais informações, consulte [Preços de banco de dados do SQL Server Alongar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="use-transact-sql-to-disable-stretch-database-for-a-database"></a>Use Transact\-SQL para desabilitar o banco de dados de Alongar para um banco de dados
Execute o seguinte comando.

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

>   [AZURE.NOTE] Desabilitando o banco de dados de Alongar para um banco de dados não exclui o banco de dados remoto. Se você quiser excluir o banco de dados remoto, você precisa soltá-lo usando o portal de gerenciamento do Azure. Banco de dados remoto continua provoca custos Azure até você excluí-lo. Para obter mais informações, consulte [Preços de banco de dados do SQL Server Alongar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="see-also"></a>Consulte também

[Definir opções de banco de dados da ALTER (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Pausar e continuar Alongar banco de dados](sql-server-stretch-database-pause.md)
