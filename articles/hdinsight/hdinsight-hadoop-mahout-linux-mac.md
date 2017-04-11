<properties
    pageTitle="Gerar recomendações usando Mahout e HDInsight baseados em Linux | Microsoft Azure"
    description="Saiba como usar a biblioteca de aprendizado de máquina Apache Mahout para gerar recomendações de filme com baseado em Linux HDInsight (Hadoop)."
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
    ms.date="08/30/2016"
    ms.author="larryfr"/>

#<a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight"></a>Gerar recomendações de filme usando Apache Mahout com baseado em Linux Hadoop em HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Saiba como usar a biblioteca com Azurehdinsight de aprendizado de máquina [Apache Mahout](http://mahout.apache.org) para gerar recomendações de filme.

Mahout é um [aprendizado de máquina] [ ml] biblioteca para Apache Hadoop. Mahout contém algoritmos para processar dados, como filtragem, classificação e agrupamento. Neste artigo, você vai usar um mecanismo de recomendação para gerar recomendações de filme que são baseadas no filmes que seus amigos viu.

> [AZURE.NOTE] As etapas neste documento exigem um Hadoop baseado em Linux em cluster HDInsight. Para obter informações sobre como usar Mahout com um cluster baseado no Windows, consulte [recomendações de filme gerar usando Apache Mahout com baseado no Windows Hadoop em HDInsight](hdinsight-mahout.md)

##<a name="prerequisites"></a>Pré-requisitos

* Um Hadoop baseado em Linux em cluster HDInsight. Para obter informações sobre a criação de um, consulte [Introdução ao uso baseados em Linux Hadoop em HDInsight][getstarted]

##<a name="mahout-versioning"></a>Controle de versão mahout

Para obter mais informações sobre a versão do Mahout incluído com o seu cluster HDInsight, consulte [HDInsight versões e componentes do Hadoop](hdinsight-component-versioning.md).

> [AZURE.WARNING] Embora seja possível carregar uma versão diferente do Mahout para cluster HDInsight, somente os componentes fornecidos com o cluster HDInsight são totalmente suportados e Microsoft Support ajudará para isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudar a solucionar o problema. Isso pode resultar em resolver o problema ou solicitando envolva canais disponíveis para as tecnologias de fonte aberta onde profunda especialização para que a tecnologia é encontrada. Por exemplo, existem muitos sites de comunidade que podem ser usados, como: [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).

##<a name="recommendations"></a>Recomendações de Noções básicas sobre

Uma das funções fornecidos pelo Mahout é um mecanismo de recomendação. Este mecanismo aceita dados no formato de `userID`, `itemId`, e `prefValue` (a preferência de usuários para o item). Mahout pode executar análise de ocorrência co para determinar: _usuários que tenham uma preferência de um item também tem uma preferência para esses outros itens_. Mahout determinará usuários com preferências de tipo de item, que podem ser usados para fazer recomendações.

Este é um exemplo muito simple que usa filmes:

* __Co ocorrência__: Jorge, Alice e Paulo curtiu _estrela conflitos_, _O Empire greves Back_e _retornar do Jedi_. Mahout determina que os usuários que desejarem qualquer um desses filmes também como os outros dois.

* __Co ocorrência__: Paulo e Alice também curtiram _A ameaça fantasma_, _ataque dos Clones_e _Vingança da Sith_. Mahout determina que os usuários que curtiram os três filmes anterior também como esses três.

* __Recomendação de similaridade__: Jorge porque curtiu os três primeiros filmes, Mahout examina filmes que outras pessoas com as preferências de semelhantes gostou, mas José não viu (curtiu/classificado). Nesse caso, Mahout recomenda _O fantasma ameaça_, _ataque dos Clones_e _Vingança da Sith_.

###<a name="understanding-the-data"></a>Noções básicas sobre os dados

Convenientemente, [Pesquisa de GroupLens] [ movielens] fornece dados de classificação para filmes em um formato compatível com Mahout. Esses dados estão disponíveis no armazenamento de padrão do seu cluster em `/HdiSamples/HdiSamples/MahoutMovieData`.

Existem dois arquivos, `moviedb.txt` (informações sobre os filmes,) e `user-ratings.txt`. O arquivo de ratings.txt de usuário é usado durante a análise, enquanto moviedb.txt é usado para fornecer informações de texto amigável quando exibindo os resultados da análise.

Os dados contidos em ratings.txt de usuário tem uma estrutura de `userID`, `movieID`, `userRating`, e `timestamp`, que informa como altamente cada usuário classificada como um filme. Aqui está um exemplo de dados:


    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

##<a name="run-the-analysis"></a>Executar a análise

Use o seguinte comando para executar o trabalho de recomendação:

    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [AZURE.NOTE] O trabalho pode demorar vários minutos para ser concluída e pode executar vários trabalhos de MapReduce.

##<a name="view-the-output"></a>Exibir a saída

1. Quando o trabalho estiver concluído, use o comando a seguir para exibir a saída gerada:

        hdfs dfs -text /example/data/mahoutout/part-r-00000

    A saída aparecerão da seguinte maneira:

        1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    A primeira coluna é o `userID`. Os valores contidos em ' [' e ']' são `movieId`:`recommendationScore`.

2. Você pode usar a saída, juntamente com o moviedb.txt, para exibir mais informações de usuário amigável. Primeiro, precisamos copiar os arquivos localmente usando os seguintes comandos:

        hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
        hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .

    Isso irá copiar os dados de saída para um arquivo denominado **recommendations.txt** na pasta atual, juntamente com os arquivos de dados de filme.

3. Use o seguinte comando para criar um novo script Python que ficará nomes de filme para os dados na saída recomendações:

        nano show_recommendations.py

    Quando o editor é aberto, use o seguinte como o conteúdo do arquivo:

        #!/usr/bin/env python

        import sys

        if len(sys.argv) != 5:
                print "Arguments: userId userDataFilename movieFilename recommendationFilename"
                sys.exit(1)

        userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

        print "Reading Movies Descriptions"
        movieFile = open(movieFilename)
        movieById = {}
        for line in movieFile:
                tokens = line.split("|")
                movieById[tokens[0]] = tokens[1:]
        movieFile.close()

        print "Reading Rated Movies"
        userDataFile = open(userDataFilename)
        ratedMovieIds = []
        for line in userDataFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        ratedMovieIds.append((tokens[1],tokens[2]))
        userDataFile.close()

        print "Reading Recommendations"
        recommendationFile = open(recommendationFilename)
        recommendations = []
        for line in recommendationFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        movieIdAndScores = tokens[1].strip("[]\n").split(",")
                        recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
                        break
        recommendationFile.close()

        print "Rated Movies"
        print "------------------------"
        for movieId, rating in ratedMovieIds:
                print "%s, rating=%s" % (movieById[movieId][0], rating)
        print "------------------------"

        print "Recommended Movies"
        print "------------------------"
        for movieId, score in recommendations:
                print "%s, score=%s" % (movieById[movieId][0], score)
        print "------------------------"

    Pressione **Ctrl-X**, **Y**e finalmente **Enter** para salvar os dados.

3. Use o seguinte comando para tornar o arquivo executável:

        chmod +x show_recommendations.py

4. Execute o script de Python. Este procedimento pressupõe que está no diretório de onde todos os arquivos foram baixados:

        ./show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt

    Isso examinará as recomendações geradas para 4 de ID de usuário.

    * O arquivo de **ratings.txt de usuário** é usado para recuperar filmes que o usuário tiver classificado
    * O arquivo **moviedb.txt** é usado para recuperar os nomes de filmes
    * O **recommendations.txt** é usado para recuperar as recomendações de filme para este usuário

    A saída deste comando será semelhante ao seguinte:

        Reading Movies Descriptions
        Reading Rated Movies
        Reading Recommendations
        Rated Movies
        ------------------------
        Mimic (1997), rating=3
        Ulee's Gold (1997), rating=5
        Incognito (1997), rating=5
        One Flew Over the Cuckoo's Nest (1975), rating=4
        Event Horizon (1997), rating=4
        Client, The (1994), rating=3
        Liar Liar (1997), rating=5
        Scream (1996), rating=4
        Star Wars (1977), rating=5
        Wedding Singer, The (1998), rating=5
        Starship Troopers (1997), rating=4
        Air Force One (1997), rating=5
        Conspiracy Theory (1997), rating=3
        Contact (1997), rating=5
        Indiana Jones and the Last Crusade (1989), rating=3
        Desperate Measures (1998), rating=5
        Seven (Se7en) (1995), rating=4
        Cop Land (1997), rating=5
        Lost Highway (1997), rating=5
        Assignment, The (1997), rating=5
        Blues Brothers 2000 (1998), rating=5
        Spawn (1997), rating=2
        Wonderland (1997), rating=5
        In & Out (1997), rating=5
        ------------------------
        Recommended Movies
        ------------------------
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        Rock, The (1996), score=5.0
        ------------------------

##<a name="delete-temporary-data"></a>Excluir dados temporários

Mahout trabalhos não remova dados temporários criados durante o processamento da tarefa. O `--tempDir` parâmetro for especificado no trabalho de exemplo para isolar os arquivos temporários em um caminho específico para fácil exclusão. Para remover os arquivos temporários, use o seguinte comando:

    hdfs dfs -rm -f -r /temp/mahouttemp

> [AZURE.WARNING] Se você quiser executar o comando novamente, você também deve excluir o diretório de saída. Use este procedimento para excluir este diretório:
>
> ```hdfs dfs -rm -f -r /example/data/mahoutout```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como usar Mahout, descubra outras maneiras de trabalhar com dados em HDInsight:

* [Seção com HDInsight](hdinsight-use-hive.md)
* [Porco com HDInsight](hdinsight-use-pig.md)
* [MapReduce com HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 
