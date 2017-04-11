<properties
pageTitle="Adicionar bibliotecas de seção durante a criação de cluster HDInsight | Azure"
description="Aprenda a adicionar bibliotecas de seção (arquivos jar), a um cluster de HDInsight durante a criação de cluster."
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
ms.date="09/20/2016"
ms.author="larryfr"/>

#<a name="add-hive-libraries-during-hdinsight-cluster-creation"></a>Adicionar bibliotecas de seção durante a criação de cluster HDInsight

Se você tiver bibliotecas que você usa com frequência com seção em HDInsight, este documento contém informações sobre como usar uma ação de Script para carregar previamente as bibliotecas durante a criação de cluster. Isso disponibiliza as bibliotecas globalmente na seção (sem precisar usar [Adicionar JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) carregá-las.)

##<a name="how-it-works"></a>Como ele funciona

Ao criar um cluster, opcionalmente, você pode especificar uma ação de Script que executa um script em nós do cluster enquanto estão sendo criados. O script neste documento aceita um único parâmetro, que é um local de WASB que contém as bibliotecas (armazenadas como arquivos jar,) que serão carregadas previamente.

Durante a criação de cluster, o script enumerar os arquivos, copiá-los para o `/usr/lib/customhivelibs/` diretório em nós de cabeça e trabalhador, em seguida, adiciona-los para o `hive.aux.jars.path` propriedade na `core-site.xml` arquivo. Em clusters baseados em Linux, ele também atualiza a `hive-env.sh` arquivo com o local dos arquivos.

> [AZURE.NOTE] Usando as ações de script neste artigo torna as bibliotecas disponíveis nas seguintes situações:
>
> * __HDInsight baseados em Linux__ - ao usar a __seção de linha de comando__, __WebHCat__ (Templeton) e __HiveServer2__.
> * __Baseado no Windows HDInsight__ - ao usar a __seção de linha de comando__ e __WebHCat__ (Templeton).

##<a name="the-script"></a>O script

__Local de script__

Para __clusters baseados em Linux__: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Para __clusters baseados no Windows__: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

__Requisitos__

* Os scripts devem ser aplicados a __nós de cabeça__ e __nós de trabalho__.

* Os jars que você deseja instalar devem ser armazenados no armazenamento de Blob do Azure em um __único contêiner__. 

* A conta de armazenamento que contém a biblioteca de jar arquivos __deve__ ser vinculado ao cluster HDInsight durante a criação. Isso pode ser feito em uma das duas maneiras:

    * Por ser em um contêiner da conta de armazenamento padrão para o cluster.
    
    * Por ser em um contêiner em um contêiner de armazenamento vinculado. Por exemplo, no portal do, você pode usar __Configuração opcional__, __contas de armazenamento vinculado__ para adicionar armazenamento adicional.

* O caminho WASB ao contêiner deve ser especificado como um parâmetro para a ação de Script. Por exemplo, se os jars são armazenados em um contêiner chamado __as bibliotecas__ em uma conta de armazenamento denominada __mystorage__, o parâmetro seria __wasbs://libs@mystorage.blob.core.windows.net/__.

    > [AZURE.NOTE] Este documento supõe que você já tem uma conta de armazenamento de criação, blob contêiner e os arquivos carregados para ele. 
    >
    > Se você não tiver criado uma conta de armazenamento, você pode fazer isso por meio do [portal do Azure](https://portal.azure.com). Em seguida, você pode usar um utilitário como [Gerenciador de armazenamento do Azure](http://storageexplorer.com/) para criar um novo recipiente na conta e carregar arquivos para lá.

##<a name="create-a-cluster-using-the-script"></a>Criar um cluster usando o script

> [AZURE.NOTE] As etapas a seguir criam um novo cluster HDInsight baseados em Linux. Para criar um novo cluster baseado no Windows, selecione __Windows__ como cluster SO quando criar o cluster e use o script do Windows (PowerShell) em vez do script bash.
> 
> Você também pode usar o PowerShell do Azure ou o SDK do .NET HDInsight para criar um cluster usando esse script. Para obter mais informações sobre como usar esses métodos, consulte [Personalizar HDInsight clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Iniciar um cluster de provisionamento usando as etapas em [clusters provisionar HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), mas não concluída provisionamento.

2. Na lâmina **Configuração opcional** , selecione **Ações de Script**e forneça as informações conforme mostrado abaixo:

    * __Nome__: digite um nome amigável para a ação de script.
    * __URI de SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
    * __Cabeça__: marque esta opção
    * __TRABALHADOR__: marque esta opção.
    * __ZOOKEEPER__: deixe em branco.
    * __Parâmetros__: insira o endereço WASB para a conta de armazenamento e o recipiente que contém os jars. Por exemplo, __wasbs://libs@mystorage.blob.core.windows.net/__.

3. Na parte inferior das **Ações de Script**, use o botão **Selecionar** para salvar a configuração.

4. Na lâmina **Configuração opcional** , selecione __Contas de armazenamento vinculadas__ e selecione o link __Adicionar uma chave de armazenamento__ . Selecione a conta de armazenamento que contém os jars e, em seguida, use os botões de __Selecionar__ para salvar as configurações e retornar a lâmina de __Configuração opcional__ .

5. Use o botão **seleção** na parte inferior da lâmina **Configuração opcional** para salvar as informações de configuração opcional.

6. Continue cluster de provisionamento, conforme descrito em [clusters provisionar HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

Depois de concluir a criação do cluster, você poderá usar os jars adicionados por meio desse script na seção sem precisar usar o `ADD JAR` instrução.

##<a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como adicionar bibliotecas de seção contidas em arquivos jar para um cluster de HDInsight durante a criação de cluster. Para obter mais informações sobre como trabalhar com seção, consulte [Usar seção com HDInsight](hdinsight-use-hive.md)
