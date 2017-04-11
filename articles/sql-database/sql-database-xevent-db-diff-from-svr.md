<properties
    pageTitle="Estendido eventos no banco de dados SQL | Microsoft Azure"
    description="Descreve eventos estendidos (XEvents) no banco de dados do SQL Azure e como sessões de evento diferem ligeiramente sessões de evento no Microsoft SQL Server."
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


# <a name="extended-events-in-sql-database"></a>Eventos estendidos no banco de dados SQL

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Este tópico explica como a implementação de eventos estendidos no banco de dados do SQL Azure é um pouco diferente em comparação com eventos estendidos no Microsoft SQL Server.


- V12 de banco de dados do SQL ganho o recurso de eventos estendido na segunda metade de calendário de 2015.
- SQL Server teve eventos estendidos desde 2008.
- O conjunto de recursos de eventos estendidos no banco de dados do SQL é um subconjunto robusto dos recursos no SQL Server.


*XEvents* é um apelido informal que às vezes é usado para 'eventos estendidos' em blogs e outros locais informais.


> [AZURE.NOTE] A partir de outubro de 2015, o recurso de sessão de evento estendido é ativado no Azure SQL Database no nível de visualização. A data de disponibilidade geral (GA) ainda não está definida.
>
> A página de [Atualizações de serviço](https://azure.microsoft.com/updates/?service=sql-database) do Azure tem postagens quando anúncios de GA são feitos.


Informações adicionais sobre eventos estendidos para Azure SQL Database e Microsoft SQL Server, estão disponíveis em:

- [Início rápido: Eventos estendidos no SQL Server](http://msdn.microsoft.com/library/mt733217.aspx)
- [Eventos estendidos](http://msdn.microsoft.com/library/bb630282.aspx)


## <a name="prerequisites"></a>Pré-requisitos


Este tópico pressupõe que você já tiver algum conhecimento de:


- [Serviço do azure SQL Database](https://azure.microsoft.com/services/sql-database/).


- [Eventos de estendidas](http://msdn.microsoft.com/library/bb630282.aspx) no Microsoft SQL Server.
 - A maior parte da nossa documentação sobre eventos estendidos se aplica ao SQL Server e o banco de dados SQL.


Exposição anterior para os itens a seguir é útil ao escolher o arquivo de evento como o [destino](#AzureXEventsTargets):


- [Serviço de armazenamento do Azure](https://azure.microsoft.com/services/storage/)


- PowerShell
 - [Usando o PowerShell do Azure com o armazenamento do Azure](../storage/storage-powershell-guide-full.md) - oferece informações completas sobre PowerShell e o serviço de armazenamento do Azure.


## <a name="code-samples"></a>Exemplos de código


Tópicos relacionados fornecem dois exemplos de código:


- [Ligar para o código de destino de Buffer para eventos estendidos no banco de dados SQL](sql-database-xevent-code-ring-buffer.md)
 - Pequeno script simples de Transact-SQL.
 - Podemos enfatizar no tópico de exemplo de código que, quando terminar com um destino de Buffer em anel, você deve liberar seus recursos executando um soltar alter `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` instrução. Depois que você possa adicionar outra instância do Buffer em anel por `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Código de destino do arquivo de evento para eventos estendidos no banco de dados SQL](sql-database-xevent-code-event-file.md)
 - Fase 1 é o PowerShell para criar um contêiner de armazenamento do Azure.
 - Fase 2 é Transact-SQL que usa o contêiner de armazenamento do Azure.


## <a name="transact-sql-differences"></a>Diferenças de Transact-SQL


- Quando você executar o comando [Criar evento sessão](http://msdn.microsoft.com/library/bb677289.aspx) no SQL Server, use a cláusula **ON SERVER** . Mas no banco de dados do SQL que você usa a cláusula de **Banco de dados ativado** .


- A cláusula de **Banco de dados ativado** também se aplica ao [Evento ALTER sessão](http://msdn.microsoft.com/library/bb630368.aspx) e comandos do [Evento SOLTAR sessão](http://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL.


- Uma prática recomendada é incluir a opção de sessão de evento de **STARTUP_STATE = ON** em suas instruções **Criar evento sessão** ou **sessão de evento ALTER** .
 - O valor **= ON** suporta a reinicialização automática após uma reconfiguração do banco de dados lógico devido a um failover.


## <a name="new-catalog-views"></a>Novos modos de exibição de catálogo


O recurso de eventos estendido é compatível com várias [exibições do catálogo](http://msdn.microsoft.com/library/ms174365.aspx). Exibições do catálogo informa sobre *metadados ou definições* de sessões de evento criado pelo usuário do banco de dados atual. Os modos de exibição não retornar informações sobre instâncias de sessões de eventos ativos.


| Nome do<br/>modo de exibição de catálogo | Descrição |
| :-- | :-- |
| **sys.database_event_session_actions** | Retorna uma linha para cada ação em cada evento de uma sessão de eventos. |
| **sys.database_event_session_events** | Retorna uma linha para cada evento em uma sessão de eventos. |
| **sys.database_event_session_fields** | Retorna uma linha para cada coluna capaz de personalizar que foi definida explicitamente em eventos e destinos. |
| **sys.database_event_session_targets** | Retorna uma linha para cada destino de evento para uma sessão de eventos. |
| **sys.database_event_sessions** | Retorna uma linha para cada sessão de eventos do banco de dados do banco de dados SQL. |


No Microsoft SQL Server, semelhante exibições do catálogo têm nomes que incluem *.server\_ * , em vez de *.database\_*. O padrão de nome é como **sys.server_event_%**.


## <a name="new-dynamic-management-views-dmvshttpmsdnmicrosoftcomlibraryms188754aspx"></a>Modos de exibição do novo gerenciamento dinâmico [DMVs)](http://msdn.microsoft.com/library/ms188754.aspx)


Banco de dados do SQL Azure tem [gerenciamento dinâmico DMVs (exibições)](http://msdn.microsoft.com/library/bb677293.aspx) que suportam eventos estendidos. DMVs informa sobre sessões de eventos *ativas* .


| Nome do departamento de trânsito | Descrição |
| :-- | :-- |
| **sys.dm_xe_database_session_event_actions** | Retorna informações sobre ações de sessão do evento. |
| **sys.dm_xe_database_session_events** | Retorna informações sobre eventos de sessão. |
| **sys.dm_xe_database_session_object_columns** | Mostra os valores de configuração para objetos que estão vinculados a uma sessão. |
| **sys.dm_xe_database_session_targets** | Retorna informações sobre os destinos de sessão. |
| **sys.dm_xe_database_sessions** | Retorna uma linha para cada sessão de evento que é delimitado para o banco de dados atual. |


No Microsoft SQL Server, semelhante exibições do catálogo são chamadas sem o * \_banco de dados* parte do nome, tais como:


- **sys.dm_xe_sessions**, em vez de nome<br/>**sys.dm_xe_database_sessions**.


### <a name="dmvs-common-to-both"></a>DMVs comuns ao


Para eventos estendidos há DMVs adicionais que são comuns a Azure SQL Database e o Microsoft SQL Server:


- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**



 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Localizar as disponíveis estendido eventos, ações e destinos


Você pode executar um simples SQL **Selecione** para obter uma lista dos eventos disponíveis, ações e destino.


```
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```



<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a>

&nbsp;

## <a name="targets-for-your-sql-database-event-sessions"></a>Destinos seu banco de dados SQL para sessões de evento


Aqui estão os destinos que podem capturar os resultados das suas sessões de evento no banco de dados do SQL:


- [Destino de Buffer em anel](http://msdn.microsoft.com/library/ff878182.aspx) - brevemente contém dados do evento na memória.
- [Contador de eventos destino](http://msdn.microsoft.com/library/ff878025.aspx) - conta todos os eventos que ocorrem durante uma sessão de eventos estendido.
- [Arquivo de evento destino](http://msdn.microsoft.com/library/ff878115.aspx) - gravações buffers concluídas a um contêiner de armazenamento do Azure.


O [Evento de rastreamento para Windows (ETW)](http://msdn.microsoft.com/library/ms751538.aspx) API não está disponível para eventos estendidos no banco de dados SQL.


## <a name="restrictions"></a>Restrições


Há algumas diferenças segurança assumindo o ambiente de nuvem do banco de dados SQL:


- Eventos estendidos são fundação no modelo de isolamento de locatário do único. Uma sessão de eventos em um banco de dados não pode acessar dados ou eventos de outro banco de dados.

- Você não pode emitir uma instrução de **Criar evento sessão** no contexto do banco de dados **mestre** .


## <a name="permission-model"></a>Modelo de permissão


Você deve ter permissão de **controle** no banco de dados para emitir uma instrução de **Criar evento sessão** . O proprietário do banco de dados (dbo) tem permissão de **controle** .


### <a name="storage-container-authorizations"></a>Autorizações de contêiner de armazenamento


O token SAS que gerar para o contêiner de armazenamento do Azure especifique **rwl** para as permissões. O valor de **rwl** fornece as seguintes permissões:


- Leitura
- Gravação
- Lista


## <a name="performance-considerations"></a>Considerações sobre o desempenho


Existem cenários onde uso intenso de eventos estendidos pode acumular mais ativa memória do que eficaz, para o sistema geral. Portanto, o sistema de banco de dados do Azure SQL define dinamicamente e ajusta limita a quantidade de memória ativa que pode ser acumulada por uma sessão de eventos. Muitos fatores entram no cálculo dinâmico.


Se você receber uma mensagem de erro que diz que um máximo de memória foi aplicada, algumas ações corretivas que você pode realizar são:


- Execute menos sessões de evento simultâneas.


- Por meio de suas instruções **criar** e **Alterar** para sessões de evento, reduza a quantidade de memória que você especificar o **Máx\_memória** cláusula.


### <a name="network-latency"></a>Latência de rede


O destino do **Arquivo de evento** pode enfrentar falhas ao persistir dados para o armazenamento do Azure blobs ou latência de rede. Outros eventos no banco de dados SQL podem ser atrasados enquanto eles aguardam a comunicação de rede concluir. Esse atraso pode reduzir sua carga de trabalho.

- Para atenuar o risco de desempenho, evite definir a opção de **EVENT_RETENTION_MODE** para **NO_EVENT_LOSS** no seu evento definições da sessão.


## <a name="related-links"></a>Links relacionados


- [Usando o PowerShell Azure com o armazenamento do Azure](../storage/storage-powershell-guide-full.md).
- [Cmdlets de armazenamento do Azure](http://msdn.microsoft.com/library/dn806401.aspx)


- [Usando o PowerShell do Azure com o armazenamento do Azure](../storage/storage-powershell-guide-full.md) - oferece informações completas sobre PowerShell e o serviço de armazenamento do Azure.
- [Como usar o armazenamento de Blob do .NET](../storage/storage-dotnet-how-to-use-blobs.md)


- [Criar CREDENCIAL (Transact-SQL)](http://msdn.microsoft.com/library/ms189522.aspx)
- [CRIAR sessão de evento (Transact-SQL)](http://msdn.microsoft.com/library/bb677289.aspx)


- [Postagens no blog de Jonathan de Kehayias sobre eventos estendidos no Microsoft SQL Server](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


Outros tópicos de amostra de código para eventos estendidos estão disponíveis nos seguintes links. No entanto, você sempre deve verificar qualquer amostra para ver se a amostra destinos Microsoft SQL Server versus Azure SQL Database. Em seguida, você pode decidir se pequenas alterações são necessários para executar a amostra.


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
