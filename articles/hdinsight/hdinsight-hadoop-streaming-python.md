<properties
   pageTitle="Desenvolver Python MapReduce trabalhos com HDInsight | Microsoft Azure"
   description="Saiba como criar e executar trabalhos de Python MapReduce em clusters de HDInsight baseados em Linux."
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

#<a name="develop-python-streaming-programs-for-hdinsight"></a>Desenvolver Python streaming programas para HDInsight

Hadoop fornece uma API streaming para MapReduce que permite que você escrever mapa e reduzir funções em idiomas diferentes Java. Neste artigo, você aprenderá como usar Python para executar operações de MapReduce.

> [AZURE.NOTE] Enquanto o código Python neste documento pode ser usado com um cluster de HDInsight baseados no Windows, as etapas neste documento são específicas para clusters baseados em Linux.

Este artigo baseia-se em informações e exemplos publicados por Michael Noll em [escrever um programa de MapReduce Hadoop em Python](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

##<a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um Hadoop baseado em Linux em HDInsight cluster

* Um editor de texto

    > [AZURE.IMPORTANT] O editor de texto deve usar LF como o final da linha. Se ele usa CRLF, isso causará erros durante a execução do trabalho MapReduce em clusters de HDInsight baseados em Linux. Se você não tiver certeza, use a etapa opcional na seção [MapReduce executar](#run-mapreduce) para converter qualquer CRLF LF.

* Para clientes do Windows, Acabamento e PSCP. Esses utilitários estão disponíveis na <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Página de Download de acabamento</a>.

##<a name="word-count"></a>Contagem de palavras

Neste exemplo, você irá implementar uma contagem de palavras básicas usando um mapeador e Redutor. O mapeador divide sentenças em palavras individuais e o Redutor agrega as palavras e conta produzir a saída.

O fluxograma a seguir ilustra o que acontece durante o mapa e reduzir fases.

![Ilustração de mapa reduzir](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##<a name="why-python"></a>Por que Python?

Python é uma linguagem de programação geral de alto nível que permite que você express conceitos em menos linhas de código que muitos outros idiomas. Tem recentemente ficou populares com cientistas de dados como uma linguagem de criação de protótipo pois sua natureza interpretada, digitando dinâmico e sintaxe elegante mais adequado para rápido desenvolvimento de aplicativos.

Python é instalado em todos os clusters de HDInsight.

##<a name="streaming-mapreduce"></a>MapReduce Streaming

Hadoop permite que você especificar um arquivo que contém o mapa e reduzir a lógica que é usada por um trabalho. Os requisitos específicos para o mapa e reduzir lógica são:

* **Entrada**: O mapa e reduzir componentes devem ler dados de entrada de STDIN.

* **Saída**: O mapa e reduzir componentes devem gravar dados de saída para STDOUT.

* **Formato de dados**: os dados consumidas e produzidas devem ser um par de chave/valor, separado por um caractere de tabulação.

Python pode facilmente manipular esses requisitos usando o módulo do **sistema** leiam STDIN e **Imprimir** para imprimir STDOUT. A tarefa restante é simplesmente formatar os dados com uma guia (`\t`) caracteres entre a chave e valor.

##<a name="create-the-mapper-and-reducer"></a>Criar o mapeador e Redutor

O mapeador e Redutor são arquivos de texto, neste caso **mapper.py** e **reducer.py** (para esclarecer que faz o quê). Você pode criá-los usando o editor de sua escolha.

###<a name="mapperpy"></a>Mapper.py

Criar um novo arquivo chamado **mapper.py** e use o seguinte código como o conteúdo:

    #!/usr/bin/env python

    # Use the sys module
    import sys

    # 'file' in this case is STDIN
    def read_input(file):
        # Split each line into words
        for line in file:
            yield line.split()

    def main(separator='\t'):
        # Read the data using read_input
        data = read_input(sys.stdin)
        # Process each words returned from read_input
        for words in data:
            # Process each word
            for word in words:
                # Write to STDOUT
                print '%s%s%d' % (word, separator, 1)

    if __name__ == "__main__":
        main()

Reserve um tempo para ler o código para que você possa entender o que ela faz.

###<a name="reducerpy"></a>Reducer.py

Criar um novo arquivo chamado **reducer.py** e use o seguinte código como o conteúdo:

    #!/usr/bin/env python

    # import modules
    from itertools import groupby
    from operator import itemgetter
    import sys

    # 'file' in this case is STDIN
    def read_mapper_output(file, separator='\t'):
        # Go through each line
        for line in file:
            # Strip out the separator character
            yield line.rstrip().split(separator, 1)

    def main(separator='\t'):
        # Read the data using read_mapper_output
        data = read_mapper_output(sys.stdin, separator=separator)
        # Group words and counts into 'group'
        #   Since MapReduce is a distributed process, each word
        #   may have multiple counts. 'group' will have all counts
        #   which can be retrieved using the word as the key.
        for current_word, group in groupby(data, itemgetter(0)):
            try:
                # For each word, pull the count(s) for the word
                #   from 'group' and create a total count
                total_count = sum(int(count) for current_word, count in group)
                # Write to stdout
                print "%s%s%d" % (current_word, separator, total_count)
            except ValueError:
                # Count was not a number, so do nothing
                pass

    if __name__ == "__main__":
        main()

##<a name="upload-the-files"></a>Carregue os arquivos

**Mapper.py** e **reducer.py** devem ser no nó principal do cluster antes nós podem ser executados. A maneira mais fácil para carregá-los é usar **scp** (**pscp** se você estiver usando um cliente Windows).

Do cliente, no mesmo diretório como **mapper.py** e **reducer.py**, use o comando a seguir. Substitua o **nome de usuário** com um usuário SSH e **clustername** com o nome do seu cluster.

    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

Isso copia os arquivos do sistema local para o nó principal.

> [AZURE.NOTE] Se você usou uma senha para proteger sua conta SSH, você será solicitado a senha. Se você usou uma chave SSH, você talvez precise usar o `-i` parâmetro e o caminho para a chave privada, por exemplo, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

##<a name="run-mapreduce"></a>Executar MapReduce

1. Conecte-se ao cluster usando SSH:

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] Se você usou uma senha para proteger sua conta SSH, você será solicitado a senha. Se você usou uma chave SSH, você talvez precise usar o `-i` parâmetro e o caminho para a chave privada, por exemplo, `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`.

2. (Opcional) Se você usou um editor de texto que usa CRLF como a linha final ao criar os arquivos mapper.py e reducer.py, ou não souber qual editor de fim de linha usa, use o seguinte comandos para converter ocorrências de CRLF no mapper.py e reducer.py em LF.

        perl -pi -e 's/\r\n/\n/g' mappery.py
        perl -pi -e 's/\r\n/\n/g' reducer.py

2. Use o seguinte comando para iniciar o trabalho MapReduce.

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasbs:///example/data/gutenberg/davinci.txt -output wasbs:///example/wordcountout

    Este comando tem as seguintes partes:

    * **Hadoop-streaming.jar**: usado quando executar operações de MapReduce streaming. Ele interfaces Hadoop com o código de MapReduce externo que você fornecer.

    * **-arquivos**: Hadoop informa que os arquivos especificados são necessários para este trabalho MapReduce, e elas devem ser copiadas para todos os nós de trabalho.

    * **-mapeador**: informa Hadoop qual arquivo para usar como o mapeador.

    * **-Redutor**: informa Hadoop qual arquivo para usar como o Redutor.

    * **-entrada**: O arquivo de entrada que estamos deve contar palavras do.

    * **-saída**: O diretório que a saída será gravada.

        > [AZURE.NOTE] Este diretório será criado pelo trabalho.

Você deve consulte várias instruções de **informações** como a inicialização do trabalho e finalmente a operação de **mapa** e **reduzir** exibida como porcentagens.

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

Você receberá informações de status sobre o trabalho quando ela for concluída.

##<a name="view-the-output"></a>Exibir a saída

Quando o trabalho estiver concluído, use o comando a seguir para exibir a saída:

    hdfs dfs -text /example/wordcountout/part-00000

Isso deve exibir uma lista de palavras e quantas vezes a palavra ocorreu. Este é um exemplo dos dados de saída:

    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2

##<a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como usar o streaming trabalhos de MapRedcue com HDInsight, use os links a seguir para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Use a seção com HDInsight](hdinsight-use-hive.md)
* [Usar porco com HDInsight](hdinsight-use-pig.md)
* [Usar MapReduce trabalhos com HDInsight](hdinsight-use-mapreduce.md)
