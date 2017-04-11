<properties
   pageTitle="Gerenciar dados históricos em tabelas Temporal com política de retenção | Microsoft Azure"
   description="Saiba como usar a política de retenção temporal para manter dados históricos sob seu controle."
   services="sql-database"
   documentationCenter=""
   authors="bonova"
   manager="drasumic"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sql-database"
   ms.date="10/12/2016"
   ms.author="bonova"/>

#<a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Gerenciar dados históricos em tabelas Temporal com política de retenção

Tabelas temporais podem aumentar o tamanho do banco de dados em mais de tabelas regulares, especialmente se manter dados históricos para um período de tempo maior. Portanto, a política de retenção de dados históricos é um aspecto importante de planejar e gerenciar o ciclo de vida de cada tabela temporal. Temporais tabelas no banco de dados do SQL Azure vêm com o mecanismo de retenção de fácil de usar que ajuda você a realizar essa tarefa.

Retenção histórico temporal pode ser configurado no nível de tabela individuais, que permite que os usuários criem vencimento flexível políticas. A aplicação de retenção temporal é simple: requer apenas um parâmetro a ser definida durante a alteração de criação ou esquema de tabela.

Após você definir a política de retenção, o Azure SQL Database começarão a verificação regularmente se há linhas históricas que são elegíveis para a limpeza de dados automática. Identificação de linhas correspondentes e seu remoção da tabela de histórico ocorrerem transparente, na tarefa do plano de fundo que está agendada e executada pelo sistema. Condição de idade para as linhas da tabela de histórico está marcada com base na coluna que representa o final do período SYSTEM_TIME. Se o período de retenção, por exemplo, estiver definido para seis meses, linhas de tabela qualificadas para limpeza atendem a seguinte condição:

````
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
````

No exemplo acima, podemos considerado que **ValidTo** coluna corresponde ao final do período SYSTEM_TIME.

##<a name="how-to-configure-retention-policy"></a>Como configurar a política de retenção?

Antes de configurar política de retenção para uma tabela temporal, verifique primeiro se temporal retenção histórica está habilitado *no nível do banco de dados*.

````
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
````

Sinalizador de banco de dados **is_temporal_history_retention_enabled** está definido para ativado por padrão, mas os usuários podem alterá-lo com instrução ALTER DATABASE. Ele também automaticamente está definido como desativado após a operação de [ponto no tempo restauração](sql-database-point-in-time-restore-portal.md) . Para habilitar a limpeza de retenção de histórico temporal para seu banco de dados, execute a seguinte instrução:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

> [AZURE.IMPORTANT] Mesmo se **is_temporal_history_retention_enabled** é desativada, mas limpeza automática para linhas antigos não será acionada nesse caso, você pode configurar retenção para tabelas Temporais.


Política de retenção é configurada durante a criação de tabela, especificando o valor para o parâmetro HISTORY_RETENTION_PERIOD:

````
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
````

Banco de dados do SQL Azure permite que você especifique o período de retenção usando unidades de tempo diferentes: dias, semanas, meses e anos. Se HISTORY_RETENTION_PERIOD for omitido, será considerada retenção INFINITA. Você também pode usar palavra-chave infinito explicitamente.

Em alguns cenários, talvez você queira configurar retenção após a criação de tabela ou alterar anteriormente valor configurado. Nesse caso use instrução ALTER TABLE:

````
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
````

> [AZURE.IMPORTANT]  Configurando SYSTEM_VERSIONING para o valor de período de retenção *não preserva* desativá-lo. Definir SYSTEM_VERSIONING como ON sem HISTORY_RETENTION_PERIOD especificado explicitamente resultados no período de retenção INFINITA.

Para revisar o estado atual da política de retenção, use a seguinte consulta que associa sinalizador de ativação de retenção temporal no nível do banco de dados com períodos de retenção para tabelas individuais:

````
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
````


##<a name="how-sql-database-deletes-aged-rows"></a>Como o banco de dados SQL exclui antigos linhas?

O processo de limpeza depende do layout de índice da tabela de histórico. É importante notar que *somente tabelas de histórico com um índice de cluster (árvore B ou columnstore) podem ter configurada de política de retenção finita*. Uma tarefa de plano de fundo é criada para executar a limpeza de dados antigos para todas as tabelas temporais com o período de retenção finita.
Lógica de limpeza para o índice de cluster rowstore (B-árvore) exclui linha antigos em partes menores (até 10 K) minimizando pressão log de banco de dados e subsistema e/s. Embora a lógica de limpeza utiliza o índice de árvore B necessário, ordem de exclusões para as linhas mais o período de retenção não é possível garantir firmemente antigas. Portanto, *não terão qualquer dependência na ordem de limpeza em seus aplicativos*.

A tarefa de limpeza para o columnstore agrupada remove toda [grupos de linha](https://msdn.microsoft.com/library/gg492088.aspx) de uma vez (normalmente contêm 1 milhão de linhas cada), que é muito eficiente, especialmente quando dados históricos são gerados em um ritmo alto.

![Retenção columnstore agrupada](./media/sql-database-temporal-tables-retention-policy/cciretention.png)


Compactação de dados excelente e retenção eficiente limpeza torna agrupadas columnstore índice uma opção ideal para cenários quando sua carga de trabalho gera rapidamente grande quantidade de dados históricos. Esse padrão é normal intenso [cargas de trabalho de processamento de transações que usam tabelas temporais](https://msdn.microsoft.com/library/mt631669.aspx) para o controle de alterações e auditoria, análise de tendência ou IoT inclusão de dados.

##<a name="index-considerations"></a>Considerações de índice

A tarefa de limpeza para tabelas com índice agrupado rowstore requer índice a iniciar com a coluna correspondente no final do período SYSTEM_TIME. Se esse índice não existir, você não será capaz de configurar o período de retenção finita:

*Msg 13765, Level 16, estado 1 <br> </br> definir o período de retenção finita Falha na tabela de temporal sistema versão 'temporalstagetestdb.dbo.WebsiteUserInfo' porque a tabela de histórico 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' não contém índice clusterizado necessário. Considere a criação de uma columnstore agrupada ou um índice de árvore B começando com a coluna que corresponde ao final da SYSTEM_TIME período, na tabela de histórico.*

É importante notar que a tabela de histórico padrão criada por Azure SQL Database já tem agrupada índice, que é compatível com política de retenção. Se você tentar remover índice em uma tabela com o período de retenção finita, operação falhar com o seguinte erro:

*Msg 13766, Level 16, estado 1 <br> </br> não é possível cancelar o índice de cluster 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' porque ele está sendo usado para limpeza automática de dados antigos. Considere a possibilidade de configuração HISTORY_RETENTION_PERIOD para infinito na tabela temporal sistema versão correspondente, se for necessário descartar este índice.*

Limpeza no índice clusterizado columnstore ideal funciona se históricos linhas são inseridas na ordem crescente (ordenada ao final do período coluna), que é sempre o caso quando a tabela de histórico é preenchida exclusivamente pelo mecanismo de SYSTEM_VERSIONIOING. Se as linhas da tabela de histórico não são ordenadas por final do período coluna (que pode ser o caso se você migrado dados históricos existentes), você deverá recriar índice clusterizado columnstore na parte superior de índice de rowstore árvore B que está ordenada corretamente, para atingir o desempenho ideal.

Evite a recriação índice clusterizado columnstore na tabela histórico com o período de retenção finita, porque ele pode ser alterado ordenação nos grupos linha naturalmente impostos pela operação de controle de versão do sistema. Se você precisar recriar o índice clusterizado columnstore na tabela de histórico, fazer isso clicando criá-lo novamente na parte superior de conformidade com índice de árvore B, preservando a ordenação no rowgroups necessários para limpeza de dados regular. A mesma abordagem deve ser levada se você criar tabela temporal com tabela de histórico existente que tem agrupada índice de coluna sem ordem garantia de dados:

````
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
````

Quando o período de retenção finita estiver configurado para a tabela de histórico com o índice de columnstore agrupada, é possível criar índices adicionais de árvore B não-clusterizados naquela tabela:

````
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
````

Uma tentativa de executar acima declaração falhará com o seguinte erro:

*Msg 13772, Level 16, estado 1 <br> </br> não é possível criar índice não agrupado em uma tabela de histórico temporal 'WebsiteUserInfoHistory' já que tem o período de retenção finita e índice clusterizado columnstore definido.*

##<a name="querying-tables-with-retention-policy"></a>Consultar tabelas com política de retenção

Todas as consultas na tabela temporal filtram automaticamente históricas linhas que correspondem a política de retenção finita, para evitar resultados imprevisíveis e inconsistentes, como linhas antigos podem ser excluídas pela tarefa limpeza, *em qualquer ponto no tempo e em ordem aleatório*.

A figura a seguir mostra o plano de consulta para uma consulta simples:

````
SELECT * FROM dbo.WebsiteUserInfo FROM SYSTEM_TIME ALL;
````

O plano de consulta inclui adicional filtro aplicado ao final da coluna período (ValidTo) no operador "Agrupada exame de índice" na tabela de histórico (realçado). Este exemplo assume esse período de retenção de 1 mês foi definido na tabela WebsiteUserInfo.

![Filtro de consulta de retenção](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

No entanto, se você consulta tabela de histórico diretamente, você poderá ver linhas que são mais antigas que retenção especificado período, mas sem nenhuma garantia para resultados de consulta repetitivo. A figura a seguir mostra o plano de execução de consulta para a consulta na tabela de histórico sem filtros adicionais aplicados:

![Consultando histórico sem filtro de retenção](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Não confie sua lógica comercial na tabela de histórico além do período de retenção de leitura como você pode obter resultados inesperados ou inconsistentes. É recomendável usar consultas temporais com cláusula FOR SYSTEM_TIME para analisar dados em tabelas Temporais.

##<a name="point-in-time-restore-considerations"></a>Aponte em considerações de restauração de tempo

Quando você cria um novo banco de dados, [Restaurando o banco de dados existente a um ponto específico no tempo](sql-database-point-in-time-restore-portal.md), ele tem retenção temporal desabilitada no nível do banco de dados. (sinalizador**is_temporal_history_retention_enabled** definido para desativado). Esta funcionalidade permite que você examine todas as linhas de históricas durante a restauração, sem se preocupar que linhas antigos são removidas antes de chegar ao consultá-los. Você pode usá-lo para *inspecionar dados históricos além do período de retenção configurado*.

Digamos que uma tabela temporal tem retenção de mês de um período especificada. Se seu banco de dados foi criado no nível de serviço Premium, você poderá criar cópia do banco de dados com o estado de banco de dados para cima para 35 dias novamente no passado. Que efetivamente permitirá que você analisar históricas linhas com até 65 dias consultando a tabela de histórico diretamente.

Se você deseja ativar a limpeza de retenção temporal, execute a seguinte instrução Transact-SQL após ponto no tempo restauração:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

##<a name="next-steps"></a>Próximas etapas

Para saber como usar tabelas Temporal em seus aplicativos Confira [Introdução ao Temporal tabelas no banco de dados do SQL Azure](sql-database-temporal-tables.md).

Visite o canal 9 para ouvir uma [história de sucesso do cliente real implementação temporal](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) e assistir a um [live demonstração temporal](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Para obter informações detalhadas sobre tabelas Temporal, examine a [documentação do MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
