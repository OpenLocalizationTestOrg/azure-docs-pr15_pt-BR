<properties
    pageTitle="Alterar o nível de desempenho e nível de serviço de um banco de dados do SQL Azure | Microsoft Azure"
    description="Alterar o nível de serviço e nível de desempenho de um banco de dados do SQL Azure mostra como dimensionar seu banco de dados SQL para cima ou para baixo. Alterando o nível de preço de um banco de dados do SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-using-the-azure-portal"></a>Alterar o nível e desempenho nível de serviço (preço camada) de um banco de dados do SQL usando o portal do Azure


> [AZURE.SELECTOR]
- [**Portal do Azure**](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


Níveis de serviço e níveis de desempenho descrevem os recursos e os recursos disponíveis para o seu banco de dados SQL e podem ser atualizados conforme as necessidades de seu aplicativo mude. Para obter detalhes, consulte [Níveis de serviço](sql-database-service-tiers.md).

Observe que alterar o nível de serviço e/ou nível de desempenho de um banco de dados cria uma réplica do banco de dados original com o novo nível de desempenho e alterna conexões para a réplica. Nenhum dado é perdido durante esse processo, mas durante o momento breve quando podemos alterna para a réplica, conexões ao banco de dados estão desativadas, portanto algumas transações em trânsito podem ser revertidas. Esta janela varia, mas é, em média, em 4 segundos e em mais de 99% de casos é menos de 30 segundos. Raramente, especialmente se houver grandes números de transações em trânsito nas conexões de momento serão desabilitados, esta janela pode ser maior.  

A duração de todo o processo de escala-up depende a camada serviço e tamanho do banco de dados antes e depois da alteração. Por exemplo, um banco de dados de 250 GB que está mudando para, de ou em um nível de serviço padrão, deve ser concluída dentro de 6 horas. Para um banco de dados do mesmo tamanho que está alterando os níveis de desempenho em nível de serviço Premium, ele deverá ser concluída dentro 3 horas.


Use as informações em [níveis de serviço de banco de dados do Azure SQL e níveis de desempenho](sql-database-service-tiers.md) para determinar o nível de desempenho e nível de serviço apropriado para seu banco de dados do SQL Azure.

- Para fazer um downgrade um banco de dados, o banco de dados deve ser menor que o tamanho máximo permitido de nível de serviço de destino. 
- Ao atualizar um banco de dados com [Replicação geográfica](sql-database-geo-replication-overview.md) habilitada, primeiro atualize seus bancos de dados secundários para o nível de desempenho desejado antes de atualizar o banco de dados principal.
- Quando fazer downgrade um nível de serviço, você deve primeiro encerrar todas as relações de replicação geográfica. 
- As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se você estiver fazendo downgrade você pode perder a capacidade de restaurar para um ponto no tempo, ou ter um período de retenção de backup inferior. Para obter mais informações, consulte [Backup de banco de dados do SQL Azure e restauração](sql-database-business-continuity.md).
- Alterar seu nível de preço de banco de dados não altera o tamanho máximo do banco de dados. Para alterar seu banco de dados tamanho máximo use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- As novas propriedades do banco de dados não são aplicadas até que as alterações sejam concluídas.



**Para concluir este artigo, você precisa do seguinte:**

- Um banco de dados do SQL Azure. Se você não tiver um banco de dados do SQL, criar uma seguindo as etapas neste artigo: [criar seu banco de dados do SQL Azure primeiro](sql-database-get-started.md).


## <a name="change-the-service-tier-and-performance-level-of-your-database"></a>Alterar o nível de desempenho e nível de serviço do seu banco de dados


Abra a lâmina de banco de dados SQL do banco de dados que você deseja dimensionar para cima ou para baixo:

1.  No [portal do Azure](https://portal.azure.com), clique em **mais serviços** > **bancos de dados SQL**.
2.  Clique em do banco de dados que você deseja alterar.
3.  No **banco de dados SQL** blade clique **camada de preços (escala DTUs)**:

    ![nível de preço][1]

1.  Escolha um novo nível e clique em **Selecionar**:

    Clicando em **Selecionar** envia uma solicitação de escala para alterar o nível de preços. Dependendo do tamanho do banco de dados, a operação de escala pode levar algum tempo para completo (consulte as informações na parte superior deste artigo).

    > [AZURE.NOTE] Alterar seu nível de preço de banco de dados não altera o tamanho máximo do banco de dados. Para alterar seu banco de dados tamanho máximo use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).

    ![Selecione o nível de preço][2]

3.  Clique no ícone de notificação (bell), no canto superior direito:

    ![notificações][3]

    Clique no texto de notificação para abrir o painel de detalhes onde você pode ver o status da solicitação.




## <a name="next-steps"></a>Próximas etapas

- Altere o tamanho máximo do banco de dados usando o [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- [Dimensionar in e check-out](sql-database-elastic-scale-get-started.md)
- [Conectar-se e consultar um banco de dados do SQL com SSMS](sql-database-connect-query-ssms.md)
- [Exportar um banco de dados do SQL Azure](sql-database-export.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Visão geral de continuidade de negócios](sql-database-business-continuity.md)
- [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/new-tier.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png
