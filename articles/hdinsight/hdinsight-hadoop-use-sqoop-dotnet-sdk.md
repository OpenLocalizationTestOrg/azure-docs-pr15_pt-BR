<properties
    pageTitle="Usar Hadoop Sqoop em HDInsight | Microsoft Azure"
    description="Saiba como usar o SDK do .NET HDInsight para executar Sqoop importar e exportar entre um cluster de Hadoop e um banco de dados do SQL Azure."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
   ms.date="09/14/2016"
    ms.author="jgao"/>

#<a name="run-sqoop-jobs-using-net-sdk-for-hadoop-in-hdinsight"></a>Executar trabalhos de Sqoop usando o SDK do .NET para Hadoop em HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar o SDK do .NET HDInsight para executar Sqoop trabalhos em HDInsight para importar e exportar entre HDInsight cluster e o banco de dados do SQL Azure ou o banco de dados do SQL Server.

> [AZURE.NOTE] As etapas neste artigo podem ser usadas com qualquer um baseado no Windows ou baseado em Linux HDInsight cluster; No entanto, estas etapas somente funcionarão de um cliente do Windows. Use o seletor de tabulação na parte superior deste artigo para escolher outros métodos.

###<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Hadoop um cluster em HDInsight**. Consulte [criar cluster e banco de dados SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="run-sqoop-using-net-sdk"></a>Executar Sqoop usando o SDK do .NET

O SDK do .NET HDInsight fornece bibliotecas de cliente .NET, que torna mais fácil trabalhar com clusters de HDInsight do .NET. Nesta seção, você criará um aplicativo de console c# para exportar o hivesampletable para a tabela de banco de dados SQL que você criou anteriormente neste tutoriais.

**Para enviar um trabalho de Sqoop**

1. Crie um aplicativo de console c# no Visual Studio.
2. No Console do Gerenciador de pacote do Visual Studio, execute o seguinte comando Nuget para importar o pacote.

        Install-Package Microsoft.Azure.Management.HDInsight.Job
        
3. Use o seguinte código no arquivo Program.cs:

        using System.Collections.Generic;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
        
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
        
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
        
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
        
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
        
                    SubmitSqoopJob();
        
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
        
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
        
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
        
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
        
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
        
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }
        
4. Pressione **F5** para executar o programa. 

##<a name="limitations"></a>Limitações

* Exportação em massa - baseados em Linux com HDInsight, o conector de Sqoop usado para exportar dados para o Microsoft SQL Server ou banco de dados do Azure SQL atualmente não oferece suporte para inserções em massa.

* Em lotes - com HDInsight baseados em Linux, ao usar o `-batch` mudar ao executar inserções, Sqoop executará várias inserções em vez de processamento em lotes as operações de inserção.

##<a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como usar Sqoop. Para saber mais, consulte:

- [Usar Oozie com HDInsight](hdinsight-use-oozie.md): usar Sqoop ação em um fluxo de trabalho Oozie.
- [Analisar dados de atrasos de voos usando HDInsight](hdinsight-analyze-flight-delay-data.md): usar seção analisar voos atrasar dados e depois use Sqoop para exportar dados para um banco de dados do SQL Azure.
- [Carregar dados ao HDInsight](hdinsight-upload-data.md): encontrar outros métodos para carregar dados para armazenamento de Blob HDInsight/Azure.


