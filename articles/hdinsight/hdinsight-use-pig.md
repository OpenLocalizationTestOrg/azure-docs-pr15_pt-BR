<properties
   pageTitle="Usar porco Hadoop em HDInsight | Microsoft Azure"
   description="Saiba como usar porco com Hadoop em HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/14/2016"
   ms.author="larryfr"/>

# <a name="use-pig-with-hadoop-on-hdinsight"></a>Usar porco com Hadoop em HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

[Porco Apache](http://pig.apache.org/) é uma plataforma para a criação de programas para Hadoop usando uma linguagem de procedimento conhecida como *latino porco*. Porco é uma alternativa para Java para criar soluções *MapReduce* e ele está incluído no Azure HDInsight.

Neste artigo, você aprenderá como você pode usar porco com HDInsight.

##<a id="why"></a>Por que usar porco?

Um dos desafios de processamento de dados usando MapReduce em Hadoop está implementando sua lógica de processamento usando somente um mapa e uma função de reduzir. Para processamento complexo, você frequentemente precisa dividir processamento em várias operações de MapReduce que estão encadeadas juntos para atingir o resultado desejado.

Em vez de forçar você use apenas mapa e reduzir funções, porco permite que você defina processamento como uma série de transformações que os dados fluem por meio de para produzir a saída desejada.

O idioma latino porco permite descrever o fluxo de dados de entrada bruta, por meio de uma ou mais transformações, para produzir a saída desejada. Programas de porco latino siga este padrão geral:

- **Carga**: ler dados sejam manipulados do sistema de arquivos
- **Transformar**: manipular os dados
- **Despejo ou repositório**: dados na tela de saída ou armazená-la para processamento

Porco latino também suporta funções definidas pelo usuário (UDF), que permite que você chame componentes externos que implementam a lógica que é difícil de modelar em latim porco.

Para saber mais sobre porco latino, consulte [porco latino referência Manual 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) e [2 de Manual de referência de porco latino](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Para obter um exemplo do uso UDFs com porco, consulte os seguintes documentos:

* [Usar DataFu com porco no HDInsight](hdinsight-hadoop-use-pig-datafu-udf.md) - DataFu é uma coleção de útil UDFs mantidos pelo Apache

* [Usar Python com porco e seção no HDInsight](hdinsight-python.md)

* [Usar c# com seção e porco em HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

##<a id="data"></a>Sobre os dados de exemplo

Este exemplo usa um arquivo de amostra *log4j* , que é armazenado em **/example/data/sample.log** em seu contêiner de armazenamento de blob. Cada log dentro do arquivo consiste em uma linha de campos que contém uma `[LOG LEVEL]` campo para mostrar o tipo e a gravidade, por exemplo:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

No exemplo anterior, o nível de log é o erro.

> [AZURE.NOTE] Você pode também gerar um arquivo de log4j usando a ferramenta de registro em log [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) e carregue esse arquivo seu blob. Consulte [Carregar dados ao HDInsight](hdinsight-upload-data.md) para obter instruções. Para obter mais informações sobre como blobs no armazenamento do Azure são usados com HDInsight, consulte [Usar armazenamento de Blob Azure com HDInsight](hdinsight-hadoop-use-blob-storage.md).

Os dados de exemplo são armazenados no armazenamento de Blob do Azure, que HDInsight usa como o sistema de arquivo padrão para Hadoop clusters. HDInsight pode acessar arquivos armazenados em bolhas usando o prefixo **wasb** . Por exemplo, para acessar o arquivo de sample.log, você usaria a seguinte sintaxe:

    wasbs:///example/data/sample.log

Porque WASB é o padrão de armazenamento para HDInsight, você também pode acessar o arquivo usando **/example/data/sample.log** de porco latino.

> [AZURE.NOTE] A sintaxe, **wasbs: / / /**, é usado para acessar arquivos armazenados no contêiner de armazenamento de padrão para o seu cluster HDInsight. Se você especificou contas de armazenamento adicional quando você provisionado seu cluster, e você deseja acessar arquivos armazenados nessas contas, você pode acessar os dados, especificando o contêiner nome e armazenamento endereço da conta, por exemplo: **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.


##<a id="job"></a>Sobre o trabalho de amostra

O trabalho de porco latino seguinte carrega o arquivo de **sample.log** do armazenamento padrão para o seu cluster HDInsight. Em seguida, ele executa uma série de transformações que resultam em uma contagem de quantas vezes cada nível de log ocorreu nos dados de entrada. Os resultados serão despejados em STDOUT.

    LOGS = LOAD 'wasbs:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

A imagem a seguir mostra uma divisão do que faz cada transformação aos dados.

![Representação gráfica das transformações][image-hdi-pig-data-transformation]

##<a id="run"></a>Executar o trabalho de porco latino

HDInsight pode executar trabalhos de porco latino usando uma variedade de métodos. Use a tabela a seguir para decidir qual método é adequado para você, em seguida, siga o link para um passo a passo.

| **Use esta opção** se quiser...                                   | … shell **interativo** .an | ... processamento **em lotes** | … .with esse **sistema operacional de cluster** | …. E1. esse **sistema operacional cliente** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-pig-ssh.md)                        |              ✔              |            ✔            | Linux                                     | Linux, Unix, Windows ou Mac OS X        |
| [Ondulação](hdinsight-hadoop-use-pig-curl.md)                      |           &nbsp;            |            ✔            | Linux ou Windows                          | Linux, Unix, Windows ou Mac OS X        |
| [.NET SDK para Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) |           &nbsp;            |            ✔            | Linux ou Windows                          | Windows (por enquanto)                        |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md)  |           &nbsp;            |            ✔            | Linux ou Windows                          | Windows                                  |
| [Área de trabalho remota](hdinsight-hadoop-use-pig-remote-desktop.md)  |              ✔              |            ✔            | Windows                                   | Windows                                  |


## <a name="running-pig-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>Executando trabalhos de porco em Azurehdinsight usando local SQL Server Integration Services

Você também pode usar o SSIS SQL Server Integration Services () para executar um trabalho porco. O Azure Feature Pack para SSIS fornece os seguintes componentes que trabalhar com trabalhos de porco em HDInsight.


- [Tarefa do Azure HDInsight porco][pigtask]
- [Gerenciador de Conexão de assinatura do Azure][connectionmanager]


Saiba mais sobre o Azure Feature Pack para SSIS [aqui][ssispack].


##<a id="nextsteps"></a>Próximas etapas

Agora que você aprendeu como usar porco com HDInsight, use os links a seguir para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Carregar dados ao HDInsight][hdinsight-upload-data]
* [Use a seção com HDInsight][hdinsight-use-hive]
* [Usar Sqoop com HDInsight](hdinsight-use-sqoop.md)
* [Usar Oozie com HDInsight](hdinsight-use-oozie.md)
* [Usar MapReduce trabalhos com HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-pig/hdi.checkmark.png

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: ../powershell-install-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png
