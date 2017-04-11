<properties
    pageTitle="Executar um trabalho de Hadoop usando DocumentDB e HDInsight | Microsoft Azure"
    description="Saiba como executar um trabalho de seção, porco e MapReduce simple com DocumentDB e Azurehdinsight."
    services="documentdb"
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"
    documentationCenter=""/>


<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="denlee"/>

#<a name="DocumentDB-HDInsight"></a>Executar um trabalho de Hadoop usando DocumentDB e HDInsight

Este tutorial mostra como executar [Apache seção][apache-hive], [Apache porco][apache-pig]e [Apache Hadoop] [ apache-hadoop] trabalhos de MapReduce em Azurehdinsight com conector de Hadoop do DocumentDB. Conector do Hadoop do DocumentDB permite DocumentDB agir como uma fonte e o receptor para trabalhos de seção, porco e MapReduce. Este tutorial usará DocumentDB como a fonte de dados e o destino para trabalhos de Hadoop.

Depois de concluir este tutorial, você poderá responder às seguintes perguntas:

- Como faço para carregar dados de DocumentDB usando um trabalho de seção, porco ou MapReduce?
- Como armazena dados em DocumentDB usando um trabalho de seção, porco ou MapReduce?

Recomendamos que você está começando a assistindo vídeo a seguir, onde podemos executar por meio de um trabalho de seção usando DocumentDB e HDInsight.

> [AZURE.VIDEO use-azure-documentdb-hadoop-connector-with-azure-hdinsight]

Em seguida, retorne neste artigo, onde você receberá os detalhes completos sobre como você pode executar trabalhos de análise em seus dados de DocumentDB.

> [AZURE.TIP] Este tutorial supõe que você tenha experiência anterior usando Apache Hadoop, seção e/ou porco. Se você estiver começando a usar o Apache Hadoop, seção e porco, recomendamos visitar a [documentação de Apache Hadoop][apache-hadoop-doc]. Este tutorial também pressupõe que você tenha experiência anterior DocumentDB e tiver uma conta de DocumentDB. Se você estiver começando a DocumentDB ou você não tem uma conta de DocumentDB, confira nossa [Introdução] [ getting-started] página.

Não tem tempo para concluir o tutorial e quer apenas obter dos scripts do PowerShell de exemplo completo para seção, porco e MapReduce? Não é um problema, obtê-los [aqui][documentdb-hdinsight-samples]. O download também contém os arquivos hql, porco e java para esses exemplos.

## <a name="NewestVersion"></a>Versão mais recente

<table border='1'>
    <tr><th>Versão do Hadoop conector</th>
        <td>1.2.0</td></tr>
    <tr><th>Uri de script</th>
        <td>https://portalcontent.blob.Core.Windows.NET/scriptaction/documentdb-Hadoop-Installer-v04.ps1</td></tr>
    <tr><th>Data de modificação</th>
        <td>26/04/2016</td></tr>
    <tr><th>Versões de HDInsight com suporte</th>
        <td>3.1, 3,2</td></tr>
    <tr><th>Log de alterações</th>
        <td>Atualizado DocumentDB Java SDK para 1.6.0</br>
            Adicionado suporte para conjuntos de particionada como uma fonte e o receptor</br>
        </td></tr>
</table>

## <a name="Prerequisites"></a>Pré-requisitos
Antes de seguir as instruções neste tutorial, certifique-se de que você tenha o seguinte:

- Uma conta de DocumentDB, um banco de dados e um conjunto de documentos dentro. Para obter mais informações, consulte [Introdução ao DocumentDB][getting-started]. Importar dados de exemplo para sua conta de DocumentDB com a [ferramenta de importação de DocumentDB][documentdb-import-data].
- Taxa de transferência. Leituras e gravações de HDInsight serão contadas na direção de suas unidades de solicitação alocado para seus conjuntos. Para obter mais informações, consulte [provisionado produtividade, unidades de solicitação e operações de banco de dados][documentdb-manage-throughput].
- Capacidade de um procedimento armazenado adicional em cada conjunto de saída. Os procedimentos armazenados são usados para transferir documentos resultantes. Para obter mais informações, consulte [coleções e produtividade provisionada][documentdb-manage-document-storage].
- Capacidade para os documentos resultantes dos trabalhos de seção, porco ou MapReduce. Para obter mais informações, consulte [desempenho e capacidade de gerenciar DocumentDB][documentdb-manage-collections].
- [*Opcional*] Capacidade de um conjunto de adicional. Para obter mais informações, consulte [provisionado armazenamento de documentos e índice sobrecarga][documentdb-manage-document-storage].

> [AZURE.WARNING] Para evitar a criação de um novo conjunto durante qualquer um dos trabalhos, você pode imprimir os resultados para stdout, salvar a saída ao seu contêiner WASB ou especificar um conjunto já existente. No caso de especificar um conjunto existente, novos documentos serão criados dentro do conjunto e documentos já existentes só serão afetados se há um conflito em *ids*. **O conector substituirá automaticamente os documentos existentes com conflitos de id**. Você pode desativar esse recurso, definindo a opção upsert para false. Se upsert for falso, e um conflito ocorre, o trabalho Hadoop falhará; relatar um erro de conflito de id.


## <a name="ProvisionHDInsight"></a>Etapa 1: Criar um novo cluster de HDInsight
Este tutorial usa a ação de Script a partir do Portal do Azure para personalizar seu cluster HDInsight. Neste tutorial, usaremos o Portal do Azure para criar seu cluster HDInsight. Para obter instruções sobre como usar cmdlets do PowerShell ou o SDK do .NET HDInsight, confira os [clusters personalizar HDInsight usando a ação de Script] [ hdinsight-custom-provision] artigo.

1. Entre [Portal do Azure][azure-portal].

2. Clique em **+ nova** na parte superior do painel de navegação esquerdo, procure **HDInsight** na barra de pesquisa superior na nova lâmina.

3. **HDInsight** publicadas pela **Microsoft** aparecerá na parte superior dos resultados. Clique nela e, em seguida, clique em **criar**.

4. No novo HDInsight Cluster criar blade, insira seu **Nome de Cluster** e selecione a **assinatura** que você deseja configurar esse recurso em.

    <table border='1'>
        <tr><td>Nome do cluster</td><td>Nome do cluster.<br/>
   Nome de DNS deve começar e terminar com um caractere alfanumérico e pode conter traços.<br/>
   O campo deve ser uma cadeia de caracteres entre 3 e 63 caracteres.</td></tr>
        <tr><td>Nome da assinatura</td>
            <td>Se você tiver mais de uma assinatura do Azure, selecione a assinatura que irá hospedar seu cluster HDInsight. </td></tr>
    </table>

5. Clique em **Selecionar tipo de Cluster** e defina as seguintes propriedades para os valores especificados.

    <table border='1'>
        <tr><td>Tipo de cluster</td><td><strong>Hadoop</strong></td></tr>
        <tr><td>Nível de cluster</td><td><strong>Padrão</strong></td></tr>
        <tr><td>Sistema Operacional</td><td><strong>Windows</strong></td></tr>
        <tr><td>Versão</td><td>versão mais recente</td></tr>
    </table>

    Agora, clique em **Selecionar**.

    ![Forneça detalhes de cluster inicial de Hadoop HDInsight][image-customprovision-page1]

6. Clique em **credenciais** para configurar seu login e credenciais de acesso remoto. Escolha **o nome de usuário do Cluster Login** e **senha de logon do Cluster**.

    Se você quiser remoto em seu cluster, selecione *Sim* na parte inferior da lâmina e forneça um nome de usuário e senha.

7. Clique na **Fonte de dados** para definir seu local principal para acesso a dados. Escolha o **Método de seleção** e especifique uma conta de armazenamento já existente ou crie um novo.

8. Na mesma lâmina, especifique um **Contêiner padrão** e um **local**. E, clique em **Selecionar**.

    > [AZURE.NOTE] Selecione um local Fechar para sua região de conta DocumentDB para obter melhor desempenho

8. Clique em **preços** para selecionar o número e tipo de nós. Você pode manter a configuração padrão e dimensionar o número de nós de trabalhador posteriormente.

9. Clique em **configuração opcional**, **ações de Script** na lâmina configuração opcional.

    Em ações de Script, insira as seguintes informações para personalizar seu cluster HDInsight.

    <table border='1'>
        <tr><th>Propriedade</th><th>Valor</th></tr>
        <tr><td>Nome</td>
            <td>Especifique um nome para a ação de script.</td></tr>
        <tr><td>Script URI</td>
            <td>Especifique o URI para o script que é invocado para personalizar o cluster.</br></br>
   Digite: </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</strong>.</td></tr>
        <tr><td>Cabeça</td>
            <td>Clique na caixa de seleção para executar o script do PowerShell para o nó de cabeça.</br></br>
            <strong>Marque esta caixa de seleção</strong>.</td></tr>
        <tr><td>Trabalhador</td>
            <td>Clique na caixa de seleção para executar o script do PowerShell para o nó de trabalho.</br></br>
            <strong>Marque esta caixa de seleção</strong>.</td></tr>
        <tr><td>Zookeeper</td>
            <td>Clique na caixa de seleção para executar o script do PowerShell para o Zookeeper.</br></br>
            <strong>Não necessária</strong>.
            </td></tr>
        <tr><td>Parâmetros</td>
            <td>Especifique os parâmetros, se necessário pelo script.</br></br>
            <strong>Sem parâmetros necessários</strong>.</td></tr>
    </table>

10. Crie um novo **Grupo de recursos** ou usar um grupo de recursos existente em sua assinatura do Azure.

11. Agora, verifique o **Pin para dashboard** para controlar sua implantação e clique em **criar**!

## <a name="InstallCmdlets"></a>Etapa 2: Instalar e configurar o PowerShell do Azure

1. Instale o PowerShell Azure. Instruções podem ser encontradas [aqui][powershell-install-configure].

    > [AZURE.NOTE] Como alternativa, apenas para consultas de seção, você pode usar o Editor de seção online do HDInsight. Para fazer isso, entre [Portal do Azure][azure-portal], clique em **HDInsight** no painel à esquerda para exibir uma lista de seus clusters HDInsight. Clique no cluster que você deseja executar consultas de seção no e, em seguida, clique em **Console de consulta**.

2. Abra o ambiente de script integrado do PowerShell Azure:
    - Em um computador executando o Windows 8 ou o Windows Server 2012 ou superior, você pode usar a pesquisa interna. Na tela Iniciar, digite **powershell ise** e clique em **Inserir**.
    - Em um computador executando uma versão anterior ao Windows 8 ou o Windows Server 2012, use o menu Iniciar. No menu Iniciar, digite o **Prompt de comando** na caixa de pesquisa, em seguida, na lista de resultados, clique em **Prompt de comando**. No Prompt de comando, digite **powershell_ise** e clique em **Inserir**.

3. Adicione sua conta do Azure.
    1. No painel do Console, digite **AzureAccount de adicionar** e clique em **Inserir**.
    2. Digite o endereço de email associado a sua assinatura do Azure e clique em **continuar**.
    3. Digite a senha da sua assinatura do Azure.
    4. Clique em **entrar**.

4. O diagrama a seguir identifica as partes importantes do seu ambiente de script do Azure PowerShell.

    ![Diagrama do PowerShell Azure][azure-powershell-diagram]

## <a name="RunHive"></a>Etapa 3: Executar um trabalho de seção usando DocumentDB e HDInsight

> [AZURE.IMPORTANT] Todas as variáveis indicadas pelo < > devem ser preenchidas usando as configurações.

1. Defina as seguintes variáveis no seu painel de Script do PowerShell.

        # Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide the HDInsight cluster name where you want to run the Hive job.
        $clusterName = "<HDInsightClusterName>"

2. <p>Vamos começar construindo a cadeia de caracteres de consulta. Vamos escrever uma consulta de seção que leva carimbos de hora do todos os documentos gerados pelo sistema (_ts) e identificações exclusivas (_rid) de uma coleção de DocumentDB, calcula todos os documentos por minuto e armazena os resultados de volta para um novo conjunto de DocumentDB.</p>

    <p>Primeiro, vamos criar uma tabela de seção da nossa coleção de DocumentDB. Adicione o trecho de código a seguir para o Script do PowerShell painel <strong>após</strong> o trecho de código da #1. Verifique se você incluir o opcional DocumentDB.query parâmetro t ARRUMAR nossos documentos para apenas _ts e _rid.</p>

    > [AZURE.NOTE]**DocumentDB.inputCollections de nomenclatura não era um erro.** Sim, podemos permitir a adição de vários conjuntos de como entrada: </br>

        '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' A1A</br> The collection names are separated without spaces, using only a single comma.


        # Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "drop table DocumentDB_timestamps; "  +
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " +
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  Em seguida, vamos criar uma tabela de seção para a coleção de saída. As propriedades do documento de saída será o mês, dia, hora, minuto e o número total de ocorrências.

    > [AZURE.NOTE]**Novamente, DocumentDB.outputCollections de nomenclatura não era um erro.** Sim, podemos permitir adicionando vários conjuntos como uma saída: </br>
'*DocumentDB.outputCollections*'='*\<nome do conjunto de saída DocumentDB 1\>*,*\<nome do conjunto de saída DocumentDB 2\>*' </br> Os nomes de conjunto são separados sem espaços, usando apenas uma única vírgula. </br></br>
Documentos serão distribuída alternada em vários conjuntos. Um lote de documentos será armazenado em uma coleção e, em seguida, um segundo lote de documentos será armazenado na próxima coleta e assim por diante.

        # Create a Hive table for the output data to DocumentDB.
        $queryStringPart2 = "drop table DocumentDB_analytics; " +
                              "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                              "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " +
                              "tblproperties ( " +
                                  "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                  "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                  "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                  "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "

4. Por fim, vamos contar os documentos por mês, dia, hora e minuto e insira os resultados volta para a saída da tabela de seção.

        # GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "

5. Adicione o seguinte trecho de script para criar uma definição de trabalho da seção da consulta anterior.

        # Create a Hive job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

    Você também pode usar o arquivo switch - especificar um arquivo de script HiveQL em HDFS.

6. Adicione o trecho a seguir para salvar a hora de início e enviar o trabalho de seção.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7. Adicione o seguinte para aguardar o trabalho de seção concluir.

        # Wait for the Hive job to complete.
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8. Adicione o seguinte para imprimir a saída padrão e as horas de início e término.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **Executar o** seu novo script! **Clique** no botão verde executar.

10. Verifique os resultados. Entrar no [Portal do Azure][azure-portal].
    1. No painel do lado esquerdo, clique em <strong>Procurar</strong> . </br>
    2. Clique em <strong>tudo</strong> no canto superior direito do painel de navegação. </br>
    3. Localize e clique em <strong>Contas de DocumentDB</strong>. </br>
    4. Em seguida, localize sua <strong>Conta de DocumentDB</strong>, e o <strong>Banco de dados de DocumentDB</strong> e seu <strong>Conjunto de DocumentDB</strong> associada à coleção de saída especificada na sua consulta de seção.</br>
    5. Por fim, clique em <strong>Document Explorer</strong> sob <strong>Ferramentas de desenvolvedor</strong>.</br></p>

    Você verá os resultados da sua consulta de seção.

    ![Resultados de consulta de seção][image-hive-query-results]

## <a name="RunPig"></a>Etapa 4: Executar um trabalho de porco usando DocumentDB e HDInsight

> [AZURE.IMPORTANT] Todas as variáveis indicadas pelo < > devem ser preenchidas usando as configurações.

1. Defina as seguintes variáveis no seu painel de Script do PowerShell.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"

2. <p>Vamos começar construindo a cadeia de caracteres de consulta. Vamos escrever uma consulta de porco que leva carimbos de hora do todos os documentos gerados pelo sistema (_ts) e identificações exclusivas (_rid) de uma coleção de DocumentDB, calcula todos os documentos por minuto e armazena os resultados de volta para um novo conjunto de DocumentDB.</p>
    <p>Primeiro, carregue documentos de DocumentDB no HDInsight. Adicione o trecho de código a seguir para o Script do PowerShell painel <strong>após</strong> o trecho de código da #1. Certifique-se de adicionar uma consulta de DocumentDB para o parâmetro de consulta DocumentDB opcional para aparar nossos documentos para apenas _ts e _rid.</p>

    > [AZURE.NOTE]Sim, podemos permitir a adição de vários conjuntos de como entrada: </br>
'*\<DocumentDB entrada conjunto nome 1\>*,*\<nome de conjunto de entrada de DocumentDB 2\>*'</br> Os nomes de conjunto são separados sem espaços, usando apenas uma única vírgula. </b>

    Documentos serão distribuída alternada em vários conjuntos. Um lote de documentos será armazenado em uma coleção e, em seguida, um segundo lote de documentos será armazenado na próxima coleta e assim por diante.

        # Load data from DocumentDB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  Em seguida, vamos contar os documentos por mês, dia, hora, minuto e o número total de ocorrências.

        # GROUP BY minute and COUNT entries for each.
        $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                            "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                            "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "

4. Por fim, vamos armazenar os resultados em nosso novo conjunto de saída.

    > [AZURE.NOTE]Sim, podemos permitir adicionando vários conjuntos como uma saída: </br>
'\<Nome do conjunto de saída DocumentDB 1\>,\<nome do conjunto de saída DocumentDB 2\>'</br> Os nomes de conjunto são separados sem espaços, usando apenas uma única vírgula.</br>
Documentos serão distribuída alternada em vários conjuntos. Um lote de documentos será armazenado em uma coleção e, em seguida, um segundo lote de documentos será armazenado na próxima coleta e assim por diante.

        # Store output data to DocumentDB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "

5. Adicione o seguinte trecho de script para criar uma definição de trabalho porco da consulta anterior.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

    Você também pode usar o arquivo switch - especificar um arquivo de script porco em HDFS.

6. Adicione o trecho a seguir para salvar a hora de início e enviar o trabalho de porco.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

7. Adicione o seguinte para aguardar o trabalho porco concluir.

        # Wait for the Pig job to complete.
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8. Adicione o seguinte para imprimir a saída padrão e as horas de início e término.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **Executar o** seu novo script! **Clique** no botão verde executar.

10. Verifique os resultados. Entrar no [Portal do Azure][azure-portal].
    1. No painel do lado esquerdo, clique em <strong>Procurar</strong> . </br>
    2. Clique em <strong>tudo</strong> no canto superior direito do painel de navegação. </br>
    3. Localize e clique em <strong>Contas de DocumentDB</strong>. </br>
    4. Em seguida, localize sua <strong>Conta de DocumentDB</strong>, e o <strong>Banco de dados de DocumentDB</strong> e seu <strong>Conjunto de DocumentDB</strong> associada à coleção de saída especificada na sua consulta porco.</br>
    5. Por fim, clique em <strong>Document Explorer</strong> sob <strong>Ferramentas de desenvolvedor</strong>.</br></p>

    Você verá os resultados da sua consulta porco.

    ![Resultados de consulta de porco][image-pig-query-results]

## <a name="RunMapReduce"></a>Etapa 5: Executar um trabalho de MapReduce usando DocumentDB e HDInsight

1. Defina as seguintes variáveis no seu painel de Script do PowerShell.

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name

2. Podemos vai executar um trabalho MapReduce que calcula o número de ocorrências para cada propriedade de documento no conjunto de DocumentDB. Adicione este script trecho **após** o trecho de código acima.

        # Define the MapReduce job.
        $TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

    > [AZURE.NOTE] TallyProperties-v01.jar vem com a instalação personalizada do conector Hadoop DocumentDB.

3. Adicione o seguinte comando para enviar o trabalho MapReduce.

        # Save the start time and submit the job.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Além da definição de trabalho MapReduce, você também fornecer o nome do cluster HDInsight onde você deseja executar o trabalho MapReduce e as credenciais. Início-AzureHDInsightJob é uma chamada sincronizada. Para verificar a conclusão do trabalho, use o cmdlet *Espera-AzureHDInsightJob* .

4. Adicione o seguinte comando para verificar erros com executando o trabalho MapReduce.

        # Get the job output and print the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

5. **Executar o** seu novo script! **Clique** no botão verde executar.

6. Verifique os resultados. Entrar no [Portal do Azure][azure-portal].
    1. No painel do lado esquerdo, clique em <strong>Procurar</strong> .
    2. Clique em <strong>tudo</strong> no canto superior direito do painel de navegação.
    3. Localize e clique em <strong>Contas de DocumentDB</strong>.
    4. Em seguida, localize sua <strong>Conta de DocumentDB</strong>, e o <strong>Banco de dados de DocumentDB</strong> e seu <strong>Conjunto de DocumentDB</strong> associada ao conjunto de saída especificado em seu trabalho MapReduce.
    5. Por fim, clique em <strong>Document Explorer</strong> sob <strong>Ferramentas de desenvolvedor</strong>.

    Você verá os resultados do seu trabalho MapReduce.

    ![Resultados da consulta MapReduce][image-mapreduce-query-results]

## <a name="NextSteps"></a>Próximas etapas

Parabéns! Você acabou de seus trabalhos de seção, porco e MapReduce primeiro usando DocumentDB do Azure e HDInsight.

Temos Abrir origem nosso conector Hadoop. Se você estiver interessado, você pode contribuir no [GitHub][documentdb-github].

Para saber mais, consulte os seguintes artigos:

- [Desenvolver um aplicativo de Java com Documentdb][documentdb-java-application]
- [Desenvolver programas Java MapReduce para Hadoop em HDInsight][hdinsight-develop-deploy-java-mapreduce]
- [Começar a usar Hadoop com seção no HDInsight para analisar o uso do celular][hdinsight-get-started]
- [Usar MapReduce com HDInsight][hdinsight-use-mapreduce]
- [Use a seção com HDInsight][hdinsight-use-hive]
- [Usar porco com HDInsight][hdinsight-use-pig]
- [Personalizar clusters HDInsight usando a ação de Script][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-portal]: https://portal.azure.com/
[azure-powershell-diagram]: ./media/documentdb-run-hadoop-with-hdinsight/azurepowershell-diagram-med.png

[documentdb-hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[documentdb-github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[documentdb-manage-collections]: documentdb-manage.md#Collections
[documentdb-manage-document-storage]: documentdb-manage.md#IndexOverhead
[documentdb-manage-throughput]: documentdb-manage.md#ProvThroughput
[documentdb-import-data]: documentdb-import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md#powershell
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight/hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight/hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight-use-mapreduce]: ../hdinsight/hdinsight-use-mapreduce.md
[hdinsight-use-pig]: ../hdinsight/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page1.png
[image-hive-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: ../powershell-install-configure.md
