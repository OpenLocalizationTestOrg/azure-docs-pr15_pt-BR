<properties
    pageTitle="Pausar e continuar a migração de dados (banco de dados de alongar) | Microsoft Azure"
    description="Saiba como pausar ou continuar a migração de dados no Azure."
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
    ms.date="06/14/2016"
    ms.author="douglasl"/>

# <a name="pause-and-resume-data-migration-stretch-database"></a>Pausar e continuar a migração de dados (banco de dados de alongar)

Para pausar ou continuar a migração de dados no Azure, selecione **Alongar** para uma tabela no SQL Server Management Studio e selecione **Pausar** pausar a migração de dados ou **retomar** retomar a migração de dados. Você também pode usar Transact\-SQL para pausar ou continuar a migração de dados.

Migração de dados de pausar em tabelas individuais quando você deseja solucionar problemas no servidor local ou para maximizar a largura de banda de rede disponível.

## <a name="pause-data-migration"></a>Migração de dados de pausa

### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>Use o SQL Server Management Studio para pausar a migração de dados

1.  No SQL Server Management Studio, no Pesquisador de objetos, selecione a Alongar\-habilitado para a tabela para a qual você deseja pausar a migração de dados.

2.  Direita\-clique em e selecione **Alongar**, selecione **Pausar**.

### <a name="use-transact-sql-to-pause-data-migration"></a>Use Transact\-SQL para pausar a migração de dados
Execute o seguinte comando.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>Migração de dados de currículo

### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>Use o SQL Server Management Studio para retomar a migração de dados

1.  No SQL Server Management Studio, no Pesquisador de objetos, selecione a Alongar\-habilitado para a tabela para a qual você deseja retomar a migração de dados.

2.  Direita\-clique em e selecione **Alongar**, selecione **continuar**.

### <a name="use-transact-sql-to-resume-data-migration"></a>Use Transact\-SQL retomar a migração de dados
Execute o seguinte comando.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>Verifique se a migração está ativo ou pausado

### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>Usar o SQL Server Management Studio para verificar se a migração está ativo ou pausado
No SQL Server Management Studio, abra o **Monitor de banco de dados de alongar** e verifique o valor da coluna **Estado de migração** . Para obter mais informações, consulte [Monitor e solucionar problemas de migração de dados](sql-server-stretch-database-monitor.md).

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>Usar Transact-SQL para verificar se a migração está ativo ou pausado
O modo de exibição de catálogo **sys.remote_data_archive_tables** da consulta e verifique o valor da coluna **is_migration_paused** . Para obter mais informações, consulte [sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="see-also"></a>Consulte também

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[Monitor e solucionar problemas de migração de dados](sql-server-stretch-database-monitor.md)
