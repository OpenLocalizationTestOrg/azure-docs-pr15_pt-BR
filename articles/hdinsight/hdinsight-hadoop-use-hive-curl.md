<properties
   pageTitle="Use a seção Hadoop com ondulação em HDInsight | Microsoft Azure"
   description="Saiba como enviar remotamente trabalhos de porco ao HDInsight usando ondulação."
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
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#<a name="run-hive-queries-with-hadoop-in-hdinsight-with-curl"></a>Executar consultas de seção com Hadoop em HDInsight com ondulação

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Neste documento, você aprenderá como usar ondulação para executar consultas de seção em um Hadoop em Azurehdinsight cluster.

Ondulação é usada para demonstrar como você pode interagir com HDInsight usando brutas solicitações HTTP para executar, monitorar e recuperar os resultados de consultas de seção. Isso funciona usando a API de resto do WebHCat (conhecida anteriormente como Templeton) fornecida pelo seu cluster HDInsight.

> [AZURE.NOTE] Se você já estiver familiarizado com o uso de servidores baseados em Linux Hadoop, mas é novos ao HDInsight, consulte [o que você precisa saber sobre Hadoop em HDInsight baseado em Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um Hadoop em cluster HDInsight (Linux ou baseado no Windows)

* [Ondulação](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Executar consultas de seção usando ondulação

> [AZURE.NOTE] Ao usar Ondulação ou qualquer outro tipo de comunicação do resto com WebHCat, você deve autenticar as solicitações, fornecendo o nome de usuário e senha para o administrador de cluster HDInsight. Você também deve usar o nome de cluster como parte do URI Uniform Resource Identifier () usado para enviar as solicitações para o servidor.
>
> Para os comandos nesta seção, substitua o **nome de usuário** com o usuário para autenticar ao cluster e **senha** com a senha da conta de usuário. Substitua o nome do seu cluster **CLUSTERNAME** .
>
> A API REST é protegida por meio de [autenticação básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Você sempre deve fazer solicitações usando Secure HTTP (HTTPS) para ajudar a garantir que suas credenciais são enviadas com segurança no servidor.

1. Uma linha de comando, use o seguinte comando para verificar se você pode se conectar ao cluster HDInsight:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Você deve receber uma resposta semelhante à seguinte:

        {"status":"ok","version":"v1"}

    Os parâmetros usados neste comando são da seguinte maneira:

    * **-u** - o nome de usuário e senha usados para autenticar a solicitação.
    * **-G** - indica que esta é uma solicitação GET.

    Início da URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será o mesmo para todas as solicitações. O caminho, **/status**, indica que a solicitação retornar um status de WebHCat (também conhecido como Templeton) para o servidor. Você também pode solicitar a versão da seção usando o comando a seguir:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    Isso deve retornar uma resposta semelhante à seguinte:

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Use este procedimento para criar uma nova tabela denominada **log4jLogs**:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasbs:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Os parâmetros usados neste comando são da seguinte maneira:

    * **-d** - desde `-G` não for usado, a solicitação de padrões para o método de POSTAGEM. `-d`Especifica os valores de dados que são enviados com a solicitação.

        * **user.name** - o usuário que está executando o comando.

        * **Executar** - HiveQL as instruções para executar.

        * **statusdir** - o diretório que o status para este trabalho será gravado.

    Essas instruções executam as seguintes ações:

    * **DROP TABLE** - exclui a tabela e o arquivo de dados, se a tabela já existe.

    * **Criar tabela externa** - cria uma nova tabela 'external' na seção. Tabelas externas armazenam apenas a definição de tabela na seção. Os dados são deixados no local original.

        > [AZURE.NOTE] Tabelas externas devem ser usadas quando você espera os dados subjacentes para ser atualizados por uma fonte externa, como um processo de carregamento de dados automatizada ou por outra operação MapReduce, mas quer sempre que as consultas de seção para usar os dados mais recentes.
        >
        > Soltar uma tabela externa faz **não** excluir os dados, somente a definição da tabela.

    * **Formato de linha** - informa seção como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

    * **Local de arquivo de texto como armazenados** - informa seção onde os dados estão armazenados (o diretório de dados do exemplo) e que ele está armazenado como texto.

    * **Selecione** - seleciona uma contagem de todas as linhas onde coluna **t4** contém o valor **[ERROR]**. Isso deve retornar um valor de **3** como existem três linhas que contêm esse valor.

    > [AZURE.NOTE] Observe que os espaços entre as instruções de HiveQL são substituídos pela `+` quando usada com ondulação de caracteres. Valores entre aspas que contêm um espaço, como o delimitador, não devem ser substituídos por `+`.

    * **INPUT__FILE__NAME como '% 25.log'** - neste limita a pesquisa somente usar arquivos que terminam em. log. Se isso não estiver presente, seção tentará pesquisar todos os arquivos neste diretório e seus subdiretórios, incluindo os arquivos que não correspondem o esquema de coluna definido para esta tabela.

    > [AZURE.NOTE] Observe que 25% é a URL codificada como formulário de %, portanto, é a condição real `like '%.log'`. % Deve ser URL codificado, como ele será tratado como um caractere especial em URLs.

    Este comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho.

        {"id":"job_1415651640909_0026"}

3. Para verificar o status do trabalho, use o comando a seguir. Substitua **JOBID** o valor retornado na etapa anterior. Por exemplo, se o valor de retorno foi `{"id":"job_1415651640909_0026"}`, e em seguida, **JOBID** seria `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Se o trabalho tiver terminado, o estado será **bem-sucedido**.

    > [AZURE.NOTE] Esta solicitação de ondulação retorna um documento de JavaScript Object Notation (JSON) com informações sobre o trabalho; jq é usado para recuperar somente o valor de estado.

4. Depois que o estado do trabalho foi alterada para **êxito**, você pode recuperar os resultados do trabalho do armazenamento de Blob do Azure. O `statusdir` parâmetro passado com a consulta contém o local do arquivo de saída; Nesse caso, **wasbs: / / / exemplo/ondulação**. Este endereço armazena a saída do trabalho no **diretório/ondulação de exemplo** no contêiner de armazenamento de padrão usado pelo seu cluster HDInsight.

    Você pode listar e baixar estes arquivos usando a [CLI do Azure](../xplat-cli-install.md). Por exemplo, para listar os arquivos no **exemplo/ondulação**, use o seguinte comando:

        azure storage blob list <container-name> example/curl

    Para baixar um arquivo, use o seguinte:

        azure storage blob download <container-name> <blob-name> <destination-file>

    > [AZURE.NOTE] Você deve especificar o nome da conta de armazenamento que contém o blob usando o `-a` e `-k` parâmetros ou definir o **AZURE\_armazenamento\_conta** e **AZURE\_armazenamento\_acesso\_chave** variáveis de ambiente. Consulte < um href = "hdinsight-carregamento-data.md" target = blank"para obter mais informações.

6. Use as instruções a seguir para criar uma nova tabela 'interna' denominada **logs de erros**:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Essas instruções executam as seguintes ações:

    * **Criar tabela se não existir** - cria uma tabela, se ele ainda não exista. Como a palavra-chave **externa** não for usada, isso é uma tabela interna, que é armazenada na seção data warehouse e é gerenciada completamente pela seção.

        > [AZURE.NOTE] Ao contrário de tabelas externas, soltar uma tabela interna excluirá os dados subjacentes também.

    * **ARMAZENADO como ORC** - armazena os dados no formato de linha de otimizado colunas (ORC). Este é um formato altamente otimizado e eficiente para armazenar dados de seção.
    * SUBSTITUIR **Inserir … Selecione** - seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]**e, em seguida, insere os dados na tabela **logs de erros** .
    * **Selecione** - seleciona todas as linhas da nova tabela **logs de erros** .

7. Use a identificação de trabalho retornada para verificar o status do trabalho. Depois que ela foi bem-sucedida, use CLI do Azure para Mac, Linux e Windows conforme descrito anteriormente para baixar e exibir os resultados. A saída deve conter três linhas, todos os quais contêm **[ERROR]**.


##<a id="summary"></a>Resumo

Como demonstrou neste documento, você pode usar uma solicitação HTTP bruta para executar, monitorar e exibir os resultados de trabalhos de seção em seu cluster HDInsight.

Para obter mais informações sobre a interface REST usada neste artigo, consulte a <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">Referência de WebHCat</a>.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre seção com HDInsight:

* [Use a seção com Hadoop em HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop em HDInsight:

* [Usar porco com Hadoop em HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce com Hadoop em HDInsight](hdinsight-use-mapreduce.md)

Se você estiver usando Tez com seção, consulte os seguintes documentos para informações de depuração:

* [Usar a interface do usuário Tez em HDInsight baseado no Windows](hdinsight-debug-tez-ui.md)

* [Usar o modo de exibição Ambari Tez HDInsight baseados em Linux](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


