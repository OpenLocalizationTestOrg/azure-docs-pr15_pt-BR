<properties
   pageTitle="Usar Hadoop porco com ondulação em HDInsight | Microsoft Azure"
   description="Saiba como utilizar ondulação para executar trabalhos de porco latino em um cluster de Hadoop em Azurehdinsight."
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
   ms.date="08/23/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-curl"></a>Executar trabalhos de porco com Hadoop em HDInsight usando ondulação

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Neste documento, você aprenderá como usar ondulação para executar trabalhos de porco latino em um cluster de Azurehdinsight. A linguagem de programação porco latino permite descrever transformações que são aplicadas para os dados de entrada para produzir a saída desejada.

Ondulação é usada para demonstrar como você pode interagir com HDInsight usando brutas solicitações HTTP para executar, monitorar e recuperar os resultados de trabalhos de porco. Isso funciona usando a WebHCat API REST (conhecida anteriormente como Templeton) fornecido pelo seu cluster HDInsight.

> [AZURE.NOTE] Se você já estiver familiarizado com o uso de servidores baseados em Linux Hadoop, mas é novos ao HDInsight, consulte [Dicas de HDInsight baseados em Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um cluster de Azurehdinsight (Hadoop em HDInsight) (baseado em Linux ou baseado no Windows)

* [Ondulação](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Executar trabalhos de porco usando ondulação

> [AZURE.NOTE] Ao usar Ondulação ou qualquer outro tipo de comunicação do resto com WebHCat, você deve autenticar as solicitações, fornecendo o nome de usuário de administrador e a senha para o cluster HDInsight. Você também deve usar o nome de cluster como parte do URI Uniform Resource Identifier () que é usado para enviar as solicitações para o servidor.
>
> Para os comandos nesta seção, substitua o **nome de usuário** com o usuário para autenticar ao cluster e **senha** com a senha da conta de usuário. Substitua o nome do seu cluster **CLUSTERNAME** .
>
> A API REST é protegida por meio de [autenticação de acesso básico](http://en.wikipedia.org/wiki/Basic_access_authentication). Você sempre deve fazer solicitações usando Secure HTTP (HTTPS) para ajudar a garantir que suas credenciais são enviadas com segurança no servidor.

1. Uma linha de comando, use o seguinte comando para verificar se você pode se conectar ao cluster HDInsight:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Você deve receber uma resposta semelhante à seguinte:

        {"status":"ok","version":"v1"}

    Os parâmetros usados neste comando são da seguinte maneira:

    * **-u**: O nome de usuário e senha usados para autenticar a solicitação
    * **-G**: indica que esta é uma solicitação GET

    Início da URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será o mesmo para todas as solicitações. O caminho, **/status**, indica que a solicitação retornar o status de WebHCat (também conhecido como Templeton) para o servidor.

2. Use o código a seguir para enviar um trabalho de porco latino para cluster:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasbs:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasbs:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    Os parâmetros usados neste comando são da seguinte maneira:

    * **-d**: porque `-G` não for usado, a solicitação de padrões para o método de POSTAGEM. `-d`Especifica os valores de dados que são enviados com a solicitação.

        * **User.Name**: O usuário que está executando o comando
        * **Executar**: O latino porco instruções para executar
        * **statusdir**: O diretório que o status para este trabalho será gravado

    > [AZURE.NOTE] Observe que os espaços nas instruções de porco latino são substituídos pela `+` quando usada com ondulação de caracteres.

    Este comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho, por exemplo:

        {"id":"job_1415651640909_0026"}

3. Para verificar o status do trabalho, use o comando a seguir. Substitua **JOBID** o valor retornado na etapa anterior. Por exemplo, se o valor de retorno foi `{"id":"job_1415651640909_0026"}`, e em seguida, **JOBID** seria `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Se o trabalho tiver terminado, o estado será **bem-sucedido**.

    > [AZURE.NOTE] Esta solicitação de ondulação retorna um documento de JavaScript Object Notation (JSON) com informações sobre o trabalho e jq é usado para recuperar somente o valor de estado.

##<a id="results"></a>Exibir resultados

Quando o estado do trabalho foi alterada para **êxito**, você pode recuperar os resultados do trabalho do armazenamento de Blob do Azure. O `statusdir` parâmetro passado com a consulta contém o local do arquivo de saída; Nesse caso, **wasbs: / / / exemplo/pigcurl**. Este endereço armazena a saída do trabalho no diretório de **exemplo/pigcurl** no contêiner de armazenamento de padrão usado pelo seu cluster HDInsight.

Você pode listar e baixar estes arquivos usando a [CLI do Azure](../xplat-cli-install.md). Por exemplo, para listar os arquivos no **exemplo/pigcurl**, use o seguinte comando:

    azure storage blob list <container-name> example/pigcurl

Para baixar um arquivo, use o seguinte:

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Especifique o nome da conta de armazenamento que contém o blob usando o `-a` e `-k` parâmetros ou definir o **AZURE\_armazenamento\_conta** e **AZURE\_armazenamento\_acesso\_chave** variáveis de ambiente.

##<a id="summary"></a>Resumo

Como demonstrou neste documento, você pode usar uma solicitação HTTP bruta para executar, monitorar e exibir os resultados de trabalhos de porco no cluster HDInsight.

Para obter mais informações sobre a interface REST usada neste artigo, consulte a [Referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre porco no HDInsight:

* [Usar porco com Hadoop em HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop em HDInsight:

* [Use a seção com Hadoop em HDInsight](hdinsight-use-hive.md)

* [Usar MapReduce com Hadoop em HDInsight](hdinsight-use-mapreduce.md)
