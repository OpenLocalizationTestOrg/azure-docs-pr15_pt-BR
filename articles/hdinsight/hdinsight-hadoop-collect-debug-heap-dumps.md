<properties
    pageTitle="Depurar e analisar os serviços do Hadoop com pilha despejos | Microsoft Azure"
    description="Coletar despejos de pilha para serviços de Hadoop e coloque dentro a conta de armazenamento de Blob do Azure para análise e depuração automaticamente."
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
    ms.date="10/19/2016"
    ms.author="jgao"/>


# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>Pilha coletar descarta no armazenamento de Blob para depurar e analisar os serviços do Hadoop

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Despejos de pilha conter um instantâneo da memória do aplicativo, incluindo os valores de variáveis no momento que o despejo foi criado. Para que elas fiquem muito útil para diagnosticar problemas que ocorrem em tempo de execução. Pilha despejos podem ser automaticamente coletados para serviços de Hadoop e colocados dentro a conta de armazenamento de Blob do Azure de um usuário em HDInsightHeapDumps /. 

A coleção de despejos de pilha de vários serviços deve ser habilitada para serviços em clusters individuais. O padrão para esse recurso é estar desativada para um cluster. Esses despejos de pilha podem ser grandes, portanto, é aconselhável monitorar a conta de armazenamento de Blob onde eles estão sendo salvos após a coleção foi ativada.

> [AZURE.NOTE] As informações neste artigo aplica-se somente ao HDInsight baseados no Windows. Para obter informações sobre baseados em Linux HDInsight, consulte [Ativar despejos de pilha Hadoop nos serviços baseados em Linux HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## <a name="eligible-services-for-heap-dumps"></a>Serviços de qualificada para despejos de pilha

Você pode ativar despejos de pilha para os seguintes serviços:

*  **hcatalog** - tempelton
*  **seção** - hiveserver2, metastore, derbyserver
*  **mapreduce** - jobhistoryserver
*  **fio colorido** - resourcemanager, nodemanager, timelineserver
*  **hdfs** - datanode, secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Elementos de configuração que permitem despejos de pilha

Para ativar despejos de pilha para um serviço, você precisa definir os elementos de configuração apropriado na seção para esse serviço, o que é especificada por **service_name**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

O valor de **service_name** pode ser qualquer um dos serviços listados acima: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode, ou namenode.

## <a name="enable-using-azure-powershell"></a>Habilitar o uso do PowerShell do Azure

Por exemplo, para ativar despejos de pilha usando o PowerShell do Azure para jobhistoryserver, faça o seguinte:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Ativar usando o SDK do .NET

Por exemplo, para ativar despejos de pilha usando o SDK do Azure HDInsight .NET para jobhistoryserver, faça o seguinte:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
