<properties
    pageTitle="Introdução ao trabalhos elástica banco de dados"
    description="como usar trabalhos elástica banco de dados"
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="ddove"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="ddove" />

# <a name="getting-started-with-elastic-database-jobs"></a>Introdução ao trabalhos elástica banco de dados

Elástico trabalhos de banco de dados (visualização) para o banco de dados do Azure SQL permite que você confiabilidade executa scripts de T-SQL que abrangem vários bancos de dados enquanto repetindo e fornecendo garantias eventual conclusão automaticamente. Para obter mais informações sobre o recurso de trabalho elástica banco de dados, consulte a [página de visão geral do recurso](sql-database-elastic-jobs-overview.md).

Este tópico estende a amostra encontrada na [guia de Introdução com ferramentas de banco de dados elástica](sql-database-elastic-scale-get-started.md). Quando concluir, você irá: Aprenda a criar e gerenciar trabalhos que gerenciar um grupo de bancos de dados relacionados. Não é necessário para usar as ferramentas de dimensionamento Elástico para aproveitar os benefícios de trabalhos Elástico.

## <a name="prerequisites"></a>Pré-requisitos

Baixar e executar o [guia de Introdução do exemplo de ferramentas de banco de dados elástica](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Criar um fragmentar Gerenciador de mapa usando o aplicativo de amostra

Aqui você criará um mapa de fragmentar manager juntamente com vários fragmentos, seguido de inserção de dados para os fragmentos. Se você já tiver fragmentos configurado com dados sharded neles, você pode ignorar as etapas a seguir e mover para a próxima seção.

1. Criar e executar o aplicativo de exemplo do **guia de Introdução com ferramentas de banco de dados elástica** . Siga as etapas até a etapa 7 na seção [baixar e executar o aplicativo de amostra](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). No final da etapa 7, você verá o seguinte prompt de comando:

    ![prompt de comando][1]

2.  Na janela de comando, digite "1" e pressione **Enter**. Isso cria o fragmentar Gerenciador do mapa e adiciona dois fragmentos ao servidor. Em seguida, digite "3" e pressione **Enter**; Repita essa ação quatro vezes. Isso insere as linhas de dados de amostra no seu fragmentos.

3.  O [Portal do Azure](https://portal.azure.com) deve mostrar três novos bancos de dados no seu servidor v12:

    ![Confirmação do Visual Studio][2]

    Neste ponto, vamos criar um conjunto de banco de dados personalizado que reflete todos os bancos de dados no mapa fragmentar. Isso nos permitirá criar e executar um trabalho que adicionar uma nova tabela em fragmentos.

Aqui nós geralmente criaria um destino de mapa fragmentar, usando o cmdlet **New-AzureSqlJobTarget** . O banco de dados do Gerenciador de mapa fragmentar deve ser definido como um destino de banco de dados e, em seguida, o mapa de fragmentar específica é especificado como um destino. Em vez disso, vamos enumerar todos os bancos de dados no servidor e adicionar os bancos de dados para o novo conjunto personalizado com exceção de banco de dados mestre.

##<a name="creates-a-custom-collection-and-adds-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Cria uma coleção personalizada e adiciona todos os bancos de dados no servidor para o destino de coleção personalizada excepto mestre.


    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    $dbsinserver | %{
    $currentdb = $_.DatabaseName 
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason
                
        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target." 
        }

        else
        {
            throw $_
        }
    
    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
}
    
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Criar um Script de T-SQL para execução em bancos de dados

    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

##<a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Criar o trabalho para executar um script entre o grupo personalizado de bancos de dados

    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job


##<a name="execute-the-job"></a>Executar o trabalho 

O seguinte script do PowerShell pode ser usado para executar um trabalho existente:

Atualize a seguinte variável para refletir o nome desejado de trabalho a ser executado:

    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## <a name="retrieve-the-state-of-a-single-job-execution"></a>Recuperar o estado de um único de execução do trabalho

Use o cmdlet **Get-AzureSqlJobExecution** mesmo com o parâmetro **IncludeChildren** para exibir o estado de execuções de trabalho filho, ou seja, o estado específico para cada execução de trabalho em relação a cada banco de dados direcionada pelo trabalho.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="view-the-state-across-multiple-job-executions"></a>Exibir o estado entre várias execuções de trabalho

O cmdlet **Get-AzureSqlJobExecution** tem vários parâmetros opcionais que podem ser usados para exibir várias execuções de trabalho, filtradas por meio de parâmetros fornecidos. Algumas das possíveis maneiras de usar o Get-AzureSqlJobExecution demonstra o seguinte:

Recupere todas as execuções de trabalho ativo de nível superior:

    Get-AzureSqlJobExecution

Recupere todas as execuções de trabalho de nível superior, incluindo execuções de trabalho inativo:

    Get-AzureSqlJobExecution -IncludeInactive

Recupere todas as execuções de trabalho filho de uma identificação de execução de trabalho fornecidos, incluindo execuções de trabalho inativo:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

Recuperar todas as execuções de trabalho criadas usando um cronograma / combinação, incluindo trabalhos inativos de trabalho:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Recupere todos os trabalhos de direcionamento de um mapa de fragmentar especificado, incluindo trabalhos inativos:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

Recupere todos os trabalhos de direcionamento de um conjunto personalizado especificado, incluindo trabalhos inativos:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
Recupere a lista de execuções de tarefa de trabalho dentro de uma execução de trabalho específico:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Recupere detalhes de execução de tarefa de trabalho:

O seguinte script do PowerShell pode ser usado para exibir os detalhes de uma execução de tarefa de trabalho, que é especialmente útil quando depuração falhas na execução.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="retrieve-failures-within-job-task-executions"></a>Recuperar falhas dentro execuções de tarefa de trabalho

O objeto JobTaskExecution inclui uma propriedade para o ciclo de vida da tarefa junto com uma propriedade de mensagem. Se um trabalho tarefa Falha na execução, a propriedade de ciclo de vida será definida como *Falha* e a propriedade da mensagem será definida para a mensagem de exceção resultante e sua pilha. Se um trabalho não foi bem-sucedida, é importante exibir os detalhes de tarefas de trabalho que não faziam bem-sucedida para um determinado trabalho.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="waiting-for-a-job-execution-to-complete"></a>Aguardando uma execução de trabalho ser concluída

O seguinte script do PowerShell pode ser usado para aguardar para uma tarefa de trabalho ser concluída:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Criar uma política de execução personalizada

Elástico trabalhos de banco de dados suporta a criação de diretivas de execução personalizados que podem ser aplicadas ao iniciar trabalhos.
  
Políticas de execução atualmente permitem definir:

* Nome: O identificador para a política de execução.
* Tempo limite do trabalho: Tempo Total antes de um trabalho será cancelado por elástica trabalhos de banco de dados.
* Intervalo de repetição inicial: Intervalo aguardar antes de repetir primeiro.
* Intervalo máximo de repetição: O limite de intervalos de repetição para usar.
* Coeficiente de retirada de intervalo de repetição: Coeficiente usado para calcular o próximo intervalo entre as tentativas.  A seguinte fórmula é usada: (intervalo de repetição inicial) * Math.pow ((coeficiente de retirada de intervalo), (número de tentativas de) - 2). 
* Máximo de tentativas: O número máximo de repetir tenta executar dentro de um trabalho.

A política de execução padrão usa os seguintes valores:

* Nome: Política de execução de padrão
* Tempo limite do trabalho: 1 semana
* Intervalo de repetição inicial: 100 milissegundos
* Intervalo máximo de repetição: 30 minutos
* Repetir o coeficiente de intervalo: 2
* Máximo de tentativas: 2.147.483.647

Crie a política de execução desejado:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Atualizar uma política de execução personalizada

Atualize a política de execução desejado para atualizar:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
 
## <a name="cancel-a-job"></a>Cancelar um trabalho

Elástico trabalhos de banco de dados compatível com solicitações de cancelamento de trabalhos.  Se trabalhos de banco de dados elástica detectar uma solicitação de cancelamento para um trabalho está sendo executada no momento, ele tentará parar o trabalho.

Há duas maneiras diferentes que elástica trabalhos de banco de dados pode executar um cancelamento:

1. Cancelando execução atualmente tarefas: se um cancelamento for detectado enquanto uma tarefa estiver em execução, um cancelamento será tentado dentro o aspecto atualmente em execução da tarefa.  Por exemplo: se houver uma consulta de execução longa está sendo executada atualmente quando um cancelamento é tentado, haverá uma tentativa de cancelar a consulta.
2. Cancelamento tentativas de tarefa: Se um cancelamento for detectado pelo thread controle antes de uma tarefa é iniciada para execução, o controle thread será evitar a inicialização da tarefa e declarar a solicitação como cancelada.

Se for solicitado um cancelamento de trabalho para um trabalho pai, a solicitação de cancelamento será atendida para o trabalho pai e para todos os seus trabalhos de filho de.
 
Para enviar uma solicitação de cancelamento, use o cmdlet **Parada AzureSqlJobExecution** e defina o parâmetro **JobExecutionId** .

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Excluir um trabalho por nome e histórico do trabalho

Elástico trabalhos de banco de dados compatível com a exclusão assíncrona de trabalhos. Um trabalho pode ser marcado para exclusão e o sistema excluirá o trabalho e todo o seu histórico de trabalho depois de concluir todas as execuções de trabalho para o trabalho. O sistema não cancelará automaticamente execuções de trabalho ativo.  

Em vez disso, parar-AzureSqlJobExecution deve ser invocado para cancelar execuções de trabalho ativo.

Para disparar a exclusão de trabalho, use o cmdlet **Remover AzureSqlJob** e definir o parâmetro **nome_do_trabalho** .

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## <a name="create-a-custom-database-target"></a>Criar um destino de banco de dados personalizado
Destinos de banco de dados personalizados podem ser definidos em trabalhos elástica banco de dados que podem ser usados para execução diretamente ou para inclusão dentro de um grupo de banco de dados personalizado. Desde que o **banco de dados elástica pools** ainda não são diretamente aceitas por meio das APIs do PowerShell, basta criar um destino de banco de dados personalizado e o destino de conjunto de banco de dados personalizado que abrange todos os bancos de dados no pool.

Defina as seguintes variáveis para refletir as informações de banco de dados desejado:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="create-a-custom-database-collection-target"></a>Criar um destino de conjunto de banco de dados personalizado
Um destino de conjunto de banco de dados personalizados pode ser definido para habilitar a execução por vários destinos de banco de dados definido. Depois de um grupo de banco de dados é criado, bancos de dados podem ser associados ao destino coleção personalizada.

Defina as seguintes variáveis para refletir a configuração de destino coleção personalizada desejada:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="add-databases-to-a-custom-database-collection-target"></a>Adicionar bancos de dados para um destino de conjunto de banco de dados personalizado

Destinos de banco de dados podem ser associados a destinos de conjunto de banco de dados personalizado para criar um grupo de bancos de dados. Sempre que um trabalho é criado destinos um destino de conjunto de banco de dados personalizado, ele será expandido para direcionar os bancos de dados associados ao grupo ao tempo de execução.

Adicione o banco de dados desejado a um conjunto específico de personalizado:

    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Examine os bancos de dados dentro de um destino de conjunto de banco de dados personalizado

Use o cmdlet **Get-AzureSqlJobTarget** para recuperar os bancos de dados filho dentro de um destino de conjunto de banco de dados personalizado. 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Criar um trabalho para executar um script em um destino de conjunto de banco de dados personalizado

Use o cmdlet **New-AzureSqlJob** para criar um trabalho em relação a um grupo de bancos de dados definidos por um destino de conjunto de banco de dados personalizado. Elástico trabalhos de banco de dados irá expandir o trabalho em vários trabalhos de filho cada correspondente a um banco de dados associado com o destino de conjunto de banco de dados personalizado e certifique-se de que o script é executado em cada banco de dados. Novamente, é importante que os scripts sejam idempotentes seja flexível a tentativas.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Coleta de dados em bancos de dados

**Trabalhos de banco de dados elástica** suporta a execução de uma consulta em um grupo de bancos de dados e envia os resultados a tabela do banco de dados especificada. A tabela pode ser consultada após o fato para ver os resultados da consulta de cada banco de dados. Isso fornece um mecanismo assíncrono para executar uma consulta em muitos bancos de dados. Casos de falha como um banco de dados está sendo temporariamente indisponível são tratados automaticamente por meio de tentativas.

A tabela de destino especificada será criada automaticamente se ele não existir ainda, o esquema do conjunto de resultados retornados de correspondência. Se uma execução de script retornar vários conjuntos de resultados, trabalhos de banco de dados elástica enviará somente o primeiro que a tabela de destino fornecido.

O seguinte script do PowerShell pode ser usado para executar um script coletar seus resultados em uma tabela especificada. Esse script pressupõe que foi criado um script T-SQL que produz um único conjunto de resultados e um destino de conjunto de banco de dados personalizado tiver sido criado.

Defina o seguinte para refletir o script desejado, credenciais e destino de execução:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Criar e iniciar um trabalho para cenários de conjunto de dados
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Criar um agendamento para execução de trabalhos usando um gatilho de trabalho

O seguinte script do PowerShell pode ser usado para criar um cronograma redundantes. Este script usa um intervalo de um minuto, mas New-AzureSqlJobSchedule também suporta parâmetros - DayInterval, - HourInterval, - MonthInterval e - WeekInterval. Agendas que executam somente uma vez podem ser criadas por passando - única.

Crie uma nova agenda:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
    Write-Output $schedule

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Criar um gatilho de trabalho para que um trabalho seja executado em um cronograma de tempo

Um gatilho de trabalho pode ser definido para ter um trabalho executado de acordo com uma agenda de tempo. O seguinte script do PowerShell pode ser usado para criar um disparador de trabalho.

Defina as seguintes variáveis para corresponder ao trabalho desejado e o cronograma:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
    Write-Output $jobTrigger

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Remover uma associação agendada para interromper o trabalho de executar no cronograma

Para interromper a execução do trabalho redundantes por meio de um disparador de trabalho, o disparador de trabalho pode ser removido. Remova um gatilho de trabalho para interromper um trabalho seja executado de acordo com uma programação usando o cmdlet **Remove-AzureSqlJobTrigger** .

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName





## <a name="import-elastic-database-query-results-to-excel"></a>Importar resultados de consulta de banco de dados elástica para o Excel

 Você pode importar os resultados de uma consulta para um arquivo do Excel.

1. Inicie o Excel 2013.
2.  Navegue até a faixa de opções de **dados** .
3.  Clique em **De outras fontes** e **Do SQL Server**.

    ![Importação do Excel de outras fontes][5]
4.  No **Assistente para Conexão de dados** , digite as credenciais de nome e faça logon de servidor. Clique em **Avançar**.
5.  Na caixa de diálogo **Selecione o banco de dados que contém os dados desejados**, selecione o banco de dados de **ElasticDBQuery** .
6.  Selecione a tabela **clientes** no modo de exibição de lista e clique em **Avançar**. Clique em **Concluir**.
7.  No formulário de **Importar dados** , em **Selecione como você deseja exibir esses dados na pasta de trabalho**, selecione a **tabela** e clique **Okey**.

Todas as linhas de tabela de **clientes** , armazenados em fragmentos diferentes preencher a planilha do Excel.

## <a name="next-steps"></a>Próximas etapas
Agora você pode usar funções de dados do Excel. Use a cadeia de conexão com o nome do servidor, nome do banco de dados e as credenciais para conectar suas ferramentas de integração de BI e dados no banco de dados de consulta elástica. Certifique-se de que o SQL Server é suportado como uma fonte de dados para sua ferramenta. Consulte o banco de dados de consulta elástica e tabelas externas assim como qualquer outro banco de dados do SQL Server e SQL Server que você deseja se conectar a com a ferramenta.

### <a name="cost"></a>Custo
Não há nenhum custo adicional para usar o recurso de consulta de banco de dados elástica. No entanto, neste momento este recurso está disponível apenas em bancos de dados premium como um ponto final, mas os fragmentos podem ser de qualquer nível de serviço.

Para informações sobre preços consulte [Detalhes de preços de banco de dados do SQL](https://azure.microsoft.com/pricing/details/sql-database/).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
