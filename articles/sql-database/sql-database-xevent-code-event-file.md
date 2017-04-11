<properties 
    pageTitle="Código de arquivo de evento XEvent para banco de dados SQL | Microsoft Azure" 
    description="Fornece PowerShell e Transact-SQL para uma amostra de código de duas fases que demonstra o destino do arquivo de evento em um evento estendido no Azure SQL Database. Armazenamento do Azure é uma parte necessária deste cenário." 
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


# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Código de destino do arquivo de evento para eventos estendidos no banco de dados SQL

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Você deseja uma amostra de código completo para uma maneira eficiente de capturar e relatório de informações para um evento estendido.


No Microsoft SQL Server, o [destino do arquivo de evento](http://msdn.microsoft.com/library/ff878115.aspx) é usado para armazenar saídas de evento em um arquivo de disco rígido local. Mas esses arquivos não estão disponíveis para Azure SQL Database. Em vez disso, usamos o serviço de armazenamento do Azure para o destino do arquivo de evento de suporte.


Este tópico apresenta uma amostra de código de duas fases:


- PowerShell, para criar um contêiner de armazenamento do Azure na nuvem.

- Transact-SQL:
 - Para atribuir o contêiner de armazenamento do Azure para um destino de arquivo de evento.
 - Para criar e inicie a sessão de evento e assim por diante.


## <a name="prerequisites"></a>Pré-requisitos


- Uma conta do Azure e assinatura. Você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).


- Qualquer banco de dados que você pode criar uma tabela no.
 - Opcionalmente, você pode [criar um banco de dados de demonstração de **AdventureWorksLT** ](sql-database-get-started.md) em minutos.


- SQL Server Management Studio (ssms.exe), ideal mensais sua versão mais recente atualização. Você pode baixar o ssms.exe mais recente do:
 - Tópico intitulado [Baixar o SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
 - [Um link direto para o download.](http://go.microsoft.com/fwlink/?linkid=616025)


- Você deve ter o [Azure PowerShell módulos](http://go.microsoft.com/?linkid=9811175) instalados.
 - Os módulos fornecem comandos como - **AzureStorageAccount de novo**.


## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fase 1: Código do PowerShell para contêiner de armazenamento do Azure


Este PowerShell é fase 1 do código duas fases de exemplo.

O script começa com comandos para limpar após um possível anterior executado e é rerunnable.



1. Cole o script PowerShell em um editor de texto simples como Notepad.exe e salve o script como um arquivo com a extensão **. ps1**.

2. Inicie o PowerShell ISE como administrador.

3. No prompt, digite<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>e, em seguida, pressione Enter.

4. No PowerShell ISE, abra o arquivo **. ps1** . Execute o script.

5. Primeiro, o script inicia uma nova janela em que você efetuar login no Azure.
 - Se você executar novamente o script sem interromper a sessão, você tem a opção conveniente de comentar o comando **Adicionar AzureAccount** .


![PowerShell ISE, com módulo Azure instalado, pronto para executar o script.][30_powershell_ise]


&nbsp;


```
## TODO: Before running, find all 'TODO' and make each edit!!

#--------------- 1 -----------------------


# You can comment out or skip this Add-AzureAccount
# command after the first run.
# Current PowerShell environment retains the successful outcome.

'Expect a pop-up window in which you log in to Azure.'


Add-AzureAccount

#-------------- 2 ------------------------


'
TODO: Edit the values assigned to these variables, especially the first few!
'

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME'
$policySasExpiryTime = '2016-01-28T23:44:56Z'
$policySasStartTime  = '2015-08-01'


$storageAccountName     = 'gmstorageaccountxevent'
$storageAccountLocation = 'West US'
$contextName            = 'gmcontext'
$containerName          = 'gmcontainerxevent'
$policySasToken         = 'gmpolicysastoken'


# Leave this value alone, as 'rwl'.
$policySasPermission = 'rwl'

#--------------- 3 -----------------------


# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.'


Select-AzureSubscription -SubscriptionName $subscriptionName


#-------------- 4 ------------------------


'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.'


If ($storageAccountName)
{
    Remove-AzureStorageAccount -StorageAccountName $storageAccountName
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString()

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...'

New-AzureStorageAccount `
    -StorageAccountName $storageAccountName `
    -Location           $storageAccountLocation

[System.DateTime]::Now.ToString()

[System.Media.SystemSounds]::Beep.Play()


'
Get the primary access key for your storage account.
'


$primaryAccessKey_ForStorageAccount = `
    (Get-AzureStorageKey `
        -StorageAccountName $storageAccountName).Primary

"`$primaryAccessKey_ForStorageAccount = $primaryAccessKey_ForStorageAccount"

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
'

#--------------- 6 -----------------------


# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
'

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $primaryAccessKey_ForStorageAccount


'Create a container within the storage account.
'


$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context


'Create a security policy to be applied to the SAS token.
'

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime 

'
Generate a SAS token for the container.
'
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken
}
Catch 
{
    $Error[0].Exception.ToString()
}

#-------------- 7 ------------------------


'Display the values that YOU must edit into the Transact-SQL script next!:
'

"storageAccountName: $storageAccountName"
"containerName:      $containerName"
"sasTokenWithPolicy: $sasTokenWithPolicy"

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
'

'
(Later, return here to delete your Azure Storage account. See the preceding - Remove-AzureStorageAccount -StorageAccountName $storageAccountName)'

'
Now shift to the Transact-SQL portion of the two-part code sample!'

# EOFile
```


&nbsp;


Tome nota dos alguns valores nomeados que o script do PowerShell imprime quando termina. Você deve editar esses valores no script Transact-SQL que segue como fase 2.


## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fase 2: Código de Transact-SQL que usa o contêiner de armazenamento do Azure


- Na fase 1 deste exemplo de código, você executou um script do PowerShell para criar um contêiner de armazenamento do Azure.
- Avançar em fase 2, o seguinte script Transact-SQL deve usar o contêiner.


O script começa com comandos para limpar após um possível anterior executado e é rerunnable.


O script do PowerShell impressa alguns valores nomeada quando ele encerrada. Você deve editar o script Transact-SQL para usar esses valores. Encontre **TODO** o script Transact-SQL para localizar os pontos de edição.


1. Abra o SQL Server Management Studio (ssms.exe).

2. Se conecte ao seu banco de dados do Azure SQL Database.

3. Clique para abrir um novo painel de consulta.

4. Cole o seguinte script Transact-SQL no painel consulta.

5. Localize cada **TODO** no script e faça as edições apropriadas.

6. Salve e execute o script.


&nbsp;


> [AZURE.WARNING] O valor da chave SAS gerado pelo script do PowerShell anterior pode começar com um '?' (ponto de interrogação). Quando você usa a tecla SAS no script de T-SQL a seguir, você deve *Remover os principais '?'*. Caso contrário, seus esforços podem ser bloqueados pelo segurança.


&nbsp;


```
---- TODO: First, run the PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;
GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO


-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


&nbsp;


Se o destino não conseguir anexar ao executar, você deve parar e reiniciar a sessão de eventos:


```
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


&nbsp;


## <a name="output"></a>Saída


Quando o script Transact-SQL for concluído, clique em uma célula sob o cabeçalho de coluna **event_data_XML** . Um **<event>** elemento é exibido que mostra uma instrução UPDATE.

Eis uma **<event>** elemento que foi gerado durante o teste:


&nbsp;


```
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

&nbsp;


O script anterior do Transact-SQL usada a função de sistema a seguir para ler o event_file:

- [sys.fn_xe_file_target_read_file (Transact-SQL)](http://msdn.microsoft.com/library/cc280743.aspx)


Obter uma explicação das opções avançadas para a visualização de dados de eventos estendidos está disponível em:

- [Exibição avançada de dados de destino de eventos estendidos](http://msdn.microsoft.com/library/mt752502.aspx)

&nbsp;


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Converter o código de exemplo para executar em SQL Server


Suponha que você queira executar o exemplo anterior do Transact-SQL no Microsoft SQL Server.


- Para simplificar, você deseja substituir completamente o uso do contêiner de armazenamento do Azure com um arquivo simple como **C:\myeventdata.xel**. O arquivo deve ser gravado na unidade de disco rígido local do computador que hospeda o SQL Server.


- Você não precisará qualquer tipo de instruções Transact-SQL para **criar chave mestre** e **Criar CREDENCIAIS**.


- Na instrução **Criar evento sessão** , na sua cláusula **Adicionar destino** , você poderia substituir o valor de Http atribuído feitas **filename =** com uma cadeia de caracteres do caminho completo como **C:\myfile.xel**.
 - Nenhuma conta de armazenamento do Azure precisa ser envolvida.


## <a name="more-information"></a>Mais informações


Para obter mais informações sobre contas e contêineres no serviço de armazenamento do Azure, consulte:

- [Como usar o armazenamento de Blob do .NET](../storage/storage-dotnet-how-to-use-blobs.md)
- [Nomear e referenciando contêineres, Blobs e metadados](http://msdn.microsoft.com/library/azure/dd135715.aspx)
- [Trabalhando com o contêiner raiz](http://msdn.microsoft.com/library/azure/ee395424.aspx)
- [Lição 1: Criar uma política de acesso armazenadas e uma assinatura de acesso compartilhado em um contêiner Azure](http://msdn.microsoft.com/library/dn466430.aspx)
    - [Lição 2: Criar uma credencial de SQL Server usando uma assinatura de acesso compartilhado](http://msdn.microsoft.com/library/dn466435.aspx)




<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

