<properties
   pageTitle="Usar Hadoop porco com a área de trabalho remota em HDInsight | Microsoft Azure"
   description="Saiba como usar o comando porco executar porco latino instruções de uma conexão de área de trabalho remota para um cluster baseado no Windows Hadoop em HDInsight."
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

#<a name="run-pig-jobs-from-a-remote-desktop-connection"></a>Executar trabalhos de porco de uma conexão de área de trabalho remota

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este documento fornece instruções passo a passo para usar o comando porco executar porco latino instruções de uma conexão de área de trabalho remota para um cluster baseado no Windows HDInsight. Porco latino permite criar aplicativos MapReduce descrevendo transformações de dados, em vez de mapear e reduzir funções.

Neste documento, saiba como

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster baseado no Windows HDInsight (Hadoop em HDInsight)

* Um computador cliente executando o Windows 10, Windows 8 ou Windows 7

##<a id="connect"></a>Conectar-se com a área de trabalho remota

Habilitar a área de trabalho remota para o cluster HDInsight, em seguida, conectá-la seguindo as instruções em [conectar ao HDInsight clusters usando o RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="pig"></a>Use o comando porco

2. Depois que você tiver uma conexão de área de trabalho remota, inicie a **linha de comando do Hadoop** usando o ícone da área de trabalho.

2. Use este procedimento para iniciar o comando porco:

        %pig_home%\bin\pig

    Você receberá um `grunt>` prompt.

3. Insira a seguinte instrução:

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    Este comando carrega o conteúdo do arquivo sample.log no arquivo de LOGS. Você pode exibir o conteúdo do arquivo usando o seguinte comando:

        DUMP LOGS;

4. Transforme os dados aplicando uma expressão regular para extrair apenas o nível de log de cada registro:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Você pode usar o **DESPEJAR** para exibir os dados após a transformação. Nesse caso, `DUMP LEVELS;`.

5. Continue aplicando transformações usando as instruções a seguir. Use `DUMP` para exibir o resultado da transformação após cada etapa.

    <table>
    <tr>
    <th>Instrução</th><th>O que ela faz</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = níveis de filtro por LOGLEVEL não é nulo.</td><td>Remove linhas que contêm um valor nulo para o nível de log e armazena os resultados em FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = FILTEREDLEVELS de grupo por LOGLEVEL;</td><td>As linhas por nível de log de grupos e armazena os resultados em GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FREQUÊNCIAS = foreach GROUPEDLEVELS Gerar grupo como LOGLEVEL, contagem (FILTEREDLEVELS. LOGLEVEL) como contar;</td><td>Cria um novo conjunto de dados que contém cada log exclusivo valor nível e quantas vezes ela ocorre. Isso é armazenado em FREQUÊNCIAS</td>
    </tr>
    <tr>
    <td>RESULTADO = ordem FREQUÊNCIAS por contagem desc;</td><td>Pedidos os níveis de log por contagem (decrescente) e armazena em resultado</td>
    </tr>
    </table>

6. Você também pode salvar os resultados de uma transformação usando a `STORE` instrução. Por exemplo, o comando a seguir salva a `RESULT` no diretório **/example/data/pigout** no contêiner de armazenamento de padrão para o seu cluster:

        STORE RESULT into 'wasbs:///example/data/pigout'

    > [AZURE.NOTE] Os dados são armazenados no diretório especificado nos arquivos nomeados **nnnnn parte**. Se o diretório já existir, você receberá uma mensagem de erro.

7. Para sair do prompt de pesado, insira a seguinte instrução.

        QUIT;

###<a name="pig-latin-batch-files"></a>Arquivos em lotes de porco latino

Você também pode usar o comando porco para executar latino porco contida em um arquivo.

3. Depois de sair do prompt de pesado, abra **o bloco de notas** e crie um novo arquivo chamado **pigbatch.pig** no diretório **% PIG_HOME %** .

4. Digite ou cole as seguintes linhas no arquivo **pigbatch.pig** e, em seguida, salvá-lo:

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. Use o seguinte para executar o arquivo **pigbatch.pig** usando o comando porco.

        pig %PIG_HOME%\pigbatch.pig

    Quando o trabalho em lotes for concluído, você verá a seguinte saída, que deve ser a mesma que quando você usou `DUMP RESULT;` nas etapas anteriores:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="summary"></a>Resumo

Como você pode ver, o comando porco permite interativamente executar operações de MapReduce ou executar trabalhos de porco latino que são armazenados em um arquivo em lotes.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre porco em HDInsight:

* [Usar porco com Hadoop em HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop em HDInsight:

* [Use a seção com Hadoop em HDInsight](hdinsight-use-hive.md)

* [Usar MapReduce com Hadoop em HDInsight](hdinsight-use-mapreduce.md)
