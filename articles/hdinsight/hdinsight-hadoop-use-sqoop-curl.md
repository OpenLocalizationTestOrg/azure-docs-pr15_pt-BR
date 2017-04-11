<properties
   pageTitle="Use Hadoop Sqoop com ondulação em HDInsight | Microsoft Azure"
   description="Saiba como enviar remotamente trabalhos de Sqoop ao HDInsight usando ondulação."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="jgao"/>

#<a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Executar trabalhos de Sqoop com Hadoop em HDInsight com ondulação

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar ondulação para executar Sqoop trabalhos em um cluster de Hadoop em HDInsight.

Ondulação é usada para demonstrar como você pode interagir com HDInsight usando brutas solicitações HTTP para executar, monitorar e recuperar os resultados de Sqoop trabalhos. Isso funciona usando a API de resto do WebHCat (conhecida anteriormente como Templeton) fornecida pelo seu cluster HDInsight.

> [AZURE.NOTE] Se você já estiver familiarizado com o uso de servidores baseados em Linux Hadoop, mas é novos ao HDInsight, consulte [informações sobre como usar HDInsight no Linux](hdinsight-hadoop-linux-information.md).

##<a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um Hadoop em cluster HDInsight (Linux ou baseado no Windows)

* [Ondulação](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a name="submit-sqoop-jobs-by-using-curl"></a>Enviar Sqoop trabalhos usando ondulação

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

    Início da URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será o mesmo para todas as solicitações. O caminho, **/status**, indica que a solicitação retornar um status de WebHCat (também conhecido como Templeton) para o servidor. 

2. Use o seguinte para enviar um trabalho de sqoop:


        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /tutorials/usesqoop/data --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop

    Os parâmetros usados neste comando são da seguinte maneira:

    * **-d** - desde `-G` não for usado, a solicitação de padrões para o método de POSTAGEM. `-d`Especifica os valores de dados que são enviados com a solicitação.

        * **user.name** - o usuário que está executando o comando.

        * **comando** - Sqoop o comando para executar.

        * **statusdir** - o diretório que o status para este trabalho será gravado.

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

##<a name="limitations"></a>Limitações

* Exportação em massa - baseados em Linux com HDInsight, o conector de Sqoop usado para exportar dados para o Microsoft SQL Server ou banco de dados do Azure SQL atualmente não oferece suporte para inserções em massa.

* Em lotes - com HDInsight baseados em Linux, ao usar o `-batch` mudar ao executar inserções, Sqoop executará várias inserções em vez de processamento em lotes as operações de inserção.

##<a name="summary"></a>Resumo

Como demonstrou neste documento, você pode usar uma solicitação HTTP bruta para executar, monitorar e exibir os resultados de Sqoop trabalhos em seu cluster HDInsight.

Para obter mais informações sobre a interface REST usada neste artigo, consulte o <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">guia da API REST de Sqoop</a>.

##<a name="next-steps"></a>Próximas etapas

Para obter informações gerais sobre seção com HDInsight:

* [Usar Sqoop com Hadoop em HDInsight](hdinsight-use-sqoop.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop em HDInsight:

* [Use a seção com Hadoop em HDInsight](hdinsight-use-hive.md)

* [Usar porco com Hadoop em HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce com Hadoop em HDInsight](hdinsight-use-mapreduce.md)

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


