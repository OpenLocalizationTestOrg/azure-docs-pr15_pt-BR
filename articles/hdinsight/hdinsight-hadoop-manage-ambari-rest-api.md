<properties
   pageTitle="Monitorar e gerenciar clusters de HDInsight usar a API REST do Apache Ambari | Microsoft Azure"
   description="Saiba como usar Ambari para monitorar e gerenciar clusters de HDInsight baseados em Linux. Neste documento, você aprenderá como usar a API REST de Ambari incluído no clusters de HDInsight."
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
   ms.date="09/20/2016"
   ms.author="larryfr"/>

#<a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Gerenciar clusters HDInsight usando a API REST Ambari

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifica o gerenciamento e o monitoramento de um cluster de Hadoop, fornecendo um fácil de usar web UI e API REST. Ambari está incluído em clusters baseados em Linux HDInsight e é usado para monitorar o cluster e fazer alterações de configuração. Neste documento, você vai aprender as Noções básicas para trabalhar com a API REST Ambari realizando tarefas comuns usando ondulação.

##<a name="prerequisites"></a>Pré-requisitos

* [cURL](http://curl.haxx.se/): Ondulação é um utilitário de várias plataformas que pode ser usado para trabalhar com as APIs REST de linha de comando. Neste documento, ele é usado para se comunicar com a API REST Ambari.
* [jq](https://stedolan.github.io/jq/): jq é um utilitário de linha de comando entre plataformas para trabalhar com documentos JSON. Neste documento, ele é usado para analisar os documentos JSON retornados da API REST Ambari.
* [Azure CLI](../xplat-cli-install.md): um utilitário de linha de comando de plataforma híbrida para trabalhar com os serviços do Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

##<a id="whatis"></a>O que é Ambari?

[Apache Ambari](http://ambari.apache.org) torna o gerenciamento de Hadoop mais simples, fornecendo um web fáceis de usar interface do usuário que pode ser usado para provisionar, gerenciar e monitorar clusters Hadoop. Os desenvolvedores podem integrar esses recursos em seus aplicativos usando as [APIs REST de Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari é fornecido por padrão com clusters HDInsight baseados em Linux.

##<a name="rest-api"></a>API REST

O URI de base para a API REST de Ambari em HDInsight é https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, onde __CLUSTERNAME__ é o nome do seu cluster.

> [AZURE.IMPORTANT] Enquanto o nome do cluster na parte de nome (FQDN) de domínio totalmente qualificado do URI (CLUSTERNAME.azurehdinsight.net) diferencia maiusculas de minúsculas, outras ocorrências no URI diferenciam maiusculas de minúsculas. Por exemplo, se seu cluster é denominado meucluster, a seguir são URIs válidos:
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> Os seguintes URIs retornar um erro porque a segunda ocorrência do nome não for o caso correto.
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

Conectando ao Ambari em HDInsight requer HTTPS. Ao autenticar a conexão, você deve usar o nome de conta de administrador (o padrão é __administrador__) e senha que você forneceu quando o cluster foi criado.

O exemplo a seguir usa ondulação para fazer uma solicitação GET contra a API REST. Substitua a __senha__ com a senha de administrador para o cluster. Substitua o nome do cluster __CLUSTERNAME__ :

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
A resposta é um documento JSON que começa com informações similares ao seguinte:

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

Como isso é JSON, é mais fácil usar um analisador JSON para trabalhar com os dados. Por exemplo, o exemplo a seguir usa jq para exibir somente os `health_report` elemento.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report'

##<a name="example-get-the-fqdn-of-cluster-nodes"></a>Exemplo: Obtenha o FQDN de nós de cluster

Ao trabalhar com HDInsight, você talvez precise saber o nome de domínio totalmente qualificado (FQDN) de um nó de cluster. Você pode recuperar facilmente o FQDN para os vários nós no cluster utilizando o seguinte:

* __Nós de cabeça__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __Nós de trabalho__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Nós zookeeper__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Observe que cada um desses exemplos segue o mesmo padrão:

1. Consulta um componente que sabemos é executado em nós.
2. Recuperar o `host_name` elementos, que contêm o FQDN para esses nós.

O `host_components` elemento do documento retorno contém vários itens. Usando `.host_components[]`, e, em seguida, especificar um caminho dentro do elemento será loop em cada item e retirar o valor do caminho específico. Se você só quiser um valor, como a primeira entrada FQDN, você pode retornar os itens como uma coleção e, em seguida, selecione uma entrada específica:

    jq '[.host_components[].HostRoles.host_name][0]'

Isso retorna o primeiro FQDN da coleção.

##<a name="example-get-the-default-storage-account-and-container"></a>Exemplo: Obter a conta padrão de armazenamento e o contêiner

Quando você cria um cluster de HDInsight, você deve usar uma conta de armazenamento do Azure e um contêiner de blob como o armazenamento padrão para o cluster. Você pode usar Ambari para recuperar essas informações depois cluster tiver sido criado. Por exemplo, se você quiser programaticamente gravar dados diretamente para o contêiner.

A seguir recuperará o URI WASB do armazenamento clusters padrão:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE] Retorna a primeira configuração aplicada ao servidor (`service_config_version=1`,) que contém essas informações. Se você recuperar um valor que foi modificado após a criação de cluster, você talvez precise liste as versões de configuração e recuperar na última vez.

Isso retorna um valor semelhante ao exemplo a seguir, onde __CONTÊINER__ é o recipiente padrão e __ACCOUNTNAME__ é o nome da conta de armazenamento do Azure:

    wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

Você pode usar essas informações com o [Azure CLI](../xplat-cli-install.md) para carregar ou baixar dados do contêiner.

1. Obtenha o grupo de recursos para a conta de armazenamento. Substitua o nome da conta de armazenamento recuperado de Ambari __ACCOUNTNAME__ :

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Isso retorna o nome do grupo de recursos para a conta.
    
    > [AZURE.NOTE] Se nada for retornado desse comando, você talvez precise alterar a CLI do Azure para o modo do Gerenciador de recursos do Azure e execute o comando novamente. Para alternar para o modo do Gerenciador de recursos do Azure, use o seguinte comando:
    >
    > `azure config mode arm`
    
2. Obter a chave para a conta de armazenamento. Substitua o __nome do grupo__ com o grupo de recursos da etapa anterior. Substitua __ACCOUNTNAME__ com o nome da conta de armazenamento:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Este exemplo retorna a chave primária da conta.
    
3. Use o comando de carregamento para armazenar um arquivo no contêiner de:

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Substitua o nome da conta de armazenamento __ACCOUNTNAME__ . Substitua __ACCOUNTKEY__ com a chave recuperada anteriormente. __Caminho de arquivo__ é o caminho para o arquivo que você deseja carregar, enquanto __BLOBPATH__ é o caminho no contêiner.

    Por exemplo, se desejar que o arquivo seja exibido em HDInsight em wasbs://example/data/filename.txt, __BLOBPATH__ seria `example/data/filename.txt`.

##<a name="example-update-ambari-configuration"></a>Exemplo: Configuração de Ambari de atualização

1. Obter a configuração atual, o que Ambari armazena como a "configuração desejada":

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
        
    Este exemplo retorna um documento JSON contendo a configuração atual (identificada por valor de _marca_ ) para os componentes instalados no cluster. O exemplo a seguir é um trecho dos dados retornados de um tipo de cluster Spark.
    
        "spark-metrics-properties" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-fairscheduler" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-sparkconf" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        }

    Nesta lista, você precisa copiar o nome do componente (por exemplo, __spark\_e mais Próspero banco\_sparkconf__ e o valor da __marca__ .
    
2. Recupere a configuração para o componente e marca usando o comando a seguir. Substitua __spark-e mais Próspero banco-sparkconf__ e __inicial__ com o componente e a marca que você deseja recuperar a configuração.

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    
    Ondulação recupera o documento JSON e, em seguida, jq é usado para fazer modificações aos dados para criar um modelo. O modelo, em seguida, é usado para Adicionar/modificar valores de configuração. Especificamente faz o seguinte:
    
    * Cria um valor exclusivo que contêm a cadeia de caracteres "versão" e a data, que está armazenada em __newtag__.
    * Cria um documento de raiz para a nova configuração desejada.
    * Obtém o conteúdo do `.items[]` matriz e adiciona sob o elemento __desired_config__ .
    * Exclui a __href__, __versão__e elementos de __configuração__ , como esses elementos não são necessários para enviar uma nova configuração.
    * Adiciona um novo elemento de __marca__ e define seu valor como __versão # # #__. A parte numérica baseia-se na data atual. Cada configuração deve ter um marcador exclusivo.
    
    Por fim, os dados são salvos para o documento de __newconfig.json__ . A estrutura do documento deve aparecer semelhante ao seguinte exemplo:
    
        {
            "Clusters": {
                "desired_config": {
                "tag": "version1459260185774265400",
                "type": "spark-thrift-sparkconf",
                "properties": {
                    ....
                 },
                 "properties_attributes": {
                     ....
                 }
            }
        }

3. Abra os valores de documento e Adicionar/modificar __newconfig.json__ no objeto de __Propriedades__ . O exemplo a seguir altera o valor de __"spark.yarn.am.memory"__ de __"1 g"__ para __"3G"__ e e adiciona um novo elemento para __"spark.kryoserializer.buffer.max"__ com um valor de __"256 m"__.

        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",

    Salve o arquivo depois de terminar de fazer modificações.

4. Use o seguinte comando para enviar a configuração atualizada para Ambari.

        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
        
    Este comando pipes o conteúdo do arquivo __newconfig.json__ a solicitação de ondulação, que envia para o cluster como a nova configuração desejada. A solicitação de ondulação retorna um documento JSON. O elemento __versionTag__ neste documento deve corresponder à versão que você enviou e o objeto de __configurações__ conterá as alterações de configuração solicitadas.

###<a name="example-restart-a-service-component"></a>Exemplo: Reinicie um componente de serviço

Neste ponto, se você olhar web Ambari da interface do usuário, o serviço de Spark indicará que ele precisa ser reiniciado para a nova configuração entrem em vigor. Use as etapas a seguir para reiniciar o serviço.

1. Use o seguinte para habilitar o modo de manutenção do serviço Spark:

        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Este comando envia um documento JSON no servidor (contidas a `echo` instrução,) que ative o modo de manutenção.
    Você pode verificar que o serviço agora está no modo de manutenção usando a seguinte solicitação:
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
        
    Isso retornará um valor de `"ON"`.

3. Em seguida, use o seguinte para desativar o serviço:

        echo '{"RequestInfo": {"context" :"Stopping the Spark service"}, "Body": {"ServiceInfo": {"state": "INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
        
    Esse comando retorna uma resposta semelhante à seguinte.
    
        {
            "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
            "Requests" : {
                "id" : 29,
                "status" : "Accepted"
            }
        }
    
    O `href` valor retornado por esse URI está usando o endereço IP interno do nó cluster. Para usá-lo de fora do cluster, substitua a parte '10.0.0.18:8080' com o FQDN do cluster. Por exemplo, o comando a seguir recupera o status da solicitação.
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    
    Se isso retornará um valor de `"COMPLETED"` , em seguida, a solicitação foi concluída.

4. Uma vez concluída a solicitação anterior, use o seguinte para iniciar o serviço.

        echo '{"RequestInfo": {"context" :"Restarting the Spark service"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Depois de reinicia o serviço, é as novas configurações.

5. Por fim, use o seguinte para desativar o modo de manutenção.

        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

##<a name="next-steps"></a>Próximas etapas

Para obter uma referência completa da API REST, consulte [Ambari API referência V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE] Algumas funcionalidades de Ambari estiver desabilitada, como ele é gerenciado pelo serviço de nuvem HDInsight; Por exemplo, adicionando ou removendo hosts de cluster ou adicionando novos serviços.
