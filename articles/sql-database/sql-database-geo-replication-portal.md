<properties 
    pageTitle="Configurar replicação geográfica do banco de dados do SQL Azure com o portal do Azure | Microsoft Azure" 
    description="Configurar a localização geográfica-replicação de banco de dados do SQL Azure usando o portal do Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="NA"
    ms.date="10/18/2016"
    ms.author="sstein"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-the-azure-portal"></a>Configurar replicação geográfica do banco de dados do SQL Azure com o portal do Azure


> [AZURE.SELECTOR]
- [Visão geral](sql-database-geo-replication-overview.md)
- [Portal do Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Este artigo mostra como configurar replicação de localização geográfica ativa para banco de dados do SQL com o [portal do Azure](http://portal.azure.com).

Para iniciar o failover com o portal do Azure, consulte [Iniciar um failover planejado ou para o banco de dados SQL Azure com o portal do Azure](sql-database-geo-replication-failover-portal.md).

>[AZURE.NOTE] Localização geográfica-replicação ativa (legíveis secundários) agora está disponível para todos os bancos de dados em todos os níveis de serviço. Em abril de 2017, o tipo de secundário não legível será desativado e bancos de dados não legível existentes serão automaticamente atualizados para secundários legíveis.

Para configurar replicação geográfica usando o portal de Azure, é necessário o seguinte recurso:

- Um banco de dados do SQL Azure - é o principal banco de dados que você deseja duplicar para uma região geográfica diferente.

## <a name="add-secondary-database"></a>Adicionar banco de dados secundário

As etapas a seguir criam um novo banco de dados secundário em uma parceria de replicação geográfica.  

Para adicionar um secundário, você deve ser o proprietário da assinatura ou coproprietário. 

Banco de dados secundário terá o mesmo nome de banco de dados principal e, por padrão, terá o mesmo nível de serviço. O banco de dados secundário pode ser um banco de dados único ou elástica. Para obter mais informações, consulte [Níveis de serviço](sql-database-service-tiers.md).
O secundário for criado e propagado, dados começará replicação de banco de dados principal para o novo banco de dados secundário. 

> [AZURE.NOTE] Se o banco de dados de parceiro já existe (por exemplo - como encerrando uma relação de replicação geográfica anterior) o comando falhará.

### <a name="add-secondary"></a>Adicionar secundário

1. No [portal do Azure](http://portal.azure.com), navegue até o banco de dados que você deseja configurar para replicação geográfica.
2. Na página de banco de dados do SQL, selecione **Replicação geográfica**e, em seguida, selecione a região para criar o banco de dados secundário. Você pode selecionar qualquer uma região diferente da região que hospeda o banco de dados principal, mas recomenda-se a [região par](../best-practices-availability-paired-regions.md) .

    ![Configurar a replicação de localização geográfica](./media/sql-database-geo-replication-portal/configure-geo-replication.png)


4. Selecionar ou configurar o servidor e o nível de preços do banco de dados secundário.

    ![Configurar secundário](./media/sql-database-geo-replication-portal/create-secondary.png)

5. Opcionalmente, você pode adicionar um banco de dados secundário a um pool de banco de dados elástica:

 Para criar o banco de dados secundário em um pool, clique em **pool elástica banco de dados** e selecione um pool no servidor de destino. Um pool já deve existir no servidor de destino como este fluxo de trabalho não criar um pool.

6. Clique em **criar** para adicionar o secundário.
 
6. O banco de dados secundário é criado e o processo de propagação começa. 
 
    ![Configurar secundário](./media/sql-database-geo-replication-portal/seeding0.png)

7. Quando o processo de propagação estiver concluído, o banco de dados secundário exibe seu status.

    ![propagação completa](./media/sql-database-geo-replication-portal/seeding-complete.png)


## <a name="remove-secondary-database"></a>Remover o banco de dados secundário

A operação permanentemente encerra a replicação ao banco de dados secundária e altera a função do secundário para um banco de dados de leitura e gravação regular. Se a conectividade com o banco de dados secundária foi desfeita, o comando é bem sucedida, mas a serão secundária que não se tornar leitura e gravação até depois conectividade é restaurada.  

1. No [portal do Azure](http://portal.azure.com), navegue até o banco de dados principal a parceria de replicação geográfica.
2. Na página do banco de dados SQL, selecione **Replicação geográfica**.
3. Na lista **SECUNDÁRIOS** , selecione o banco de dados que você deseja remover da parceria de replicação geográfica.
4. Clique em **Parar replicação**.

    ![Remover secundário](./media/sql-database-geo-replication-portal/remove-secondary.png)

5. Clicar **Parar duplicação** abre uma janela de confirmação clique em **Sim** para remover o banco de dados da parceria de replicação geográfica (defini-lo para um banco de dados de leitura e gravação não faz parte de qualquer replicação).


## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre Active geográfica replicação, consulte - [Replicação de localização geográfica ativa](sql-database-geo-replication-overview.md)
- Para uma visão de continuidade de negócios e cenários, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md)

