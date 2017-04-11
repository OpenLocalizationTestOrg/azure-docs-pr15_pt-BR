<properties
   pageTitle="Gerenciamento de concorrência e carga de trabalho no SQL Data Warehouse | Microsoft Azure"
   description="Compreenda o gerenciamento de concorrência e carga de trabalho no depósito de dados do SQL Azure para desenvolvimento de soluções."
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
   ms.date="09/27/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>Gerenciamento de concorrência e carga de trabalho no depósito de dados do SQL

Para oferecer o desempenho previsível em escala, Ajuda do Microsoft Azure SQL Data Warehouse você controlar níveis de concorrência e alocações de recursos como memória e prioridade de CPU. Este artigo apresenta os conceitos de gerenciamento de concorrência e carga de trabalho, explicando como os dois recursos foram implementados e como você pode controlá-los no data warehouse. Gerenciamento de carga de trabalho de SQL Data Warehouse destina-se para ajudá-lo a oferecer suporte a ambientes de vários usuários. Ele não se destina cargas de trabalho de vários locatários.

## <a name="concurrency-limits"></a>Limites de concorrência

SQL Data Warehouse permite até 1.024 conexões simultâneas. Todas as conexões de 1.024 podem enviar consultas simultaneamente. No entanto, para otimizar a produtividade, SQL Data Warehouse pode fila algumas consultas para garantir que cada consulta recebe uma concessão de memória mínima. Fila ocorre no tempo de execução de consulta. Por consultas filas quando concorrência limites, SQL Data Warehouse pode aumentar produtividade total garantindo que consultas ativas obtém acesso aos recursos de memória drasticamente necessária.  

Limites de concorrência são controlados por dois conceitos: *consultas simultâneas* e *slots de concorrência*. Para uma consulta executar, ele deve executar dentro do limite de concorrência de consulta e a alocação de slot concorrência.

- Consultas simultâneas são as consultas em execução ao mesmo tempo. SQL Data Warehouse oferece suporte a até 32 consultas simultâneas em tamanhos maiores de DWU.
- Slots de concorrência estão alocados com base em DWU. Cada DWU 100 fornece 4 slots de concorrência. Por exemplo, um DW100 alocar 4 slots de concorrência e DW1000 aloca 40. Cada consulta consome um ou mais concorrência slot dependente da [classe de recurso](#resource-classes) da consulta. Consultas em execução da classe de recurso de smallrc consumam um slot de concorrência. Consultas em execução em uma classe de recurso maior consumam mais slots de concorrência.

A tabela a seguir descreve os limites de consultas simultâneas e slots de concorrência em vários tamanhos DWU.

### <a name="concurrency-limits"></a>Limites de concorrência

|  DWU   | Consultas simultâneas max  | Slots de concorrência alocados |
| :----  | :---------------------: | :-------------------------: |
| DW100  |            4            |                4            |
| DW200  |            8            |                8            |
| DW300  |           12            |               12            |
| DW400  |           16            |               16            |
| DW500  |           20            |               20            |
| DW600  |           24            |               24            |
| DW1000 |           32            |               40            |
| DW1200 |           32            |               48            |
| DW1500 |           32            |               60            |
| DW2000 |           32            |               80            |
| DW3000 |           32            |              120            |
| DW6000 |           32            |              240            |

Quando um desses limites for satisfeito, novas consultas são enfileiradas e executadas em uma base first-in, First.  Como terminar uma consultas e o número de consultas e slots ficar abaixo dos limites, consultas enfileiradas são lançadas. 

> [AZURE.NOTE]  *Selecione* consultas em execução exclusivamente nos modos de exibição de gerenciamento dinâmico (DMVs) ou exibições do catálogo não são controladas por qualquer um dos limites concorrência. Você pode monitorar o sistema independentemente o número de consultas executando nele.

## <a name="resource-classes"></a>Classes de recursos

Recurso classes ajuda que você controla a alocação de memória e ciclos de CPU atribuídos a uma consulta. Você pode atribuir quatro classes de recursos a um usuário no formulário de *funções de banco de dados*. As classes de quatro recursos são **smallrc**, **mediumrc**, **largerc**e **xlargerc**. Os usuários smallrc recebem uma quantidade menor de memória e podem aproveitar concorrência superior. Em contraste, os usuários atribuídos a xlargerc recebem grandes quantidades de memória e, portanto, menos de suas consultas podem ser executados simultaneamente.

Por padrão, cada usuário é um membro da classe recursos pequeno, smallrc. O procedimento `sp_addrolemember` é usado para aumentar a classe de recurso, e `sp_droprolemember` é usado para diminuir a classe de recurso. Por exemplo, este comando pode aumentar a classe de recurso de loaduser para largerc:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

Uma prática recomendada é permanentemente atribuir usuários a uma classe de recurso em vez de alterar suas classes de recurso. Por exemplo, cargas às tabelas columnstore agrupada criar índices de alta qualidade quando alocado mais memória. Para garantir que carrega tenha acesso ao memória superior, crie um usuário especificamente para carregar dados e permanentemente atribua este usuário a uma classe superior do recurso.

Existem alguns tipos de consultas que não se beneficiar por uma alocação de memória maior. O sistema ignorará sua alocação de classe do recurso e sempre executar essas consultas da classe de recursos pequeno em vez disso. Se essas consultas sempre executam da classe de recursos pequeno, eles podem executar quando concorrência slot é sob pressão e eles não consumam mais slots que o necessário. Consulte [exceções de classe do recurso](#query-exceptions-to-concurrency-limits) para obter mais informações.

Alguns mais detalhes na classe de recurso:

- *Alterar a função* permissão é necessária para alterar a classe de recurso de um usuário.  
- Embora você possa adicionar um usuário a um ou mais as maiores de classes de recurso, os usuários terão os atributos da classe de recurso mais alta à qual eles estão atribuídos. Isto é, se um usuário é atribuído a mediumrc e largerc, a classe de recurso superior (largerc) é a classe de recurso que será cumprida.  
- A classe de recurso do usuário administrativo do sistema não pode ser alterada.

Para obter um exemplo detalhado, consulte [alterando exemplo de classe de recurso de usuário](#changing-user-resource-class-example).

## <a name="memory-allocation"></a>Alocação de memória

Há prós e contras ao aumento da classe de recurso de um usuário. Aumentar uma classe de recurso para um usuário terá seu acesso de consultas para mais memória, o que pode significam executar consultas com mais rapidez.  No entanto, maiores classes de recursos também reduzem o número de consultas simultâneas que podem ser executados. Esta é a desvantagem entre alocar grandes quantidades de memória para uma única consulta ou permitir outras consultas, que também precisam alocações de memória, para executar simultaneamente. Se um usuário for fornecido altas alocações de memória para uma consulta, outros usuários não terão acesso aos mesmo memória para executar uma consulta.

A tabela a seguir mapeia a memória alocada para cada distribuição pela classe DWU e recursos.

### <a name="memory-allocations-per-distribution-mb"></a>Alocações de memória por distribuição (MB)

|  DWU   | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100  |   100   |    100   |    200  |    400   |
| DW200  |   100   |    200   |    400  |    800   |
| DW300  |   100   |    200   |    400  |    800   |
| DW400  |   100   |    400   |    800  |  1.600   |
| DW500  |   100   |    400   |    800  |  1.600   |
| DW600  |   100   |    400   |    800  |  1.600   |
| DW1000 |   100   |    800   |  1.600  |  3.200   |
| DW1200 |   100   |    800   |  1.600  |  3.200   |
| DW1500 |   100   |    800   |  1.600  |  3.200   |
| DW2000 |   100   |  1.600   |  3.200  |  6,400   |
| DW3000 |   100   |  1.600   |  3.200  |  6,400   |
| DW6000 |   100   |  3.200   |  6,400  |  12,800  |

Na tabela anterior, você pode ver que uma consulta em execução em um DW2000 da classe de recurso de xlargerc teria acesso 6.400 MB de memória em cada um dos bancos de dados distribuídos 60.  Em SQL Data Warehouse, há 60 distribuições. Portanto, para calcular a alocação de memória total de uma consulta em uma classe de determinado recurso, os valores acima devem ser multiplicados por 60.

### <a name="memory-allocations-system-wide-gb"></a>Memória alocações todo o sistema (GB)

|  DWU   | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100  |    6    |    6     |    12   |    23    |
| DW200  |    6    |    12    |    23   |    47    |
| DW300  |    6    |    12    |    23   |    47    |
| DW400  |    6    |    23    |    47   |    94    |
| DW500  |    6    |    23    |    47   |    94    |
| DW600  |    6    |    23    |    47   |    94    |
| DW1000 |    6    |    47    |    94   |   188    |
| DW1200 |    6    |    47    |    94   |   188    |
| DW1500 |    6    |    47    |    94   |   188    |
| DW2000 |    6    |    94    |   188   |   375    |
| DW3000 |    6    |    94    |   188   |   375    |
| DW6000 |    6    |   188    |   375   |   750    |

Esta tabela de alocações de memória do sistema, você pode ver que uma consulta em execução em um DW2000 da classe de recurso de xlargerc está alocada um total de 375 GB de memória (6.400 MB * 60 distribuições / 1.024 para converter em GB) sobre na íntegra do SQL Data Warehouse.

## <a name="concurrency-slot-consumption"></a>Consumo de slot de concorrência

Depósito de dados do SQL concede mais memória às consultas em execução no maiores classes de recursos. Memória é um recurso fixo.  Portanto, a memória mais alocada por consulta, as consultas simultâneas menos podem executar. A tabela a seguir reitera todos os conceitos anterior em um único modo de exibição que mostra o número de slots de concorrência disponíveis por DWU e os slots consumidos por cada classe de recurso.

### <a name="allocation-and-consumption-of-concurrency-slots"></a>Alocação e o consumo de slots de concorrência

|  DWU   | Máximos consultas simultâneas  | Slots de concorrência alocados | Slots usados pelo smallrc |  Slots usados pelo mediumrc |  Slots usados pelo largerc |  Slots usados pelo xlargerc |
| :----  | :---------------------: | :-------------------------: | :-----: | :------: | :-----: | :------: |
| DW100  |            4            |                4            |    1    |     1    |    2    |    4     |
| DW200  |            8            |                8            |    1    |     2    |    4    |    8     |
| DW300  |           12            |               12            |    1    |     2    |    4    |    8     |
| DW400  |           16            |               16            |    1    |     4    |    8    |   16     |
| DW500  |           20            |               20            |    1    |     4    |    8    |   16     |
| DW600  |           24            |               24            |    1    |     4    |    8    |   16     |
| DW1000 |           32            |               40            |    1    |     8    |   16    |   32     |
| DW1200 |           32            |               48            |    1    |     8    |   16    |   32     |
| DW1500 |           32            |               60            |    1    |     8    |   16    |   32     |
| DW2000 |           32            |               80            |    1    |    16    |   32    |   64     |
| DW3000 |           32            |              120            |    1    |    16    |   32    |   64     |
| DW6000 |           32            |              240            |    1    |    32    |   64    |  128     |


Desta tabela, você pode ver que a execução SQL Data Warehouse como DW1000 aloca um máximo de 32 consultas simultâneas e um total de 40 slots de concorrência. Se todos os usuários estão executando em smallrc, 32 consultas simultâneas seriam permitidas porque cada consulta seria consumir 1 slot de concorrência. Se todos os usuários em um DW1000 mediumrc estavam em execução, cada consulta seria alocada 800 MB por distribuição total alocação de memória de 47 GB por consulta e concorrência seria limitada a 5 usuários (40 slots de concorrência / 8 slots por usuário mediumrc).

## <a name="query-importance"></a>Prioridade de consulta

SQL Data Warehouse implementa classes de recursos usando grupos de carga de trabalho. Há um total de oito grupos de carga de trabalho que controlam o comportamento das classes recurso entre os vários tamanhos DWU. Para qualquer DWU, SQL Data Warehouse usa somente quatro dos grupos de carga de oito trabalho. Isso faz sentido porque cada grupo de carga de trabalho é atribuída a uma das quatro classes de recurso: smallrc, mediumrc, largerc, ou xlargerc. A importância de compreender os grupos de carga de trabalho é que alguns desses grupos de carga de trabalho estiverem definidas como mais alta *prioridade*. Prioridade é usada para CPU agendamento. Consultas executadas com alta prioridade obterá três vezes mais ciclos de CPU daqueles com prioridade média. Portanto, os mapeamentos de slot concorrência também determinam prioridade CPU. Quando uma consulta consome 16 ou mais slots, ele executa como alta prioridade.

A tabela a seguir mostra os mapeamentos de prioridade para cada grupo de carga de trabalho.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Mapeamentos de grupo de carga de trabalho para slots de concorrência e prioridade

| Grupos de carga de trabalho | Mapeamento de slot de concorrência | MB / distribuição | Mapeamento de prioridade |
| :-------------- | :----------------------: | :---------------: | :----------------- |
| SloDWGroupC00   |            1             |         100       |       Média       |
| SloDWGroupC01   |            2             |         200       |       Média       |
| SloDWGroupC02   |            4             |         400       |       Média       |
| SloDWGroupC03   |            8             |         800       |       Média       |
| SloDWGroupC04   |           16             |       1.600       |       Alto         |
| SloDWGroupC05   |           32             |       3.200       |       Alto         |
| SloDWGroupC06   |           64             |       6,400       |       Alto         |
| SloDWGroupC07   |          128             |      12,800       |       Alto         |

Gráfico de **alocação e o consumo de slots de concorrência** , você pode ver que uma DW500 usa 1, 4, 8 ou slots de concorrência 16 para smallrc, mediumrc, largerc e xlargerc, respectivamente. Você pode pesquisar esses valores no gráfico anterior para localizar a importância para cada classe de recurso.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Mapeamento de DW500 de classes de recurso a importância

| Classe de recurso | Grupo de carga de trabalho | Slots de concorrência usados | MB / distribuição | Prioridade |
| :------------- | :------------- | :--------------------: | :---------------: | :--------- |
| smallrc        | SloDWGroupC00  |           1            |         100       |   Média   |
| mediumrc       | SloDWGroupC02  |           4            |         400       |   Média   |
| largerc        | SloDWGroupC03  |           8            |         800       |   Média   |
| xlargerc       | SloDWGroupC04  |          16            |        1.600      |   Alto     |


Você pode usar a seguinte consulta do departamento de trânsito para examinar as diferenças de alocação de recursos de memória em detalhes da perspectiva do Gerenciador de recursos, ou para analisar o uso de ativo e histórico dos grupos de carga de trabalho quando a solução de problemas.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                      AS node_type
    ,pn.pdw_node_id                 AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                  AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                     AS group_max_dop
    ,wg.effective_max_dop               AS group_effective_max_dop
    ,wg.total_request_count             AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON  wg.pdw_node_id  = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,   group_request_max_memory_grant_pcnt
,   group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>Consultas que cumprir limites de concorrência

A maioria das consultas são controladas por classes de recursos. Essas consultas devem caber em ambos os os simultâneas consulta e concorrência slot limites. Um usuário não pode escolher excluir uma consulta a partir do modelo de slot concorrência.

Para repetir, as instruções a seguir cumprir classes de recursos:

- SELECIONE INSERIR
- ATUALIZAÇÃO
- EXCLUIR
- Selecionar (quando consultando tabelas do usuário)
- ALTER RECOMPILAÇÃO DE ÍNDICE
- ALTER ÍNDICE REORGANIZAR
- ALTER RECRIAÇÃO DE TABELA
- CRIAR ÍNDICE
- CRIAR ÍNDICE COLUMNSTORE AGRUPADA
- CRIAR TABELA COMO SELECIONAR (CTAS)
- Carregamento de dados
- Operações de movimentação de dados realizadas pelo serviço de movimentação de dados (DMS)

## <a name="query-exceptions-to-concurrency-limits"></a>Exceções de consulta aos limites de concorrência

Algumas consultas não aceitar a classe de recurso a que o usuário está atribuído. Essas exceções para os limites de concorrência são feitas quando os recursos de memória necessários para um determinado comando são baixos, geralmente porque o comando é uma operação de metadados. O objetivo dessas exceções é evitar maiores alocações de memória para consultas que nunca serão necessário-los. Nesses casos, a classe de recursos pequeno padrão (smallrc) é sempre usada, independentemente da classe de recurso real atribuída ao usuário. Por exemplo, `CREATE LOGIN` sempre será executado em smallrc. Os recursos necessários para cumprir esta operação são muito baixos, para que ele não faz sentido para incluir a consulta o modelo de slot concorrência.  Essas consultas também não são limitadas pelo limite de concorrência de 32 usuário, um número ilimitado dessas consultas pode ser executados para cima o limite de sessão de 1.024 sessões.

As instruções a seguir não aceitar classes de recursos:

- Criar ou DESCARTE de tabela
- ALTERE TABELA … MUDANÇA, dividir ou Mesclar partição
- ALTERAR DESATIVAR DE ÍNDICE
- DESCARTAR ÍNDICE
- CRIAR, atualizar ou SOLTAR estatísticas
- TRUNCAR TABELA
- ALTERAR AUTORIZAÇÃO
- CRIAR LOGON
- CRIAR, alterar ou DROP USER
- CRIAR, alterar ou procedimento SOLTAR
- Criar ou descartar o modo de exibição
- INSERIR VALORES
- Selecione DMVs e modos de exibição do sistema
- EXPLIQUE
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="change-a-user-resource-class-example"></a>Alterar um exemplo de classe do recurso de usuário

1. **Criar login:** Abra uma conexão ao seu banco de dados **mestre** no SQL server que hospeda seu banco de dados do SQL Data Warehouse e execute os seguintes comandos.

    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```

    > [AZURE.NOTE] É uma boa ideia criar um usuário no banco de dados mestre para usuários de depósito de dados do SQL Azure. Criar um usuário no mestre permite que um usuário faça logon usando ferramentas como SSMS sem especificar um nome de banco de dados.  Ele também permite que eles usem o Explorador de objetos para exibir todos os bancos de dados em um servidor SQL.  Para obter mais detalhes sobre como criar e gerenciar usuários, consulte [seguro um banco de dados SQL Data Warehouse][].

2. **Usuário criar SQL Data Warehouse:** Abra uma conexão ao banco de dados **SQL Data Warehouse** e execute o comando a seguir.

    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```

3. **Conceder permissões:** O exemplo a seguir concede `CONTROL` no banco de dados **SQL Data Warehouse** . `CONTROL`o banco de dados nível é o equivalente de db_owner no SQL Server.

    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```

4. **Aumentar a classe de recurso:** Use a consulta a seguir para adicionar um usuário a uma função de gerenciamento de carga de trabalho maior.

    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```

5. **Diminuir a classe de recurso:** Use a consulta a seguir para remover um usuário de uma função de gerenciamento de carga de trabalho.

    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```

    > [AZURE.NOTE] Não é possível remover um usuário do smallrc.

## <a name="queued-query-detection-and-other-dmvs"></a>Detecção de consulta na fila e outros DMVs

Você pode usar o `sys.dm_pdw_exec_requests` departamento de trânsito para identificar consultas que estão aguardando na fila de concorrência. Consultas aguardando um slot concorrência terá um status de **suspenso**.

```sql
SELECT   r.[request_id]              AS Request_ID
        ,r.[status]              AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]              AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

Funções de gerenciamento de carga de trabalho podem ser exibidas com `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

A consulta a seguir mostra a função que cada usuário é atribuído a.

```sql
SELECT   r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id     = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id   = m.principal_id
WHERE   r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL Data Warehouse tem os seguintes tipos de esperar:

- **LocalQueriesConcurrencyResourceType**: consultas que ficam fora a estrutura de slot concorrência. Consultas de departamento de trânsito e sistema funciona como `SELECT @@VERSION` são exemplos de consultas de locais.
- **UserConcurrencyResourceType**: consultas que ficam dentro do framework de slot concorrência. Consultas em tabelas do usuário final representam exemplos que usam esse tipo de recurso.
- **DmsConcurrencyResourceType**: aguarda resultantes de operações de movimentação de dados.
- **BackupConcurrencyResourceType**: essa espera indica que um banco de dados está sendo feito backup. O valor máximo para esse tipo de recurso é 1. Se vários backups foram solicitados ao mesmo tempo, as outras ficarão na fila.

O `sys.dm_pdw_waits` departamento de trânsito pode ser usado para ver quais recursos uma solicitação está aguardando.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,   SESSION_ID()            AS Current_session
,   s.[status]          AS Session_status
,   s.[login_name]
,   s.[query_count]
,   s.[client_id]
,   s.[sql_spid]
,   r.[command]         AS Request_command
,   r.[label]
,   r.[status]          AS Request_status
,   r.[submit_time]
,   r.[start_time]
,   r.[end_compile_time]
,   r.[end_time]
,   DATEDIFF(ms,r.[submit_time],r.[start_time])     AS Request_queue_time_ms
,   DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,   DATEDIFF(ms,r.[end_compile_time],r.[end_time])      AS Request_execution_time_ms
,   r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE   w.[session_id] <> SESSION_ID();
```

O `sys.dm_pdw_resource_waits` departamento de trânsito mostra apenas o aguarda recurso consumido por uma determinada consulta. Tempo de espera do recurso avalia apenas o tempo esperando recursos fornecido, em vez de tempo de espera de sinal, que é o tempo necessário para os servidores SQL subjacentes agendar a consulta em CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE   [session_id] <> SESSION_ID();
```

O `sys.dm_pdw_wait_stats` departamento de trânsito pode ser usado para análise de tendência históricos de espera.

```sql
SELECT  w.[pdw_node_id]
,       w.[wait_name]
,       w.[max_wait_time]
,       w.[request_count]
,       w.[signal_time]
,       w.[completed_count]
,       w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o gerenciamento de segurança e seus usuários de banco de dados, consulte [seguro um banco de dados SQL Data Warehouse][]. Para obter mais informações sobre como maior recurso classes pode melhorar a qualidade de índice columnstore agrupada, consulte [índices de reconstrução para melhorar a qualidade de segmento].

<!--Image references-->

<!--Article references-->
[Proteger um banco de dados no depósito de dados do SQL]: ./sql-data-warehouse-overview-manage-security.md
[Recriar índices para melhorar a qualidade do segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Proteger um banco de dados no depósito de dados do SQL]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
