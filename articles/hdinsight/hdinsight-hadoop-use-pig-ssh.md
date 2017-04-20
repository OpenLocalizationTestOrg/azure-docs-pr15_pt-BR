<properties
   pageTitle="Use Hadoop porco com SSH em um cluster HDInsight | Microsoft Azure"
   description="Saiba como conectar a um cluster Hadoop baseados em Linux com SSH e use o comando porco para executar instruções porco latino interativamente, ou como um trabalho em lotes."
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
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Execute trabalhos de porco em um cluster baseado em Linux com o comando porco (SSH)

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Neste documento, você orientará durante o processo de conexão para um cluster baseado em Linux HDInsight do Windows Azure usando Secure Shell (SSH), usando o comando porco para executar instruções porco latino interativamente, ou como um trabalho em lotes.

A linguagem de programação porco latino permite que você descreva transformações que são aplicadas aos dados de entrada para produzir a saída desejada.

> [AZURE.NOTE] Se você já estiver familiarizado com o uso de servidores baseados em Linux Hadoop, mas é novos no HDInsight, consulte [Dicas de HDInsight baseados em Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster baseado no Linux HDInsight (Hadoop em HDInsight).

* Um cliente SSH. Mac OS, Unix e Linux devem vêm com um cliente SSH. Usuários do Windows devem fazer o download de um cliente, como [Acabamento](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Conecte-se com SSH

Conecte-se para o nome de domínio totalmente qualificado (FQDN) do cluster HDInsight usando o comando SSH. O FQDN será o nome que você forneceu o cluster, em seguida, **. azurehdinsight.net**. Por exemplo, o seguinte seria conectar a um cluster chamado **myhdinsight**.

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se você forneceu uma chave de certificado para autenticação de SSH** quando você criou o cluster HDInsight, você pode precisar especificar o local da chave particular no seu sistema do cliente.

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se você forneceu uma senha para autenticação SSH** quando você criou o cluster HDInsight, você precisará fornecer a senha, quando solicitado.

Para obter mais informações sobre como usar o SSH com HDInsight, consulte [Uso SSH com baseados em Linux Hadoop em HDInsight do Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>Acabamento (clientes baseados no Windows)

Windows não fornece um cliente SSH interno. É recomendável usar **Acabamento**, que pode ser baixado em [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obter mais informações sobre como usar o acabamento, consulte [Uso SSH com baseado em Linux Hadoop em HDInsight do Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="pig"></a>Use o comando porco

2. Uma vez conectado, inicie a interface de linha de comando porco (CLI) usando o comando a seguir.

        pig

    Após um momento, você deverá ver uma `grunt>` prompt.

3. Insira a instrução a seguir.

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    Este comando carrega o conteúdo do arquivo sample.log em LOGS. Você pode exibir o conteúdo do arquivo usando o seguinte.

        DUMP LOGS;

4. Em seguida, transforme os dados aplicando uma expressão regular para extrair apenas o nível de log de cada registro usando o seguinte.

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Você pode usar o **despejo** para exibir os dados após a transformação. Nesse caso, use `DUMP LEVELS;`.

5. Continue aplicando transformações usando as instruções a seguir. Use `DUMP` para exibir o resultado da transformação após cada etapa.

    <table>
    <tr>
    <th>Instrução</th><th>O que ele faz</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = níveis de filtro por LOGLEVEL não é nulo.</td><td>Remove linhas que contêm um valor nulo para o nível de log e armazena os resultados em FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = grupo FILTEREDLEVELS por LOGLEVEL;</td><td>Agrupa as linhas por nível de log e armazena os resultados em GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FREQUÊNCIAS = foreach GROUPEDLEVELS Gerar grupo como LOGLEVEL, COUNT (FILTEREDLEVELS. LOGLEVEL) como contar;</td><td>Cria um novo conjunto de dados que contém a cada log exclusivo nível valor e quantas vezes ele ocorre. Isso é armazenado em FREQUÊNCIAS.</td>
    </tr>
    <tr>
    <td>RESULTADO = ordem FREQUÊNCIAS de contagem desc;</td><td>Ordena os níveis de log por count (ordem decrescente) e o armazena em resultado.</td>
    </tr>
    </table>

6. Você também pode salvar os resultados de uma transformação usando o `STORE` instrução. Por exemplo, o seguinte salva o `RESULT` ao diretório **/example/data/pigout** no contêiner do armazenamento padrão para o cluster.

        STORE RESULT into 'wasbs:///example/data/pigout';

    > [AZURE.NOTE] Os dados são armazenados no diretório especificado nos arquivos denominados **nnnnn da parte**. Se o diretório já existir, você receberá um erro.

7. Para sair do prompt pesado, insira a instrução a seguir.

        QUIT;

###<a name="pig-latin-batch-files"></a>Arquivos de lote porco latino

Você também pode usar o comando porco para executar latino porco contidos em um arquivo.

3. Depois de sair do prompt pesado, use o seguinte comando para pipe STDIN em um arquivo denominado **pigbatch.pig**. Este arquivo será criado no diretório base para a conta que você fez logon para a sessão SSH.

        cat > ~/pigbatch.pig

4. Digite ou cole as seguintes linhas e, em seguida, usar Ctrl + D quando terminar.

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. Use o seguinte para executar o arquivo **pigbatch.pig** usando o comando porco.

        pig ~/pigbatch.pig

    Depois que o trabalho em lotes estiver concluída, você verá a seguinte saída, que deve ser o mesmo quando você usou `DUMP RESULT;` nas etapas anteriores.

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="summary"></a>Resumo

Como você pode ver, o comando porco permite interativamente executar operações MapReduce usando porco latino, bem como executar instruções armazenadas em um arquivo em lotes.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre porco em HDInsight.

* [Use porco com Hadoop em HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop em HDInsight.

* [Use o Hive com Hadoop em HDInsight](hdinsight-use-hive.md)

* [Use MapReduce com Hadoop em HDInsight](hdinsight-use-mapreduce.md)
