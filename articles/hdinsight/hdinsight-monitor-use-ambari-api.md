<properties
    pageTitle="Monitorar clusters Hadoop em HDInsight usando a API Ambari | Microsoft Azure"
    description="Use as Apache Ambari APIs para criar, gerenciar e monitoramento Hadoop clusters. APIs e ferramentas de operador intuitivo ocultar a complexidade da Hadoop."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    editor="cgronlun"
    manager="jhubbard"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>Monitorar clusters Hadoop em HDInsight usando a API Ambari

Saiba como monitorar clusters HDInsight usando Ambari APIs.

> [AZURE.NOTE] As informações neste artigo são principalmente para clusters de HDInsight baseados no Windows, que fornecem uma versão somente leitura da API REST Ambari. Para clusters baseados em Linux, consulte [Gerenciar Hadoop clusters usando Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="what-is-ambari"></a>O que é Ambari?

[Apache Ambari] [ ambari-home] é usado para provisionamento, gerenciamento e monitoramento Apache Hadoop clusters. Ele inclui uma coleção intuitiva das ferramentas de operador e um conjunto robusto de APIs que ocultam a complexidade do Hadoop, simplificando a operação de clusters. Para obter mais informações sobre as APIs, consulte [Referência de API do Ambari][ambari-api-reference]. 

HDInsight atualmente suporta somente o recurso de monitoramento de Ambari. Ambari API 1.0 é compatível com clusters de versão 3.0 e 2.1 do HDInsight. Este artigo aborda acessando Ambari APIs em clusters de versão 3.1 e 2.1 do HDInsight. A principal diferença entre os dois é que alguns dos componentes foram alterados com a introdução de novos recursos (como o servidor de histórico de trabalho). 

**Pré-requisitos**

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma estação de trabalho com o PowerShell do Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- (Opcional) [cURL] [curl]. Para instalá-lo, consulte [cURL versões e Downloads][curl-download].

    >[AZURE.NOTE] Quando usar o comando ondulação no Windows, use aspas duplas em vez de aspas para os valores de opção.

- **Azurehdinsight um cluster**. Para obter instruções sobre cluster de provisionamento, consulte [Introdução ao uso HDInsight] [ hdinsight-get-started] ou [clusters provisionar HDInsight][hdinsight-provision]. Você precisará os seguintes dados de percorrer o tutorial:

    Propriedade de cluster|Nome da variável PowerShell Azure|Valor|Descrição
    ---|---|---|---
    Nome de cluster HDInsight|$clusterName||O nome do seu cluster HDInsight.
    Nome de usuário de cluster|$clusterUsername||Nome de usuário de cluster especificado quando o cluster foi criado.
    Senha cluster|$clusterPassword||Senha de usuário de cluster.

    >[AZURE.NOTE] Preencher os valores na tabela. Isso será útil para passar por esse tutorial.

## <a name="jump-start"></a>Início de salto

Há várias maneiras de usar Ambari para monitorar clusters de HDInsight.

**Usar o PowerShell Azure**

A seguir está um script do PowerShell do Azure para obter as informações do controlador de trabalho MapReduce *em um cluster de HDInsight 3.1.*  A principal diferença é que podemos retirar esses detalhes do serviço de fio COLORIDO (em vez de MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

Este é um script do PowerShell do Azure para obter o MapReduce trabalho controlador informações *em um cluster de HDInsight 2.1*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

A saída é:

![Saída Jobtracker][img-jobtracker-output]

**Use ondulação**

Este é um exemplo de obtenção de informações de cluster usando ondulação:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

A saída é:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**Para as 10/8/2014 versão**:

Ao usar o ponto de extremidade do Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", o campo *nome_do_host* retorna o nome de domínio totalmente qualificado (FQDN) do nó em vez do nome de host. Antes do lançamento de 8/10/2014, este exemplo retornado simplesmente "**headnode0**". Após o lançamento de 8/10/2014, você obtém o FQDN "**headnode0. { ClusterDNS} .azurehdinsight .net**", conforme mostrado no exemplo anterior. Essa alteração foi exigida para facilitar a cenários onde vários tipos de cluster (como HBase e Hadoop) podem ser implantados em uma rede virtual (VNET). Isso acontece, por exemplo, ao usar o HBase como uma plataforma de back-end para Hadoop.

## <a name="ambari-monitoring-apis"></a>Ambari APIs de monitoramento

A tabela a seguir lista alguns do Ambari mais comuns chamadas API de monitoramento. Para obter mais informações sobre a API, consulte [Referência de API do Ambari][ambari-api-reference].

Chamada de API do monitor|URI|Descrição
---|---|---
Obter clusters|`/api/v1/clusters`|
Obter informações de cluster.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net`|clusters, serviços, hosts
Obter serviços|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services`|Os serviços incluem: hdfs, mapreduce
Obter as informações de serviços.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>`|
Obter componentes de serviço|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components`|HDFS: namenode, datanode<br/>MapReduce: jobtracker; tasktracker
Obter as informações de componente.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>`|ServiceComponentInfo, componentes do host, métricas
Obter hosts|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts`|headnode0, workernode0
Obter informações de host.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>`|
Obter componentes do host|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components`|namenode, resourcemanager
Obtenha informações do componente de host.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>`|HostRoles, componente, host, métricas
Obter configurações|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations`|Tipos de config: sites core, hdfs sites, mapred-site, site de seção
Obter as informações de configuração.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>`|Tipos de config: sites core, hdfs sites, mapred-site, site de seção


##<a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como usar Ambari chamadas API de monitoramento. Para saber mais, consulte:

- [Gerenciar clusters HDInsight usando o Portal do Azure][hdinsight-admin-portal]
- [Gerenciar clusters HDInsight usando o PowerShell do Azure][hdinsight-admin-powershell]
- [Gerenciar clusters HDInsight usando a interface de linha][hdinsight-admin-cli]
- [Documentação do HDInsight][hdinsight-documentation]
- [Introdução ao HDInsight][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: /documentation/services/hdinsight/
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
