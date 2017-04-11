<properties
   pageTitle="Serviço de Proxy reverso tecidos | Microsoft Azure"
   description="Usar proxy reverso do serviço tecidos para comunicação microservices de dentro e fora do cluster"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/04/2016"
   ms.author="vturecek"/>

# <a name="service-fabric-reverse-proxy"></a>Proxy reverso do serviço tecidos

O proxy inverso de tecidos do serviço é uma estrutura de integrado ao serviço de proxy reverso que permite endereçamento microservices no cluster tecidos serviço que expor pontos de extremidade HTTP.

## <a name="microservices-communication-model"></a>Modelo de comunicação de Microservices

Microservices em estrutura de serviço normalmente executados em um subconjunto do máquina virtual no cluster e pode mover de uma VM para outro por vários motivos. Para que os pontos de extremidade microservices podem alterar dinamicamente. O padrão típico para se comunicar com o microservice é o loop de resolver abaixo,

1. Resolva o local do serviço inicialmente através do serviço de nomenclatura.
2. Conecte ao serviço.
3. Determine a causa das falhas de conexão e resolver novamente o local do serviço quando necessário.

Geralmente, este processo envolve a disposição bibliotecas de comunicação do lado do cliente em um loop de repetição que implementa as políticas de serviço resolução e tente novamente.
Para obter mais informações sobre este tópico, consulte a [comunicação com os serviços](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-via-sf-reverse-proxy"></a>Comunicar-se por meio de proxy reverso IT
Estrutura de serviço de proxy reverso é executado em todos os nós no cluster. Ele executa o processo de resolução de todo o serviço em nome do cliente e encaminha a solicitação de cliente. Portanto clientes executando em cluster podem usar quaisquer bibliotecas de comunicação de HTTP do lado do cliente para se comunicar com o serviço de destino por meio do proxy reverso IT executando localmente no mesmo nó.

![Comunicação interna][1]

## <a name="reaching-microservices-from-outside-the-cluster"></a>Contatar Microservices de fora do cluster
O modelo de comunicação externa padrão para microservices é **opt-in** onde cada serviço por padrão não pode ser acessado diretamente de clientes externos. O [Balanceador de carga do Azure](../load-balancer/load-balancer-overview.md) é um limite de rede entre microservices e clientes externos, que executa NAT e encaminha solicitações externas para interno **IP: porta** pontos de extremidade. Para fazer o ponto de extremidade de um microservice diretamente acessíveis para clientes externos, o balanceador de carga do Azure primeiro deve ser configurado para encaminhar o tráfego para cada porta usada pelo serviço no cluster. Além disso, a maioria dos microservices (especialmente com informações de estado microservices) não live em todos os nós do cluster e eles podem mover entre nós em failover, portanto nesses casos, o balanceador de carga do Azure efetivamente não pode determinar o nó de destino das réplicas estão localizados para encaminhar o tráfego para.

### <a name="reaching-microservices-via-the-sf-reverse-proxy-from-outside-the-cluster"></a>Com Microservices via IT proxy inverso de fora do cluster

Em vez de configurar portas do serviço individuais no balanceador de carga azure, apenas a porta do proxy inverso IT pode ser configurada no balanceador de carga de Azure. Isso permite que clientes fora do cluster alcançar serviços dentro do cluster por meio do proxy reverso sem um configurações adicionais.

![Comunicação externa][0]

>[AZURE.WARNING] Configurar a porta do proxy inverso no balanceador de carga, torna todos os serviços micro no cluster que expor um ponto de extremidade de http, seja addressible de fora do cluster.


## <a name="uri-format-for-addressing-services-via-the-reverse-proxy"></a>Formato de URI para endereçar serviços por meio do proxy reverso

O proxy inverso usa um formato URI específico para identificar quais partição de serviço a solicitação de entrada deve ser encaminhada para:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&Timeout=<timeout_in_seconds>
```

 - **http (s):** O proxy inverso pode ser configurado para aceitar o tráfego de HTTP ou HTTPS. No caso de tráfego HTTPS, o encerramento de SSL ocorre no proxy inverso. Solicitações encaminhadas pelo proxy reverso serviços no cluster são sobre http.
 - **Cluster FQDN | IP interno:** Para clientes externos, o proxy inverso pode ser configurado para que ele seja acessível por meio de domínio do cluster (por exemplo, mycluster.eastus.cloudapp.azure.com). Por padrão que o proxy inverso é executado em cada nó, portanto para tráfego interno ele possa ser encontrado no host local ou em qualquer IP node interno (por exemplo, 10.0.0.1).
 - **Porta:** A porta que foi especificada para o proxy inverso. Por exemplo: 19008.
 - **ServiceInstanceName:** Este é o nome da instância de serviço implantado totalmente qualificado do serviço que você está tentando alcançar a "tecidos: /" esquema. Por exemplo, para alcançar serviço *tecidos: / myapp/meuserviço/*, você usaria *myapp/meuserviço*.
 - **Caminho de sufixo:** Este é o caminho de URL real para o serviço que você deseja se conectar. Por exemplo, *myapi/valores/adicionar/3*
 - **PartitionKey:** Para um serviço particionado, esta é a chave de partição computado da partição que você deseja contatar. Observe que isso é *não* a partição GUID de ID. Este parâmetro não é necessário para serviços usando o esquema de partição simples.
 - **PartitionKind:** O esquema de partição de serviço. Isso pode ser 'Int64Range' ou 'Nomeado'. Este parâmetro não é necessário para serviços usando o esquema de partição simples.
 - **Tempo limite:**  Especifica o tempo limite para a solicitação de http criado pelo proxy inverso para o serviço em nome da solicitação do cliente. O valor padrão para isso é 60 segundos. Este é um parâmetro opcional.

### <a name="example-usage"></a>Exemplo de uso

Como exemplo, vamos serviço **tecidos: / MyApp/meuserviço** que abre um ouvinte HTTP no URL a seguir:

```
http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Com os seguintes recursos:

 - `/index.html`
 - `/api/users/<userId>`

Se o serviço usa o esquema de partição simples, os parâmetros de cadeia de caracteres de consulta *PartitionKey* e *PartitionKind* não são necessários e o serviço pode ser acessado através do gateway como:

 - Externamente:`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService`
 - Internamente:`http://localhost:19008/MyApp/MyService`

Se o serviço usa o esquema de partição Int64 uniforme, os parâmetros de cadeia de caracteres de consulta *PartitionKey* e *PartitionKind* devem ser usados para alcançar uma partição do serviço:

 - Externamente:`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
 - Internamente:`http://localhost:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Para acessar os recursos expostos pelo serviço, simplesmente coloque o caminho do recurso após o nome do serviço na URL:

 - Externamente:`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
 - Internamente:`http://localhost:19008/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

O gateway então encaminhará essas solicitações a URL do serviço:

 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Tratamento especial para o compartilhamento de porta serviços

O Gateway de aplicativo tenta resolver novamente um endereço de serviço e repita a solicitação quando um serviço não pode ser acessado. Este é um dos principais benefícios do gateway, como código do cliente não precisa implementar sua própria solução de serviço e resolver loop.

Geralmente quando um serviço não pode ser acessado significa que a instância de serviço ou réplica foi movido para um nó diferente, como parte do ciclo de vida normal. Quando isso acontece, o gateway pode receber um erro de conexão de rede indicando que um ponto de extremidade já não estiver aberto no endereço originalmente resolvido.

No entanto, réplicas ou instâncias de serviço pode compartilhar um processo de host e também pode compartilhar uma porta quando hospedadas por um servidor web baseados em http. sys, incluindo:

 - [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
 - [WebListener principais do ASP.NET](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
 - [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Nessa situação, é provável que o servidor web está disponível no processo do host e responder às solicitações, mas a instância de serviço resolvido ou réplica não está mais disponível no host. Nesse caso, o gateway recebe uma resposta de HTTP 404 do servidor web. Como resultado, um HTTP 404 tem dois significados distintos:

 1. O endereço do serviço está correto, mas o recurso solicitado pelo usuário não existe.
 2. O endereço de serviço está incorreto e o recurso solicitado pelo usuário realmente pode existir em um nó diferente.

No primeiro caso, essa é um HTTP 404 normal, que é considerado um erro de usuário. Entretanto, no segundo caso, o usuário tiver solicitado um recurso que existe, mas o gateway foi possível localizá-lo, porque o próprio serviço foi movido, caso em que o gateway precisa resolver novamente o endereço e tente novamente.

O gateway, portanto, precisa de uma forma para distinguir entre esses dois casos. Para fazer essa distinção, uma dica do servidor é necessária.

 - Por padrão, o Gateway de aplicativo supõe caso #2 e tenta resolver novamente e reenviar a solicitação.
 - Para indicar caso #1 para o Gateway de aplicativo, o serviço deve retornar o seguinte cabeçalho de resposta HTTP:

`X-ServiceFabric : ResourceNotFound`

Esse cabeçalho de resposta HTTP indica uma situação de HTTP 404 normal no qual o recurso solicitado não existir e o gateway não tentará resolver novamente o endereço do serviço.

## <a name="setup-and-configuration"></a>Instalação e configuração
O proxy inverso de tecidos do serviço pode ser habilitado para o cluster por meio do [modelo de Gerenciador de recursos do Azure](./service-fabric-cluster-creation-via-arm.md).

Depois que você tiver o modelo para o cluster que você deseja implantar (dos modelos de exemplo ou criando um modelo do Gerenciador de recursos personalizados) do proxy reverso pode ser habilitado no modelo pelas etapas a seguir.

1. Defina uma porta para o proxy inverso na [seção parâmetros](../resource-group-authoring-templates.md) do modelo.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19008,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Especifique a porta para cada um dos objetos nodetype na [seção de tipo de recurso](../resource-group-authoring-templates.md) de **Cluster**

    Para apiVersion antes ' 2016-09-01', a porta é identificada pelo parâmetro nome ***httpApplicationGatewayEndpointPort***

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "httpApplicationGatewayEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```

    Para apiVersion em ou após ' 2016-09-01', a porta é identificada pelo parâmetro nome ***reverseProxyEndpointPort***

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```

3. Para resolver o proxy inverso de fora do azure cluster, configure as **regras de Balanceador de carga azure** para a porta especificada na etapa 1.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Para configurar os certificados SSL na porta para o proxy inverso, adicione o certificado para a propriedade httpApplicationGatewayCertificate na [seção de tipo de recurso](../resource-group-authoring-templates.md) de **Cluster**

    Para apiVersion antes ' 2016-09-01', o certificado é identificado pelo parâmetro nome ***httpApplicationGatewayCertificate***

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "httpApplicationGatewayCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```
    Para apiVersion em ou após ' 2016-09-01', o certificado é identificado pelo parâmetro nome ***reverseProxyCertificate***
    
    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

## <a name="next-steps"></a>Próximas etapas
 - Veja um exemplo de comunicação de HTTP entre serviços em um [projeto de amostra no GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Chamadas de procedimento remoto com remota serviços confiáveis](service-fabric-reliable-services-communication-remoting.md)

 - [Web API que usa OWIN nos serviços confiável](service-fabric-reliable-services-communication-webapi.md)

 - [Comunicação WCF usando serviços confiáveis](service-fabric-reliable-services-communication-wcf.md)


[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
