<properties 
    pageTitle="Criar e gerenciar trabalhos de elástica banco de dados usando o PowerShell" 
    description="PowerShell usada para gerenciar pools de banco de dados do Azure SQL" 
    services="sql-database" documentationCenter=""  
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="create-and-manage-a-sql-database-elastic-database-jobs-using-powershell-preview"></a>Criar e gerenciar trabalhos de banco de dados elástica um banco de dados SQL usando o PowerShell (visualização)

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)



APIs do PowerShell para **trabalhos de banco de dados elástica** (nos modos de visualização) permitem que você defina um grupo de bancos de dados em relação à qual scripts executará. Este artigo mostra como criar e gerenciar **trabalhos de banco de dados elástica** usando cmdlets do PowerShell. Consulte [Visão geral de trabalhos elástica](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. Para uma avaliação gratuita, consulte [avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Um conjunto de bancos de dados criados com as ferramentas de banco de dados elástica. Consulte [Introdução às ferramentas de banco de dados elástica](sql-database-elastic-scale-get-started.md).
* PowerShell Azure. Para obter informações detalhadas, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).
* **Trabalhos de banco de dados elástica** Pacote PowerShell: consulte [instalar o banco de dados elástica trabalhos](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Selecione a sua assinatura do Azure

Para selecionar a assinatura que você precisa de sua assinatura Id (**-SubscriptionId**) ou assinatura nome (**-Nome de inscrição**). Se você tiver várias assinaturas, você pode executar o cmdlet **Get-AzureRmSubscription** e copie as informações de assinatura desejada do conjunto de resultados. Depois que você inserir suas informações de assinatura, execute o seguinte commandlet para definir esta assinatura como padrão, ou seja, o destino para criar e gerenciar trabalhos:

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

O [PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) é recomendado para uso desenvolver e executar scripts do PowerShell contra os trabalhos elástica banco de dados.

## <a name="elastic-database-jobs-objects"></a>Elástico objetos de trabalhos de banco de dados

A tabela a seguir lista todos os tipos de objeto de **banco de dados elástica trabalhos** juntamente com sua descrição e APIs do PowerShell relevantes.

<table style="width:100%">
  <tr>
    <th>Tipo de objeto</th>
    <th>Descrição</th>
    <th>APIs relacionadas do PowerShell</th>
  </tr>
  <tr>
    <td>Credencial</td>
    <td>Nome de usuário e senha para usar ao conectar com bancos de dados para execução de scripts ou aplicativo de DACPACs. <p>A senha está criptografada antes de enviar para e armazenar no banco de dados elástica trabalhos de banco de dados.  A senha é descriptografar pelo serviço elástica trabalhos de banco de dados via a credencial criado e carregados em script de instalação.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>Novo AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Script</td>
    <td>Script de Transact-SQL a ser usado para execução em bancos de dados.  O script deve ser criado para ser idempotentes desde que o serviço tentará execução do script após falhas.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>Novo AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Aplicativo de camada de dados </a> pacote seja aplicada em bancos de dados.

    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Novo AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Destino do banco de dados</td>
    <td>Banco de dados e nome do servidor apontando para um banco de dados do SQL Azure.

    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Novo AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Destino de mapa fragmentar</td>
    <td>Combinação de um destino de banco de dados e uma credencial a ser usado para determinar informações armazenadas dentro de um mapa de fragmentar elástica banco de dados.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Novo AzureSqlJobTarget</p>
    <p>Set-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Destino de coleção personalizada</td>
    <td>Grupo definido de bancos de dados coletivamente usar para execução.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Novo AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Coleção personalizada filho destino</td>
    <td>Destino de banco de dados que é referenciado em uma coleção personalizada.</td>
    <td>
    <p>AzureSqlJobChildTarget adicionar</p>
    <p>Remover AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Trabalho</td>
    <td>
    <p>Definição de parâmetros para um trabalho que podem ser usados para disparar execução ou para atender a uma programação.</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>Novo AzureSqlJob</p>
    <p>Set-AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>Execução de trabalho</td>
    <td>
    <p>Contêiner de tarefas necessárias para atender a execução de um script ou aplicando um DACPAC a um destino usando credenciais para conexões de banco de dados com falhas tratados de acordo com uma política de execução.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Início-AzureSqlJobExecution</p>
    <p>Parar AzureSqlJobExecution</p>
    <p>AzureSqlJobExecution de espera</p>
  </tr>

<tr>
    <td>Execução de tarefa de trabalho</td>
    <td>
    <p>Única unidade de trabalho para atender a um trabalho.</p>
    <p>Se uma tarefa de trabalho com êxito não é capaz de executar a mensagem de exceção resultante será registrada e uma nova tarefa de trabalho correspondente será criada e executada de acordo com a política de execução especificado.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Início-AzureSqlJobExecution</p>
    <p>Parar AzureSqlJobExecution</p>
    <p>AzureSqlJobExecution de espera</p>
  </tr>

<tr>
    <td>Política de execução de trabalho</td>
    <td>
    <p>Controles de trabalho tempos limite de execução, limites de repetição e intervalos entre as tentativas.</p>
    <p>Elástico trabalhos de banco de dados inclui uma política de execução de trabalho padrão que causar essencialmente infinitas tentativas de falhas de tarefa de trabalho com retirada exponencial dos intervalos entre cada tentativa.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>Novo AzureSqlJobExecutionPolicy</p>
    <p>Set-AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>Cronograma</td>
    <td>
    <p>Especificação para execução para ser feito em um intervalo redundantes ou uma única vez com base no tempo.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>Novo AzureSqlJobSchedule</p>
    <p>Set-AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>Gatilhos de trabalho</td>
    <td>
    <p>Um mapeamento entre um trabalho e um cronograma para execução de trabalho do disparador acordo com a agenda.</p>
    </td>
    <td>
    <p>Novo AzureSqlJobTrigger</p>
    <p>Remover AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Tipos de grupo de trabalhos de elástica banco de dados com suporte
O trabalho executa scripts Transact-SQL (T-SQL) ou aplicativo de DACPACs em um grupo de bancos de dados. Quando um trabalho é submetido a ser executado em um grupo de bancos de dados, o trabalho "se expande" o em trabalhos filho onde cada realiza a execução solicitada em relação a um banco de dados no grupo. 
 
Há dois tipos de grupos que você pode criar: 

* Grupo de [Mapa de fragmentar](sql-database-elastic-scale-shard-map-management.md) : quando um trabalho é enviado para um mapa de fragmentar de destino, o trabalho de consultas mapa fragmentar para determinar seu conjunto atual de fragmentos e cria filho trabalhos para cada fragmentar no mapa fragmentar.
* Grupo de conjunto personalizado: um personalizado definido conjunto de bancos de dados. Quando um trabalho destinos uma coleção personalizada, ele cria filho trabalhos para cada banco de dados atualmente no conjunto de personalizado.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Para definir o banco de dados elástica trabalhos de conexão

Uma conexão precisa ser definida ao banco trabalhos de *banco de dados de controle* antes de usar os trabalhos APIs. Executar este cmdlet aciona uma janela de credencial para pop-up solicitando o nome de usuário e a senha criada durante a instalação trabalhos elástica banco de dados. Todos os exemplos contidos neste tópico pressupõem que esta primeira etapa já foi executada.

Abra uma conexão para os trabalhos de banco de dados elástica:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Credenciais criptografadas dentro os trabalhos elástica banco de dados

Credenciais de banco de dados podem ser inseridas os trabalhos de *banco de dados de controle* com sua senha criptografada. É necessário armazenar credenciais para habilitar trabalhos a ser executado mais tarde, (usando cronogramas de trabalho).
 
Criptografia funciona através de um certificado criado como parte do script de instalação. O script de instalação cria e carrega o certificado para o serviço de nuvem do Azure para descriptografia das senhas criptografadas armazenadas. O serviço de nuvem do Azure posteriormente armazena a chave pública do trabalhos de *banco de dados do controle* que habilita a interface API do PowerShell ou Portal clássico do Azure para criptografar uma senha fornecida sem a necessidade do certificado a ser instalado localmente.
 
As senhas de credencial são criptografados e seguro de usuários com acesso somente leitura aos objetos do banco de dados elástica trabalhos. Mas é possível que um usuário mal-intencionado com acesso de leitura / gravação em objetos elástica trabalhos de banco de dados extrair uma senha. Credenciais foram projetadas para ser reutilizadas em execuções de trabalho. Credenciais são passadas aos bancos de dados de destino ao estabelecer conexões. Atualmente, não existem restrições nos bancos de dados de destino usados para cada credencial, usuário mal-intencionado adicionar um destino de banco de dados para um banco de dados sob controle do usuário mal-intencionado. O usuário subsequentemente poderia iniciar um trabalho direcionamento este banco de dados para obter a senha da credencial.

Práticas recomendadas de segurança para trabalhos de banco de dados elástica incluem:

* Limite o uso das APIs para indivíduos confiáveis.
* Credenciais devem ter os privilégios mínimos necessários para executar a tarefa de trabalho.  Mais informações podem ser vistas dentro deste artigo SQL Server MSDN [autorização e permissões](https://msdn.microsoft.com/library/bb669084.aspx) .

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Para criar uma credencial criptografada para execução de trabalhos em bancos de dados

Para criar uma nova credencial criptografada, o [**cmdlet Get-Credential**](https://technet.microsoft.com/library/hh849815.aspx) solicita um nome de usuário e senha que pode ser passada para o [**cmdlet New-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346063.aspx).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Atualizar credenciais

Quando alterar senhas, use o [**cmdlet Set-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346062.aspx) e defina o parâmetro **CredentialName** .

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Para definir um destino de mapa de fragmentar elástica banco de dados

Para executar um trabalho em relação a todos os bancos de dados em um conjunto de fragmentar (criado com a [biblioteca de cliente do banco de dados elástica](sql-database-elastic-database-client-library.md)), use um mapa de fragmentar como o destino de banco de dados. Este exemplo requer um aplicativo sharded criado usando a biblioteca de cliente elástica banco de dados. Consulte [Introdução ao exemplo de ferramentas de banco de dados elástica](sql-database-elastic-scale-get-started.md).

O banco de dados do Gerenciador de mapa fragmentar deve ser definido como um destino de banco de dados e, em seguida, o mapa de fragmentar específico deve ser especificado como um destino.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Criar um Script de T-SQL para execução em bancos de dados

Ao criar scripts T-SQL para execução, é altamente recomendável criá-las para ser [idempotentes](https://en.wikipedia.org/wiki/Idempotence) e flexíveis contra falhas. Elástico trabalhos de banco de dados tentará execução de um script, sempre que a execução encontrar uma falha, independentemente da classificação da falha.

Use o [**cmdlet New-AzureSqlJobContent**](https://msdn.microsoft.com/library/mt346085.aspx) para criar e salvar um script para execução e definir os parâmetros **- ContentName** e **- CommandText** .

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>Criar um novo script a partir de um arquivo
Se o script T-SQL é definido dentro de um arquivo, use isso para importar o script:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Para atualizar um script T-SQL para execução em bancos de dados  

Este script do PowerShell atualiza o texto de comando T-SQL para um script existente.

Defina as seguintes variáveis para refletir a definição de script desejado seja definida:

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>Para atualizar a definição para um script existente

    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Para criar um trabalho para executar um script em um mapa de fragmentar

Este script do PowerShell inicia um trabalho para execução de um script em cada fragmentar em um mapa de fragmentar dimensionamento Elástico.

Defina as seguintes variáveis para refletir o script desejado e destino:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>Para executar uma tarefa 

Este script do PowerShell executa um trabalho existente:

Atualize a seguinte variável para refletir o nome desejado de trabalho a ser executado:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Para recuperar o estado de um único de execução do trabalho

Use o [**cmdlet Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) e defina o parâmetro **JobExecutionId** para exibir o estado de execução do trabalho.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Use o cmdlet **Get-AzureSqlJobExecution** mesmo com o parâmetro **IncludeChildren** para exibir o estado de execuções de trabalho filho, ou seja, o estado específico para cada execução de trabalho em relação a cada banco de dados direcionada pelo trabalho.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Para exibir o estado entre várias execuções de trabalho

O [**cmdlet Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) tem vários parâmetros opcionais que podem ser usados para exibir várias execuções de trabalho, filtradas por meio de parâmetros fornecidos. Algumas das possíveis maneiras de usar o Get-AzureSqlJobExecution demonstra o seguinte:

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

## <a name="to-retrieve-failures-within-job-task-executions"></a>Para recuperar falhas dentro execuções de tarefa de trabalho

O **objeto JobTaskExecution** inclui uma propriedade para o ciclo de vida da tarefa junto com uma propriedade de mensagem. Se um trabalho tarefa Falha na execução, a propriedade de ciclo de vida será definida como *Falha* e a propriedade da mensagem será definida para a mensagem de exceção resultante e sua pilha. Se um trabalho não foi bem-sucedida, é importante exibir os detalhes de tarefas de trabalho que não faziam bem-sucedida para um determinado trabalho.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>Aguardar uma execução de trabalho ser concluída

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
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
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

1. Cancelar tarefas em execução no momento: se um cancelamento for detectado enquanto uma tarefa estiver em execução, um cancelamento será tentado dentro o aspecto atualmente em execução da tarefa.  Por exemplo: se houver uma consulta de execução longa está sendo executada atualmente quando um cancelamento é tentado, haverá uma tentativa de cancelar a consulta.
2. Cancelando tentativas de tarefa: se um cancelamento for detectado pelo thread controle antes de uma tarefa é iniciada para execução, o thread de controle será evitar a inicialização da tarefa e declarar a solicitação como cancelada.

Se for solicitado um cancelamento de trabalho para um trabalho pai, a solicitação de cancelamento será atendida para o trabalho pai e para todos os seus trabalhos de filho de.
 
Para enviar uma solicitação de cancelamento, use o [**cmdlet de parada AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346053.aspx) e defina o parâmetro **JobExecutionId** .

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Para excluir um trabalho e histórico do trabalho assíncrona

Elástico trabalhos de banco de dados compatível com a exclusão assíncrona de trabalhos. Um trabalho pode ser marcado para exclusão e o sistema excluirá o trabalho e todo o seu histórico de trabalho depois de concluir todas as execuções de trabalho para o trabalho. O sistema não cancelará automaticamente execuções de trabalho ativo.  

Chame [**AzureSqlJobExecution de parar**](https://msdn.microsoft.com/library/mt346053.aspx) para cancelar execuções de trabalho ativo.

Para disparar a exclusão de trabalho, use o [**cmdlet remover AzureSqlJob**](https://msdn.microsoft.com/library/mt346083.aspx) e definir o parâmetro **nome_do_trabalho** .

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## <a name="to-create-a-custom-database-target"></a>Para criar um destino de banco de dados personalizado

Você pode definir os destinos de banco de dados personalizado para execução direta ou para inclusão dentro de um grupo de banco de dados personalizado. Por exemplo, porque **pools de banco de dados elástica** ainda não são diretamente aceitas usando APIs do PowerShell, você pode criar um destino de banco de dados personalizado e o destino de conjunto de banco de dados personalizado que abrange todos os bancos de dados no pool.

Defina as seguintes variáveis para refletir as informações de banco de dados desejado:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Para criar um destino de conjunto de banco de dados personalizado

Use o cmdlet [**New-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) para definir um destino de conjunto de banco de dados personalizado para habilitar a execução por vários destinos de banco de dados definido. Após criar um grupo de banco de dados, bancos de dados podem ser associados a destino coleção personalizada.

Defina as seguintes variáveis para refletir a configuração de destino coleção personalizada desejada:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Para adicionar bancos de dados a um destino de conjunto de banco de dados personalizado

Para adicionar um banco de dados a um conjunto específico de personalizado use o cmdlet [**Add-AzureSqlJobChildTarget**](https://msdn.microsoft.comlibrary/mt346064.aspx) .

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Examine os bancos de dados dentro de um destino de conjunto de banco de dados personalizado

Use o cmdlet [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) para recuperar os bancos de dados filho dentro de um destino de conjunto de banco de dados personalizado. 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Criar um trabalho para executar um script em um destino de conjunto de banco de dados personalizado

Use o cmdlet [**New-AzureSqlJob**](https://msdn.microsoft.com/library/mt346078.aspx) para criar um trabalho em relação a um grupo de bancos de dados definidos por um destino de conjunto de banco de dados personalizado. Elástico trabalhos de banco de dados irá expandir o trabalho em vários trabalhos de filho cada correspondente a um banco de dados associado com o destino de conjunto de banco de dados personalizado e certifique-se de que o script é executado em cada banco de dados. Novamente, é importante que os scripts sejam idempotentes seja flexível a tentativas.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Coleta de dados em bancos de dados

Você pode usar um trabalho para executar uma consulta em um grupo de bancos de dados e enviar os resultados para uma tabela específica. A tabela pode ser consultada após o fato para ver os resultados da consulta de cada banco de dados. Isso fornece um método assíncrono para executar uma consulta em muitos bancos de dados. Tentativas são tratadas automaticamente por meio de tentativas.

A tabela de destino especificada será criada automaticamente se ela ainda não existir. A nova tabela corresponde o esquema do conjunto de resultados retornados. Se um script retornar vários conjuntos de resultados, trabalhos de banco de dados elástica enviará somente o primeiro à tabela de destino.

O seguinte script do PowerShell executa um script e reúne seus resultados em uma tabela especificada. Esse script pressupõe que um script T-SQL foi criado que produz um único conjunto de resultados e que foi criado um destino de conjunto de banco de dados personalizado.

Esse script usa o cmdlet [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) . Defina os parâmetros de script, credenciais e destino de execução:

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

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Para criar e iniciar um trabalho para cenários de conjunto de dados

Esse script usa o cmdlet [**Start-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346055.aspx) .
 
    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>Agendar um gatilho de execução de trabalho

O seguinte script do PowerShell pode ser utilizado para criar um agendamento recorrente. Este script usa um intervalo de minuto, mas [**New-AzureSqlJobSchedule**](https://msdn.microsoft.com/library/mt346068.aspx) também suporta parâmetros - DayInterval, - HourInterval, - MonthInterval e - WeekInterval. Agendas que executam somente uma vez podem ser criadas por passando - única.

Crie uma nova agenda:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Disparar um trabalho executado em um cronograma de tempo

Um gatilho de trabalho pode ser definido para ter um trabalho executado de acordo com uma agenda de tempo. O seguinte script do PowerShell pode ser usado para criar um disparador de trabalho.

Use o [Novo AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346069.aspx) e definir as seguintes variáveis para corresponder ao trabalho desejado e o cronograma:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    –JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Para remover uma associação agendada para interromper o trabalho de executar no cronograma

Para interromper a execução do trabalho redundantes por meio de um disparador de trabalho, o disparador de trabalho pode ser removido. Remova um gatilho de trabalho para interromper um trabalho seja executado de acordo com uma programação usando o [**cmdlet Remove-AzureSqlJobTrigger**](https://msdn.microsoft.com/library/mt346070.aspx).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Recuperar gatilhos de trabalho associados a um plano de horário

O seguinte script do PowerShell pode ser usado para obter e exibir os disparadores de trabalho registrados para agendar uma hora específica.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Para recuperar gatilhos de trabalho associado a um trabalho 

Use [Get-AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346067.aspx) para obter e Exibir agendas contendo um trabalho registrado.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Para criar um aplicativo de camada de dados (DACPAC) para execução em bancos de dados

Para criar um DACPAC, consulte [aplicativos de camada de dados](https://msdn.microsoft.com/library/ee210546.aspx). Para implantar um DACPAC, use o [cmdlet New-AzureSqlJobContent](https://msdn.microsoft.com/library/mt346085.aspx). O DACPAC deve estar acessível para o serviço. É recomendável para carregar um DACPAC criado ao armazenamento do Azure e criar uma [Assinatura de acesso compartilhado](../storage/storage-dotnet-shared-access-signature-part-1.md) para o DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Para atualizar um aplicativo de camada de dados (DACPAC) para execução em bancos de dados

DACPACs existentes registrados em elástica trabalhos de banco de dados podem ser atualizados para apontar para o novo URIs. Use o [**cmdlet Set-AzureSqlJobContentDefinition**](https://msdn.microsoft.com/library/mt346074.aspx) para atualizar o URI DACPAC em um DACPAC registrado existente:

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Para criar um trabalho para aplicar um aplicativo de camada de dados (DACPAC) em bancos de dados

Após um DACPAC tiver sido criado dentro elástica trabalhos de banco de dados, um trabalho pode ser criado para aplicar o DACPAC em um grupo de bancos de dados. O seguinte script do PowerShell pode ser usado para criar um trabalho DACPAC através de um conjunto personalizado de bancos de dados:

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
