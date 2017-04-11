<properties
pageTitle="Usar DataFu com porco em HDInsight"
description="DataFu é uma coleção de bibliotecas para uso com Hadoop. Saiba como você pode usar DataFu com porco no cluster HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/23/2016"
ms.author="larryfr"/>

#<a name="use-datafu-with-pig-on-hdinsight"></a>Usar DataFu com porco em HDInsight

DataFu é uma coleção de bibliotecas de fonte aberta para uso com Hadoop. Neste documento, você aprenderá como usar DataFu no seu cluster HDInsight e como usar funções de definido de usuário de DataFu (UDF) com porco.

##<a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure.

* Um cluster de Azurehdinsight (Linux ou baseado no Windows)

* Familiaridade básica com o [uso de porco em HDInsight](hdinsight-use-pig.md)

##<a name="install-datafu-on-linux-based-hdinsight"></a>Instalar o DataFu em HDInsight baseado em Linux

> [AZURE.NOTE] DataFu é instalado na versão de clusters baseados em Linux 3.3 e superiores e em clusters baseados no Windows. Ele não está instalado em clusters baseados em Linux anteriores ao 3.3.
>
> Se você estiver usando uma versão de cluster baseado em Linux 3.3 ou superior, ou um cluster baseado no Windows, você pode ignorar esta seção.

DataFu pode ser baixada e instalada do repositório Maven. Use as etapas a seguir para adicionar DataFu ao cluster HDInsight:

1. Conectar-se ao cluster HDInsight baseados em Linux usando o SSH. Para obter mais informações sobre como usar SSH com HDInsight, consulte um dos seguintes documentos:

    * [Use SSH com baseado em Linux Hadoop em HDInsight do Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-unix.md)
    
2. Use o seguinte comando para baixar o arquivo de jar DataFu usando o utilitário de wget, ou copie e cole o link em seu navegador para começar o download.

        wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar

3. Em seguida, carregue o arquivo ao armazenamento padrão para o seu cluster HDInsight. Isso disponibiliza o arquivo para todos os nós no cluster e o arquivo permanecerá no armazenamento, mesmo que você exclua e recrie o cluster.

        hdfs dfs -put datafu-1.2.0.jar /example/jars
    
    > [AZURE.NOTE] O exemplo acima armazena o jar no `wasbs:///example/jars` porque este diretório já existe no armazenamento de cluster. Você pode usar qualquer local em que você quer que o armazenamento de cluster HDInsight.

##<a name="use-datafu-with-pig"></a>Usar DataFu com porco

As etapas desta seção presumem que você estiver familiarizado com porco em HDInsight e fornece somente as instruções de porco latino, não as etapas sobre como usá-las com o cluster. Para obter mais informações sobre como usar porco com HDInsight, consulte [Usar porco com HDInsight](hdinsight-use-pig.md).

> [AZURE.IMPORTANT] Ao usar DataFu de porco em um cluster baseado no Linux HDInsight, você deve primeiro registrar o arquivo jar usando a seguinte instrução de porco latino:
>
> ```register wasbs:///example/jars/datafu-1.2.0.jar```
>
> DataFu é registrado por padrão em clusters de HDInsight baseados no Windows.

Geralmente, você irá definir um alias para funções DataFu. Por exemplo:

    DEFINE SHA datafu.pig.hash.SHA();
    
Define um alias nomeado `SHA` para o SHA hash função. Em seguida, você pode usar isso em um script de porco latino para gerar um hash para os dados de entrada. Por exemplo, o seguinte substitui os nomes nos dados de entrada com um valor de hash:

    raw = LOAD '/data/raw/' USING PigStorage(',') AS  
        (name:chararray, 
        int1:int, 
        int2:int,
        int3:int); 
    mask = FOREACH raw GENERATE SHA(name), int1, int2, int3; 
    DUMP mask;

Se isso for usado com os seguintes dados de entrada:

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5
    
Ele gerará o seguinte resultado:

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

##<a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre DataFu ou porco, consulte os seguintes documentos:

* [Guia de DataFu porco Apache](http://datafu.incubator.apache.org/docs/datafu/guide.html).

* [Usar porco com HDInsight](hdinsight-use-pig.md)
