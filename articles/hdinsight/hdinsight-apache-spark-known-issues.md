<properties 
    pageTitle="Problemas de Spark Apache no HDInsight conhecidos | Microsoft Azure" 
    description="Problemas conhecidos do Apache Spark em HDInsight." 
    services="hdinsight" 
    documentationCenter="" 
    authors="mumian" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="nitinme"/>

# <a name="known-issues-for-apache-spark-cluster-on-hdinsight-linux"></a>Problemas conhecidos para cluster Spark Apache no HDInsight Linux

Este documento controla os todos os problemas conhecidos do Preview público HDInsight Spark.  

##<a name="livy-leaks-interactive-session"></a>Livy vazamentos sessão interativa
 
Quando Livy é reiniciado com uma sessão interativa (do Ambari ou devido a reinicialização de máquina virtual headnode 0) ainda ativa, uma sessão de trabalho interativo será perdida. Por isso, novos trabalhos podem preso no estado aceitos e não podem ser iniciados.

**Atenuação:**

Use o procedimento a seguir para solucionar o problema:

1. SSH em headnode. 
2. Execute o seguinte comando para encontrar as IDs de aplicativo dos trabalhos interativos iniciados através de Livy. 

        yarn application –list

    O trabalho de padrão nomes serão Livy se os trabalhos foram iniciados com uma sessão interativa Livy sem explícita nomes especificado, para o Livy sessão iniciada pelo bloco de anotações de Jupyter, o nome do trabalho será iniciado com remotesparkmagics_ *. 

3. Execute o seguinte comando para eliminar esses trabalhos. 

        yarn application –kill <Application ID>

Novos trabalhos começarão a execução. 

##<a name="spark-history-server-not-started"></a>Servidor de histórico Spark não iniciado 

Servidor de histórico Spark não é iniciado automaticamente após um cluster é criado.  

**Atenuação:** 

Inicie manualmente o servidor de histórico de Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problema de permissão no diretório de log Spark 

Quando hdiuser envia um trabalho com spark-submit, há um erro java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (permissão negado) e o log de driver não está escrito. 

**Atenuação:**
 
1. Adicione hdiuser ao grupo Hadoop. 
2. Forneça 777 permissões em /var/log/spark após a criação de cluster. 
3. Atualize a localização do log de spark usando Ambari para ser um diretório com permissões de 777.  
4. Executar spark enviar como sudo.  

## <a name="issues-related-to-jupyter-notebooks"></a>Problemas relacionados ao Jupyter blocos de anotações

A seguir estão alguns problemas conhecidos relacionados à Jupyter blocos de anotações.


### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Blocos de anotações com caracteres não-ASCII em nomes de arquivos

Blocos de anotações de Jupyter que podem ser usados em clusters Spark HDInsight não devem ter caracteres não-ASCII em nomes de arquivos. Se você tentar carregar um arquivo por meio da UI Jupyter que tem um nome de arquivo não-ASCII, ele falhará silenciosamente (ou seja, Jupyter não permitem que você carregar o arquivo, mas ela não acionar um erro visível ou). 

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Erro ao carregar os blocos de anotações de tamanhos maiores

Talvez você veja um erro **`Error loading notebook`** quando você carrega os blocos de anotações são maiores em tamanho.  

**Atenuação:**

Se você receber esse erro, não significa que seus dados estão corrompido ou perdido.  Seus blocos de anotações ainda estão no disco no `/var/lib/jupyter`, e você pode SSH em cluster acessá-los. Você pode copiar seus blocos de anotações do seu cluster para sua máquina local (usando SCP ou WinSCP) como um backup para evitar a perda de quaisquer dados importantes no bloco de anotações. Você pode então túnel SSH em seu headnode na porta 8001 acessem Jupyter sem passar pelo gateway.  A partir daí, você pode desmarcar a saída de seu bloco de anotações e salvá-lo para diminuir o tamanho do bloco de anotações novamente.

Para evitar esse erro aconteça no futuro, você deve seguir algumas práticas recomendadas:

* É importante manter pequeno o tamanho do bloco de anotações. Qualquer saída seus trabalhos Spark que é enviada à Jupyter é mantida no bloco de anotações.  É uma prática recomendada com Jupyter em geral para evitar a execução `.collect()` em RDD grande ou dataframes; em vez disso, se desejar inspecionar conteúdo de um RDD, considere a execução `.take()` ou `.sample()` para que a saída não cheguem grande demais.
* Além disso, quando você salva um bloco de anotações, desmarque todos saída células para reduzir o tamanho.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Inicialização do bloco de anotações demora mais do que o esperado 

Primeira instrução de código no bloco de anotações de Jupyter usando mágico Spark pode levar mais de um minuto.  

**Explicação:**
 
Isso acontece porque quando a primeira célula de código é executada. No plano de fundo, isso inicia a configuração da sessão e Spark, SQL e contextos de seção são definidos. Depois de definidos nesses contextos, a primeira instrução é executada e isso dá a impressão de que a instrução levou muito tempo para ser concluída.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Tempo limite do bloco de anotações de Jupyter na criação da sessão

Ao cluster Spark estiver fora do recursos, os kernels Spark e Pyspark no bloco de anotações Jupyter irá tempo limite ao tentar criar a sessão. 

**Reduções:** 

1. Libere alguns recursos no seu cluster Spark por:

    - Interrompendo outros blocos de anotações Spark indo até o menu fechar e interromper ou clicando em desligamento no Explorador de bloco de anotações.
    - Interrompendo outros aplicativos Spark fio COLORIDO.

2. Reinicie o bloco de anotações que você estava tentando de inicialização. Recursos suficientes devem estar disponíveis para você criar uma sessão agora.

##<a name="see-also"></a>Consulte também

* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Spark com BI: executar análise de dados interativos usando Spark em HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark com aprendizado de máquina: Spark de uso em HDInsight para analisar a temperatura de construção usando dados HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark com aprendizado de máquina: Spark de uso em HDInsight prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: Uso Spark no HDInsight para criar aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análise de log de site usando Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos

* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Executar trabalhos remotamente em um cluster de Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Usar o plug-in de ferramentas de HDInsight para IntelliJ IDEIA para criar e enviar Spark Scala aplicativos](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usar o plug-in de ferramentas de HDInsight para IntelliJ IDEIA para depurar aplicativos de Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Usar blocos de anotações de Zeppelin com um cluster Spark em HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponíveis para o bloco de anotações de Jupyter em cluster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usar os pacotes externos com blocos de anotações de Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter no seu computador e se conectar a um cluster de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar recursos para cluster Spark Apache no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Rastrear e depurar trabalhos em execução em um cluster de Apache Spark em HDInsight](hdinsight-apache-spark-job-debugging.md)
