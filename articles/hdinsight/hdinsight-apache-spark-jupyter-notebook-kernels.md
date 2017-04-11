<properties 
    pageTitle="Disponível com blocos de anotações de Jupyter em HDInsight Spark kernels clusters no Linux | Microsoft Azure" 
    description="Saiba mais sobre os kernels de bloco de anotações de Jupyter adicionais disponíveis com cluster Spark no HDInsight Linux." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="kernels-available-for-jupyter-notebooks-with-apache-spark-clusters-on-hdinsight-linux"></a>Kernels disponíveis para blocos de anotações de Jupyter com clusters Spark Apache no HDInsight Linux

Cluster Apache Spark em HDInsight (Linux) inclui Jupyter blocos de anotações que você pode usar para testar seus aplicativos. Um núcleo é um programa que é executado e interprete seu código. HDInsight Spark clusters fornecem dois kernels que você pode usar com o bloco de anotações de Jupyter. Estas são:

1. **PySpark** (para aplicativos escritos em Python)
2. **Spark** (para aplicativos escritos em Scala)

Neste artigo, você aprenderá sobre como usar esses kernels e quais são os benefícios da usá-los.

**Pré-requisitos:**

Você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster de Apache Spark no HDInsight Linux. Para obter instruções, consulte [criar Apache Spark clusters no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="how-do-i-use-the-kernels"></a>Como usar os kernels? 

1. No [Portal do Azure](https://portal.azure.com/), do startboard, clique no bloco para o seu cluster Spark (se você fixados-lo a startboard). Você também pode navegar para o seu cluster em **Procurar tudo** > **HDInsight Clusters**.   

2. Da lâmina cluster Spark, clique em **Painel de Cluster**e clique em **Bloco de anotações de Jupyter**. Se solicitado, digite as credenciais de administrador para o cluster.

    > [AZURE.NOTE] Você também pode atingir o bloco de anotações de Jupyter para o seu cluster abrindo a seguinte URL no seu navegador. Substitua o nome do seu cluster __CLUSTERNAME__ :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo bloco de anotações com os kernels de novo. Clique em **novo**e, em seguida, clique em **Pyspark** ou **Spark**. Você deve usar o núcleo Spark para aplicativos de Scala e PySpark núcleo para aplicativos de Python.

    ![Criar um novo bloco de anotações de Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Criar um novo bloco de anotações de Jupyter") 

3. Isso deve abrir um novo bloco de anotações com o núcleo selecionada.

## <a name="why-should-i-use-the-pyspark-or-spark-kernels"></a>Por que devo usar os kernels PySpark ou Spark?

Aqui estão algumas vantagens de usar os novos kernels.

1. **Predefinir contextos**. Com o **PySpark** ou kernels **Spark** que são fornecidos com blocos de anotações de Jupyter, você não precisa definir os contextos Spark ou seção explicitamente antes de começar a trabalhar com o aplicativo que você está desenvolvendo; eles estão disponíveis para você por padrão. Nesses contextos são:

    * **sc** - contexto Spark
    * **sqlContext** - seção contexto


    Portanto, você não precisa executar instruções como a definir os contextos a seguir:

        ###################################################
        # YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
        ###################################################
        sc = SparkContext('yarn-client')
        sqlContext = HiveContext(sc)

    Em vez disso, você pode usar diretamente os contextos predefinidos em seu aplicativo.
    
2. **Magics de célula**. O núcleo PySpark fornece alguns predefinidos "magics", que são comandos especiais que você pode chamar com `%%` (por exemplo, `%%MAGIC` <args>). O comando mágico deve ser a primeira palavra em uma célula de código e permitir para várias linhas de conteúdo. A palavra mágica deve ser a primeira palavra na célula. Adicionar qualquer coisa antes da magia, até mesmo comentários, causará um erro.   Para obter mais informações sobre magics, veja [aqui](http://ipython.readthedocs.org/en/stable/interactive/magics.html).

    A tabela a seguir lista os diferentes magics disponíveis por meio dos kernels.

  	| Mágico     | Exemplo                         | Descrição  |
  	|-----------|---------------------------------|--------------|
  	| Ajuda      | `%%help`                            | Gera uma tabela de todas as magics disponíveis com exemplo e descrição |
  	| informações      | `%%info`                          | Informações de sessão de saídas para o ponto de extremidade Livy atual |
  	| Configurar | `%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} | Configura os parâmetros para a criação de uma sessão. O sinalizador força (-f) é obrigatório se uma sessão já foi criada e a sessão será descartada e recriada. Examine [/sessions de POSTAGEM de Livy corpo da solicitação](https://github.com/cloudera/livy#request-body) para obter uma lista de parâmetros válidos. Parâmetros devem ser passados como uma cadeia de JSON e devem ser na próxima linha após a magia, conforme mostrado no exemplo a coluna. |
  	| SQL       |  `%%sql -o <variable name>`<br> `SHOW TABLES`    | Executa uma consulta de seção em relação a sqlContext. Se o `-o` parâmetro é passado, o resultado da consulta é mantido na % contexto Python local como um dataframe [Pandas](http://pandas.pydata.org/) .   |
  	| local     |     `%%local`<br>`a=1`              | Todo o código nas linhas subsequentes será executado localmente. Código deve ser código Python válido. |
  	| logs      | `%%logs`                        | Saídas os logs da sessão Livy atual.  |
  	| Excluir    | `%%delete -f -s <session number>` | Exclui uma sessão específica do ponto de extremidade Livy atual. Observe que não é possível excluir a sessão é iniciada para o núcleo em si. |
  	| limpeza   | `%%cleanup -f`                    | Exclui todas as sessões da empresa Livy atual, incluindo a sessão deste bloco de anotações. O sinalizador -f Forçar é obrigatório.  |

    >[AZURE.NOTE] Além do magics adicionados pelo núcleo PySpark, você também pode usar o [magics de IPython internos](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), incluindo `%%sh`. Você pode usar o `%%sh` mágico para executar scripts e bloco de código em headnode o cluster. 

3. **Visualização de automático**. O núcleo **Pyspark** automaticamente visualiza a saída de seção e as consultas SQL. Você tem a opção para escolher entre vários tipos diferentes de visualizações incluindo tabela, pizza, linha, área, barra.

## <a name="parameters-supported-with-the-sql-magic"></a>Parâmetros compatíveis com o % mágico de sql

O % sql mágico suporta parâmetros diferentes que você pode usar para controlar o tipo de saída que recebe quando você executa consultas. A tabela a seguir lista a saída.

| Parâmetro     | Exemplo                         | Descrição  |
|-----------|---------------------------------|--------------|
| -o      | `-o <VARIABLE NAME>`                          | Use este parâmetro para manter o resultado da consulta, no % contexto Python local, como um dataframe [Pandas](http://pandas.pydata.org/) . O nome da variável dataframe é o nome da variável que você especifica. |
| -p      | `-q`                          | Use isto para desativar visualizações para a célula. Se você não deseja automático-visualizar o conteúdo de uma célula e deseja capturá-lo como um dataframe e use apenas `-q -o <VARIABLE>`. Se você deseja desativar visualizações sem capturando os resultados (por exemplo, para executar uma consulta SQL com efeitos de lado, como um `CREATE TABLE` instrução), basta usar `-q` sem especificar um `-o` argumento. |
| -m       |  `-m <METHOD>`    | Onde o **método** é **levar** ou **amostra** (o padrão é **levar**). Se o método é **levar**, o núcleo seleciona os elementos da parte superior do conjunto de dados de resultado especificado pelo MAXROWS (descrito posteriormente nesta tabela). Se o método for **amostra**, o núcleo aleatoriamente capturará elementos do conjunto de dados de acordo com `-r` parâmetro, descrito a seguir nesta tabela.   |
| -r     |     `-r <FRACTION>`            | Aqui **fração** é um número de ponto flutuante entre 0,0 e 1,0. Se o método de amostra para a consulta SQL é `sample`, e em seguida, o núcleo amostras aleatoriamente a fração especificada dos elementos do resultado definidas para você; Por exemplo, se você executar uma consulta SQL com os argumentos `-m sample -r 0.01`, e em seguida, 1% das linhas resultado será amostragem aleatoriamente. |
| -n      | `-n <MAXROWS>`                        | **MAXROWS** é um valor inteiro. O núcleo limita o número de linhas de saída para **MAXROWS**. Se **MAXROWS** for um número negativo como **-1**, o número de linhas no conjunto de resultados não serão limitado. |

**Exemplo:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2 
    SELECT * FROM hivesampletable

A instrução acima faz o seguinte:

* Seleciona todos os registros de **hivesampletable**.
* Porque usamos - p, ele desabilitará autovisualização.
* Porque usamos `-m sample -r 0.1 -n 500` aleatoriamente amostras de 10% das linhas da hivesampletable e limita o tamanho do resultado definido para 500 linhas.
* Por fim, como usamos `-o query2` também salva a saída em uma dataframe chamado **consulta2**.
    

## <a name="considerations-while-using-the-new-kernels"></a>Considerações ao usar os novos kernels

Independentemente núcleo você usar (PySpark ou Spark), deixando os blocos de anotações em execução irá consumir os recursos de cluster.  Com estas kernels, pois os contextos forem predefinidos, basta sair os blocos de anotações não eliminar o contexto e, portanto, aos recursos de cluster continuará a estar em uso. Uma boa prática com os kernels PySpark e Spark seria usar a opção **Fechar e parar** no menu de **arquivo** do bloco de anotações. Isso elimina o contexto e, em seguida, sai do bloco de anotações.    


## <a name="show-me-some-examples"></a>Mostre-me alguns exemplos

Quando você abre um bloco de anotações de Jupyter, você verá duas pastas disponíveis no nível raiz.

* A pasta **PySpark** tem blocos de anotações de amostra que usam a nova **Python** .
* A pasta **Scala** tem blocos de anotações de amostra que usam a nova **Spark** .

Você pode abrir o bloco de anotações de **00 - [LEIA-ME primeiro] Spark mágico núcleo recursos** da pasta **PySpark** ou **Spark** para saber mais sobre as diferentes magics disponíveis. Você também pode usar os outros amostra blocos de anotações disponíveis em duas pastas para saber como obter diferentes cenários usando blocos de anotações de Jupyter com HDInsight Spark clusters.

## <a name="where-are-the-notebooks-stored"></a>Onde são armazenados os blocos de anotações?

Blocos de anotações de Jupyter são salvas na conta de armazenamento associado ao cluster sob a pasta de **/HdiNotebooks** .  Blocos de anotações, arquivos de texto e pastas que você criar no Jupyter será acessíveis a partir do WASB.  Por exemplo, se você usar Jupyter para criar uma pasta **minhapasta** e um bloco de anotações **myfolder/mynotebook.ipynb**, você pode acessar esse bloco de anotações no `wasbs:///HdiNotebooks/myfolder/mynotebook.ipynb`.  O inverso também é verdadeiro, ou seja, se você carregar um bloco de anotações diretamente para sua conta de armazenamento em `/HdiNotebooks/mynotebook1.ipynb`, o bloco de anotações será visível de Jupyter também.  Blocos de anotações permanecerá na conta de armazenamento, mesmo após o cluster é excluído.

A maneira como os blocos de anotações são salvas na conta de armazenamento é compatível com HDFS. Portanto, se você SSH em cluster que você pode usar o arquivo de comandos de gerenciamento como o seguinte:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                 # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter


Caso haja problemas ao acessar a conta de armazenamento para o cluster, os blocos de anotações também são salvos na headnode `/var/lib/jupyter`.

## <a name="supported-browser"></a>Navegador com suporte
Blocos de anotações de Jupyter executando contra HDInsight Spark clusters são suportados apenas no Google Chrome.

## <a name="feedback"></a>Comentários

Os novos kernels estão em evolução estágio e serão desenvolver ao longo do tempo. Isso também poderia significa que APIs pode mudar à medida que esses kernels desenvolver. Seria Agradecemos quaisquer comentários que você tenha enquanto você usa esses kernels novo. Isso será muito útil para a versão final destas kernels de modelagem. Você pode deixar seus comentários/comentários na seção **comentários** na parte inferior deste artigo.


## <a name="seealso"></a>Consulte também


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

* [Usar os pacotes externos com blocos de anotações de Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter no seu computador e se conectar a um cluster de HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar recursos para cluster Spark Apache no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Rastrear e depurar trabalhos em execução em um cluster de Apache Spark em HDInsight](hdinsight-apache-spark-job-debugging.md)
