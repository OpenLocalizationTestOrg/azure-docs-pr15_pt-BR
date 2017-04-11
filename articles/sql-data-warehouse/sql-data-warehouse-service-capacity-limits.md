<properties
   pageTitle="Limites de capacidade de SQL Data Warehouse | Microsoft Azure"
   description="Valores máximos para conexões, bancos de dados, tabelas e consultas para SQL Data Warehouse."
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
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="sql-data-warehouse-capacity-limits"></a>Limites de capacidade de depósito de dados do SQL

As tabelas a seguir contêm os valores máximos permitidos para vários componentes de depósito de dados do SQL Azure.


## <a name="workload-management"></a>Gerenciamento de carga de trabalho

| Categoria            | Descrição                                       | Máximo            |
| :------------------ | :------------------------------------------------ | :----------------- |
| [Unidades de dados de depósito (DWU)][]| Max DWU para um único depósito de dados do SQL | 6000               |
| [Unidades de dados de depósito (DWU)][]| Max DWU para um único SQL server         | 6000 por padrão<br/><br/> Por padrão, cada SQL server (por exemplo, myserver.database.windows.net) tem uma cota de DTU de 45.000 que permite até DWU 6000. Essa cota é simplesmente um limite de segurança. Você pode aumentar sua cota [Criando um tíquete][] e selecionando *cota* como o tipo de solicitação.  Para calcular sua DTU necessidades, multiplique o 7.5 pelo total que DWU necessário. Você pode exibir seu consumo de DTU atual da lâmina do servidor SQL no portal. Bancos de dados pausados e cancelamento pausados contam a cota DTU. |
| Conexão de banco de dados | Sessões abertas simultâneas                          | 1024<br/><br/>Oferecemos suporte um máximo de conexões ativas 1024, cada uma delas pode enviar solicitações para um banco de dados do SQL Data Warehouse ao mesmo tempo. Observe que há limites no número de consultas que realmente podem ser executadas simultaneamente. Quando o limite de concorrência é excedido, a solicitação vai para uma fila interna onde ele espera para ser processada.|
| Conexão de banco de dados | Memória máxima para instruções preparadas            | 20 MB              |
| [Gerenciamento de carga de trabalho][] | Máximos consultas simultâneas                    | 32<br/><br/> Por padrão, o SQL Data Warehouse pode executar um máximo de 32 consultas simultâneas e filas restante consultas.<br/><br/>O nível de concorrência pode diminuir quando os usuários são atribuídos a uma classe de recurso maior ou quando SQL Data Warehouse está configurado com DWU baixa. Algumas consultas, como o departamento de trânsito consultas, sempre terão permissão para executar.|
| [Tempdb][]          | Tamanho máximo do Tempdb                                | 399 GB por DW100. Portanto em Tempdb DWU1000 é dimensionada 3,99 TB |


## <a name="database-objects"></a>Objetos de banco de dados

| Categoria          | Descrição                                  | Máximo            |
| :---------------- | :------------------------------------------- | :----------------- |
| Banco de dados          | Tamanho máximo                                     | 240 TB compactados em disco<br/><br/>Este espaço é independente do espaço de tempdb ou log e, portanto, esse espaço dedicado a tabelas permanentes.  Compactação de columnstore agrupada é estimada em 5 vezes.  Esta compactação permite que o banco de dados atinja aproximadamente 1 PB quando todas as tabelas são agrupada columnstore (o tipo de tabela padrão).|
| Tabela             | Tamanho máximo                                     | 60 TB compactado em disco   |
| Tabela             | Tabelas por banco de dados                          | 2 bilhões          |
| Tabela             | Colunas por tabela                            | 1024 colunas       |
| Tabela             | Bytes por coluna                             | Depende de coluna [tipo de dados][].  Limite é de 8000 para tipos de dados de caractere, 4000 para nvarchar ou 2 GB máx para tipos de dados.|
| Tabela             | Bytes por linha, tamanho definido                  | 8060 bytes<br/><br/>O número de bytes por linha é calculado da mesma maneira como ele é para SQL Server com compactação de página. Como o SQL Server, SQL Data Warehouse suporta o armazenamento de estouro de linha que permite que as **colunas de comprimento variável** ser enviado a linha. Quando as linhas de comprimento variável são colocadas linha, somente 24 bytes raiz é armazenada no registro principal. Para obter mais informações, consulte o artigo da MSDN de [estouro de linha dados ultrapassando 8 KB][] .|
| Tabela             | Partições por tabela                    | 15.000<br/><br/>Alto desempenho, é recomendável para minimizar o número de partições necessária enquanto ainda suporte às suas necessidades de negócios. Como o número de partições cresce, a sobrecarga para operações de linguagem de definição de dados (DDL) e dados manipulação DML (linguagem) cresce e faz com que o desempenho mais lento.|
| Tabela             | Caracteres por valor de limite de partição.| 4000 |
| Índice             | Índices não agrupados por tabela.        | 999<br/><br/>Aplica-se às tabelas de rowstore somente.|
| Índice             | Índices agrupados por tabela.            | 1<br><br/>Aplica-se às tabelas rowstore e columnstore.|
| Índice             | Tamanho da chave de índice.                          | 900 bytes.<br/><br/>Aplica-se ao somente índices de rowstore.<br/><br/>Podem ser criados índices em colunas varchar com um tamanho máximo de mais de 900 bytes se os dados existentes nas colunas não pode exceder 900 bytes quando o índice for criado. No entanto, INSERI-lo posteriormente ou ações de atualização em colunas que fazer com que o tamanho total exceder 900 bytes falhará.|
| Índice             | Colunas de chave por índice.                   | 16<br/><br/>Aplica-se ao somente índices de rowstore. Índices de columnstore agrupada incluem todas as colunas.|
| Estatísticas        | Tamanho dos valores de coluna combinados.      | 900 bytes.         |
| Estatísticas        | Colunas por objeto de estatísticas.           | 32                 |
| Estatísticas        | Estatísticas criadas em colunas por tabela. | 30.000            |
| Procedimentos armazenados | Níveis máximos de aninhamento.               | 8                 |
| Modo de exibição              | Colunas por modo de exibição                         | 1.024             |


## <a name="loads"></a>Cargas

| Categoria          | Descrição                                  | Máximo            |
| :---------------- | :------------------------------------------- | :----------------- |
| Carregamentos de Polybase    | Bytes por linha                                | 32.768<br/><br/>Carregamentos de Polybase limitam-se ao carregamento ambas as linhas menor que 32K e não é possível carregar VARCHR(MAX), nvarchar (max) ou varbinary (max).  Embora esse limite exista hoje, ele será removido bastante em breve.<br/><br/>


## <a name="queries"></a>Consultas

| Categoria          | Descrição                                  | Máximo            |
| :---------------- | :------------------------------------------- | :----------------- |
| Consulta             | Consultas enfileiradas em tabelas do usuário.               | 1000               |
| Consulta             | Consultas simultâneas nos modos de exibição do sistema.          | 100                |
| Consulta             | Consultas enfileiradas em modos de exibição do sistema               | 1000               |
| Consulta             | Parâmetros máximos                           | 2098               |
| Em lotes             | Tamanho máximo                                 | 65, 536 * 4096        |
| SELECIONADOS resultados    | Colunas por linha                              | 4096<br/><br/>Você nunca pode ter mais de 4096 colunas por linha no resultado da seleção. Não há nenhuma garantia de que você sempre pode ter 4096. Se o plano de consulta requer uma tabela temporária, as colunas de 1024 por tabela máxima podem se aplicar.|
| SELECIONE            | Subconsultas aninhadas                            | 32<br/><br/>Você nunca pode ter mais de 32 subconsultas aninhadas em uma instrução SELECT. Não há nenhuma garantia de que você sempre pode ter 32. Por exemplo, uma junção pode introduzir uma subconsulta no plano de consulta. O número de subconsultas também pode ser limitado pela memória disponível.|
| SELECIONE            | Colunas por associação                             | 1024 colunas<br/><br/>Você nunca pode ter mais de 1024 colunas na associação. Não há nenhuma garantia de que você sempre pode ter 1024. Se o plano de junção requer uma tabela temporária com mais colunas do que o resultado de junção, o limite de 1024 se aplica à tabela temporária. |
| SELECIONE            | Bytes por Agrupar por colunas.                  | 8060<br/><br/>As colunas na cláusula GROUP BY podem ter um máximo de 8060 bytes.|
| SELECIONE            | Bytes por ordem por colunas                   | 8060 bytes.<br/><br/>As colunas na cláusula ORDER BY podem ter um máximo de 8060 bytes.|
| Identificadores e constantes por declaração | Número de identificadores referenciados e constantes. | 65.535<br/><br/>SQL Data Warehouse limita o número de identificadores e constantes que podem ser contidos em uma única expressão de uma consulta. Esse limite é 65.535. Ultrapassando este número resulta em erro de SQL Server 8632. Para obter mais informações, consulte [erro interno: um limite de serviços de expressão atingido][].|


## <a name="metadata"></a>Metadados

| Modo de exibição do sistema                        | Máximo de linhas |
| :--------------------------------- | :------------|
| sys.dm_pdw_component_health_alerts | 10.000       |
| sys.dm_pdw_dms_cores               | 100          |
| sys.dm_pdw_dms_workers             | Número total de trabalhadores DMS para o mais recente 1000 solicitações de SQL. |
| sys.dm_pdw_errors                  | 10.000       |
| sys.dm_pdw_exec_requests           | 10.000       |
| sys.dm_pdw_exec_sessions           | 10.000       |
| sys.dm_pdw_request_steps           | Número total de etapas para as solicitações SQL 1000 mais recentes que são armazenadas em sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs           | 10.000       |
| sys.dm_pdw_sql_requests            | O mais recente SQL de 1000 solicita que estão armazenadas no sys.dm_pdw_exec_requests. |


## <a name="next-steps"></a>Próximas etapas
Para obter informações de referência, consulte [Visão geral de referência de depósito de dados do SQL][].

<!--Image references-->

<!--Article references-->
[Unidades de dados de depósito (DWU)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[Visão geral da referência SQL Data Warehouse]: ./sql-data-warehouse-overview-reference.md
[Gerenciamento de carga de trabalho]: ./sql-data-warehouse-develop-concurrency.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[tipo de dados]: ./sql-data-warehouse-tables-data-types.md
[Criando um tíquete]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Dados de estouro de linha ultrapassando 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[Erro interno: um limite de serviços de expressão atingido]: https://support.microsoft.com/kb/913050
