<properties
    pageTitle="Atualização para V12 de banco de dados do SQL Azure usando o portal de Azure | Microsoft Azure"
    description="Explica como atualizar para V12 de banco de dados do SQL Azure incluindo como atualizar bancos de dados da Web e Business e como atualizar um servidor de V11 migrando seus bancos de dados diretamente em um pool de elástica banco de dados usando o portal do Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="08/08/2016"
    ms.author="sstein"/>


# <a name="upgrade-to-azure-sql-database-v12-using-the-azure-portal"></a>Atualização para V12 de banco de dados do SQL Azure usando o portal do Azure


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


V12 de banco de dados do SQL é a versão mais recente para que a atualização de servidores existentes para V12 de banco de dados do SQL é recomendado.
V12 de banco de dados SQL tem várias [vantagens em relação a versão anterior](sql-database-v12-whats-new.md) incluindo:

- Maior compatibilidade com o SQL Server.
- Desempenho aprimorado premium e novos níveis de desempenho.
- [Pools elástica banco de dados](sql-database-elastic-pool.md).

Este artigo fornece instruções para atualizar os servidores de V11 de banco de dados do SQL existentes e bancos de dados para V12 de banco de dados do SQL.

Durante o processo de atualização para V12 você atualizará bancos de dados da Web e de negócios para um novo nível de serviço para que instruções para atualizar bancos de dados da Web e Business estão incluídas.

Além disso, migrar para um [pool de banco de dados elástica](sql-database-elastic-pool.md) pode ser mais econômico que a atualização para níveis de desempenho individuais (níveis de preços) para bancos de dados único. Pools também simplificam o gerenciamento de banco de dados porque você só precisa gerenciar as configurações de desempenho para o pool em vez de separadamente Gerenciando os níveis de desempenho de bancos de dados individuais. Se você tiver bancos de dados em vários servidores convém movê-los para o mesmo servidor e as vantagens de colocá-los em um pool. Você pode facilmente [auto-Migrar bancos de dados de servidores V11 diretamente em pools elástica banco de dados usando o PowerShell](sql-database-upgrade-server-powershell.md). Você também pode usar o portal Migrar bancos de dados de V11 para um pool, mas no portal do, você já deve ter um servidor V12 para criar um pool. Direções são fornecidas neste artigo para criar o pool após a atualização do servidor, se você tiver [bancos de dados que podem se beneficiar um pool](sql-database-elastic-pool-guidance.md).

Observe que seus bancos de dados permanecerá online e continuar a trabalhar em toda a operação de atualização. No momento da transição real para o novo nível de desempenho temporário soltar as conexões ao banco de dados pode acontecer por uma duração muito pequena que normalmente é em torno de 90 segundos, mas pode ser tanto quanto 5 minutos. Se seu aplicativo tiver [falhas temporárias tratamento de encerramentos de conexão](sql-database-connectivity-issues.md) é suficiente para proteger contra queda de conexão no final da atualização.

Atualização para o SQL V12 de banco de dados não pode ser desfeita. Após uma atualização, o servidor não pode ser revertido para V11.

Após a atualização para V12, [recomendações de nível de serviço](sql-database-service-tier-advisor.md) e [Considerações de desempenho de pool elástica](sql-database-elastic-pool-guidance.md) não imediatamente estará disponíveis até que o serviço tem tempo para avaliar as cargas de trabalho no novo servidor. Histórico de recomendação de servidor de V11 não se aplica aos servidores V12 para que ele não é mantido.

## <a name="prepare-to-upgrade"></a>Preparar a atualização

- **Atualizar todos os bancos de dados da Web e Business**: consulte [atualizar todos os bancos de dados da Web e Business](sql-database-upgrade-server-portal.md#upgrade-all-web-and-business-databases) seção abaixo ou consulte [Monitor e gerenciar um pool de banco de dados elástica (PowerShell)](sql-database-elastic-pool-manage-powershell.md).
- **Revisão e Suspender replicação geográfica**: se o seu banco de dados do SQL Azure está configurado para replicação de localização geográfica você deve documentar sua configuração atual e [Parar replicação geográfica](sql-database-geo-replication-portal.md#remove-secondary-database). Após a conclusão da atualização reconfigure seu banco de dados para replicação de localização geográfica.
- **Abrir essas portas se você tiver clientes em uma máquina virtual do Azure**: se o programa cliente se conecta ao V12 de banco de dados do SQL enquanto seu cliente é executado em uma máquina virtual Azure (máquina virtual), você deve abrir intervalos de porta 11999 11000 e 14000-14999 sobre a máquina virtual. Para obter detalhes, consulte [portas para V12 de banco de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md).



## <a name="start-the-upgrade"></a>Iniciar a atualização

1. Na procurar [portal do Azure](https://portal.azure.com/) para o servidor que você deseja atualizar, selecionando **Procurar** > **servidores SQL**e selecionando o servidor de v 2.0 que você deseja atualizar.
2. Selecione a **Atualização mais recente do banco de dados SQL**e, em seguida, selecione **atualizar este servidor**.

      ![Atualizar servidor][1]

3. Atualizando um servidor para a atualização mais recente do banco de dados SQL é permanente e irreversível. Para confirmar a atualização, digite o nome do seu servidor e clique em **Okey**.

## <a name="upgrade-all-web-and-business-databases"></a>Atualizar todos os bancos de dados de negócios e da Web

Se seu servidor tiver qualquer Web ou bancos de dados de negócios que você deve atualizá-los. Durante o processo de atualização para V12 de banco de dados do SQL, você irá atualizar todos os bancos de dados da Web e Business para um novo nível de serviço.    

Para ajudá-lo com a atualização, o serviço de banco de dados SQL recomenda uma camada e desempenho nível de serviço adequado (nível de preço) para cada banco de dados. O serviço recomenda a camada melhor para execução de carga de trabalho do seu banco de dados existente analisando o uso de histórico para seu banco de dados.

3. Na lâmina **atualizar este servidor** selecione cada banco de dados para revisar e selecione os preços recomendado nível para atualizar para. Sempre é possível procurar os níveis de preços disponíveis e selecione aquela que seja adequado ao seu ambiente melhor.


     ![bancos de dados][2]


7. Após clicar na camada sugerida você será apresentada com a lâmina de **Escolher sua camada de preços** onde você pode selecionar uma camada e clique no botão **Selecionar** para alterar para essa camada. Selecione um novo nível para cada banco de dados da Web ou Business.

    O que é importante observar é que o seu banco de dados do SQL não está bloqueado em qualquer nível de desempenho ou nível de serviço específico, portanto como os requisitos da sua alteração de banco de dados que você pode alterar facilmente entre os níveis de serviço disponíveis e níveis de desempenho. Na verdade, Basic, Standard e Premium bancos de dados do SQL são cobrados por hora, e você tiver a capacidade de dimensionar cada banco de dados para cima ou para baixo 4 vezes dentro de um período de 24 horas.

    ![recomendações][6]


Depois de todos os bancos de dados no servidor são elegíveis estiver pronto para iniciar a atualização

## <a name="confirm-the-upgrade"></a>Confirmar a atualização

3. Quando todos os bancos de dados no servidor estão qualificados para atualização é necessário **Digite o nome do servidor** para confirmar que você deseja executar a atualização e clique em **Okey**.

    ![Verifique se a atualização][3]


4. A atualização é iniciado e exibe o progresso em notificação. O processo de atualização é iniciado. Dependendo dos detalhes de seus bancos de dados específicos a atualização para V12 pode levar alguns instantes. Durante esse tempo todos os bancos de dados no servidor permanecerá on-line mas servidor e ações de gerenciamento de banco de dados será restritas.

    ![atualização em andamento][4]

    No momento da transição real no desempenho do novo nível temporário soltar as conexões ao banco de dados pode acontecer por uma duração muito pequena (normalmente medida em segundos). Se um aplicativo tiver manipulação de falhas temporárias (lógica de repetição) for encerramentos de conexão é suficiente para proteger contra queda de conexão no final da atualização.

5. Após a conclusão da operação de atualização a **Atualização mais recente** lâmina exibirão **ativado**.

    ![V12 habilitado][5]  

## <a name="move-your-databases-into-an-elastic-database-pool"></a>Mover seus bancos de dados em um pool de banco de dados elástica

No [portal do Azure](https://portal.azure.com/) procure o servidor V12 e clique em **Adicionar pool**.

- ou -

Se você vir uma mensagem informando **clique aqui para exibir os pools de banco de dados elástica recomendado para este servidor**, clique em para criar facilmente um pool que é otimizado para bancos de dados do seu servidor. Para obter detalhes, consulte [Considerações de preço e desempenho para um pool de elástica banco de dados](sql-database-elastic-pool-guidance.md).

![Adicionar pool a um servidor][7]

Siga as instruções no artigo [criar um pool de banco de dados elástica](sql-database-elastic-pool.md) para concluir a criação de seu pool.

## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>Monitorar bancos de dados após a atualização para V12 de banco de dados SQL

>[AZURE.IMPORTANT] Atualizar para a versão mais recente do SQL Server Management Studio (SSMS) para tirar proveito dos novos recursos v12. [Baixar o SQL Server Management Studio] (https://msdn.microsoft.com/library/mt238290.aspx).

Após a atualização, monitorar ativamente o banco de dados para garantir que aplicativos estão sendo executados com o desempenho desejado e, em seguida, otimizar as configurações conforme necessário.

Além de monitorar bancos de dados individuais, você pode monitorar o banco de dados elástica pools [Monitor, gerenciar e o tamanho de um pool de banco de dados elástica com o portal do Azure](sql-database-elastic-pool-manage-portal.md) ou com o [PowerShell](sql-database-elastic-pool-manage-powershell.md).


**Dados de consumo de recursos:** Basic, Standard e Premium bancos de dados de consumo de recursos estão disponíveis através do [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) departamento de trânsito do banco de dados do usuário. Essa DMV oferece perto informações de consumo de recurso tempo real no detalhamento de segunda 15 para hora anterior da operação. O consumo de porcentagem DTU um intervalo é calculado como o consumo de porcentagem máxima das dimensões de CPU, IO e log. Aqui está uma consulta para calcular o consumo de porcentagem DTU médio ao longo de última hora:

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Informações adicionais de monitoramento:

- [Orientações sobre desempenho de banco de dados do SQL azure para bancos de dados único](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Considerações de preço e desempenho para um pool de elástica banco de dados](sql-database-elastic-pool-guidance.md).
- [Monitoramento de banco de dados do SQL Azure usando modos de exibição de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md)




**Alertas:** Configure 'Alertas' no portal do Azure para notificá-lo quando o consumo de DTU para um banco de dados atualizado abordagens certos alto nível. Alertas de banco de dados podem ser configurado no portal do Azure de várias métricas de desempenho como DTU, CPU, IO e Log. Navegue até o banco de dados e selecione **regras de alerta** na lâmina **configurações** .

Por exemplo, você pode configurar um alerta de email em "DTU porcentagem", se o valor médio de porcentagem DTU excede 75% sobre os últimos 5 minutos. Consulte [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) para saber mais sobre como configurar as notificações de alerta.





## <a name="next-steps"></a>Próximas etapas

- [Verificar se há pool recomendações e criar um pool](sql-database-elastic-pool-create-portal.md).
- [Alterar o nível de desempenho e nível de serviço do seu banco de dados](sql-database-scale-up.md).



## <a name="related-links"></a>Links relacionados

- [O que há de novo no V12 de banco de dados SQL](sql-database-v12-whats-new.md)
- [Planejar e preparar a atualização para V12 de banco de dados SQL](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-upgrade-server-portal/latest-sql-database-update.png
[2]: ./media/sql-database-upgrade-server-portal/upgrade-server2.png
[3]: ./media/sql-database-upgrade-server-portal/upgrade-server3.png
[4]: ./media/sql-database-upgrade-server-portal/online-during-upgrade.png
[5]: ./media/sql-database-upgrade-server-portal/enabled.png
[6]: ./media/sql-database-upgrade-server-portal/recommendations.png
[7]: ./media/sql-database-upgrade-server-portal/new-elastic-pool.png
