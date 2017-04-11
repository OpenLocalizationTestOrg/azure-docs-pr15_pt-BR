<properties
    pageTitle="Quais são os diferentes componentes disponíveis com um cluster de HDInsight? | Microsoft Azure"
    description="HDInsight oferece suporte a vários componentes de cluster Hadoop implantadas e versões. Consulte as versões de distribuição Hadoop e HortonWorks dados plataforma (HDP) com suporte."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    tags="azure-portal"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="jgao"/>


# <a name="what-are-the-different-hadoop-components-available-with-hdinsight"></a>Quais são os diferentes componentes de Hadoop disponíveis com HDInsight?

Saiba mais sobre os diferentes níveis de serviço oferecidos pelo HDInsight, bem como as versões dos componentes do hadoop diferentes incluídos no HDInsight.

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard e Premium de HDInsight

Azure HDInsight fornece as ofertas de nuvem de grande volume em duas categorias: **Standard** e **Premium**. A tabela abaixo seção lista os recursos que estão disponíveis **apenas como parte do Premium**. Recursos que não sejam explicitamente chamados na tabela aqui estão disponíveis como parte do padrão.

>[AZURE.NOTE] A oferta de HDInsight Premium está na visualização e está disponível somente para clusters Linux.

| Recurso HDInsight Premium | Descrição |
|--------------|---------------|
| Domínio HDInsight clusters       | Ingresse HDInsight clusters para domínios do Azure Active Directory (AAD) para segurança de nível empresarial. Agora você pode configurar uma lista de funcionários da sua empresa que pode autenticar por meio do Active Directory do Azure para fazer logon no cluster HDInsight. O administrador da empresa também pode configurar o controle de acesso baseado em função para seção segurança usando [Apache Ranger](http://hortonworks.com/apache/ranger/), assim como restringir o acesso aos dados apenas tanto quanto necessário. Por fim, o administrador pode auditar os dados acessados por funcionários e qualquer alteração feita às políticas de controle de acesso, portanto, alcançar um alto grau de gestão de seus recursos corporativos. Para obter mais informações, consulte [Configurar domínio HDInsight clusters](hdinsight-domain-joined-configure).

### <a name="cluster-types-supported-for-premium"></a>Tipos de cluster com suporte para Premium

A tabela a seguir lista o tipo de cluster HDInsight e matriz de suporte Premium.

| Tipo de cluster | Padrão  | Premium |
|--------------|---------------|--------------|
| Hadoop       | Sim           | Sim (somente HDInsight 3.5)         |
| Spark        | Sim           | Não          |
| HBase        | Sim           | Não           |
| Tempestade        | Sim           | Não           |
| Seção interativa (prévia) | Sim | Não |
| R Server (demonstração) | Sim | Não |

Esta tabela será atualizada como mais tipos de cluster estão incluídos no HDInsight Premium.

### <a name="pricing-and-sla"></a>Preços e SLA

Para obter informações sobre preços e SLA para Premium de HDInsight, consulte [HDInsight preços](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>Componentes de Hadoop disponíveis com diferentes versões do HDInsight

Azure HDInsight oferece suporte a várias versões de cluster Hadoop que podem ser implantadas em qualquer momento. Cada opção de versão cria uma versão específica da distribuição Hortonworks dados plataforma (HDP) e um conjunto de componentes contidos que distribuição. As versões de componente associadas a versões de cluster HDInsight são detalhadas na tabela a seguir. Observe que a versão de cluster padrão usada pelo Azurehdinsight está atualmente 3.4 e, a partir de 14/09/2016, com base em 2,4 HDP.

> [AZURE.NOTE] A versão padrão do serviço pode mudar sem aviso prévio. Recomendamos que você especificar a versão quando você cria clusters usando .NET SDK/Azure PowerShell e CLI do Azure, se você tiver uma dependência de versão. 

Componente|HDInsight versão 3.5 | HDInsight versão 3.4 (padrão) | HDInsight versão 3.3 | Versão de HDInsight 3,2 |HDInsight versão 3.1 |HDInsight versão 3.0|
---|---|---|---|---|---|---
Plataforma de dados de Hortonworks|2.5|2,4|2.3|2.2|2.1.7|2.0|
Fio COLORIDO & Apache Hadoop|2.7.3|2.7.1|2.7.1|2.6.0|2.4.0|2.2.0|
Apache Tez|0.7.0|0.7.0|0.7.0 | 0.5.2|0.4.0||
Apache porco|0.16.0|0.15.0|0.15.0|0.14.0|0.12.1|0.12.0|
HCatalog & seção Apache|1.2.1.2.5|1.2.1|1.2.1|0.14.0|0.13.1|0.12.0|
Apache HBase |1.1.2|1.1.2|1.1.1|0.98.4|0.98.0||
Apache Sqoop|1.4.6|1.4.6|1.4.6|1.4.5|1.4.4|1.4.4|1.4.3
Apache Oozie|4.2.0|4.2.0|4.2.0|versão 4.1.0|4.0.0|4.0.0|
Apache Zookeeper|3.4.6|3.4.6|3.4.6|3.4.6|3.4.5|3.4.5|
Apache tempestade|1.0.1|0.10.0|0.10.0|0.9.3|0.9.1||
Apache Mahout|0.9.0+|0.9.0+|0.9.0+|0.9.0|0.9.0||
Rio de Apache|4.7.0|4.4.0|4.4.0|4.2.0|4.0.0.2.1.7.0-2162||
Apache Spark|1.6.2 + 2.0 (somente Linux)|1.6.0 (somente Linux)|1.5.2 (Linux somente/Experimental build)|1.3.1 (somente Windows)|||

**Obter informações de versão atual do componente**

As versões de componente associadas a versões de cluster HDInsight podem mudar no futuro atualizações para HDInsight. Uma maneira para determinar os componentes disponíveis e para verificar quais versões estão sendo usadas para um cluster é usar a API REST de Ambari. O comando **GetComponentInformation** pode ser usado para recuperar informações sobre um componente de serviço. Para obter detalhes, consulte a [documentação de Ambari][ambari-docs]. Outra maneira de obter essas informações é fazer logon um cluster usando a área de trabalho remota e examinar o conteúdo do "C:\apps\dist\" diretório diretamente.


**Notas de versão**

Consulte [HDInsight notas de versão](hdinsight-release-notes.md) para notas de versão adicionais nas versões mais recentes do HDInsight.


## <a name="supported-hdinsight-versions"></a>Versões de HDInsight com suporte
A tabela a seguir lista as versões do HDInsight disponível no momento, as versões de plataforma de dados Hortonworks correspondentes que usar e suas datas de lançamento. Quando conhecido, suas datas de vencimento e substituição de suporte também são fornecidas. Observe o seguinte:

* Altamente disponíveis clusters com dois nós de cabeça são implantadas por padrão para HDInsight 2.1 e acima. Eles não estão disponíveis para clusters 1,6 HDInsight.
* Depois que o suporte expirou para uma versão específica, ele pode não estar disponível por meio do portal Azure. A tabela a seguir indica quais versões estão disponíveis no Portal de clássico do Azure. Versões de cluster continuará disponível usando o `Version` parâmetro no comando do Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) e o SDK do .NET até a sua data de substituição.

Versão de HDInsight|Versão HDP|SISTEMA OPERACIONAL DE MÁQUINA VIRTUAL|Alta disponibilidade|Data do lançamento|Disponível no portal do Azure|Data de expiração de suporte|Data de substituição
---|---|---|---|---|---|---|---
HDI 3.5 | HDP 2.5| Ubuntu 16 | Sim | 30/9/2016 | Sim | 
HDI 3.4|HDP 2,4|Ubuntu 14.0.4 LTS|Sim|29/03/2016|Sim|29/12/2016|9/1/2018|
HDI 3.3|HDP 2.3|Ubuntu 14.0.4 2012R2 LTS ou o Windows Server|Sim|12/02/2015|Sim|27/06/2016|31/07/2017
HDI 3,2|HDP 2.2|Ubuntu 12.04 2012R2 LTS ou o Windows Server|Sim|18/2/2015|Sim|1/3/2016|04/01/2017
HDI 3.1|HDP 2.1|Windows Server 2012R2|Sim|24/6/2014|Não|18/05/2015|30/06/2016
HDI 3.0|HDP 2.0|Windows Server 2012R2|Sim|02/11/2014|Não|17/09/2014|30/06/2015
HDI 2.1|HDP 1.3|Windows Server 2012R2|Sim|28/10/2013|Não|05/12/2014|31/05/2015
HDI 1,6|HDP 1.1||Não|28/10/2013|Não|26/04/2014|31/05/2015

**Implantação de clusters não padrão**

### <a name="the-service-level-agreement-for-hdinsight-cluster-versions"></a>O contrato de nível de serviço para as versões de cluster HDInsight

O SLA é definido em termos de uma "janela de suporte". Uma janela de suporte se refere ao período de tempo que uma versão de cluster HDInsight é suportada pelo suporte e atendimento ao cliente Microsoft. Um cluster de HDInsight está fora da janela de suporte se sua versão tem uma **Data de expiração de suporte** após a data atual. Uma lista de versões de cluster HDInsight suportadas pode ser encontrada na tabela acima. A data de expiração de suporte para um determinado HDInsight versão X (uma vez que uma versão mais recente do X + 1 estará disponível) é calculada como o mais tarde de:  

- Fórmula 1: Adicione 180 dias à data HDInsight cluster versão que x foi lançado.
- Fórmula 2: Adicionar 90 dias à data HDInsight versão cluster X + 1 (a versão subsequente após X) é disponibilizado no Portal.

A **Data de substituição** é a data após o qual a versão de cluster não pode ser criada em HDInsight.

> [AZURE.NOTE] Cluster de HDInsight baseados no Windows (incluindo a versão 2.1, 3.0, 3.1, 3,2 e 3,3) executar no Azure convidado SO família 4, que usa a versão de 64 bits do Windows Server 2012 R2 e suporte .NET Framework 4.0, 4,5, 4.5.1 e 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Hortonworks notas associado ao HDInsight versões##

* Versão de cluster HDInsight 3.4 usa uma distribuição de Hadoop com base em [Hortonworks 2,4 de plataforma de dados](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html). Este é o cluster de Hadoop **padrão** criado quando usando o portal.



* Versão de cluster HDInsight 3.3 usa uma distribuição de Hadoop com base em [Hortonworks 2.3 de plataforma de dados](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).
    * Notas de versão do Apache tempestade estão disponíveis [aqui](https://storm.apache.org/2015/11/05/storm0100-released.html).
    * Seção de Apache notas de versão estão disponíveis [aqui](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843).

* Versão de cluster de HDInsight 3,2 usa uma distribuição de Hadoop com base em [Hortonworks dados plataforma 2.2][hdp-2-2].  

    * Notas de versão para componentes específicos de Apache - [ [seção 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [porco 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Rio 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [2.6 M/R](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [2.6 HDFS](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [2.6 fio COLORIDO](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [comum](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [tempestade 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), [Oozie versão 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).


* Versão de cluster de HDInsight 3.1 usa uma distribuição de Hadoop com base em [Plataforma de dados Hortonworks 2.1.7][hdp-2-1-7]. HDInsight 3.1 clusters criados antes de 11/7/2014 foram baseados na [Plataforma de dados Hortonworks 2.1.1][hdp-2-1-1].

* Versão de cluster HDInsight 3.0 usa uma distribuição de Hadoop com base em [Hortonworks dados plataforma 2.0][hdp-2-0-8].

* Versão de cluster de HDInsight 2.1 usa uma distribuição de Hadoop com base em [Hortonworks dados plataforma 1.3][hdp-1-3-0].

* Versão de cluster de HDInsight 1,6 usa uma distribuição de Hadoop com base em [1.1 de plataforma de dados de Hortonworks][hdp-1-1-0].


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/
