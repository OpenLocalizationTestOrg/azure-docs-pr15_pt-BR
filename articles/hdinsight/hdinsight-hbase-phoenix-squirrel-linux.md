<properties 
   pageTitle="Use Apache rio e esquilo em HDInsight | Microsoft Azure" 
   description="Saiba como usar rio Apache no HDInsight e como instalar e configurar esquilo em sua estação de trabalho para se conectar a um cluster de HBase em HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Use Apache rio com clusters baseados em Linux HBase no HDInsight  

Saiba como usar [Rio Apache](http://phoenix.apache.org/) no HDInsight e como usar o SQLLine. Para obter mais informações sobre rio, consulte [Rio em 15 minutos ou menos](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Para a gramática rio, consulte [Rio gramática](http://phoenix.apache.org/language/index.html).

>[AZURE.NOTE] As informações de versão do rio no HDInsight, consulte [Novidades nas versões Hadoop cluster fornecidas pelo HDInsight?] [hdinsight-versions].

##<a name="use-sqlline"></a>Use SQLLine
[SQLLine](http://sqlline.sourceforge.net/) é um utilitário de linha de comando para executar SQL. 

###<a name="prerequisites"></a>Pré-requisitos
Antes de poder usar SQLLine, você deve ter o seguinte:

- **HBase um cluster em HDInsight**. Para obter informações sobre como provisionar HBase cluster, consulte [Introdução ao Apache HBase em HDInsight][hdinsight-hbase-get-started].
- **Conectar-se ao cluster HBase através do protocolo de desktop remoto**. Para obter instruções, consulte [Gerenciar Hadoop clusters em HDInsight usando o Portal de clássico do Azure][hdinsight-manage-portal].


Quando você se conectar a um cluster de HBase, você precisará se conectar a um as Zookeepers. Cada cluster HDInsight possui 3 Zookeepers. 

**Para descobrir o nome do host Zookeeper**

1. Abra Ambari, navegue até **https://<ClusterName>. azurehdinsight.net**.
2. Insira o nome de usuário HTTP (cluster) e a senha para efetuar login.
3. Clique em **ZooKeeper** no menu à esquerda. Você deverá ver 3 **ZooKeeper servidor** listado.
4. Clique em um **Servidor ZooKeeper** listados. No painel de resumo, localize o **nome do host**. Ele é semelhante a *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Usar SQLLine**

1. Conectar-se ao cluster usando SSH. Para obter instruções, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ou [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md) , dependendo do seu sistema operacional do computador cliente.

2. A partir do SSH, execute os seguintes comandos para executar SQLLine:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ClusterName>:2181:/hbase-unsecure

2. Execute os seguintes comandos para criar uma tabela de HBase e inserir alguns dados:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
    
        !tables
        
        UPSERT INTO Company VALUES(1, 'Microsoft');
        
        SELECT * FROM Company;
        
        !quit

Para obter mais informações, consulte o [manual de SQLLine](http://sqlline.sourceforge.net/#manual) e a [Gramática de rio](http://phoenix.apache.org/language/index.html).


 
##<a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como usar rio Apache no HDInsight.  Para saber mais, consulte

- [Visão geral de HDInsight HBase][hdinsight-hbase-overview]: HBase é um Apache, código-fonte aberto, NoSQL banco de dados criado no Hadoop que fornece acesso aleatório e consistência forte para grandes quantidades de dados não estruturados e semi-estruturados.
- [Provisionar HBase clusters em rede Virtual do Azure][hdinsight-hbase-provision-vnet]: com integração de rede virtual, HBase clusters podem ser implantados à mesma rede virtual como seus aplicativos para que os aplicativos possam se comunicar com HBase diretamente.
- [Replicação de configurar HBase em HDInsight](hdinsight-hbase-geo-replication.md): Saiba como configurar HBase replicação entre dois centros de dados Azure. 
- [Analisar sentimento Twitter com HBase em HDInsight][hbase-twitter-sentiment]: Saiba como executar [análise sentimento](http://en.wikipedia.org/wiki/Sentiment_analysis) em tempo real de dados grandes usando HBase em um cluster de Hadoop no HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 
