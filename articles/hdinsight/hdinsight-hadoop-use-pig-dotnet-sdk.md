<properties
   pageTitle="Usar Hadoop porco com .NET em HDInsight | Microsoft Azure"
   description="Saiba como usar o SDK do .NET para Hadoop para enviar trabalhos de porco a Hadoop em HDInsight."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/17/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-using-the-net-sdk-for-hadoop-in-hdinsight"></a>Executar trabalhos de porco usando o SDK do .NET para Hadoop em HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este documento fornece um exemplo de como usar o SDK do .NET para Hadoop para enviar trabalhos de porco a um Hadoop em cluster HDInsight.

O SDK do .NET HDInsight fornece bibliotecas de cliente .NET que torna mais fácil trabalhar com clusters de HDInsight do .NET. Porco permite criar operações MapReduce por uma série de transformações de dados de modelagem. Você aprenderá como usar um aplicativo c# básico para enviar um trabalho de porco para um cluster de HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster de Azurehdinsight (Hadoop em HDInsight) (o Windows ou baseado em Linux).
* O Visual Studio 2012 ou 2013 ou 2015.

## <a name="create-the-application"></a>Criar o aplicativo

O SDK do .NET HDInsight fornece bibliotecas de cliente .NET, que torna mais fácil trabalhar com clusters de HDInsight do .NET. 


1. Abra o Visual Studio 2012 ou 2013
2. No menu **arquivo** , selecione **novo** e, em seguida, selecione o **projeto**.
3. Para o novo projeto, digite ou selecione os valores a seguir.

    <table>
    <tr>
    <th>Propriedade</th>
    <th>Valor</th>
    </tr>
    <tr>
    <th>Categoria</th>
    <th>Modelos/Visual c# / Windows</th>
    </tr>
    <tr>
    <th>Modelo</th>
    <th>Aplicativo de console</th>
    </tr>
    <tr>
    <th>Nome</th>
    <th>SubmitPigJob</th>
    </tr>
    </table>
4. Clique em **Okey** para criar o projeto.
5. No menu **Ferramentas** , selecione **Gerenciador de pacote de biblioteca** ou **Nuget Package Manager**e, em seguida, selecione **Package Manager Console**.
6. Execute o seguinte comando no console para instalar os pacotes de .NET SDK.

        Install-Package Microsoft.Azure.Management.HDInsight.Job

7. Do Solution Explorer, clique duas vezes **Program.cs** para abri-lo. Substitua o código existente com o seguinte.

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

                    SubmitPigJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitPigJob()
                {
                    var parameters = new PigJobSubmissionParameters
                    {
                        Query = @"LOGS = LOAD 'wasbs:///example/data/sample.log';
                                    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                    RESULT = order FREQUENCIES by COUNT desc;
                                    DUMP RESULT;"
                    };

                    System.Console.WriteLine("Submitting the Pig job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }


7. Pressione **F5** para iniciar o aplicativo.
8. Pressione **ENTER** para sair do aplicativo.

## <a name="summary"></a>Resumo

Como você pode ver, o SDK do .NET para Hadoop permite que você crie aplicativos .NET que enviar trabalhos de porco a um cluster de HDInsight e monitorar o status do trabalho.

## <a name="next-steps"></a>Próximas etapas

Para obter informações gerais sobre porco em HDInsight.

* [Usar porco com Hadoop em HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop em HDInsight.

* [Use a seção com Hadoop em HDInsight](hdinsight-use-hive.md)

* [Usar MapReduce com Hadoop em HDInsight](hdinsight-use-mapreduce.md) [visualização-portal]: https://portal.azure.com/
