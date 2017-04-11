<properties
    pageTitle="Executar consultas de seção usando o SDK do .NET HDInsight | Microsoft Azure"
    description="Saiba como enviar trabalhos Hadoop a Azure HDInsight Hadoop usando o SDK do .NET HDInsight."
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

# <a name="run-hive-queries-using-hdinsight-net-sdk"></a>Executar consultas de seção usando o SDK do .NET HDInsight

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


Saiba como enviar consultas de seção usando o SDK do .NET HDInsight.

> [AZURE.NOTE] As etapas neste artigo devem ser executadas a partir de um cliente do Windows. Para obter informações sobre como usar um Linux, OS X ou cliente Unix para trabalhar com a seção, use o seletor de tabulação mostrado no topo do artigo.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Hadoop um cluster em HDInsight**. Consulte [Introdução ao uso baseados em Linux Hadoop em HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database).
- **Visual Studio 2012/2013/2015**.

##<a name="submit-hive-queries-using-hdinsight-net-sdk"></a>Enviar consultas de seção usando o SDK do .NET HDInsight

O SDK do .NET HDInsight fornece bibliotecas de cliente .NET, que torna mais fácil trabalhar com clusters de HDInsight do .NET. 

**Para enviar trabalhos**

1. Crie um aplicativo de console c# no Visual Studio.
2. No Console do Nuget Package Manager, execute o seguinte comando.

        Install-Package Microsoft.Azure.Management.HDInsight.Job

2. Use o seguinte código:

        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
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

                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";

                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");

                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                    SubmitHiveJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitHiveJob()
                {
                    Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
                    List<string> args = new List<string> { { "argA" }, { "argB" } };
                    var parameters = new HiveJobSubmissionParameters
                    {
                        Query = "SHOW TABLES",
                        Defines = defines,
                        Arguments = args
                    };

                    System.Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);

                    System.Console.WriteLine("Waiting for the job completion ...");

                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }

                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                    System.Console.WriteLine("Job output is: ");

                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }

5. Pressione **F5** para executar o aplicativo.


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu várias maneiras de criar um cluster de HDInsight. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Criar clusters Hadoop em HDInsight][hdinsight-provision]
* [Gerenciar clusters Hadoop em HDInsight usando o Portal do Azure](hdinsight-administer-use-management-portal.md)
* [Referência de HDInsight .NET SDK](https://msdn.microsoft.com/library/mt271028.aspx)
* [Usar porco com HDInsight](hdinsight-use-pig.md)
* [Usar Sqoop com HDInsight](hdinsight-use-sqoop-mac-linux.md)
* [Criar aplicativos de HDInsight .NET de autenticação não interativo](hdinsight-create-non-interactive-authentication-dotnet-applications.md)


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md


