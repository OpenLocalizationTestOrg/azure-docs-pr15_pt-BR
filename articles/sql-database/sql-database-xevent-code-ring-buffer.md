<properties 
    pageTitle="Código de Buffer em anel XEvent para banco de dados SQL | Microsoft Azure" 
    description="Fornece um exemplo de código Transact-SQL que é feito fácil e rápida pelo uso do destino de Buffer em anel, no Azure SQL Database." 
    services="sql-database" 
    documentationCenter="" 
    authors="MightyPen" 
    manager="jhubbard" 
    editor="" 
    tags=""/>


<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="genemi"/>


# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Ligar para o código de destino de Buffer para eventos estendidos no banco de dados SQL

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Você deseja uma amostra de código completo para a maneira mais fácil de rápido para capturar e relatório de informações para um evento estendido durante um teste. O destino mais fácil para os dados de evento estendido é o [destino de Buffer em anel](http://msdn.microsoft.com/library/ff878182.aspx).


Este tópico apresenta uma amostra de código Transact-SQL que:


1. Cria uma tabela com dados para demonstrar com.

2. Cria uma sessão para um evento estendido existente, ou seja **sqlserver.sql_statement_starting**.
    - O evento está limitado aos instruções SQL que contêm uma determinada string de atualização: **instrução como '% atualização tabEmployee %'**.
    - Escolhe para enviar a saída do evento para um destino do tipo Buffer em anel, ou seja **package0.ring_buffer**.

3. Inicia a sessão de eventos.

4. Problemas de algumas simples instruções SQL UPDATE.

5. Problemas de uma instrução SELECT para recuperar a saída de evento do Buffer em anel.
    - outros modos de exibição de gerenciamento dinâmico (DMVs) e **Sys.dm_xe_database_session_targets** estão Unidos.

6. Interrompe a sessão do evento.

7. Descarta o destino de Buffer em anel, para liberar seus recursos.

8. Descarta a sessão de eventos e a tabela de demonstração.


## <a name="prerequisites"></a>Pré-requisitos


- Uma conta do Azure e assinatura. Você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).


- Qualquer banco de dados que você pode criar uma tabela no.
 - Opcionalmente, você pode [criar um banco de dados de demonstração de **AdventureWorksLT** ](sql-database-get-started.md) em minutos.


- SQL Server Management Studio (ssms.exe), ideal mensais sua versão mais recente atualização. Você pode baixar o ssms.exe mais recente do:
 - Tópico intitulado [Baixar o SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
 - [Um link direto para o download.](http://go.microsoft.com/fwlink/?linkid=616025)


## <a name="code-sample"></a>Amostra de código


Com muito pequena modificação, o seguinte exemplo de código de Buffer em anel pode ser executado no banco de dados do Azure SQL ou Microsoft SQL Server. A diferença é a presença do nó bancodedados' na nome alguns modos de exibição de gerenciamento dinâmico DMVs (), usado na cláusula FROM na etapa 5. Por exemplo:

- sys.dm_xe**bancodedados**_session_targets
- sys.dm_xe_session_targets


&nbsp;


```
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```


&nbsp;


## <a name="ring-buffer-contents"></a>Ligar para o conteúdo de Buffer


Usamos ssms.exe para executar a amostra de código.


Para exibir os resultados, podemos clicado na célula sob o cabeçalho de coluna **target_data_XML**.

Em seguida, no painel de resultados, podemos clicado na célula sob o cabeçalho de coluna **target_data_XML**. Este clique criada outra guia de arquivo no ssms.exe no qual o conteúdo da célula de resultado foi exibido, como XML.


A saída é mostrada no bloco a seguir. Parece longo, mas é apenas dois **<event>** elementos.


&nbsp;


```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```


#### <a name="release-resources-held-by-your-ring-buffer"></a>Recursos de lançamento mantidos pelo seu Buffer em anel


Quando terminar com o Buffer de anel, você pode removê-lo e liberar seus recursos emissão um **ALTER** semelhante ao seguinte:


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


A definição de sua sessão de evento é atualizada, mas não descartada. Posteriormente, você pode adicionar outra instância do Buffer toque à sua sessão de evento:


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```


## <a name="more-information"></a>Mais informações


O tópico principal para eventos estendidos no banco de dados do SQL Azure é:


- [Considerações de evento estendidas no banco de dados do SQL](sql-database-xevent-db-diff-from-svr.md), que contrasta alguns aspectos de eventos estendidos que diferem entre Azure SQL Database versus Microsoft SQL Server.


Outros tópicos de amostra de código para eventos estendidos estão disponíveis nos seguintes links. No entanto, você sempre deve verificar qualquer amostra para ver se a amostra destinos Microsoft SQL Server versus Azure SQL Database. Em seguida, você pode decidir se pequenas alterações são necessários para executar a amostra.


- Amostra de código para o banco de dados do Azure SQL: [código de destino do arquivo de evento para eventos estendidos no banco de dados do SQL](sql-database-xevent-code-event-file.md)


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
