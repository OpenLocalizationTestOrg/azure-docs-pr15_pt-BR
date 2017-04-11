<properties
   pageTitle="Usar MapReduce e ondulação com Hadoop em HDInsight | Microsoft Azure"
   description="Saiba como executar remotamente MapReduce trabalhos com Hadoop em HDInsight usando ondulação."
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
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-curl"></a>Executar MapReduce trabalhos com Hadoop em HDInsight usando ondulação

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Neste documento, você aprenderá como usar ondulação para executar MapReduce trabalhos em uma Hadoop em cluster HDInsight.

Ondulação é usada para demonstrar como você pode interagir com HDInsight usando brutas solicitações HTTP para executar trabalhos de MapReduce. Isso funciona usando a API de resto do WebHCat (conhecida anteriormente como Templeton) fornecida pelo seu cluster HDInsight.

> [AZURE.NOTE] Se você já estiver familiarizado com o uso de servidores baseados em Linux Hadoop, mas você ainda não conhece ao HDInsight, consulte [o que você precisa saber sobre baseados em Linux Hadoop em HDInsight](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um Hadoop em cluster HDInsight (Linux ou baseado no Windows)

* [Ondulação](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Executar trabalhos MapReduce usando ondulação

> [AZURE.NOTE] Quando você usa Ondulação ou qualquer outro tipo de comunicação do resto com WebHCat, você deve autenticar as solicitações, fornecendo o nome de usuário de administrador de cluster HDInsight e a senha. Você também deve usar o nome de cluster como parte do URI que é usado para enviar as solicitações para o servidor.
>
> Para os comandos nesta seção, substitua o **nome de usuário** com o usuário para autenticar o cluster e a **senha** com a senha da conta de usuário. Substitua o nome do seu cluster **CLUSTERNAME** .
>
> A API REST é protegida usando a [autenticação de acesso básico](http://en.wikipedia.org/wiki/Basic_access_authentication). Você sempre deve fazer solicitações usando HTTPS para garantir que suas credenciais com segurança são enviadas para o servidor.

1. De uma linha de comando, use o seguinte comando para verificar se você pode se conectar ao cluster HDInsight:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Você deve receber uma resposta semelhante à seguinte:

        {"status":"ok","version":"v1"}

    Os parâmetros usados neste comando são da seguinte maneira:

    * **-u**: indica o nome de usuário e a senha usada para autenticar a solicitação
    * **-G**: indica que esta é uma solicitação GET

    Início do URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, é o mesmo para todas as solicitações.

2. Para enviar um trabalho MapReduce, use o seguinte comando:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasbs:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasbs:///example/data/gutenberg/davinci.txt -d arg=wasbs:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar

    O final do URI (/ mapreduce/jar) informa WebHCat que esta solicitação iniciará um trabalho MapReduce de uma classe em um arquivo jar. Os parâmetros usados neste comando são da seguinte maneira:

    * **-d**: `-G` não é usado, então a solicitação de padrões para o método de POSTAGEM. `-d`Especifica os valores de dados que são enviados com a solicitação.

        * **User.Name**: O usuário que está executando o comando
        * **JAR**: O local do arquivo jar que contém a classe para ser executado
        * **classe**: A classe que contém a lógica MapReduce
        * **argumento**: os argumentos a serem transmitidos ao trabalho MapReduce; Nesse caso, o arquivo de texto de entrada e o diretório que são usadas para a saída

    Este comando deve retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho:

        {"id":"job_1415651640909_0026"}

3. Para verificar o status do trabalho, use o comando a seguir. Substitua a **JOBID** o valor retornado na etapa anterior. Por exemplo, se o valor de retorno foi `{"id":"job_1415651640909_0026"}`, e em seguida, a JOBID seria `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Se o trabalho estiver concluído, o estado será "sucedido".

    > [AZURE.NOTE] Esta solicitação de ondulação retorna um documento JSON com informações sobre o trabalho; jq é usado para recuperar somente o valor de estado.

4. Quando o estado do trabalho foi alterada para **êxito**, você pode recuperar os resultados do trabalho do armazenamento de Blob do Azure. O `statusdir` parâmetro que é passado com a consulta contém o local do arquivo de saída; Nesse caso, **wasbs: / / / exemplo/ondulação**. Este endereço armazena a saída do trabalho no **diretório/ondulação de exemplo** no contêiner de armazenamento de padrão usado pelo seu cluster HDInsight.

Você pode listar e baixar estes arquivos usando a [CLI do Azure](../xplat-cli-install.md). Por exemplo, para listar os arquivos no **exemplo/ondulação**, use o seguinte comando:

    azure storage blob list <container-name> example/curl

Para baixar um arquivo, use o seguinte:

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Especifique o nome da conta de armazenamento que contém o blob usando o `-a` e `-k` parâmetros ou definir o **AZURE\_armazenamento\_conta** e **AZURE\_armazenamento\_acesso\_chave** variáveis de ambiente. Veja [como carregar dados ao HDInsight](hdinsight-upload-data.md) para obter mais informações.

##<a id="summary"></a>Resumo

Como demonstrou neste documento, você pode usar bruta solicitação HTTP para executar, monitorar e exibir os resultados de trabalhos de seção no seu cluster HDInsight.

Para obter mais informações sobre a interface REST que é usada neste artigo, consulte a [Referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre MapReduce trabalhos em HDInsight:

* [Usar MapReduce com Hadoop em HDInsight](hdinsight-use-mapreduce.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop em HDInsight:

* [Use a seção com Hadoop em HDInsight](hdinsight-use-hive.md)

* [Usar porco com Hadoop em HDInsight](hdinsight-use-pig.md)
