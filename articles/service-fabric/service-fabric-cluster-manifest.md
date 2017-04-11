<properties
   pageTitle="Configurar seu cluster autônomo | Microsoft Azure"
   description="Este artigo descreve como configurar seu autônomo ou cluster de estrutura de serviço particular."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="dkshir"/>


# <a name="configuration-settings-for-standalone-windows-cluster"></a>Definições de configuração de cluster do Windows autônoma

Este artigo descreve como configurar um cluster de estrutura de serviço autônomo usando o arquivo de _**ClusterConfig.JSON**_ . Você pode usar este arquivo para especificar informações como os nós tecidos de serviço e seus endereços IP, tipos diferentes de nós em cluster, as configurações de segurança, bem como a topologia de rede em termos de domínios de falhas/atualização, para o seu cluster autônomo.

Quando você [baixar o pacote de serviço tecidos autônomo](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), alguns exemplos de arquivo ClusterConfig.JSON são baixadas para sua máquina de trabalho. Os exemplos tendo *DevCluster* em seus nomes ajudará você a criar um cluster com todos os três nós na mesma máquina, como nós lógicos. Fora dessas, pelo menos um nó deve ser marcado como um nó principal. Este cluster é útil para um ambiente de desenvolvimento ou teste e não é suportado como um cluster de produção. Os exemplos tendo *MultiMachine* em seus nomes, ajudará você a criar um cluster de qualidade de produção, com cada nó em uma máquina separada. O número de nós primários para esses cluster se baseará no [nível de confiabilidade](#reliability).

1. *ClusterConfig.Unsecure.DevCluster.JSON* e *ClusterConfig.Unsecure.MultiMachine.JSON* mostram como criar um cluster de teste ou de produção desprotegido respectivamente. 
    
2. *ClusterConfig.Windows.DevCluster.JSON* e *ClusterConfig.Windows.MultiMachine.JSON* mostram como criar um cluster de teste ou de produção, protegido usando a [segurança do Windows](service-fabric-windows-cluster-windows-security.md).

3. *ClusterConfig.X509.DevCluster.JSON* e *ClusterConfig.X509.MultiMachine.JSON* mostram como criar um cluster de teste ou de produção, protegida usando [X509 segurança baseada em certificado](service-fabric-windows-cluster-x509-security.md). 


Agora vamos examinar as várias seções de um arquivo de _**ClusterConfig.JSON**_ como abaixo.

## <a name="general-cluster-configurations"></a>Configurações de cluster geral
Isso abrange as configurações específicas de cluster ampla, conforme mostrado no trecho de JSON abaixo.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2015-01-01-alpha",

Você poderá dar à qualquer nome amigável para o seu cluster de estrutura de serviço atribuí-la para a variável de **nome** . O **clusterConfigurationVersion** é o número de versão do cluster; Você deve aumentá-lo sempre que você atualizar seu cluster de estrutura de serviço. No entanto, você deve deixar o **apiVersion** para o valor padrão.


<a id="clusternodes"></a>
## <a name="nodes-on-the-cluster"></a>Nós no cluster
Você pode configurar os nós no seu cluster de estrutura de serviço usando a seção de **nós** , como mostra o trecho de código a seguir.

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Um cluster de estrutura de serviço deve conter pelo menos 3 nós. Você pode adicionar mais nós a esta seção de acordo com a configuração. A tabela a seguir explica as definições de configuração para cada nó.

|**Configuração de nó**|**Descrição**|
|-----------------------|--------------------------|
|nodeName|Você pode dar qualquer nome amigável para o nó.|
|endereço IP|Descubra o endereço IP do seu nó abrindo uma janela de comando e digitando `ipconfig`. Observe o endereço IPV4 e atribuí-la para a variável de **endereço IP** .|
|nodeTypeRef|Cada nó pode ser atribuído a um tipo de nó diferente. Os [tipos de nó](#nodetypes) são definidos na seção abaixo.|
|faultDomain|Domínios de falha permitem que os administradores de cluster definir os nós físicos que podem falhar ao mesmo tempo devido à dependência física compartilhada.|
|upgradeDomain|Domínios atualização descrevem conjuntos de nós que estão encerradas para atualizações de serviço tecidos em aproximadamente ao mesmo tempo. Você pode escolher quais nós para atribuir a quais domínios de atualização, como eles não são limitados por qualquer requisitos físicos.| 


## <a name="cluster-properties"></a>Propriedades do cluster

A seção **Propriedades** a ClusterConfig.JSON é usada para configurar o cluster da seguinte maneira.

<a id="reliability"></a>
### <a name="reliability"></a>Confiabilidade 
A seção **reliabilityLevel** define o número de cópias dos serviços de sistema que podem ser executados em nós do cluster primários. Aumenta a confiabilidade desses serviços e, portanto, o cluster. Você pode definir essa variável *Bronze*, *Prata*, *ouro* ou *Platina* para 3, 5, 7 ou 9 cópias desses serviços respectivamente. Veja um exemplo abaixo.

    "reliabilityLevel": "Bronze",
    
Observe que, como um nó primário executa uma única cópia dos serviços de sistema, seria necessário um mínimo de 3 nós primários para *Bronze*, 5 para *Prata*7 para *ouro* e 9 para níveis de confiabilidade *Platinum* .


### <a name="diagnostics"></a>Diagnósticos
A seção **diagnosticsStore** permite que você configure parâmetros para habilitar o diagnóstico e solução de problemas de falhas de nó ou cluster, conforme mostrado no seguinte trecho. 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

Os **metadados** é uma descrição do seu diagnóstico de cluster e podem ser definido de acordo com a configuração. Essas variáveis ajudam na coleta ETW logs de rastreamento, despejos, bem como contadores de desempenho. Leia [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) e [Rastreamento ETW](https://msdn.microsoft.com/library/ms751538.aspx) para obter mais informações sobre logs de rastreamento ETW. Todos os logs, incluindo [despejos](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) e [contadores de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) podem ser direcionados para a pasta **connectionString** em sua máquina. Você também pode usar *AzureStorage* para o armazenamento de diagnóstico. Veja abaixo um trecho de amostra.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Segurança 
A seção de **segurança** é necessária para um cluster de estrutura de serviço autônomo seguro. O trecho a seguir mostra uma parte dessa seção.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

Os **metadados** é uma descrição do seu cluster seguro e podem ser definido de acordo com a configuração. A **ClusterCredentialType** e **ServerCredentialType** determinam o tipo de segurança que o cluster e os nós irá implementar. Elas podem ser definidas para qualquer um dos *X509* para uma segurança baseada em certificado, ou o *Windows* para uma segurança baseada no Active Directory do Azure. O resto da seção **segurança** se baseará no tipo de segurança. Leia [a segurança baseada em certificados em um cluster de autônomo](service-fabric-windows-cluster-x509-security.md) ou a [segurança do Windows em um cluster de autônomo](service-fabric-windows-cluster-windows-security.md) para obter informações sobre como preencher o resto da seção **segurança** .


<a id="nodetypes"></a>
### <a name="node-types"></a>Tipos de nós
A seção **nodeTypes** descreve o tipo de nós que tem seu cluster. Tipo de pelo menos um nó deve ser especificado para um cluster, como mostra o trecho a seguir. 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

O **nome** é o nome amigável para este tipo de nó específico. Para criar um nó desse tipo de nó, atribua seu nome amigável para a variável de **nodeTypeRef** para o nó, como mencionado [acima](#clusternodes). Para cada tipo de nó, defina os pontos de extremidade de conexão que serão usados. Você pode escolher qualquer número de porta para esses pontos de extremidade de conexão, desde que eles não entram em conflito com quaisquer outros pontos de extremidade neste cluster. Em um cluster de vários nó, haverá um ou mais nós principais (ou seja, **isPrimary** definido como *true*), dependendo do [**reliabilityLevel**](#reliability). Leia [Considerações de planejamento de capacidade do serviço tecidos cluster](service-fabric-cluster-capacity.md) para informações em valores **nodeTypes** e **reliabilityLevel** e saber o que são primário e os tipos de nós não principal. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Pontos de extremidade usados para configurar os tipos de nós

- *clientConnectionEndpointPort* é a porta usada pelo cliente para se conectar ao cluster, ao usar o cliente APIs. 
- *clusterConnectionEndpointPort* é a porta na qual os nós se comunicar com os outros.
- *leaseDriverEndpointPort* é a porta usada pelo driver de concessão do cluster para descobrir se os nós ainda estão ativos. 
- *serviceConnectionEndpointPort* é a porta usada pelos aplicativos e serviços implantados em um nó, para se comunicar com o cliente de serviço tecidos no nó específico.
- *httpGatewayEndpointPort* é a porta usada pelo serviço tecidos Explorer para conectar-se ao cluster.
- *ephemeralPorts* são as [portas dinâmicas usadas pelo sistema operacional](https://support.microsoft.com/kb/929851). Serviço tecidos usará uma parte dessas portas de aplicativo e o restante estará disponível para o sistema operacional. Ele também será mapeada este intervalo para o intervalo existente presente no sistema operacional, assim para todos os fins, você pode usar os intervalos fornecidos os arquivos JSON de exemplo. Você precisa certificar-se de que a diferença entre o início e as portas end é pelo menos de 255. 
- *applicationPorts* são as portas que serão usadas pelos aplicativos tecidos de serviço. Esses devem ser um subconjunto do *ephemeralPorts*, suficiente para cobrir o requisito de ponto de extremidade de seus aplicativos. Serviço tecidos usará essas sempre que novas portas necessários, bem como cuidam de abertura do firewall para essas portas. 
- *reverseProxyEndpointPort* é um ponto de extremidade do proxy reverso opcional. Consulte [Serviço tecidos reverter Proxy](service-fabric-reverseproxy.md) para obter mais detalhes. 


### <a name="other-settings"></a>Outras configurações
A seção **fabricSettings** permite que você defina os diretórios raiz para os dados de estrutura de serviço e os logs. Você pode personalizar esses somente durante a criação de cluster inicial. Veja abaixo um trecho de amostra desta seção.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

É recomendável usando uma unidade de não-OS como o FabricDataRoot e FabricLogRoot, pois fornece que mais confiabilidade contra SO trava. Observe que se você personalizar somente a raiz de dados, em seguida, a raiz de log será colocada um nível abaixo da raiz de dados.


## <a name="next-steps"></a>Próximas etapas

Quando você tiver um arquivo de ClusterConfig.JSON completo configurado de acordo com a configuração de cluster autônomo, você pode implantar seu cluster seguindo o artigo [criar um cluster de estrutura de serviço do Azure local ou na nuvem](service-fabric-cluster-creation-for-windows-server.md) e continue a [visualizando seu cluster com o serviço tecidos Explorer](service-fabric-visualizing-your-cluster.md).


