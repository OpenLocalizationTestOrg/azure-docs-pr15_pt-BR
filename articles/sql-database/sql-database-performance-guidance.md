<properties
    pageTitle="Banco de dados do SQL Azure e o desempenho para bancos de dados único | Microsoft Azure"
    description="Este artigo pode ajudá-lo a determinar qual nível de serviço para escolher a seu aplicativo. Ele também recomenda maneiras de ajustar o aplicativo obter o máximo do Azure SQL Database."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="09/13/2016"
    ms.author="carlrab" />

# <a name="azure-sql-database-and-performance-for-single-databases"></a>Desempenho para bancos de dados único e banco de dados do SQL Azure

Banco de dados do SQL Azure oferece três [níveis de serviço](sql-database-service-tiers.md): Basic, Standard e Premium. Cada nível de serviço terminantemente isola os recursos que seu banco de dados do SQL pode usar e garante desempenho previsível para esse nível de serviço. Neste artigo, oferecemos orientação que pode ajudá-lo a escolher o nível de serviço do aplicativo. Também discutimos maneiras que você pode ajustar seu aplicativo para obter o máximo do Azure SQL Database.

>[AZURE.NOTE] Este artigo se concentra na orientação de desempenho para bancos de dados único no Azure SQL Database. Para orientação de desempenho relacionada a pools de banco de dados elástica, consulte [Considerações de preço e desempenho para pools de elástica banco de dados](sql-database-elastic-pool-guidance.md). Observe, contudo, que você pode aplicar muitas das recomendações ajustes neste artigo aos bancos de dados em um pool de banco de dados elástica e obter benefícios de desempenho semelhante.

Estes são os três níveis de serviço de banco de dados do Azure SQL que você pode escolher (desempenho é medido em unidades de produtividade do banco de dados, ou [DTUs](sql-database-what-is-a-dtu.md):

- **Básica**. A previsão de bom desempenho de ofertas de nível de serviço básico para cada banco de dados, a hora por hora. Em um banco de dados básico, recursos suficientes suportam bom desempenho em um banco de dados pequeno que não tem várias solicitações simultâneas.
- **Padrão**. O nível de serviço padrão oferece a capacidade de previsão de melhor desempenho e eleva o padrão para bancos de dados que têm várias solicitações simultâneas, como o grupo de trabalho e aplicativos web. Quando você escolhe um banco de dados de nível de serviço padrão, você pode dimensionar seu aplicativo de banco de dados com base no desempenho previsível, minuto minuto.
- **Premium**. O nível de serviço Premium oferece desempenho previsível, segunda sobre segundo, para cada banco de dados Premium. Quando você escolhe o nível de serviço Premium, você pode dimensionar seu aplicativo de banco de dados com base na carga de pico para esse banco de dados. O plano remove casos em que desempenho variação pode causar pequenas consultas para levar mais tempo do que o esperado em operações sensíveis à latência. Esse modelo pode simplificar muito os ciclos de validação de desenvolvimento e produtos para aplicativos que precisam fazer fortes declarações sobre as necessidades de recursos de pico, variação de desempenho ou latência da consulta.

Em cada camada de serviço, você definir o nível de desempenho, para que você tenha flexibilidade para pagar somente para a capacidade de que você precisa. Você pode [Ajustar a capacidade](sql-database-scale-up.md), para cima ou para baixo, como alterações de carga de trabalho. Por exemplo, se sua carga de trabalho do banco de dados for alta durante a estação de compra de volta às aulas, você pode aumentar o nível de desempenho para o banco de dados para um horário definido, julho por meio de setembro. Você pode reduzi-lo ao seu estação de pico quando terminar. Você pode minimizar o que você paga otimizando seu ambiente de nuvem para a sazonalidade da sua empresa. Esse modelo também funciona bem para ciclos de lançamento do produto de software. Uma equipe de teste pode alocar capacidade enquanto ele execuções de teste e solta essa capacidade quando ele terminar o teste. Em um modelo de solicitação de capacidade, você paga capacidade conforme necessário e evitar gastos com recursos dedicados que você pode usar raramente.

## <a name="why-service-tiers"></a>Por que níveis de serviço?

Embora cada carga de trabalho do banco de dados pode diferir, a finalidade de níveis de serviço é fornecer a capacidade de previsão de desempenho em vários níveis de desempenho. Clientes com requisitos de recursos do banco de dados grande escala podem trabalhar em um ambiente de computação mais dedicado.

### <a name="common-service-tier-use-cases"></a>Casos de uso de nível de serviço comuns

#### <a name="basic"></a>Básico

- **Você está começando a usar o Azure SQL Database**. Aplicativos que estão em desenvolvimento muitas vezes não é necessário níveis de alto desempenho. Bancos de dados básicos são um ambiente ideal para desenvolvimento de banco de dados, com um preço baixo.
- **Você tem um banco de dados com um único usuário**. Aplicativos que associar um único usuário a um banco de dados geralmente não têm requisitos de alto desempenho e concorrência. Esses aplicativos são candidatos para a camada de serviço básico.

#### <a name="standard"></a>Padrão

- **Seu banco de dados tem várias solicitações simultâneas**. Os aplicativos que atendem a mais de um usuário por vez geralmente precisam maiores níveis de desempenho. Por exemplo, sites que recebe tráfego moderado ou aplicativos departamentais que exigem mais recursos são bons candidatos para a camada de serviço padrão.

#### <a name="premium"></a>Premium

A maioria dos casos de uso de camadas para serviço Premium tem uma ou mais dessas características:

- **Pico alto carregar**. Um aplicativo que requer muita CPU, memória ou entrada/saída (e/s) para concluir seus operações requer um nível de alto desempenho dedicado. Por exemplo, uma operação de banco de dados conhecida consumir várias cores de CPU por um longo período é um candidato para o nível de serviço Premium.
- **Muitas solicitações simultâneas**. Alguns aplicativos de banco de dados de serviço muitas solicitações simultâneas, por exemplo, quando servir um site que tem um grande volume de tráfego. Básico e padrão níveis de serviço limitam o número de solicitações simultâneas por banco de dados. Aplicativos que exigem mais conexões seriam necessário escolher um tamanho de reserva apropriado para lidar com o número máximo de solicitações necessárias.
- **Baixa latência**. Alguns aplicativos precisam garantir uma resposta do banco de dados em tempo mínimo. Se um procedimento armazenado específico é chamado como parte de uma operação de cliente mais ampla, você pode ter um requisito para ter um retorno de chamada em não mais do que 20 milissegundos, 99% do tempo. Esse tipo dos benefícios dos aplicativos do nível de serviço Premium, para garantir que a alimentação de computação necessária está disponível.

O nível de serviço que você precisa para seu banco de dados do SQL depende os requisitos de carga de pico para cada dimensão de recurso. Alguns aplicativos usam uma quantidade simples de um único recurso, mas têm requisitos significativos para outros recursos.

## <a name="service-tier-capabilities-and-limits"></a>Limites e recursos de nível de serviço
Cada nível de desempenho e nível de serviço está associado a limites diferentes e características de desempenho. Esta tabela descreve essas características para um único banco de dados.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

As seções a seguir têm mais informações sobre como exibir uso relacionado a esses limites.

### <a name="maximum-in-memory-oltp-storage"></a>Armazenamento máximo OLTP na memória

Você pode usar o modo de exibição de **sys.dm_db_resource_stats** para monitorar o uso de armazenamento do Azure na memória. Para obter mais informações sobre o monitoring, consulte [Monitor na memória OLTP armazenamento](sql-database-in-memory-oltp-monitoring.md).

>[AZURE.NOTE] Atualmente, preview (OLTP) de processamento de transações online do Azure na memória tem suporte apenas para bancos de dados único. Você não pode usá-lo em bancos de dados em pools elástica banco de dados.

### <a name="maximum-concurrent-requests"></a>Solicitações simultâneas máximos

Para ver o número de solicitações simultâneas, execute essa consulta Transact-SQL no seu banco de dados do SQL:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Para analisar a carga de trabalho de um banco de dados do SQL Server no local, modificar essa consulta para filtrar o banco de dados específico que você deseja analisar. Por exemplo, se você tiver um banco de dados local chamado MyDatabase, essa consulta Transact-SQL retorna a contagem de solicitações simultâneas no banco de dados:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Isso é apenas um instantâneo em um único ponto no tempo. Para obter uma melhor compreensão das suas necessidades de solicitação simultânea e a carga de trabalho, você precisará coletar muitos exemplos ao longo do tempo.

### <a name="maximum-concurrent-logins"></a>Máximo logon simultânea

Você pode analisar seus padrões de usuário e o aplicativo para ter uma ideia da frequência de logon. Você também pode executar cargas reais em um ambiente de teste para certificar-se de que você não estiver atingir este ou outros limites que abordaremos neste artigo. Existe uma única consulta ou a exibição de gerenciamento dinâmico (departamento de trânsito) que pode mostrar simultâneas contagens de login ou o histórico.

Se vários clientes usam a mesma cadeia de conexão, o serviço autentica cada logon. Se 10 usuários simultaneamente se conectar a um banco de dados usando o mesmo nome de usuário e senha, seria 10 logon simultânea. Esse limite se aplica somente a duração de login e autenticação. Se os mesmos 10 usuários sequencialmente se conectar ao banco de dados, o número de logon simultâneo nunca seria maior que 1.

>[AZURE.NOTE] Atualmente, esse limite não se aplica aos bancos de dados em pools elástica banco de dados.

### <a name="maximum-sessions"></a>Número máximo de sessões

Para ver o número de sessões ativas atuais, execute essa consulta Transact-SQL no seu banco de dados do SQL:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Se você estiver analisando uma carga de trabalho do SQL Server no local, modifique a consulta concentrar-se em um banco de dados específico. Essa consulta o ajudará a determinar a necessidade de sessão possíveis para o banco de dados se você estiver considerando movê-lo para Azure SQL Database.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Novamente, essas consultas retornam uma contagem no momento. Se você coletar vários exemplos ao longo do tempo, você terá a melhor compreensão de sua sessão de usar.

Para análise de banco de dados SQL, você pode obter estatísticas históricas em sessões. **Sys.resource_stats**de consulta e use a coluna **active_session_count** . Consulte a próxima seção para obter mais informações sobre como usar este modo de exibição.

## <a name="monitor-resource-use"></a>Monitorar o uso de recursos
Dois modos de exibição podem ajudá-lo a monitorar o uso de recursos para um banco de dados do SQL relativo seu nível de serviço:

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
Você pode usar o modo de exibição de [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) em cada banco de dados do SQL. A exibição **sys.dm_db_resource_stats** mostra dados de uso do recurso recentes relativo a camada de serviço. Média porcentagens para CPU, dados e/s, gravações de log e memória são registradas a cada 15 segundos e são mantidas para 1 hora.

Porque este modo de exibição fornece uma olhada mais granular no uso de recursos, use **sys.dm_db_resource_stats** primeiro para qualquer análise do estado atual ou solução de problemas. Por exemplo, esta consulta mostra o uso de recursos médio e máximo do banco de dados atual ao longo de última hora:

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data I/O in percent',
        MAX(avg_data_io_percent) AS 'Maximum data I/O in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Para outras consultas, consulte os exemplos na [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresourcestats"></a>sys.resource_stats

O modo de exibição de [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) do banco de dados **mestre** tem informações adicionais que podem ajudá-lo a monitorar o desempenho do seu banco de dados do SQL no seu nível de desempenho e nível de serviço específico. Os dados são coletados cada 5 minutos e são mantidos para aproximadamente 35 dias. Este modo de exibição é útil para uma análise histórica longo prazo de como o seu banco de dados do SQL usa recursos.

O gráfico a seguir mostra o uso de recursos do CPU para um banco de dados Premium com o nível de desempenho de P2 para cada hora em uma semana. Este gráfico começa na segunda-feira, mostra 5 dias de trabalho e, em seguida, um fim de semana, quando muito menos acontece no aplicativo.

![Uso de recursos de banco de dados SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Dos dados, este banco de dados atualmente tem uma carga de CPU pico de pouco mais de 50% o uso da CPU relativo o nível de desempenho de P2 (meio-dia na terça-feira). Se CPU seja o fator dominante no perfil de recursos do aplicativo, você pode decidir que P2 é o nível de desempenho certo para garantir que a carga de trabalho sempre se encaixa. Se você espera um aplicativo para crescer ao longo do tempo, é uma boa ideia ter um buffer extra de recursos para que o aplicativo nunca não atinge o limite de nível de desempenho. Se você aumentar o nível de desempenho, você pode ajudar a evitar erros de atendimento ao cliente-visível que podem ocorrer quando um banco de dados não tem energia suficiente para processar solicitações efetivamente, especialmente em ambientes sensíveis à latência. Um exemplo é um banco de dados que ofereça suporte a um aplicativo que pinta páginas da Web com base nos resultados de chamadas de banco de dados.

Observe que outros tipos de aplicativos podem interpretar o mesmo gráfico diferente. Por exemplo, se um aplicativo tenta processar dados folha de pagamento cada dia e tiver o mesmo gráfico, esse tipo de modelo de "trabalho em lotes" pode fazer fino em um nível de desempenho P1. O nível de desempenho P1 tem 100 DTUs em comparação com 200 DTUs no nível de desempenho de P2. O nível de desempenho P1 fornece metade do desempenho do nível de desempenho de P2. Portanto, 50% de uso da CPU em P2 equivale ao uso de CPU de 100 por cento em P1. Se o aplicativo não tiver tempos limite, talvez não importa se um trabalho levar 2 horas ou 2,5 horas para concluir, se ele obtém realizado hoje. Um aplicativo nesta categoria provavelmente pode usar um nível de desempenho P1. Você pode aproveitar o fato de que há períodos de tempo durante o dia quando o uso de recursos é menor, para que qualquer "pico grande" talvez passando para um dos vales posteriormente no dia. O nível de desempenho P1 talvez seja bom para esse tipo de aplicativo (e economizar dinheiro), desde que os trabalhos podem terminar em tempo de cada dia.

Expõe de banco de dados do SQL Azure consumida informações do recurso para cada banco de dados ativo no modo de exibição **sys.resource_stats** do banco de dados **mestre** em cada servidor. Os dados na tabela são agregados para intervalos de 5 minutos. A níveis de serviço Basic, Standard e Premium, os dados podem levar mais de 5 minutos para aparecer na tabela, para que esses dados são mais útil para análise histórica em vez de análise de quase em tempo real. Consulte o **sys.resource_stats** modo de exibição para ver o histórico recente de um banco de dados e para validar se a reserva que você escolheu entregue o desempenho quando necessário.

>[AZURE.NOTE] Você deve estar conectado ao banco de dados **mestre** do seu servidor de banco de dados SQL lógico para consultar **sys.resource_stats** nos exemplos a seguir.

Este exemplo mostra como os dados nesta exibição são expostos:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![O modo de exibição de catálogo de sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

O exemplo a seguir mostra diferentes maneiras que você pode usar o modo de exibição de catálogo de **sys.resource_stats** para obter informações sobre como o seu banco de dados do SQL usa recursos:

1. Para examinar os recursos da semana passada use para userdb1 o banco de dados, você pode executar essa consulta:

        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;

2. Para avaliar quanto sua carga de trabalho se ajuste o nível de desempenho, você precisa fazer busca detalhada em cada aspecto das métricas recurso: CPU, leituras, gravações, número de trabalhadores e número de sessões. Aqui está uma consulta revisada usando **sys.resource_stats** para relatar os valores médio e máximos das seguintes métricas de recurso:

        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data I/O use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data I/O use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

3. Com essas informações sobre os valores médio e máximos de métrica cada recurso, você pode avaliar como sua carga de trabalho se encaixa o nível de desempenho que você escolheu. Geralmente, os valores médios de **sys.resource_stats** oferecem uma linha de base boa usar contra o tamanho de destino. Ele deve ser seu punição principal medida. Por exemplo, você pode estar usando o nível de serviço padrão com o nível de desempenho de S2. A média usar porcentagens para leituras de CPU e e/s e gravações são abaixo de 40%, o número médio de trabalhadores está abaixo 50 e o número médio de sessões está abaixo 200. Sua carga de trabalho pode se encaixa no nível de desempenho de S1. É fácil ver se seu banco de dados se ajusta os limites de sessão e trabalhador. Para ver se um banco de dados se encaixa a um nível de desempenho inferior em relação à CPU, lê e gravações, divida o número DTU do nível de desempenho inferior pelo número DTU de seu nível de desempenho atual e, em seguida, multiplique o resultado por 100:

    * *S1 DTU / S2 DTU* 100 = 20 / 50* 100 = 40 * *

    O resultado é a diferença de desempenho relativo entre os níveis de desempenho de dois em porcentagem. Se o uso de recursos não exceder esse valor, sua carga de trabalho pode se encaixa no nível de desempenho inferior. No entanto, você precisará examinar todos os intervalos de valores de uso de recursos e determinar, por porcentagem, frequência caibam sua carga de trabalho do banco de dados com o nível de desempenho inferior. A seguinte consulta gera a porcentagem de ajuste por dimensão do recurso, com base no limite de 40% que estamos calculados neste exemplo:

        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

    Com base em seu objetivo de nível de serviço (SLO) do banco de dados, você pode decidir se sua carga de trabalho se encaixa o nível de desempenho inferior. Se sua carga de trabalho do banco de dados SLO é 99,9% e a consulta anterior retorna valores maiores que 99,9% para todas as dimensões de recurso três, sua carga de trabalho provavelmente caberá com o nível de desempenho inferior.

    Olhando para a porcentagem de ajuste também lhe percepção se tiver que ir até o próximo nível mais alto de desempenho para atender às sua SLO. Por exemplo, userdb1 mostra o seguinte uso de CPU da semana passada:

  	| Porcentagem de CPU média | Porcentagem de CPU máxima |
  	|---|---|
  	| 24,5 | 100,00 |

    A CPU média é sobre um quarto do limite de nível de desempenho, que seria se enquadram o nível de desempenho do banco de dados. Mas, o valor máximo mostra que o banco de dados atinge o limite do nível de desempenho. Você precisa para se mover para o próximo nível mais alto de desempenho? Você precisa observar como muitas vezes atinge sua carga de trabalho 100 por cento e, em seguida, compará-lo à sua carga de trabalho do banco de dados SLO.

        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent’
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data I/O fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

    Se essa consulta retornará um valor menor que 99,9% para qualquer uma das dimensões três recurso, considerar o mover para o próximo nível mais alto de desempenho ou usar técnicas de ajuste de aplicativo para reduzir a carga no banco de dados SQL.

4. Neste exercício também considera o aumento de carga de trabalho projetado no futuro.

## <a name="tune-your-application"></a>Ajustar o aplicativo

No local tradicional SQL Server, o processo de planejamento de capacidade inicial geralmente é separado do processo de executando um aplicativo em produção. Licenças de hardware e produtos adquiridas primeiro e ajuste de desempenho é feito posteriormente. Quando você usa o banco de dados do SQL Azure, é uma boa ideia para integrar o fluxo de processo de executando um aplicativo e ajuste-lo. Com o modelo de pagamento para capacidade sob demanda, você pode ajustar seu aplicativo para usar os recursos mínimos necessários agora, em vez de excesso de provisionamento em hardware com base em estimativas dos planos de crescimento futuro para um aplicativo, que geralmente estão incorretas. Alguns clientes podem optar por não ajustar um aplicativo e optar por Superdimensionamento recursos de hardware. Essa abordagem pode ser uma boa ideia se você não deseja alterar um aplicativo-chave durante um período de ocupado. Mas, ajuste de um aplicativo pode minimizar requisitos de recursos e faturas mensais inferiores ao usar os níveis de serviço no Azure SQL Database.

### <a name="application-characteristics"></a>Características do aplicativo

Embora os níveis de serviço de banco de dados do Azure SQL são projetados para melhorar o desempenho estabilidade e previsão para um aplicativo, algumas práticas recomendadas podem ajudá-lo a ajustar o aplicativo para melhor tirar proveito dos recursos em um nível de desempenho. Embora muitos aplicativos têm ganhos significativos de desempenho simplesmente alternando para um nível superior do desempenho ou precisam de alguns aplicativos de nível de serviço, ajustes adicionais para usufruir dos benefícios de um nível superior do serviço. Para melhorar o desempenho, considere a possibilidade de ajuste de aplicativo adicional para aplicativos que têm estas características:

- **Aplicativos que têm desempenho lento devido comportamento "informal"**. Aplicativos bem informais tornam as operações de acesso de excesso de dados que são sensíveis a latência de rede. Você talvez precise modificar esses tipos de aplicativos para reduzir o número de operações de acesso de dados no banco de dados do SQL. Por exemplo, você pode melhorar o desempenho do aplicativo usando técnicas como lotes consultas ad-hoc ou movendo as consultas para procedimentos armazenados. Para obter mais informações, consulte [consultas de lote](#batch-queries).
- **Bancos de dados com uma carga de trabalho intenso que não forem aceitas por uma única máquina inteira**. Bancos de dados que excedem os recursos o mais alto nível de desempenho Premium podem beneficiar dimensionamento a carga de trabalho. Para obter mais informações, consulte [fragmentação de bancos de dados](#cross-database-sharding) e [partição funcional](#functional-partitioning).
- **Aplicativos que têm consultas com qualidade inferior**. Aplicativos, especialmente aqueles na camada de acesso a dados, que mal ajustou consultas podem não se beneficiar de um nível superior do desempenho. Isso inclui consultas que não têm uma cláusula WHERE, faltam índices ou tiveram desatualizadas estatísticas. Esses aplicativos beneficiar técnicas de ajuste de desempenho de consulta padrão. Para obter mais informações, consulte [ausente índices](#missing-indexes) e [dicas e ajuste de consulta](#query-tuning-and-hinting).
- **Design de acesso de aplicativos que possuem dados com qualidade inferior**. Aplicativos que têm problemas de concorrência de acesso de dados inerente, travado por exemplo, não podem se beneficiar de um nível superior do desempenho. Considere a possibilidade de reduzindo idas no banco de dados do SQL Azure em cache de dados no lado do cliente com o serviço de cache Azure ou outra tecnologia de cache. Consulte [cache de camada de aplicativo](#application-tier-caching).

## <a name="tuning-techniques"></a>Técnicas de ajuste
Nesta seção, examinaremos algumas técnicas que você pode usar para ajustar o Azure SQL Database para obter o melhor desempenho para o seu aplicativo e executá-lo no nível de desempenho possível mais baixos. Algumas dessas técnicas correspondem tradicional ajuste do SQL Server práticas recomendadas, mas outras são específicas aos Azure SQL Database. Em alguns casos, você pode examinar os recursos consumidos para um banco de dados localizar áreas para ajustar ainda mais e estender técnicas tradicionais do SQL Server para trabalhar no Azure SQL Database.

### <a name="azure-portal-tools"></a>Ferramentas de portal Azure
Você encontrará duas ferramentas no portal do Azure que podem ajudar a analisar e corrigir problemas de desempenho com seu banco de dados do SQL:

- [Compreensão de desempenho de consulta](sql-database-query-performance.md)
- [Supervisor de banco de dados SQL](sql-database-advisor.md)

O portal do Azure tem mais informações sobre ambos dessas ferramentas e como usá-las. Para eficientemente diagnosticar e corrigir problemas, recomendamos que você primeiro tente as ferramentas no portal do Azure. Recomendamos que você use o manual ajuste abordagens que abordaremos a seguir, faltam índices e ajuste de consulta, em casos especiais.

### <a name="missing-indexes"></a>Índices ausentes
Um problema comum em desempenho de banco de dados OLTP relaciona o design de banco de dados físico. Muitas vezes, esquemas de banco de dados são projetados e enviados sem testes em escala (ou no carregamento ou volume de dados). Infelizmente, o desempenho de um plano de consulta pode ser aceitável em uma pequena escala, mas diminui substancialmente sob volumes de dados de nível de produção. A fonte mais comum desse problema é a falta de índices apropriados para satisfazer filtros ou outras restrições em uma consulta. Muitas vezes, manifestos de índices ausentes como uma tabela varredura quando uma busca de índice pode ser suficiente.

Neste exemplo, o plano de consulta selecionada usa uma varredura quando uma busca seria suficiente:

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![Um plano de consulta com índices ausentes](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Banco de dados do SQL Azure pode ajudá-lo a localizar e corrigir comuns ausente indexar condições. DMVs que são criados no banco de dados do Azure SQL olhar compilações de consulta em que um índice seria reduzir significativamente o custo estimado para executar uma consulta. Durante a execução da consulta, o banco de dados SQL rastreia frequência cada plano de consulta é executado e controla o espaço estimado entre o plano de consulta em execução e o imaginado onde existe o índice. Você pode usar esses DMVs supor rapidamente quais alterações ao design de banco de dados físico podem melhorar o custo total de carga de trabalho para um banco de dados e sua carga de trabalho real.

Você pode usar essa consulta para avaliar possíveis índices ausentes:

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

Neste exemplo, a consulta resultou nessa sugestão:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Após sua criação, essa mesma instrução SELECT escolhe um plano diferente, que usa uma busca em vez de uma verificação e em seguida, executa o plano com mais eficiência:

![Um plano de consulta com índices corrigidos](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

A visão de chave é a capacidade de e/s de um sistema de mercadoria compartilhado, mais limitada do que uma máquina de servidor dedicado. Não há uma premium na minimização desnecessários i/o para tirar o máximo proveito do sistema na DTU de cada nível de desempenho os níveis de serviço do Azure SQL Database. Design de banco de dados físico apropriado escolhas podem melhorar significativamente a latência para consultas individuais, melhorar a produtividade do solicitações simultâneas manipulados por unidade de escala e minimizar os custos necessários para satisfazer a consulta. Para obter mais informações sobre o índice ausente DMVs, consulte [. DM db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Ajuste de consulta e dicas
O otimizador de consulta do banco de dados do SQL Azure é semelhante ao tradicional otimizador do SQL Server. A maioria das práticas recomendadas para ajuste consultas e Noções básicas sobre o raciocínio limitações de modelo para o otimizador de consultas também se aplicam ao Azure SQL Database. Se você ajustar as consultas no banco de dados do SQL Azure, você pode receber o benefício adicional de reduzir exigências de recursos agregados. Seu aplicativo pode ser capaz de executar em um custo menor que um equivalente não sintonizado porque ele pode ser executado em um nível de desempenho inferior.

Um exemplo que é comum no SQL Server e que também se aplica ao Azure SQL Database é como o otimizador de consulta "fareja" parâmetros. Durante a compilação, o otimizador de consulta avalia o valor atual de um parâmetro para determinar se ela pode gerar um plano de consulta mais ideal. Embora essa estratégia muitas vezes pode levar a um plano de consulta significativamente mais rápido do que um plano compilado sem valores de parâmetro conhecidos, atualmente ele funciona imperfectly ambos no SQL Server e no Azure SQL Database. Às vezes, o parâmetro não está espionado e às vezes, o parâmetro está espionado mas o plano gerado é quase o ideal para o conjunto completo de valores de parâmetro em uma carga de trabalho. A Microsoft inclui dicas de consulta (diretivas) para que você possa especificar sua intenção mais deliberadamente e substituir o comportamento padrão de rastreamento de parâmetro da. Muitas vezes, se você usar dicas, você pode corrigir casos em que o comportamento padrão de SQL Server ou banco de dados do SQL Azure é imperfeito para uma carga de trabalho do cliente específico.

O exemplo a seguir demonstra como o processador da consulta pode gerar um plano que é quase o ideal para requisitos de desempenho e recursos. Este exemplo mostra também que, se você usar uma dica de consulta, você pode reduzir requisitos de recursos e de tempo de execução de consulta do banco de dados SQL:

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

O código de configuração cria uma tabela que tem Inclinado distribuição de dados. O plano de consulta ideais difere com base no qual parâmetro está selecionado. Infelizmente, o comportamento do cache de plano não sempre recompilar a consulta com base no valor do parâmetro mais comuns. Portanto, é possível para um plano com qualidade inferior sejam armazenados em cache e usadas para muitos valores, mesmo quando um plano diferente pode ser uma melhor opção de plano em média. O plano de consulta cria dois procedimentos armazenados que são idênticos, exceto que uma tem uma dica de consulta especial.

**Exemplo, parte 1**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**Exemplo, parte 2**

(Recomendamos que você aguarde pelo menos 10 minutos antes de começar a parte 2 do exemplo, para que os resultados são distintos dos dados de telemetria resultante.)

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Cada parte deste exemplo tenta executar uma instrução insert parametrizada 1.000 vezes (para gerar uma carga suficiente para usar como um conjunto de dados de teste). Quando ele executa procedimentos armazenados, o processador de consultas examina o valor do parâmetro que é passado para o procedimento durante a sua primeira compilação (parâmetro "rastreamento"). O processador armazena o plano resultante e usa para chamadas posteriores, mesmo se o valor do parâmetro for diferente. O plano ideal não pode ser usado em todos os casos. Às vezes você precisa orientar o otimizador para escolher um plano que é melhor para o caso médio em vez de caso específico de quando a consulta foi compilada pela primeira vez. Neste exemplo, o plano inicial gera um plano de "verificação" que lê todas as linhas para cada valor que corresponda ao parâmetro de localizar:

![Ajuste usando um plano de verificação de consulta](./media/sql-database-performance-guidance/query_tuning_1.png)

Como podemos executado o procedimento, usando o valor 1, o plano resultante foi ideal para o valor 1, mas foi quase o ideal para todos os outros valores na tabela. O resultado provavelmente não seria se você tivesse escolher cada plano aleatoriamente, porque o plano executa mais lentamente e usa mais recursos.

Se você executar o teste com `SET STATISTICS IO` definida como `ON`, o trabalho de verificação lógica neste exemplo é feito nos bastidores. Você pode ver o que há 1.148 leituras realizadas pelo plano (que é ineficaz, se for o caso médio retornar apenas uma linha):

![Ajuste usando um exame lógico de consulta](./media/sql-database-performance-guidance/query_tuning_2.png)

A segunda parte do exemplo usa uma dica de consulta para informar o otimizador de usar um valor específico durante o processo de compilação. Nesse caso, ele força o processador de consultas para ignorar o valor que é passado como o parâmetro, e, em vez disso assuma `UNKNOWN`. Isso se refere a um valor que tem a frequência média na tabela (ignorando skew). O plano resultante é um plano de baseada em busca de mensagens que é mais rápido e usa menos recursos, em média, que o plano na parte 1 deste exemplo:

![Ajuste de consulta usando uma dica de consulta](./media/sql-database-performance-guidance/query_tuning_3.png)

Você pode ver o efeito na tabela **sys.resource_stats** (há um atraso entre o momento que você executa o teste e quando os dados preenchem a tabela). Para este exemplo, parte 1 executadas durante a janela de tempo de 22:25:00 e parte 2 executado às 22:35:00. Observe que a janela de tempo anterior usada mais recursos na janela hora posterior daquele (devido a aperfeiçoamentos de eficiência de plano).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Ajuste de exemplo de resultados da consulta](./media/sql-database-performance-guidance/query_tuning_4.png)

>[AZURE.NOTE] Embora o volume neste exemplo é intencionalmente pequeno, o efeito de parâmetros com qualidade inferior pode ser substancial, especialmente em bancos de dados maiores. A diferença, em casos extremos, pode estar entre segundos para casos rápidos e horas para casos lentas.

Você pode examinar **sys.resource_stats** para determinar se o recurso para um teste usa recursos mais ou menos do que outro teste. Quando você comparar dados, separe o intervalo de testes de modo que não estiverem na mesma janela 5 minutos no modo de exibição **sys.resource_stats** . O objetivo do exercício é minimizar a quantidade total de recursos usados e não para minimizar os recursos de pico. Em geral, otimizar um pedaço de código para latência também reduz o consumo de recursos. Certifique-se de que as alterações feitas ao aplicativo são necessárias e que as alterações não afetam negativamente a experiência do cliente para alguém que esteja usando as dicas de consulta no aplicativo.

Se uma carga de trabalho tem um conjunto de consultas de repetição, geralmente faz sentido para capturar e validar a otimização de suas opções de plano porque orienta a unidade de tamanho mínimo recurso necessária para hospedar o banco de dados. Depois de validá-la, ocasionalmente reexaminar os planos para ajudá-lo a garantir que eles não têm degradado. Você pode saber mais sobre [as dicas de consulta (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Fragmentação de bancos de dados
Como banco de dados do SQL Azure é executado em hardware de mercadoria, os limites de capacidade para um único banco de dados são inferiores para uma instalação do SQL Server local tradicional. Alguns clientes usarem técnicas de fragmentação para distribuídas operações de banco de dados em vários bancos de dados quando as operações não ajustarem dentro dos limites de um banco de dados no Azure SQL Database. A maioria dos clientes que usam técnicas de fragmentação no Azure SQL Database dividir seus dados em uma única dimensão entre vários bancos de dados. Para essa abordagem, você precisa compreender que aplicativos OLTP geralmente executam transações que se aplicam a apenas uma linha ou a um pequeno grupo de linhas no esquema.

>[AZURE.NOTE] Agora, o banco de dados SQL fornece uma biblioteca para ajudar com fragmentação. Para obter mais informações, consulte [Visão geral de biblioteca de cliente do banco de dados elástica](sql-database-elastic-database-client-library.md).

Por exemplo, se um banco de dados tem o nome do cliente, ordem e detalhes do pedido (como exemplo tradicional banco de dados Northwind que vem com o SQL Server), você poderia dividir dados em vários bancos de dados Agrupando um cliente com a ordem relacionada e informações de detalhes do pedido. Você pode garantir que os dados do cliente permanecem em um único banco de dados. O aplicativo seria dividir clientes diferentes entre bancos de dados, ampliando efetivamente a carga entre vários bancos de dados. Com fragmentação, os clientes não só podem evitar o limite de tamanho máximo do banco de dados, mas o banco de dados do Azure SQL também pode processar cargas de trabalho que são significativamente maiores do que os limites dos níveis de desempenho diferentes, desde que cada banco de dados individual se encaixa no seu DTU.

Embora fragmentação de banco de dados não reduza a capacidade de recursos agregados para uma solução, ela é altamente eficaz muito grandes soluções que são distribuídas ao longo de vários bancos de dados de suporte. Cada banco de dados pode ser executados em um nível de desempenho diferentes para dar suporte a muito grandes, "efetivas" bancos de dados com os requisitos de recurso alta.

### <a name="functional-partitioning"></a>Partição funcional
Usuários do SQL Server com frequência combinam muitas funções em um banco de dados único. Por exemplo, se um aplicativo tiver lógica para gerenciar inventário de uma loja, esse banco de dados pode ter lógica associada acompanhamento de estoque, ordens de compra, procedimentos armazenados e modos de exibição indexados ou materializados que gerenciar relatórios de final de mês. Essa técnica facilita a administrar o banco de dados para operações como backup, mas também requer que você dimensionar o hardware para lidar com a carga de pico em todas as funções de um aplicativo.

Se você usa uma arquitetura de escala-out no banco de dados do SQL Azure, é uma boa ideia para dividir funções diferentes de um aplicativo em bancos de dados diferentes. Usando essa técnica, cada aplicativo escalas de maneira independente. Como um aplicativo se torna mais ocupado (e aumenta a carga no banco de dados), o administrador pode escolher níveis de desempenho independente para cada função no aplicativo. No limite, com essa arquitetura, um aplicativo pode ser maior do que uma máquina mercadoria único pode manipular porque a carga estiver distribuída em várias máquinas.

### <a name="batch-queries"></a>Consultas em lotes
Para aplicativos que acessam dados usando o alto volume, frequente, consultando ad-hoc, bastante tempo de resposta é gasto em comunicação de rede entre a camada de aplicativo e a camada de Azure SQL Database. Mesmo quando o aplicativo e o banco de dados do SQL Azure são no mesmo data center, a latência de rede entre os dois pode ser ampliada por um grande número de dados operações de acesso. Para reduzir a rede round férias das operações de acesso de dados, convém usar a opção ao lote ou as consultas ad hoc ou compila-los procedimentos armazenados como. Se você lote as consultas ad hoc, você pode enviar várias consultas como um lote grande em uma única viagem ao Azure SQL Database. Se você compilar consultas ad hoc em um procedimento armazenado, você pode obter o mesmo resultado como se você lote-los. Usando um procedimento armazenado também lhe o benefício de aumentar as chances de cache os planos de consulta no banco de dados do SQL Azure, para que você possa usar o procedimento armazenado novamente.

Alguns aplicativos são que requer muita gravação. Às vezes, você pode reduzir a carga de i/o total em um banco de dados, considerando como lote gravações juntos. Normalmente, isso é tão simple quanto usando transações explícitas em vez de transações de confirmação automática em procedimentos armazenados e lotes ad-hoc. Para uma avaliação técnicas diferentes que você pode usar, consulte [técnicas de lotes para aplicativos de banco de dados SQL no Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Experimentar com seu próprios carga de trabalho para localizar o modelo certo de processamento em lotes. Certifique-se de entender o que um modelo pode ter garantias de consistência transacional ligeiramente diferentes. Localizando a carga de trabalho certa que minimiza o uso de recursos requer Localizando a combinação certa de desempenho e consistência concessões.

### <a name="application-tier-caching"></a>Cache de camada de aplicativo
Alguns aplicativos de banco de dados têm cargas de trabalho pesado de leitura. Cache de camadas pode reduzir a carga no banco de dados e possivelmente pode reduzir o nível de desempenho necessário para dar suporte a um banco de dados usando o Azure SQL Database. Com o [Azure relacionada Cache](https://azure.microsoft.com/services/cache/), se você tiver uma carga de trabalho pesado de leitura, você pode ler os dados de uma vez (ou talvez uma vez por máquina de camada de aplicativo, dependendo de como ela está configurada) e, em seguida, armazenar dados fora de seu banco de dados do SQL. Essa é uma maneira para reduzir a carga de banco de dados (CPU e ler e/s), mas há um efeito sobre consistência transações porque os dados que estão sendo lidos do cache podem estar fora de sincronia com os dados no banco de dados. Embora em muitos aplicativos algum nível de inconsistência for aceitável, que não é verdadeiro para todas as cargas de trabalho. Você deve conhecer os requisitos do aplicativo antes de implementar uma estratégia de cache de camada de aplicativo.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre níveis de serviço, consulte [desempenho e opções de banco de dados SQL](sql-database-service-tiers.md)
- Para obter mais informações sobre os pools do banco de dados elástica, consulte [o que é um pool de banco de dados elástica Azure?](sql-database-elastic-pool.md)
- Para obter informações sobre desempenho e pools de banco de dados elástica, consulte [Quando considerar um pool de banco de dados elástica](sql-database-elastic-pool-guidance.md)
