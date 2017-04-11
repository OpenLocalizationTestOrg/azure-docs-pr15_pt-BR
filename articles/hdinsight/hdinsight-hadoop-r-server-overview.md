<properties
    pageTitle="O que é R em HDInsight? Introdução ao servidor de R em HDInsight (visualização) | Microsoft Azure"
    description="O que é o servidor de R em HDInsight (visualização) e como usar o servidor de R para criar aplicativos para análise de dados grande."
    services="hdinsight"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/17/2016"
   ms.author="jeffstok"/>


# <a name="overview-of-r-server-on-hdinsight-preview"></a>Visão geral do servidor de R em HDInsight \(visualização\)

Com o Microsoft Azure HDInsight Premium, Microsoft R Server agora está disponível como uma opção quando você cria HDInsight clusters no Azure. Esse novo recurso fornece cientistas dados estatísticos e programadores R com sob demanda acessem scalable, distribuído métodos de análise de HDInsight.

Clusters podem ser dimensionados para os projetos e tarefas em questão e interrompidas quando eles não são mais necessários. Uma vez que são parte do Azure HDInsight, esses clusters vêm com o suporte de 24/7 de nível empresarial, um SLA de atividade de 99,9% e flexibilidade a integração com outros componentes no ecossistema do Azure.

>[AZURE.NOTE] Servidor de R está disponível somente com HDInsight Premium. Atualmente, HDInsight Premium está disponível somente para clusters Hadoop e Spark. Portanto, atualmente você pode usar R servidor apenas com clusters Hadoop e Spark em HDInsight. Para obter mais informações, consulte [quais são os diferentes níveis de serviço e componentes de Hadoop disponíveis com HDInsight?](hdinsight-component-versioning.md).

Servidor de R em HDInsight fornece os recursos mais recentes para análise baseada em R em grandes conjuntos de dados que são carregados ao armazenamento de Blob do Azure. Como R servidor é criado em Abrir origem R, aplicativos baseados em R que você cria podem aproveitar qualquer um dos pacotes de código-fonte aberto R 8000 +, bem como as rotinas em ScaleR, pacote de análise de grande volume da Microsoft que acompanha o servidor de R.

O nó de borda de clusters Premium fornece um local conveniente para se conectar ao cluster e executar os scripts de R. Com um nó de borda, você tem a opção de execução de funções de distribuído paralelizadas do ScaleR entre as cores do servidor de nó de borda. Você também tem a opção para executá-las em todos os nós do cluster por meio Hadoop mapa reduzir do ScaleR ou Spark calcular contextos.

Os modelos de previsões que resultam de análises pode ser baixado para usam no local. Eles podem também ser operacionalizados em outro lugar no Azure, como através de um [serviço web](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md)do [Azure Studio de aprendizado de máquina](http://studio.azureml.net) .

## <a name="get-started-with-r-on-hdinsight"></a>Introdução ao R em HDInsight

Para incluir R servidor em um cluster de HDInsight, você deve criar um Hadoop ou Spark cluster com a opção Premium quando você cria um cluster usando o portal do Azure. Os dois tipos de cluster usam a mesma configuração, que inclui o servidor de R em nós de um cluster de dados e um nó de borda como uma zona de aterrissagem para análise baseadas em servidor de R. Consulte [Introdução ao servidor de R em HDInsight](hdinsight-hadoop-r-server-get-started.md) para uma passo a passo detalhada sobre como criar um cluster.

## <a name="learn-about-data-storage-options"></a>Saiba mais sobre as opções de armazenamento de dados

Armazenamento padrão para clusters de HDInsight é o armazenamento de Blob com o sistema de arquivos HDFS mapeado para um contêiner de blob. Isso garante que os dados que são carregados ao armazenamento de cluster ou gravados ao armazenamento de cluster durante o curso de análise, é tornado persistente. Você pode usar o utilitário [AzCopy](../storage/storage-use-azcopy.md) para copiar dados de e para o blob.

Além de usar o armazenamento de Blob, você tem a opção de usar o [Armazenamento do Azure dados Lucerne](https://azure.microsoft.com/services/data-lake-store/) com seu cluster. Se você usar Lucerne de dados, em seguida, você pode usar Lucerne de dados e de armazenamento de Blob para armazenamento HDFS.

Você também pode usar [Arquivos do Azure](../storage/storage-how-to-use-files-linux.md) como uma opção de armazenamento para uso no nó borda. Arquivos do Azure permite montar um compartilhamento de arquivo que foi criado no armazenamento do Azure para o sistema de arquivos do Linux. Para obter mais informações sobre opções de armazenamento de dados para servidor de R em cluster HDInsight, consulte [Opções de armazenamento para servidor de R em clusters de HDInsight](hdinsight-hadoop-r-server-storage.md).

## <a name="access-r-server-on-the-cluster"></a>Servidor de R de acesso no cluster

Depois de criar um cluster com R Server, você pode se conectar ao console R no nó borda do cluster por meio de SSH/acabamento. Você também pode conectar através de um navegador se você optar por instalar o IDE de servidor de RStudio opcional no nó borda. Para obter mais informações sobre como instalar o servidor de RStudio, consulte [Instalar o servidor de RStudio em clusters de HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).   

## <a name="develop-and-run-r-scripts"></a>Desenvolver e executar scripts de R

Criar e executar os scripts de R podem usar qualquer um dos pacotes de código-fonte aberto R 8000 + além as rotinas paralelizados e distribuídos na biblioteca ScaleR. Em geral, script que é executado no servidor de R no nó borda é executado dentro o intérprete R nesse nó. A exceção é essas etapas que chamam um ScaleR funcionarem com um contexto de computação que é definido para reduzir de mapa de Hadoop (RxHadoopMR) ou Spark (RxSpark).

Nesses casos, a função é executado em modo distribuído entre esses dados nós (tarefa) do cluster que estão associadas os dados referenciados. Para obter mais informações sobre as opções de contexto de computação diferente, consulte [Opções de contexto para servidor de R em HDInsight Premium de computação](hdinsight-hadoop-r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Colocar em operação um modelo

Quando a modelagem de dados for concluída, você pode colocar em operação o modelo para fazer previsões para novos dados no Azure e locais. Esse processo é conhecido como pontuação. Aqui estão alguns exemplos.

### <a name="score-in-hdinsight"></a>Pontuação na HDInsight

Para pontuação no HDInsight, escreva uma função de R que chama o seu modelo para fazer previsões para um novo arquivo de dados que você já carregou à sua conta de armazenamento. Salve as previsões novamente para a conta de armazenamento. Você pode executar a rotina sob demanda no nó borda do cluster ou usando um trabalho agendado.  

### <a name="score-in-azure-machine-learning"></a>Pontuação de aprendizado de máquina Azure

Para pontuação usando um serviço da web de aprendizado de máquina do Azure, use o pacote de R de aprendizado de máquina do Azure de abrir origem conhecido como [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) para publicar seu modelo como um serviço web Azure. Para sua conveniência, este pacote é vem pré-instalado no nó de borda. Em seguida, use os recursos de aprendizado de máquina para criar uma interface de usuário para o serviço da web e, em seguida, chamar o serviço web para pontuação conforme necessário.

Se você escolher essa opção, você precisará converter os objetos de modelo de ScaleR em objetos do modelo de código-fonte aberto equivalente para uso com o serviço web. Isso pode ser feito por meio do uso funções de coerção de ScaleR, tais como `as.randomForest()` para modelos baseados em ensemble.


### <a name="score-on-premises"></a>Pontuação local

Para pontuação local depois de criar seu modelo, você pode serializar o modelo no R, baixe-o, desserializá-lo e usá-lo para novos dados de pontuação. Você pode pontuação novos dados usando a abordagem descrita anteriormente [pontuação no HDInsight](#scoring-in-hdinsight) ou usando [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Manter o cluster

### <a name="install-and-maintain-r-packages"></a>Instalar e manter pacotes de R

A maioria dos pacotes R que você usar deverão no nó borda desde que a maioria dos seus scripts de R executarão lá. Para instalar pacotes de R adicionais no nó borda, você pode usar o usual `install.packages()` método em R.

Na maioria dos casos, você não precisa instalar pacotes R adicionais em nós dados se você estiver usando apenas rotinas da biblioteca do ScaleR no cluster. No entanto, talvez seja necessário pacotes adicionais para oferecer suporte o uso de **rxExec** ou **RxDataStep** execução em nós de dados.

Nesses casos, os pacotes adicionais devem ser especificados por meio do uso de uma ação de script depois de criar o cluster. Para obter mais informações, consulte [Criando um cluster de HDInsight com o servidor de R](hdinsight-hadoop-r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Alterar configurações de memória Hadoop mapa reduzir

Um cluster pode ser modificado para alterar a quantidade de memória disponível ao servidor de R quando ele está executando um trabalho reduzir o mapa. Para modificar um cluster, use o Apache Ambari interface do usuário que está disponível através do Azure blade portal para o seu cluster. Para obter instruções sobre como acessar a UI Ambari para o seu cluster, consulte [Gerenciar HDInsight clusters usando a interface do usuário do Ambari Web](hdinsight-hadoop-manage-ambari.md).

Também é possível alterar a quantidade de memória que está disponível para servidor de R usando opções de Hadoop na chamada para **RxHadoopMR** da seguinte maneira:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Dimensionar seu cluster

Um cluster existente pode ser dimensionado para cima ou para baixo por meio do portal. Dimensionando, você pode obter a capacidade adicional que você pode precisar para tarefas de processamento maiores ou redimensione volta um cluster quando está ocioso. Para obter instruções sobre como dimensionar um cluster, consulte [Gerenciar HDInsight clusters](hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Manter o sistema

Manutenção é executada nas VMs Linux subjacentes em um cluster de HDInsight expediente para aplicar patches de sistema operacional e outras atualizações. Normalmente, manutenção é feita no 3:30 AM (baseado em hora local para a máquina virtual) cada segunda-feira e quinta-feira. Atualizações são executadas de forma que eles não causam impacto mais de um trimestre do cluster por vez.  

Como os nós de cabeça são redundantes e nem todos os nós de dados são afetados, todos os trabalhos que executam durante esse tempo podem reduzir a velocidade. Eles ainda devem executar para conclusão, porém. Qualquer software personalizado ou dados locais que você tem são preservados entre esses eventos de manutenção, a menos que uma falha grave ocorre que requer uma recriação de cluster.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>Saiba mais sobre opções de IDE para R servidor em um cluster de HDInsight

O nó de borda Linux de um cluster de HDInsight Premium é a zona de aterrissagem para análise baseada em R. Depois de conectar-se ao cluster, você poderá iniciar a interface do console ao servidor de R digitando **R** no prompt de comando Linux. Uso da interface do console é aprimorado se você tiver um editor de texto para desenvolvimento de script R em outra janela e recorta e cola seções do seu script no console de R, conforme necessário.

Uma ferramenta mais sofisticada para o desenvolvimento de script R é IDE baseado em R para uso na área de trabalho, como Microsoft anunciado recentemente [R ferramentas para Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS). Isso é uma família de ferramentas de desktop e servidor de [RStudio](https://www.rstudio.com/products/rstudio-server/). Você também pode usar de baseadas no Eclipse do Walware [StatET](http://www.walware.de/goto/statet).

Outra opção é instalar um IDE no próprio nó de borda de Linux.  Uma opção popular é [RStudio Server](https://www.rstudio.com/products/rstudio-server/), que fornece um IDE baseadas no navegador para uso por clientes remotos. Instalando o servidor de RStudio no nó borda de um cluster de HDInsight Premium fornece uma experiência IDE completa para o desenvolvimento e a execução de scripts de R com R Server no cluster. Ele pode ser consideravelmente mais produtivo do console de R.  Se você quiser usar RStudio Server, consulte [Instalação RStudio Server em clusters de HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).

## <a name="learn-about-pricing"></a>Saiba mais sobre preços

As taxas que estão associadas um cluster de HDInsight Premium com R Server são estruturadas da mesma forma para as taxas para os clusters HDInsight padrão. Eles se baseiam o dimensionamento das VMs subjacentes entre o nome, dados e nós de borda, com a adição de um acréscimo core horas para Premium. Para obter mais informações sobre HDInsight Premium preços, incluindo preços durante Public Preview e a disponibilidade de uma avaliação gratuita de 30 dias, consulte [HDInsight preços](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Próximas etapas

Siga os links abaixo para ler mais sobre como usar o servidor de R com clusters de HDInsight.

- [Introdução ao servidor de R em HDInsight](hdinsight-hadoop-r-server-get-started.md)

- [Adicionar o servidor de RStudio HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)

- [Calcular opções de contexto do servidor de R em HDInsight (prévia)](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure opções de armazenamento do servidor de R em HDInsight Premium](hdinsight-hadoop-r-server-storage.md)
