<properties
    pageTitle="Saiba como usar HiveQL e o que é a seção | Microsoft Azure"
    description="Saiba mais sobre o Apache seção e como usá-lo com Hadoop em HDInsight. Escolha como executar seu trabalho de seção e use HiveQL para analisar um arquivo de log4j de Apache de exemplo."
    keywords="hiveql, o que é a seção"
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
    ms.date="09/19/2016"
    ms.author="larryfr"/>

# <a name="use-hive-and-hiveql-with-hadoop-in-hdinsight-to-analyze-a-sample-apache-log4j-file"></a>Usar seção e HiveQL com Hadoop em HDInsight para analisar um arquivo de log4j de Apache de exemplo

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


Neste tutorial, você vai aprender a usar o Apache seção em Hadoop em HDInsight e escolha como executar seu trabalho de seção. Você também aprenderá sobre HiveQL e como analisar um arquivo de log4j de Apache de exemplo.

##<a id="why"></a>O que é a seção e por que usá-lo?
[Seção de Apache](http://hive.apache.org/) é um sistema de depósito de dados para Hadoop, que permite a análise de dados, consultando e resumo de dados usando HiveQL (uma linguagem de consulta semelhante ao SQL). Seção pode ser usada para explorar interativamente dados ou criar lote reutilizável trabalhos de processamento.

Seção permite estrutura de projeto em dados amplamente. Depois de definir a estrutura, você pode usar a seção para consultar esses dados sem conhecimento de Java ou MapReduce. **HiveQL** (a linguagem de consulta de seção) permite que você escrever consultas com instruções que são semelhantes aos T-SQL.

Seção entende como trabalhar com dados estruturados e semiestruturados, como arquivos de texto onde os campos são delimitados por caracteres específicos. Seção também suporta personalizado **serializador/deserializers (SerDe)** para dados complexos ou estruturados irregulares. Para obter mais informações, consulte [como usar um SerDe JSON personalizado com HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

## <a name="user-defined-functions-udf"></a>Funções definidas pelo usuário (UDF)

Seção também pode ser estendida por meio de **funções definidas pelo usuário (UDF)**. Uma UDF permite implementar funcionalidade ou a lógica que não é facilmente modelada em HiveQL. Para obter um exemplo de como usar UDFs com seção, consulte o seguinte:

* [Função com seção definida pelo uso de um usuário de Java](hdinsight-hadoop-hive-java-udf.md)

* [Usando o Python com seção e porco em HDInsight](hdinsight-python.md)

* [Usar c# com seção e porco em HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Como adicionar uma UDF de seção personalizado ao HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Exemplo de seção UDF personalizado para converter formatos de data/hora em carimbo de hora de seção](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="hive-internal-tables-vs-external-tables"></a>Seção tabelas externas de vs tabelas internas

Há algumas coisas que você precisa saber sobre a tabela interna de seção e a tabela externa:

- O comando **CREATE TABLE** cria uma tabela interna. O arquivo de dados deve estar localizado no contêiner padrão.
- O comando **Criar tabela** move o arquivo de dados para o /hive/depósito/<TableName> pasta.
- O comando **Criar tabela externa** cria uma tabela externa. O arquivo de dados pode estar localizado fora o recipiente padrão.
- O comando **Criar tabela externa** não mover o arquivo de dados.
- O comando **Criar tabela externa** não permite que as pastas no local. Este é o motivo por que o tutorial faz uma cópia do arquivo sample.log.

Para obter mais informações, consulte [HDInsight: seção internos e externos introdução de tabelas][cindygross-hive-tables].


##<a id="data"></a>Sobre os dados de exemplo, um arquivo de log4j Apache

Este exemplo usa um arquivo de amostra *log4j* , que é armazenado em **/example/data/sample.log** em seu contêiner de armazenamento de blob. Cada log dentro do arquivo consiste em uma linha de campos que contém uma `[LOG LEVEL]` campo para mostrar o tipo e a gravidade, por exemplo:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

No exemplo anterior, o nível de log é o erro.

> [AZURE.NOTE] Você pode também gerar um arquivo de log4j usando a ferramenta de registro em log [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) e carregue esse arquivo ao contêiner de blob. Consulte [Carregar dados ao HDInsight](hdinsight-upload-data.md) para obter instruções. Para obter mais informações sobre como o armazenamento de Blob do Azure é usado com HDInsight, consulte [Usar armazenamento de Blob Azure com HDInsight](hdinsight-hadoop-use-blob-storage.md).

Os dados de exemplo são armazenados no armazenamento de Blob do Azure, que HDInsight usa como o sistema de arquivo padrão. HDInsight pode acessar arquivos armazenados em bolhas usando o prefixo **wasb** . Por exemplo, para acessar o arquivo de sample.log, você usaria a seguinte sintaxe:

    wasbs:///example/data/sample.log

Como o armazenamento de Blob do Azure é o armazenamento de padrão para HDInsight, você também pode acessar o arquivo usando **/example/data/sample.log** de HiveQL.

> [AZURE.NOTE] A sintaxe, **wasbs: / / /**, é usado para acessar arquivos armazenados no contêiner de armazenamento de padrão para o seu cluster HDInsight. Se você especificou contas de armazenamento adicional quando você provisionado seu cluster, e você deseja acessar arquivos armazenados nessas contas, você pode acessar os dados, especificando o contêiner nome e armazenamento endereço da conta, por exemplo, **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

##<a id="job"></a>Trabalho de amostra: projetar colunas para dados delimitados

As seguintes instruções de HiveQL serão projetar colunas para dados delimitados armazenado na **wasbs: / / / / dados de exemplo** diretório:

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

No exemplo anterior, as instruções de HiveQL executam as seguintes ações:

* __Definir hive.execution.engine=tez;__: define o mecanismo de execução para usar Tez. Usando Tez em vez de MapReduce pode fornecer um aumento no desempenho de consulta. Para obter mais informações sobre Tez, consulte a seção de [Uso Apache Tez para melhorar o desempenho](#usetez) .

    > [AZURE.NOTE] Esta política só é necessária ao usar um cluster baseado no Windows HDInsight; Tez é o mecanismo de execução padrão para HDInsight baseados em Linux.

* **DROP TABLE**: exclui a tabela e o arquivo de dados se a tabela já existe.
* **Criar tabela externa**: cria uma nova tabela **externa** na seção. Tabelas externas armazenam somente a definição da tabela na seção; os dados são deixados no local original e no formato original.
* **Formato de linha**: informa seção como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.
* **Local de arquivo de texto como ARMAZENADO**: informa seção onde os dados estão armazenados (o diretório de dados do exemplo) e que ele está armazenado como texto. Os dados podem estar em um arquivo ou distribuídas em vários arquivos dentro do diretório.
* **Selecione**: seleciona uma contagem de todas as linhas onde a coluna **t4** contém o valor **[ERROR]**. Isso deve retornar um valor de **3** porque há três linhas que contêm esse valor.
* **INPUT__FILE__NAME como '%.log'** - informa seção que estamos só deve retornar dados de arquivos que terminam em. log. Isso restringe a pesquisa para o arquivo de sample.log que contém os dados e impede que ela retornar dados do exemplo de outro arquivos de dados que não correspondem o esquema definimos.

> [AZURE.NOTE] Tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizadas por uma fonte externa, como um processo de carregamento de dados automatizada, ou por outra operação MapReduce e quiser sempre as consultas de seção para usar os dados mais recentes.
>
> Soltar uma tabela externa faz **não** excluir os dados, exclui somente a definição da tabela.

Depois de criar a tabela externa, as instruções a seguir são usadas para criar uma tabela **interna** .

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

Essas instruções executam as seguintes ações:

* **Criar tabela se não existir**: cria uma tabela, se ele ainda não exista. Como a palavra-chave **externa** não for usada, isso é uma tabela interna, que é armazenada na seção data warehouse e é gerenciada completamente pela seção.
* **ARMAZENADO como ORC**: armazena os dados no formato de linha de otimizado colunas (ORC). Este é um formato altamente otimizado e eficiente para armazenar dados de seção.
* SUBSTITUIR **Inserir … Selecione**: seleciona linhas da tabela **log4jLogs** que contém **[ERROR]**e, em seguida, insere os dados na tabela **logs de erros** .

> [AZURE.NOTE] Ao contrário de tabelas externas, soltar uma tabela interna também exclui os dados subjacentes.

##<a id="usetez"></a>Use Apache Tez para melhorar o desempenho

[Apache Tez](http://tez.apache.org) é uma estrutura que permite que aplicativos de dados intenso, como ramificação, para executar com mais eficiência em escala. Na versão mais recente do HDInsight, a seção suporta a execução no Tez. Tez é ativada por padrão para clusters HDInsight baseados em Linux.

> [AZURE.NOTE] Tez atualmente está desativada por padrão para clusters HDInsight baseado no Windows e ele deve ser habilitado. Para tirar proveito da Tez, o seguinte valor deve ser definido por uma consulta de seção:
>
> ```set hive.execution.engine=tez;```
>
>Isso pode ser enviado em uma base por consulta colocando-o no início de sua consulta. Você também pode definir essa opção para estar ativado por padrão em um cluster definindo o valor de configuração quando você cria o cluster. Você pode encontrar mais detalhes em [Clusters de HDInsight de provisionamento](hdinsight-provision-clusters.md).

A [seção em documentos de design de Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contiverem um número de detalhes sobre as opções de implementação e as configurações de ajustes.

Para auxiliar na depuração trabalhos executou usando Tez, HDInsight fornece a seguinte web interfaces do usuário que permitem que você exibir os detalhes de trabalhos de Tez:

* [Usar a interface do usuário Tez em HDInsight baseado no Windows](hdinsight-debug-tez-ui.md)

* [Usar o modo de exibição Ambari Tez HDInsight baseados em Linux](hdinsight-debug-ambari-tez-view.md)

##<a id="run"></a>Escolha como executar o trabalho HiveQL

HDInsight pode executar trabalhos de HiveQL usando vários métodos. Use a tabela a seguir para decidir qual método é adequado para você, em seguida, siga o link para um passo a passo.

| **Use esta opção** se quiser...                                                     | … shell **interativo** .an | ... processamento **em lotes** | … .with esse **sistema operacional de cluster** | …. E1. esse **sistema operacional cliente** |
|:--------------------------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [Modo de exibição de seção](hdinsight-hadoop-use-hive-ambari-view.md) | ✔ | ✔ | Linux | Qualquer (baseado em navegador) |
| [Comando beeline (de uma sessão SSH)](hdinsight-hadoop-use-hive-beeline.md)                                         |              ✔              |            ✔            | Linux                                     | Linux, Unix, Windows ou Mac OS X        |
| [Comando de seção (de uma sessão SSH)](hdinsight-hadoop-use-hive-ssh.md)                                         |              ✔              |            ✔            | Linux                                     | Linux, Unix, Windows ou Mac OS X        |
| [Ondulação](hdinsight-hadoop-use-hive-curl.md)                                       |           &nbsp;            |            ✔            | Linux ou Windows                          | Linux, Unix, Windows ou Mac OS X        |
| [Console de consulta](hdinsight-hadoop-use-hive-query-console.md)                     |           &nbsp;            |            ✔            | Windows                                   | Qualquer (baseado em navegador)                            |
| [Ferramentas de HDInsight para Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |           &nbsp;            |            ✔            | Linux ou Windows                          | Windows                                  |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md)                   |           &nbsp;            |            ✔            | Linux ou Windows                          | Windows                                  |
| [Área de trabalho remota](hdinsight-hadoop-use-hive-remote-desktop.md)                   |              ✔              |            ✔            | Windows                                   | Windows                                  |

## <a name="running-hive-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>Executando trabalhos de seção em Azurehdinsight usando local SQL Server Integration Services

Você também pode usar o SSIS SQL Server Integration Services () para executar um trabalho de seção. O Azure Feature Pack para SSIS fornece os seguintes componentes que trabalhar com trabalhos de seção em HDInsight.


- [Tarefa de seção do Azure HDInsight][hivetask]
- [Gerenciador de Conexão de assinatura do Azure][connectionmanager]


Saiba mais sobre o Azure Feature Pack para SSIS [aqui][ssispack].


##<a id="nextsteps"></a>Próximas etapas

Agora que você aprendeu seção é e como usá-lo com Hadoop em HDInsight, use os links a seguir para explorar outras maneiras de trabalhar com o Azure HDInsight.


- [Carregar dados ao HDInsight][hdinsight-upload-data]
- [Usar porco com HDInsight][hdinsight-use-pig]
- [Usar Sqoop com HDInsight](hdinsight-use-sqoop.md)
- [Usar Oozie com HDInsight](hdinsight-use-oozie.md)
- [Usar MapReduce trabalhos com HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md

[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
