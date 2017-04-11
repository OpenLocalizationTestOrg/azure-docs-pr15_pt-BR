<properties
    pageTitle="Usar coordenador baseada no tempo de Hadoop Oozie em HDInsight | Microsoft Azure"
    description="Use baseada em tempo Hadoop Oozie coordenador do HDInsight, um serviço de dados grande. Aprenda a definir coordenadores e fluxos de trabalho de Oozie e submeter trabalhos."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jgao"/>


# <a name="use-time-based-oozie-coordinator-with-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>Usar coordenador baseada no tempo de Oozie com Hadoop em HDInsight para definir fluxos de trabalho e coordenar trabalhos

Neste artigo, você aprenderá como definir fluxos de trabalho e coordenadores e como disparar os trabalhos de coordenador, com base no tempo. É útil dar uma olhada em [Usar Oozie com HDInsight] [ hdinsight-use-oozie] antes de ler este artigo. Além de Oozie, você também pode agendar trabalhos usando fábrica de dados do Azure. Para saber fábrica de dados do Azure, consulte [usar porco e seção com dados de fábrica](../data-factory/data-factory-data-transformation-activities.md).

> [AZURE.NOTE] Este artigo requer um cluster baseado no Windows HDInsight. Para obter informações sobre como usar o Oozie, incluindo trabalhos baseados em tempo, em um cluster baseado no Linux, consulte [Usar Oozie com Hadoop a definir e executar um fluxo de trabalho em HDInsight baseado em Linux](hdinsight-use-oozie-linux-mac.md)

##<a name="what-is-oozie"></a>O que é Oozie

Apache Oozie é um sistema de fluxo de trabalho/coordenação que gerencia trabalhos de Hadoop. Ele é integrado com a pilha Hadoop e suporta Hadoop trabalhos para Apache MapReduce, Apache porco, Apache seção e Apache Sqoop. Ele também pode ser usado para agendar trabalhos que são específicos para um sistema, como programas Java ou scripts do shell.

A imagem a seguir mostra o fluxo de trabalho que você irá implementar:

![Diagrama de fluxo de trabalho][img-workflow-diagram]

O fluxo de trabalho contém duas ações:

1. Uma ação de seção executa um script de HiveQL para contar as ocorrências de cada tipo de nível de log em um arquivo de log log4j. Cada log log4j consiste em uma linha de campos que contém um campo [nível de LOG] para mostrar o tipo e a gravidade, por exemplo:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    A saída de script de seção é semelhante a:

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Para obter mais informações sobre ramificação, consulte [Usar seção com HDInsight][hdinsight-use-hive].

2.  Uma ação Sqoop exporta a saída da ação HiveQL para uma tabela em um banco de dados do SQL Azure. Para saber mais sobre Sqoop, consulte [Usar Sqoop com HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE] Para as versões Oozie suportadas em clusters de HDInsight, consulte [Novidades nas versões cluster fornecidas pelo HDInsight?] [hdinsight-versions].


##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma estação de trabalho com o PowerShell do Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **HDInsight um cluster**. Para obter informações sobre a criação de um cluster de HDInsight, consulte [criar HDInsight clusters][hdinsight-provision], ou [Introdução ao HDInsight][hdinsight-get-started]. Você precisará os seguintes dados de percorrer o tutorial:

    <table border = "1">
    <tr><th>Propriedade de cluster</th><th>Nome da variável do Windows PowerShell</th><th>Valor</th><th>Descrição</th></tr>
    <tr><td>Nome de cluster HDInsight</td><td>$clusterName</td><td></td><td>O cluster HDInsight no qual você executará este tutorial.</td></tr>
    <tr><td>Nome de usuário do HDInsight cluster</td><td>$clusterUsername</td><td></td><td>O nome de usuário de cluster HDInsight. </td></tr>
    <tr><td>Senha de usuário de cluster HDInsight </td><td>$clusterPassword</td><td></td><td>A senha de usuário de cluster HDInsight.</td></tr>
    <tr><td>Nome da conta de armazenamento do Azure</td><td>$storageAccountName</td><td></td><td>Uma conta de armazenamento do Azure disponível ao cluster HDInsight. Para este tutorial, use a conta de armazenamento de padrão que você especificou durante o processo de provisionamento de cluster.</td></tr>
    <tr><td>Nome de contêiner do Azure Blob</td><td>$containerName</td><td></td><td>Para este exemplo, use o contêiner de armazenamento de Blob do Azure que é usado para o sistema de arquivo padrão HDInsight cluster. Por padrão, ela tem o mesmo nome que o cluster HDInsight.</td></tr>
    </table>

- **Banco de dados do SQL Azure**. Você deve configurar uma regra de firewall para o servidor de banco de dados SQL permitir o acesso de sua estação de trabalho. Para obter instruções sobre como criar um SQL Azure banco de dados e configurar o firewall, consulte [começar a usar o banco de dados do SQL Azure][sqldatabase-get-started]. Este artigo fornece um script do Windows PowerShell para criar a tabela de banco de dados do SQL Azure que você precisa para este tutorial.

    <table border = "1">
    <tr><th>Propriedade de banco de dados SQL</th><th>Nome da variável do Windows PowerShell</th><th>Valor</th><th>Descrição</th></tr>
    <tr><td>Nome do servidor de banco de dados SQL</td><td>$sqlDatabaseServer</td><td></td><td>O servidor de banco de dados SQL ao qual Sqoop os dados serão exportados. </td></tr>
    <tr><td>Nome de login do banco de dados SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Nome de login do banco de dados SQL.</td></tr>
    <tr><td>Senha de logon do banco de dados SQL</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Senha de logon do banco de dados SQL.</td></tr>
    <tr><td>Nome do banco de dados SQL</td><td>$sqlDatabaseName</td><td></td><td>O banco de dados SQL Azure ao qual Sqoop os dados serão exportados. </td></tr>
    </table>

    > [AZURE.NOTE] Por padrão um banco de dados do SQL Azure permite conexões de serviços do Azure, como Azurehdinsight. Se esta configuração de firewall estiver desabilitada, você deve habilitá-lo a partir do Portal do Azure. Para obter instruções sobre como criar um banco de dados do SQL e configurando regras de firewall, consulte [criar e configurar o banco de dados do SQL][sqldatabase-get-started].


> [AZURE.NOTE] Preencher os valores nas tabelas. Será útil para passar por esse tutorial.


##<a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Definir fluxo de trabalho Oozie e o script HiveQL relacionado

Definições de fluxos de trabalho de Oozie são escritas em hPDL (uma processo definição linguagem XML). O nome de arquivo de fluxo de trabalho padrão é *. XML*.  Você vai salvar o arquivo de fluxo de trabalho localmente e implantá-lo ao cluster HDInsight usando o PowerShell do Azure posteriormente neste tutorial.

A ação de seção no fluxo de trabalho chama um arquivo de script HiveQL. Esse arquivo de script contém três instruções de HiveQL:

1. **Instrução DROP TABLE do** exclui a tabela de seção de log4j se existir.
2. **Instrução o CREATE TABLE** cria uma log4j seção tabela externa, que aponta para o local do arquivo de log log4j;
3.  **O local do arquivo de log log4j**. O delimitador de campo é ",". O delimitador de linha padrão é "\n". Uma tabela externa seção é usada para evitar o arquivo de dados que está sendo removido do local original, caso você queira executar o fluxo de trabalho Oozie várias vezes.
3. **Declaração de inserir a substituir** conta as ocorrências de cada tipo de nível de log da tabela de seção log4j e salva a saída para um local de armazenamento de Blob do Azure.

**Observação**: há um problema conhecido de caminho de seção. Você executará esse problema ao enviar um trabalho de Oozie. As instruções para corrigir o problema podem ser encontradas na Wiki do TechNet: [HDInsight seção erro: não é possível renomear][technetwiki-hive-error].

**Para definir o arquivo de script HiveQL a ser chamado pelo fluxo de trabalho**

1. Crie um arquivo de texto com o seguinte conteúdo:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Há três variáveis usados no script:

    - ${hiveTableName}
    - ${hiveDataFolder}
    - ${hiveOutputFolder}

    O arquivo de definição de fluxo de trabalho (. XML neste tutorial) passará esses valores para esse script HiveQL em tempo de execução.

2. Salve o arquivo como **C:\Tutorials\UseOozie\useooziewf.hql** usando a codificação ANSI (ASCII). (Use o bloco de notas se seu editor de texto não fornece essa opção.) Este arquivo de script será implantado ao cluster HDInsight posteriormente no tutorial.



**Para definir um fluxo de trabalho**

1. Crie um arquivo de texto com o seguinte conteúdo:

        <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
            <start to = "RunHiveScript"/>

            <action name="RunHiveScript">
                <hive xmlns="uri:oozie:hive-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.job.queue.name</name>
                            <value>${queueName}</value>
                        </property>
                    </configuration>
                    <script>${hiveScript}</script>
                    <param>hiveTableName=${hiveTableName}</param>
                    <param>hiveDataFolder=${hiveDataFolder}</param>
                    <param>hiveOutputFolder=${hiveOutputFolder}</param>
                </hive>
                <ok to="RunSqoopExport"/>
                <error to="fail"/>
            </action>

            <action name="RunSqoopExport">
                <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.compress.map.output</name>
                            <value>true</value>
                        </property>
                    </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveOutputFolder}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\001"</arg>
                </sqoop>
                <ok to="end"/>
                <error to="fail"/>
            </action>

            <kill name="fail">
                <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
            </kill>

           <end name="end"/>
        </workflow-app>

    Há duas ações definidas no fluxo de trabalho. A ação Iniciar a é *RunHiveScript*. Se a ação executa *Okey*, a próxima ação é *RunSqoopExport*.

    O RunHiveScript tem diversas variáveis. Você irá passar os valores quando você enviar o trabalho de Oozie de sua estação de trabalho usando o PowerShell do Azure.

    <table border = "1">
    <tr><th>Variáveis de fluxo de trabalho</th><th>Descrição</th></tr>
    <tr><td>${jobTracker}</td><td>Especifique a URL do controlador Hadoop trabalho. Use <strong>jobtrackerhost:9010</strong> na versão de cluster HDInsight 3.0 e 2.0.</td></tr>
    <tr><td>${nameNode}</td><td>Especifique a URL do nó nome Hadoop. Use o wasbs de sistema de arquivo padrão: / / endereço, por exemplo, <i>wasbs: / /&lt;containerName&gt;@&lt;storageAccountName&gt;. blob.core.windows.net</i>.</td></tr>
    <tr><td>${NomeDaFila}</td><td>Especifica o nome da fila que o trabalho será enviado para. Use <strong>padrão</strong>.</td></tr>
    </table>


    <table border = "1">
    <tr><th>Variável de ação de seção</th><th>Descrição</th></tr>
    <tr><td>${hiveDataFolder}</td><td>O diretório de origem para o comando da seção Criar tabela.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>A pasta de saída para a instrução inserir SOBRESCREVER.</td></tr>
    <tr><td>${hiveTableName}</td><td>O nome da tabela de seção que referencia os arquivos de dados de log4j.</td></tr>
    </table>


    <table border = "1">
    <tr><th>Variável de ação Sqoop</th><th>Descrição</th></tr>
    <tr><td>${sqlDatabaseConnectionString}</td><td>Cadeia de caracteres de conexão de banco de dados SQL.</td></tr>
    <tr><td>${sqlDatabaseTableName}</td><td>A tabela de banco de dados do SQL Azure para onde os dados serão exportados.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>A pasta de saída para a política de seção Inserir SOBRESCREVER. Esta é a mesma pasta para a exportação de Sqoop (exportação-dir).</td></tr>
    </table>

    Para obter mais informações sobre o fluxo de trabalho Oozie e usando as ações de fluxo de trabalho, consulte [a documentação do Apache Oozie 4.0] [ apache-oozie-400] (para a versão 3.0 do cluster HDInsight) ou [Apache Oozie 3.3.2 documentação] [ apache-oozie-332] (para a versão 2.1 do cluster HDInsight).

2. Salve o arquivo como **C:\Tutorials\UseOozie\workflow.xml** usando a codificação ANSI (ASCII). (Use o bloco de notas se seu editor de texto não fornece essa opção.)

**Para definir coordenador**

1. Crie um arquivo de texto com o seguinte conteúdo:

        <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
           <action>
              <workflow>
                 <app-path>${wfPath}</app-path>
              </workflow>
           </action>
        </coordinator-app>

    Há cinco variáveis usadas no arquivo de definição:

  	| Variável          | Descrição |
  	| ------------------|------------ |
  	| ${coordFrequency} | Tempo de pausa de trabalho. Frequência é sempre expresso em minutos. |
  	| ${coordStart}     | Hora de início do trabalho. |
  	| ${coordEnd}       | Hora de término do trabalho. |
  	| ${coordTimezone}  | Oozie processa trabalhos de coordenador em um fuso horário fixo sem horário de verão (normalmente representado usando UTC). Este fuso horário é conhecido como o "fuso de horário Oozie processamento". |
  	| ${wfPath}         | O caminho para o. XML.  Se o nome de arquivo de fluxo de trabalho não é o nome de arquivo padrão (. xml), você deverá especificá-lo. |

2. Salve o arquivo como **C:\Tutorials\UseOozie\coordinator.xml** usando a codificação ANSI (ASCII). (Use o bloco de notas se seu editor de texto não fornece essa opção.)

##<a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>Implantar o projeto Oozie e preparar o tutorial

Você executará um script do PowerShell do Azure para executar o seguinte:

- Copie o script HiveQL (useoozie.hql) para armazenamento de Blob do Azure, wasbs:///tutorials/useoozie/useoozie.hql.
- Copie. XML para wasbs:///tutorials/useoozie/workflow.xml.
- Copie coordinator.xml para wasbs:///tutorials/useoozie/coordinator.xml.
- Copie o arquivo de dados (/ example/data/sample.log) para wasbs:///tutorials/useoozie/data/sample.log.
- Crie uma tabela de banco de dados do SQL Azure para armazenar dados de exportação de Sqoop. O nome da tabela é *log4jLogCount*.

**Noções básicas sobre o armazenamento de HDInsight**

HDInsight usa o armazenamento de Blob do Azure para armazenamento de dados. wasbs: / / é a implementação da Microsoft do sistema de arquivos de distribuído de Hadoop (HDFS) no armazenamento de Blob do Azure. Para obter mais informações, consulte [usar Azure Blob storage com HDInsight][hdinsight-storage].

Quando você provisionar um cluster de HDInsight, uma conta de armazenamento de Blob do Azure e um contêiner específico dessa conta é designado como o sistema de arquivo padrão, como em HDFS. Além dessa conta de armazenamento, você pode adicionar contas de armazenamento adicional da mesma assinatura do Azure ou do diferentes assinaturas do Azure durante o processo de provisionamento. Para obter instruções sobre como adicionar contas de armazenamento adicional, consulte [clusters provisionar HDInsight][hdinsight-provision]. Para simplificar o script do PowerShell do Azure usado neste tutorial, todos os arquivos são armazenados no contêiner do sistema de arquivo padrão localizado em */tutorials/useoozie*. Por padrão, este contêiner tem o mesmo nome que o nome do cluster HDInsight.
A sintaxe é:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE] Somente o *wasb: / /* sintaxe é suportada HDInsight cluster versão 3.0. O antigo *asv: / /* sintaxe é suportada no HDInsight 2.1 e 1,6 clusters, mas não é suportado em clusters de HDInsight 3.0.

> [AZURE.NOTE] O wasb: / / caminho é um caminho virtual. Para obter mais informações, consulte [usar Azure Blob storage com HDInsight][hdinsight-storage].

Um arquivo que está armazenado no contêiner do sistema de arquivo padrão pode ser acessado do HDInsight usando qualquer um dos seguintes URIs (eu estou usando. XML como um exemplo):

    wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasbs:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Se quiser acessar o arquivo diretamente da conta de armazenamento, o nome de blob para o arquivo é:

    tutorials/useoozie/workflow.xml

**Noções básicas sobre tabelas de seção internas e externas**

Há algumas coisas que você precisa saber sobre tabelas de seção internas e externas:

- O comando CREATE TABLE cria uma tabela interna, também conhecido como uma tabela gerenciada. O arquivo de dados deve estar localizado no contêiner padrão.
- O comando Criar tabela move o arquivo de dados para o /hive/depósito/<TableName> pasta no contêiner padrão.
- O comando Criar tabela externa cria uma tabela externa. O arquivo de dados pode estar localizado fora o recipiente padrão.
- O comando Criar tabela externa não mover o arquivo de dados.
- O comando Criar tabela externa não permite que todas as subpastas sob a pasta especificada na cláusula local. Este é o motivo por que o tutorial faz uma cópia do arquivo sample.log.

Para obter mais informações, consulte [HDInsight: seção internos e externos introdução de tabelas][cindygross-hive-tables].

**Para preparar o tutorial**

1. Abra o Windows PowerShell ISE (na tela inicial do Windows 8, digite **PowerShell_ISE**e clique em **Windows PowerShell ISE**. Para obter mais informações, consulte [Iniciar o Windows PowerShell no Windows 8 e Windows][powershell-start]).
2. No painel inferior, execute o seguinte comando para se conectar à sua assinatura do Azure:

        Add-AzureAccount

    Você será solicitado a inserir suas credenciais de conta do Microsoft Azure. Este método de adicionar uma conexão de assinatura expira, e depois de 12 horas, você precisará executar o cmdlet novamente.

    > [AZURE.NOTE] Se você tiver várias assinaturas Azure e a assinatura padrão não seja aquele que você deseja usar, use o cmdlet <strong>Select-AzureSubscription</strong> para selecionar uma assinatura.

3. Copie o seguinte script no painel script e, em seguida, defina as variáveis de seis primeiros:

        # WASB variables
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<BlobStorageContainerName>"

        # SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"  
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  
        $sqlDatabaseTableName = "log4jLogsCount"

        # Oozie files for the tutorial
        $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
        $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
        $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

        # WASB folder for storing the Oozie tutorial files.
        $destFolder = "tutorials/useoozie"  # Do NOT use the long path here


    Para obter mais descrições das variáveis, consulte a seção [pré-requisitos](#prerequisites) deste tutorial.

3. Acrescente o seguinte para o script no painel script:

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        function uploadOozieFiles()
        {
            Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
            Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
            Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
            Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
        }

        function prepareHiveDataFile()
        {
            Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
            Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
        }

        function prepareSQLDatabase()
        {
            # SQL query string for creating log4jLogsCount table
            $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [Level] [nvarchar](10) NOT NULL,
                    [Total] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
                (
                [Level] ASC
                )
                )"

            #Create the log4jLogsCount table
            Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
            $conn = New-Object System.Data.SqlClient.SqlConnection
            $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
            $conn.open()
            $cmd = New-Object System.Data.SqlClient.SqlCommand
            $cmd.connection = $conn
            $cmd.commandtext = $cmdCreateLog4jCountTable
            $cmd.executenonquery()

            $conn.close()
        }

        # upload workflow.xml, coordinator.xml, and ooziewf.hql
        uploadOozieFiles;

        # make a copy of example/data/sample.log to example/data/log4j/sample.log
        prepareHiveDataFile;

        # create log4jlogsCount table on SQL database
        prepareSQLDatabase;

4. Clique em **Executar Script** ou pressione **F5** para executar o script. O resultado será semelhante a:

    ![Saída de preparação tutorial][img-preparation-output]

##<a name="run-the-oozie-project"></a>Executar o projeto Oozie

PowerShell Azure atualmente não oferece qualquer cmdlets para definir os trabalhos de Oozie. Você pode usar o cmdlet **Chamar RestMethod** para chamar serviços da web de Oozie. A API de serviços web Oozie é uma API de JSON do resto de HTTP. Para obter mais informações sobre os serviços web do Oozie API, consulte [a documentação do Apache Oozie 4.0] [ apache-oozie-400] (para a versão 3.0 do cluster HDInsight) ou [Apache Oozie 3.3.2 documentação] [ apache-oozie-332] (para a versão 2.1 do cluster HDInsight).

**Para enviar um trabalho de Oozie**

1. Abra o Windows PowerShell ISE (na tela inicial do Windows 8, digite **PowerShell_ISE**e clique em **Windows PowerShell ISE**. Para obter mais informações, consulte [Iniciar o Windows PowerShell no Windows 8 e Windows][powershell-start]).

3. Copie o seguinte script no painel script e, em seguida, defina as variáveis de 14 primeiro (no entanto, pule **$storageUri**).

        #HDInsight cluster variables
        $clusterName = "<HDInsightClusterName>"
        $clusterUsername = "<HDInsightClusterUsername>"
        $clusterPassword = "<HDInsightClusterUserPassword>"

        #Azure Blob storage (WASB) variables
        $storageAccountName = "<StorageAccountName>"
        $storageContainerName = "<BlobContainerName>"
        $storageUri="wasbs://$storageContainerName@$storageAccountName.blob.core.windows.net"

        #Azure SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  

        #Oozie WF/coordinator variables
        $coordStart = "2014-03-21T13:45Z"
        $coordEnd = "2014-03-21T13:45Z"
        $coordFrequency = "1440"    # in minutes, 24h x 60m = 1440m
        $coordTimezone = "UTC"  #UTC/GMT

        $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
        $waitTimeBetweenOozieJobStatusCheck=10

        #Hive action variables
        $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
        $hiveTableName = "log4jlogs"
        $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
        $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

        #Sqoop action variables
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
        $sqlDatabaseTableName = "log4jLogsCount"

        $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    Para obter mais descrições das variáveis, consulte a seção [pré-requisitos](#prerequisites) deste tutorial.

    $coordstart e $coordend são o fluxo de trabalho inicial e a hora de término. Para descobrir o horário UTC/GMT, pesquise "hora utc" no bing.com. O $coordFrequency é frequência em minutos que você deseja executar o fluxo de trabalho.

3. Acrescente o seguinte para o script. Essa parte define a carga de Oozie:

        #OoziePayload used for Oozie web service submission
        $OoziePayload =  @"
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>

           <property>
               <name>nameNode</name>
               <value>$storageUrI</value>
           </property>

           <property>
               <name>jobTracker</name>
               <value>jobtrackerhost:9010</value>
           </property>

           <property>
               <name>queueName</name>
               <value>default</value>
           </property>

           <property>
               <name>oozie.use.system.libpath</name>
               <value>true</value>
           </property>

           <property>
               <name>oozie.coord.application.path</name>
               <value>$oozieWFPath</value>
           </property>

           <property>
               <name>wfPath</name>
               <value>$oozieWFPath</value>
           </property>

           <property>
               <name>coordStart</name>
               <value>$coordStart</value>
           </property>

           <property>
               <name>coordEnd</name>
               <value>$coordEnd</value>
           </property>

           <property>
               <name>coordFrequency</name>
               <value>$coordFrequency</value>
           </property>

           <property>
               <name>coordTimezone</name>
               <value>$coordTimezone</value>
           </property>

           <property>
               <name>hiveScript</name>
               <value>$hiveScript</value>
           </property>

           <property>
               <name>hiveTableName</name>
               <value>$hiveTableName</value>
           </property>

           <property>
               <name>hiveDataFolder</name>
               <value>$hiveDataFolder</value>
           </property>

           <property>
               <name>hiveOutputFolder</name>
               <value>$hiveOutputFolder</value>
           </property>

           <property>
               <name>sqlDatabaseConnectionString</name>
               <value>&quot;$sqlDatabaseConnectionString&quot;</value>
           </property>

           <property>
               <name>sqlDatabaseTableName</name>
               <value>$SQLDatabaseTableName</value>
           </property>

           <property>
               <name>user.name</name>
               <value>admin</value>
           </property>

        </configuration>
        "@

    >[AZURE.NOTE] A principal diferença em comparação com o arquivo de carga de envio do fluxo de trabalho é a variável **oozie.coord.application.path**. Quando você envia um fluxo de trabalho, você usar **oozie.wf.application.path** .

4. Acrescente o seguinte para o script. Essa parte verifica o status do serviço web Oozie:

        function checkOozieServerStatus()
        {
            Write-Host "Checking Oozie server status..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $oozieServerSatus = $jsonResponse[0].("systemMode")
            Write-Host "Oozie server status is $oozieServerSatus..."

            if($oozieServerSatus -notmatch "NORMAL")
            {
                Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
                exit 1
            }
        }

5. Acrescente o seguinte para o script. Essa parte cria um trabalho de Oozie:

        function createOozieJob()
        {
            # create Oozie job
            Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
            Write-Host "`n--------`n$OoziePayload`n--------"
            $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
            $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $oozieJobId = $jsonResponse[0].("id")
            Write-Host "Oozie job id is $oozieJobId..."

            return $oozieJobId
        }

    > [AZURE.NOTE] Quando você envia um fluxo de trabalho, você deve fazer com que o outro serviço web chamar para iniciar o trabalho depois que o trabalho é criado. Nesse caso, o trabalho de coordenador é disparado por vez. O trabalho será iniciado automaticamente.

6. Acrescente o seguinte para o script. Essa parte verifica o status do trabalho Oozie:

        function checkOozieJobStatus($oozieJobId)
        {
            # get job status
            Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

            Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
            $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")

            while($JobStatus -notmatch "SUCCEEDED|KILLED")
            {
                Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
                Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
                $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
                $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
                $JobStatus = $jsonResponse[0].("status")
            }

            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
            if($JobStatus -notmatch "SUCCEEDED")
            {
                Write-Host "Check logs at http://headnode0:9014/cluster for detais."
                exit -1
            }
        }

7. (Opcional) Acrescente o seguinte para o script.

        function listOozieJobs()
        {
            Write-Host "Listing Oozie jobs..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds

            write-host "Job ID                                   App Name        Status      Started                         Ended"
            write-host "----------------------------------------------------------------------------------------------------------------------------------"
            foreach($job in $response.workflows)
            {
                Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
            }
        }

        function ShowOozieJobLog($oozieJobId)
        {
            Write-Host "Showing Oozie job info..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds
            write-host $response
        }

        function killOozieJob($oozieJobId)
        {
            Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
            $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
            $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
        }

7. Acrescente o seguinte para o script:

        checkOozieServerStatus
        # listOozieJobs
        $oozieJobId = createOozieJob($oozieJobId)
        checkOozieJobStatus($oozieJobId)
        # ShowOozieJobLog($oozieJobId)
        # killOozieJob($oozieJobId)

    Remova os sinais # se você deseja executar as funções adicionais.

7. Se seu cluster HDinsight for versão 2.1, substitua "https://$clusterName.azurehdinsight.net:443/oozie/v2/" com "https://$clusterName.azurehdinsight.net:443/oozie/v1/". Versão de cluster HDInsight 2.1 faz não suporta a versão 2 dos serviços da web.

7. Clique em **Executar Script** ou pressione **F5** para executar o script. O resultado será semelhante a:

    ![Tutorial executar saída de fluxo de trabalho][img-runworkflow-output]

8. Conecte seu banco de dados SQL para ver os dados exportados.

**Verifique o log de erros de trabalho**

Para solucionar um fluxo de trabalho, o arquivo de log Oozie pode ser encontrado no C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log de headnode o cluster. Para obter informações sobre RDP, consulte [Administrando HDInsight clusters usando o portal de Azure][hdinsight-admin-portal].

**Para executar novamente o tutorial**

Para executar novamente o fluxo de trabalho, você deve executar as seguintes tarefas:

- Exclua o arquivo de saída de script da seção.
- Exclua os dados na tabela log4jLogsCount.

Aqui está um script do Windows PowerShell de exemplo que você pode usar:

    $storageAccountName = "<AzureStorageAccountName>"
    $containerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()


##<a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como definir um fluxo de trabalho Oozie e um coordenador Oozie e como executar um trabalho de coordenador Oozie usando o PowerShell do Azure. Para saber mais, consulte os seguintes artigos:

- [Introdução ao HDInsight][hdinsight-get-started]
- [Usar o armazenamento de Blob do Azure com HDInsight][hdinsight-storage]
- [Administrar HDInsight usando o PowerShell do Azure][hdinsight-admin-powershell]
- [Carregar dados ao HDInsight][hdinsight-upload-data]
- [Usar Sqoop com HDInsight][hdinsight-use-sqoop]
- [Use a seção com HDInsight][hdinsight-use-hive]
- [Usar porco com HDInsight][hdinsight-use-pig]
- [Desenvolver programas Java MapReduce para HDInsight][hdinsight-develop-java-mapreduce]



[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563


[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-develop-java-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png  

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
