<properties
    pageTitle="Executar Hadoop MapReduce amostras em baseados em Linux HDInsight | Microsoft Azure"
    description="Começar a usar as amostras de MapReduce com HDInsight baseados em Linux. Usar SSH para se conectar ao cluster e, em seguida, use o comando Hadoop para executar trabalhos de amostra."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="larryfr"/>




#<a name="run-the-hadoop-samples-in-hdinsight"></a>Executar as amostras de Hadoop em HDInsight

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Baseado em Linux HDInsight clusters fornecem um conjunto de amostras de MapReduce que podem ser usados para se familiarizar com a execução do Hadoop MapReduce trabalhos. Neste documento, você saiba mais sobre as amostras disponíveis e percorrer executando algumas delas.

##<a name="prerequisites"></a>Pré-requisitos

- **Assinatura de um Azure**: consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

- **HDInsight baseados em Linux de um cluster**: consulte [começar a usar o Hadoop com seção no HDInsight no Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

- **Cliente um SSH**: para obter informações sobre como usar SSH com HDInsight, consulte os seguintes artigos:

    - [Use SSH com baseado em Linux Hadoop em HDInsight Linux, Unix ou dos X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="the-samples"></a>Os exemplos ##

**Local**: os exemplos estão localizados no cluster HDInsight em **/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar**

**Conteúdo**: os exemplos a seguir estão contidos neste arquivo:

- **aggregatewordcount**: uma agregação com base em programa mapeamento/redução que conta as palavras em arquivos de entrada
- **aggregatewordhist**: uma agregação com base em programa mapeamento/redução que calcula o histograma das palavras de arquivos de entrada
- **bbp**: um programa de mapeamento/redução que usa Bailey-Borwein-Plouffe para calcular dígitos exatos de Pi
- **dbcount**: um trabalho de exemplo que conta os logs de página armazenados em um um banco de dados
- **distbbp**: um programa de mapeamento/redução que usa uma fórmula de tipo BBP para calcular bits exatas de Pi
- **GREP**: um programa de mapeamento/redução que conta as correspondências de um regex na entrada
- **junção**: um trabalho que efeitos uma junção ultrapassa classificados, igualmente particionado conjuntos de dados
- **multifilewc**: um trabalho que conta palavras de vários arquivos
- **pentomino**: um mapeamento/redução programa Preparando para encontrar soluções para problemas de pentomino de bloco
- **pi**: um programa de mapeamento/redução que estima Pi usando um quase-Monte de método Carlo
- **randomtextwriter**: um programa de mapeamento/redução que grava 10 GB de dados textuais aleatórios por nó
- **randomwriter**: um programa de mapeamento/redução que grava 10 GB de dados aleatórios por nó
- **secondarysort**: um exemplo definindo uma classificação secundária para a reduzir
- **Classificar**: um programa de mapeamento/redução que classifica os dados escritos pelo gravador aleatório
- **Sudoku**: um solver sudoku
- **teragen**: gerar dados para o terasort
- **terasort**: executar o terasort
- **teravalidate**: resultados de terasort de verificação
- **WordCount**: um programa de mapeamento/redução que conta as palavras em arquivos de entrada
- **wordmean**: um programa de mapeamento/redução que calcula o comprimento médio das palavras de arquivos de entrada
- **wordmedian**: um programa de mapeamento/redução que calcula o comprimento médio das palavras de arquivos de entrada
- **wordstandarddeviation**: um programa de mapeamento/redução que calcula o desvio padrão do comprimento das palavras de arquivos de entrada

**Código-fonte**: código-fonte para esses exemplos está incluído no cluster HDInsight em **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples**

> [AZURE.NOTE] O `2.2.4.9-1` no caminho é a versão da plataforma de dados de Hortonworks para o cluster HDInsight e podem ser alterados conforme HDInsight for atualizado.

## <a name="how-to-run-the-samples"></a>Como executar as amostras ##

1. Conecte ao HDInsight usando SSH, conforme descrito nos seguintes artigos:

    - [Use SSH com baseado em Linux Hadoop em HDInsight Linux, Unix ou dos X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Do `username@#######:~$` solicitar, use o seguinte comando para listar os exemplos:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

    Isso gera a lista de exemplo na seção anterior deste documento.

3. Use o comando a seguir para obter ajuda em uma amostra específica. Neste caso, a amostra de **wordcount** :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount

    Você deve receber a seguinte mensagem:

        Usage: wordcount <in> [<in>...] <out>

    Isso indica que você pode fornecer vários caminhos de entrada para documentos de origem. O caminho final é onde a saída (contagem de palavras em documentos de origem,) está armazenado.

4. Use o seguinte para contar todas as palavras dos blocos de anotações de Leonardo Da Vinci, que são fornecidos como dados de exemplo com seu cluster:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount

    Entrada para este trabalho é lido da **wasbs:///example/data/gutenberg/davinci.txt**.

    Saída para este exemplo é armazenado em **wasbs: / / / exemplo/dados/davinciwordcount**.

    > [AZURE.NOTE] Conforme observado na Ajuda de amostra wordcount, você também pode especificar vários arquivos de entrada. Por exemplo, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` seria contar palavras em davinci.txt e ulysses.txt.

5. Quando o trabalho estiver concluído, use o comando a seguir para exibir a saída:

        hdfs dfs -cat /example/data/davinciwordcount/*

    Isso concatena todos os arquivos de saída produzidos pelo trabalho e exibi-las. Este exemplo básico há apenas um arquivo, porém se houvesse mais este comando seria itera todos eles.

    A saída é similar ao seguinte:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Cada linha representa uma palavra e quantas vezes ocorreu nos dados de entrada.

## <a name="sudoku"></a>Sudoku

O exemplo Sudoku possui instruções de uso de um pouco inútil; "Incluir uma quebra-cabeças na linha de comando".

[O Sudoku](https://en.wikipedia.org/wiki/Sudoku) é um quebra-cabeças que consiste em nove 3x3 grades. Algumas células na grade têm números, enquanto outras pessoas estão em branco e, em seguida, o objetivo é resolver para as células em branco. O link acima tem mais informações sobre o quebra-cabeças, mas o propósito deste exemplo é resolver para as células em branco. Portanto nossa entrada deve ser um arquivo que está no seguinte formato:

- Nove linhas das nove colunas

- Cada coluna pode conter um número ou `?` (que indica que uma célula em branco)

- Células são separadas por um espaço

Há uma maneira certas para construir quebra-cabeças Sudoku; Você não pode repetir um número em uma coluna ou linha. Não há um exemplo no cluster HDInsight que foi criado corretamente. Ele está localizado em **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta** e contém o seguinte:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [AZURE.NOTE] O `2.2.4.9-1` parte do caminho pode ser alterados conforme as atualizações são feitas ao cluster HDInsight.

Para executar esta o exemplo Sudoku, use o seguinte comando:

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.9.1-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

Os resultados devem aparecer similares ao seguinte:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi-"></a>PI (π)

O exemplo de pi usa uma estatística (quase-Monte Carlo) método para estimar o valor de pi. Pontos colocados aleatoriamente dentro de uma unidade quadrada também estão dentro de um círculo inscritas dentro esse quadrado com uma probabilidade igual para a área do círculo, pi/4. O valor de pi pode ser estimado do valor do 4R, onde R é a razão entre o número de pontos que estão dentro do círculo para o número total de pontos que estão dentro do quadrado. Quanto maior a amostra de pontos usados, melhor a estimativa é.

O mapeador para este exemplo gera um número de pontos aleatoriamente dentro de um quadrado de unidade e, em seguida, conta o número desses pontos que estão dentro do círculo.

Em seguida, o Redutor acumula pontos contados pelas mapeadores e estima o valor de pi do 4R fórmula, onde R é a razão entre o número de pontos contados dentro do círculo para o número total de pontos que estão dentro do quadrado.

Use o seguinte comando para executar esse exemplo. Isso usa 16 mapas com 10.000.000 amostras para estimar o valor de pi:

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

O valor retornado por isso deve ser semelhante ao **3.14159155000000000000**. Referências, as 10 primeiros casas decimais de pi são 3.1415926535.

##<a name="10gb-greysort"></a>10GB Greysort

GraySort é um tipo de referência cuja métrica é a taxa de classificação (TB/minuto) que é alcançada durante a classificação grandes quantidades de dados, geralmente um 100TB mínimo.

Este exemplo utiliza um pouco mais 10GB de dados para que ele possa ser executado relativamente rapidamente. Ele usa os aplicativos de MapReduce desenvolvidos pela Owen O'Malley e Arun Murthy que conquistaram o parâmetro de comparação de classificação de terabytes geral ("daytona") anual em 2009 com uma taxa de 0.578 TB/min (100 TB em 173 minutos). Para obter mais informações sobre esta e outras avaliações de classificação, consulte o site [Sortbenchmark](http://sortbenchmark.org/) .

Este exemplo usa três conjuntos de MapReduce programas:

- **TeraGen**: um programa de MapReduce que gera linhas de dados para classificar

- **TeraSort**: amostras de dados de entrada e usa MapReduce para classificar os dados em uma ordem de total

    TeraSort é uma classificação padrão das funções de MapReduce, exceto para um particionador personalizado que usa uma lista classificada de chaves de N-1 de amostra que definem o intervalo de chave para cada reduzir. Em particular, todas as chaves tais que amostra [i-1] < = chave < amostra [i] são enviadas para reduzir i. Isso garante que as saídas do reduzem i é todos menor do que a saída de reduzir i + 1.

- **TeraValidate**: um programa de MapReduce que valida que a saída está classificada globalmente

    Ele cria um mapa por arquivo no diretório de saída, e cada mapa garante que cada chave é menor ou igual ao anterior. A função map também gera registros das chaves e sobrenomes de cada arquivo e a função de reduzir garante que a primeira chave de arquivo i é maior do que a última chave i-1 do arquivo. Todos os problemas são relatados como uma saída da reduzir com as teclas que estão fora de ordem.

Usar as seguintes etapas para gerar dados, classificar e, em seguida, validar a saída:

1. Gerar 10GB de dados, que serão armazenados ao armazenamento de padrão do cluster HDInsight no **wasbs: / / / exemplo / / 10GB-classificar-a entrada de dados**:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input

    O `-Dmapred.map.tasks` informa Hadoop quantas tarefas de mapa para usar para este trabalho. Os dois parâmetros finais instruem o trabalho para criar 10GB patrimônio de dados e armazená-la em **wasbs: / / / exemplo / / 10GB-classificar-a entrada de dados**.

2. Use o seguinte comando para classificar os dados:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output

    O `-Dmapred.reduce.tasks` informa Hadoop reduzem quantas tarefas para usar para o trabalho. Os dois parâmetros finais são apenas os locais de entrada e saídos para os dados.

3. Use o seguinte para validar os dados gerados pelo classificar:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

##<a name="next-steps"></a>Próximas etapas ##

Deste artigo, você aprendeu como executar os exemplos incluídos clusters HDInsight baseados em Linux. Para tutoriais sobre como usar porco, seção e MapReduce com HDInsight, consulte os tópicos a seguir:

* [Usar porco com Hadoop em HDInsight][hdinsight-use-pig]
* [Use a seção com Hadoop em HDInsight][hdinsight-use-hive]
* [Usar MapReduce com Hadoop em HDInsight] [hdinsight-use-mapreduce]



[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
