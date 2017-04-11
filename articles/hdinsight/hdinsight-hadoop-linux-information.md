<properties
   pageTitle="Dicas para usar Hadoop em HDInsight baseado em Linux | Microsoft Azure"
   description="Obtenha dicas de implementação para usar clusters baseados em Linux HDInsight (Hadoop) em um ambiente familiar do Linux em execução na nuvem Azure."
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
   ms.date="09/13/2016"
   ms.author="larryfr"/>

# <a name="information-about-using-hdinsight-on-linux"></a>Informações sobre como usar HDInsight no Linux

Baseado em Linux Azurehdinsight clusters fornecem Hadoop em um ambiente familiar do Linux, executando na nuvem Azure. Para a maioria das coisas, ele deve funcionar exatamente como qualquer outra instalação do Hadoop-em-Linux. Este documento destaca as diferenças específicas que você deve estar ciente.

##<a name="prerequisites"></a>Pré-requisitos

Muitas das etapas neste documento usam os seguintes utilitários, que talvez precise ser instalado no seu sistema.

* [cURL](https://curl.haxx.se/) - usado para se comunicar com serviços baseados na web
* [jq](https://stedolan.github.io/jq/) - usado para analisar JSON documentos
* [Azure CLI](../xplat-cli-install.md) - usado para gerenciar remotamente os serviços do Azure

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)] 

## <a name="domain-names"></a>Nomes de domínio

O nome de domínio totalmente qualificado (FQDN) para usar ao conectar-se ao cluster da internet é ** &lt;clustername >. azurehdinsight.net** ou (para somente SSH) ** &lt;clustername-ssh >. azurehdinsight.net**.

Internamente, cada nó no cluster tem um nome que é atribuído durante a configuração de cluster. Para localizar os nomes de cluster, você pode visitar a página de __Hosts__ da interface do usuário do Ambari Web ou use o seguinte para retornar uma lista de hosts da API REST Ambari:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Substitua __senha__ a senha da conta de administrador e __CLUSTERNAME__ com o nome do seu cluster. Isso retornará um documento JSON que contém uma lista dos hosts no cluster, em seguida, jq obtém check-out a `host_name` valor de elemento para cada host no cluster.

Se você precisar localizar o nome do nó para um serviço específico, você pode consultar Ambari desse componente. Por exemplo, para localizar os hosts para o nó de nome HDFS, use o seguinte.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Isso retorna um documento JSON descrevendo o serviço e, em seguida, jq obtém check-out somente o `host_name` valor para os hosts.

## <a name="remote-access-to-services"></a>Acesso remoto aos serviços

* **Ambari (web)** - https://&lt;clustername >. azurehdinsight.net

    Autenticar usando o usuário de administrador de cluster e a senha e, em seguida, efetuar login no Ambari. Isso também usa o usuário de administrador de cluster e a senha.

    Autenticação é texto sem formatação - sempre usar HTTPS para ajudar a garantir que a conexão é segura.

    > [AZURE.IMPORTANT] Enquanto Ambari para o seu cluster é acessível diretamente pela Internet, algumas funcionalidades depende acessando nós pelo nome de domínio interno usado pelo cluster. Já que esse é um nome de domínio interno e não público, você receberá erros de "servidor não encontrado" ao tentar acessar alguns recursos pela Internet.
    >
    > Para usar a funcionalidade total da web Ambari UI, use um túnel SSH para o tráfego de web proxy para o nó principal do cluster. Consulte [Usar SSH túnel para acessar Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie e outra web da interface do usuário](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - https://&lt;clustername >.azurehdinsight.net/ambari

    > [AZURE.NOTE] Autentica usando o usuário de administrador de cluster e a senha.
    >
    > Autenticação é texto sem formatação - sempre usar HTTPS para ajudar a garantir que a conexão é segura.

* **WebHCat (Templeton)** - https://&lt;clustername >.azurehdinsight.net/templeton

    > [AZURE.NOTE] Autentica usando o usuário de administrador de cluster e a senha.
    >
    > Autenticação é texto sem formatação - sempre usar HTTPS para ajudar a garantir que a conexão é segura.

* **SSH** - &lt;clustername >-ssh.azurehdinsight.net na porta 22 ou 23. Porta 22 é usada para conectar-se a headnode o principal, enquanto 23 é usado para conectar-se para o secundário. Para obter mais informações sobre os nós de cabeça, consulte [disponibilidade e confiabilidade de clusters Hadoop em HDInsight](hdinsight-high-availability-linux.md).

    > [AZURE.NOTE] Você só poderá acessar os nós de cabeça cluster por meio do SSH de um computador cliente. Uma vez conectado, você pode acessar os nós trabalhador usando SSH de um headnode.

## <a name="file-locations"></a>Locais de arquivos

Arquivos relacionados Hadoop podem ser encontrados em nós do cluster em `/usr/hdp`. Este diretório contém os seguintes subdiretórios:

* __2.2.4.9-1__: este diretório denomina-se para a versão da plataforma de dados de Hortonworks usado por HDInsight, portanto, o número em seu cluster pode ser diferente do listado aqui.
* __atual__: este diretório contém links para pastas sob o diretório __2.2.4.9-1__ e existe para que você não precisa digitar um número de versão (que pode mudar,) toda vez que você deseja acessar um arquivo.

Dados de exemplo e arquivos JAR podem ser encontrados no sistema de arquivo distribuído da Hadoop (HDFS) ou Azure Blob storage no ' / exemplo ' ou ' wasbs: / / / exemplo '.

## <a name="hdfs-azure-blob-storage-and-storage-best-practices"></a>HDFS, armazenamento de Blob do Azure e práticas recomendadas de armazenamento

Na maioria das distribuições do Hadoop, HDFS é feito por armazenamento local nas máquinas no cluster. Embora seja eficiente, ele pode ser caro para uma solução baseada em nuvem onde cobrado por hora ou por minuto para recursos de computação.

HDInsight usa o armazenamento de Blob do Azure como o armazenamento padrão, que fornece os seguintes benefícios:

* Armazenamento de longo prazo barato

* Acessibilidade de serviços externos, como sites, utilitários de carregamento/download de arquivos, vários SDKs de idioma e navegadores da web

Como é o armazenamento padrão para HDInsight, você normalmente não precisa fazer algo para usá-lo. Por exemplo, o seguinte comando listará arquivos na pasta **/example/data** , que é armazenada em armazenamento de Blob do Azure:

    hdfs dfs -ls /example/data

Alguns comandos podem exigir que você especificar que você está usando o armazenamento de Blob. Nesses casos, você pode prefixar o comando com **wasb: / /**, ou **wasbs: / /**.

HDInsight também permite que você associar várias contas de armazenamento de Blob com um cluster. Para acessar os dados em uma conta de armazenamento de Blob não padrão, você pode usar o formato **wasbs: / /&lt;container-name>@&lt;nome de conta >.blob.core.windows.net/**. Por exemplo, a seguir lista o conteúdo da pasta **/example/data** para o contêiner especificado e a conta de armazenamento de Blob:

    hdfs dfs -ls wasbs://mycontainer@mystorage.blob.core.windows.net/example/data

### <a name="what-blob-storage-is-the-cluster-using"></a>Qual o armazenamento de Blob cluster está usando?

Durante a criação de cluster, você optou por usar uma conta existente do armazenamento do Azure e o contêiner, ou crie um novo. Em seguida, você provavelmente esqueceu sobre ele. Você pode encontrar a conta padrão de armazenamento e o contêiner usando a API REST Ambari.

1. Use o seguinte comando para recuperar informações de configuração de HDFS usando ondulação e filtrar usando [jq](https://stedolan.github.io/jq/):

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
    > [AZURE.NOTE] Isso retornará a primeira configuração aplicada ao servidor (`service_config_version=1`,) que conterá essas informações. Se você estiver recuperando um valor que foi modificado após a criação de cluster, você talvez precise liste as versões de configuração e recuperar na última vez.

    Isso retornará um valor semelhante à seguinte, onde __CONTÊINER__ é o recipiente padrão e __ACCOUNTNAME__ é o nome da conta de armazenamento do Azure:

        wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

1. Obter o grupo de recursos para a conta de armazenamento, use a [CLI do Azure](../xplat-cli-install.md). No comando a seguir, substitua __ACCOUNTNAME__ com o nome de conta de armazenamento recuperado de Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Isso retornará o nome do grupo de recursos para a conta.
    
    > [AZURE.NOTE] Se nada for retornado desse comando, você talvez precise alterar a CLI do Azure para o modo do Gerenciador de recursos do Azure e execute o comando novamente. Para alternar para o modo do Gerenciador de recursos do Azure, use o comando a seguir.
    >
    > `azure config mode arm`
    
2. Obter a chave para a conta de armazenamento. Substitua o __nome do grupo__ com o grupo de recursos da etapa anterior. Substitua __ACCOUNTNAME__ com o nome da conta de armazenamento:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.[0].value'

    Isso retornará a chave primária da conta.

Você também pode encontrar as informações de armazenamento usando o Portal do Azure:

1. No [Portal do Azure](https://portal.azure.com/), selecione o seu cluster HDInsight.

2. Na seção __Essentials__ , selecione __todas as configurações__.

3. Em __configurações__, selecione __Teclas de armazenamento do Azure__.

4. __Teclas de armazenamento do Azure__, selecione uma das contas de armazenamento listadas. Isto irá exibir informações sobre a conta de armazenamento.

5. Selecione o ícone de chave. Isto irá exibir teclas para essa conta de armazenamento.

### <a name="how-do-i-access-blob-storage"></a>Como acessar o armazenamento de Blob?

Além de através do comando de Hadoop do cluster, há várias maneiras de acessar blobs:

* [CLI do Azure para Mac, Linux e Windows](../xplat-cli-install.md): interface de linha de comandos para trabalhar com o Azure. Após a instalação, use o `azure storage` comando para obter ajuda sobre como usar o armazenamento, ou `azure blob` para comandos específicos do blob.

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): um python script para trabalhar com blobs no armazenamento do Azure.

* Uma variedade de SDKs:

    * [Java](https://github.com/Azure/azure-sdk-for-java)

    * [Node](https://github.com/Azure/azure-sdk-for-node)

    * [PHP](https://github.com/Azure/azure-sdk-for-php)

    * [Python](https://github.com/Azure/azure-sdk-for-python)

    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

    * [.NET](https://github.com/Azure/azure-sdk-for-net)

* [API REST de armazenamento](https://msdn.microsoft.com/library/azure/dd135733.aspx)

##<a name="scaling"></a>Dimensionamento seu cluster

O cluster dimensionamento recurso permite que você altere o número de nós de dados usados por um cluster que é executado em Azurehdinsight sem ter que exclua e recrie o cluster.

Você pode executar operações de dimensionamento enquanto outros trabalhos ou processos estiverem sendo executados em um cluster.

Os tipos de cluster diferentes são afetados por dimensionamento da seguinte maneira:

* __Hadoop__: quando dimensionamento para baixo o número de nós em um cluster, alguns dos serviços no cluster são reiniciados. Isso pode causar trabalhos em execução ou pendente falha após a conclusão da operação de escala. Você pode reenviar os trabalhos quando a operação for concluída.

* __HBase__: servidores regionais são balanceados automaticamente em poucos minutos após a conclusão da operação de escala. Para conferir manualmente servidores regionais, use as seguintes etapas:

    1. Conectar-se ao cluster HDInsight usando SSH. Para obter mais informações sobre como usar SSH com HDInsight, consulte um dos seguintes documentos:

        * [Use SSH com HDInsight do Linux, Unix e Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

        * [Use SSH com HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    1. Use o seguinte para iniciar o shell de HBase:

            hbase shell

    2. Assim que o shell HBase for carregada, use o seguinte para saldo manualmente os servidores regionais:

            balancer

* __Tempestade__: você deve rebalancear qualquer topologias tempestade em execução após uma operação de dimensionamento foi realizada. Isso permite que a topologia de reajustar configurações de paralelismo com base no novo número de nós no cluster. Para rebalancear topologias em execução, use uma das seguintes opções:

    * __SSH__: conectar ao servidor e use o comando a seguir para rebalancear uma topologia:

            storm rebalance TOPOLOGYNAME

        Você também pode especificar parâmetros para substituir as dicas de paralelismo originalmente fornecidas pela topologia. Por exemplo, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` reconfigurar a topologia 5 processos de trabalho, 3 executores para o componente azul-spout e 10 executores para o componente amarelo brilhante.

    * __Interface de usuário de tempestade__: Use as seguintes etapas para reequilibrar uma topologia usando a interface do usuário tempestade.

        1. Abra __https://CLUSTERNAME.azurehdinsight.net/stormui__ no navegador da web, onde CLUSTERNAME é o nome do seu cluster tempestade. Se solicitado, insira o nome do administrador (admin) de cluster HDInsight e a senha que você especificou ao criar o cluster.

        3. Selecione a topologia que você deseja rebalancear e, em seguida, selecione o botão __rebalancear__ . Insira o atraso antes da operação de reequilíbrio dos é executada.

Para obter informações específicas sobre dimensionamento cluster HDInsight, consulte:

* [Gerenciar clusters Hadoop em HDInsight usando o Portal do Azure](hdinsight-administer-use-portal-linux.md#scaling)

* [Gerenciar clusters de Hadoop em HDinsight usando o PowerShell do Azure](hdinsight-administer-use-command-line.md#scaling)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Como instalar matiz (ou outro componente Hadoop)?

HDInsight é um serviço gerenciado, o que significa que nós em um cluster podem ser destruídos e provisionado novamente automaticamente pelo Azure se um problema é detectado. Por isso, não é recomendável instalar manualmente coisas diretamente em nós do cluster. Em vez disso, use [As ações de Script de HDInsight](hdinsight-hadoop-customize-cluster.md) quando você precisa instalar o seguinte:

* Um serviço ou site como Spark ou matiz.
* Um componente que requer alterações na configuração em vários nós no cluster. Por exemplo, uma variável de ambiente necessária, criação de um diretório de log, ou criação de um arquivo de configuração.

Ações de script são Bash scripts que são executados durante a configuração de cluster e podem ser usadas para instalar e configurar componentes adicionais em cluster. São fornecidos scripts de exemplo para instalar os seguintes componentes:

* [Matiz](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

Para obter informações sobre como desenvolver suas próprias ações de Script, consulte [desenvolvimento de ação de Script com HDInsight](hdinsight-hadoop-script-actions-linux.md).

###<a name="jar-files"></a>Arquivos JAR

Algumas tecnologias de Hadoop são fornecidas em arquivos jar independentes que contêm funções usadas como parte de um trabalho MapReduce ou de dentro de porco ou seção. Enquanto elas podem ser instaladas usando ações de Script, elas geralmente não exigem qualquer configuração e podem apenas ser carregadas ao cluster após a configuração e usadas diretamente. Se você quiser makle-se de que o componente sobrevive à fazer uma nova imagem do cluster, você pode armazenar o arquivo jar em WASB.

Por exemplo, se você quiser usar a versão mais recente do [DataFu](http://datafu.incubator.apache.org/), você pode baixar um jar que contém o projeto e carregue-o ao cluster HDInsight. Siga a documentação de DataFu na como usá-lo de porco ou seção.

> [AZURE.IMPORTANT] Alguns componentes que são arquivos de jar autônomo são fornecidos com HDInsight, mas não estão no caminho. Se você estiver procurando um componente específico, você pode usar o acompanhamento para procurá-lo em seu cluster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Isso retornará o caminho de todos os arquivos jar correspondentes.

Se o cluster já fornece uma versão de um componente como um arquivo de jar independente, mas que você deseja usar uma versão diferente, você pode carregar uma nova versão do componente ao cluster e tentar usá-lo em seus trabalhos.

> [AZURE.WARNING] Componentes fornecidos com o cluster HDInsight são totalmente suportadas e Microsoft Support ajudará para isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudar a solucionar o problema. Isso pode resultar em resolver o problema ou solicitando envolva canais disponíveis para as tecnologias de fonte aberta onde profunda especialização para que a tecnologia é encontrada. Por exemplo, existem muitos sites de comunidade que podem ser usados, como: [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).

## <a name="next-steps"></a>Próximas etapas

* [Migrar do HDInsight baseado no Windows para baseados em Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Use a seção com HDInsight](hdinsight-use-hive.md)
* [Usar porco com HDInsight](hdinsight-use-pig.md)
* [Usar MapReduce trabalhos com HDInsight](hdinsight-use-mapreduce.md)
