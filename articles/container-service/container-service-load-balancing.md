<properties
   pageTitle="Carregar contêineres de saldo em um cluster de serviço de contêiner do Azure | Microsoft Azure"
   description="Balanceamento de carga em vários contêineres em um cluster de serviço de contêiner do Azure."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contêineres, Microserviços, DC/sistema operacional, do Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="rogardle"/>

# <a name="load-balance-containers-in-an-azure-container-service-cluster"></a>Contêineres de balanço de carga em um cluster de serviço de contêiner do Azure

Neste artigo, vamos explorar como criar um balanceador de carga interno em um serviço de contêiner do Azure usando maratona kg gerenciado por um DC/SO. Isso permitirá que você dimensionar seus aplicativos horizontalmente. Ele também permitirá que você tire proveito do agente público e privado clusters colocando seu balanceadores de carga em cluster público e seus contêineres de aplicativo no cluster particular.

## <a name="prerequisites"></a>Pré-requisitos

[Implantar uma instância do serviço de contêiner do Azure](container-service-deployment.md) com tipo de orchestrator DC/sistema operacional e [garantir que seu cliente pode se conectar ao cluster](container-service-connect.md). 

## <a name="load-balancing"></a>Balanceamento de carga

Há duas camadas de balanceamento de carga do cluster de serviço do contêiner que será criamos: 

  1. Azure balanceador de carga fornece pontos de entrada pública (aqueles que os usuários finais atingirá). Isso é fornecido automaticamente pelo serviço de contêiner do Azure e é, por padrão, configurado para expor portas 80, 443 e 8080.
  2. O balanceador de carga de maratona (maratona kg) roteia solicitações de entrada para instâncias de contêiner que essas solicitações de serviço. Como podemos dimensionar os contêineres fornecendo nosso serviço web, maratona kg dinamicamente se adapta. Este balanceador de carga não é fornecido por padrão em seu serviço de contêiner, mas é muito fácil de instalar.

## <a name="marathon-load-balancer"></a>Balanceador de carga de maratona

Balanceador de carga de maratona dinamicamente reconfigura com base nos contêineres que você já implantou. Também é flexível a perda de um contêiner ou um agente - se isso ocorre, Apache Mesos simplesmente irá reiniciar o contêiner em outro lugar e maratona kg será adaptar.

Para instalar o balanceador de carga maratona você pode usar qualquer um dos DC/SO web UI ou da linha de comando.

### <a name="install-marathon-lb-using-dcos-web-ui"></a>Instalar maratona kg usando DC/SO Web UI

  1. Clique em 'Universo'
  2. Pesquisar 'Maratona kg'
  3. Clique em 'Instalar'

![Instalando maratona kg por meio da Interface de Web DC/SO](./media/dcos/marathon-lb-install.png)

### <a name="install-marathon-lb-using-the-dcos-cli"></a>Instalar kg maratona utilizando a CLI DC/SO

Depois de instalar a CLI DC/sistema operacional e assegurando que você pode conectar seu cluster, execute o seguinte comando do computador cliente:

```bash
dcos package install marathon-lb
```

Este comando instala automaticamente o balanceador de carga no cluster agentes público.

## <a name="deploy-a-load-balanced-web-application"></a>Implantar uma carga equilibrada aplicativo Web

Agora que temos o pacote maratona kg, podemos implantar um contêiner de aplicativo que desejamos balanceamento de carga. Neste exemplo, podemos implantará um servidor web simples usando a seguinte configuração:

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}

```

  * Definir o valor de `HAProxy_0_VHOST` para o FQDN do balanceador de carga para seus agentes. Este é o formulário `<acsName>agents.<region>.cloudapp.azure.com`. Por exemplo, se você criar um cluster de serviço de contêiner com nome `myacs` na região `West US`, o FQDN seria `myacsagents.westus.cloudapp.azure.com`. Você também pode encontrar isso olhando para o balanceador de carga com "agente" no nome quando você estiver procurando por meio dos recursos no grupo de recursos que você criou para contêiner de serviço no [portal do Azure](https://portal.azure.com).
  * Definir o servicePort a uma porta > = 10.000. Isso identifica o serviço que está sendo executado neste contêiner – maratona kg utiliza para identificar os serviços que ele deve saldo em.
  * Definir o `HAPROXY_GROUP` do rótulo para "externo".
  * Definir `hostPort` como 0. Isso significa que maratona arbitrariamente será alocar uma porta disponível.
  * Definir `instances` para o número de instâncias que você deseja criar. Você pode sempre dimensionar esses up e down mais tarde.

Vale a pena noing que por padrão que maratona implantará ao cluster particular, isso significa que a implantação acima só poderá ser acessada por meio de seu balanceador de carga, que geralmente é o comportamento que podemos desejada.

### <a name="deploy-using-the-dcos-web-ui"></a>Implantar usando a interface do usuário do DC/sistema operacional da Web

  1. Visite a página de maratona em http://localhost/marathon (depois de configurar sua [túnel SSH](container-service-connect.md) e clique em`Create Appliction`
  2. No `New Application` diálogo clique `JSON Mode` no canto superior direito
  3. Cole o JSON acima no editor
  4. Clique em`Create Appliction`

### <a name="deploy-using-the-dcos-cli"></a>Implantar utilizando a CLI DC/SO

Para implantar esse aplicativo com a CLI DC/SO simplesmente copie o JSON acima para um arquivo denominado `hello-web.json`e executar:

```bash
dcos marathon app add hello-web.json
```

## <a name="azure-load-balancer"></a>Balanceador de carga Azure

Por padrão, o balanceador de carga do Azure expõe portas 80, 8080 e 443. Se você estiver usando esses três portas (como fazemos no exemplo acima), nada que você precisa fazer. Você deve ser capaz acertar FQDN do balanceador de carga seu agente – e cada vez que você atualiza, você encontrará um dos seus servidores três web de forma alternada. No entanto, se você usar uma porta diferente, é necessário adicionar uma regra de alternada e um teste no balanceador de carga da porta que você usou. Você pode fazer isso da [CLI do Azure](../xplat-cli-azure-resource-manager.md), com os comandos `azure network lb rule create` e `azure network lb probe create`. Você também pode fazer isso usando o Portal do Azure.


## <a name="additional-scenarios"></a>Cenários adicionais

Você poderia ter um cenário em que você usar domínios diferentes para expor diferentes serviços. Por exemplo:

mydomain1.com -> Azure LB:80 -> maratona-lb:10001 -> mycontainer1:33292  
mydomain2.com -> Azure LB:80 -> maratona-lb:10002 -> mycontainer2:22321

Para fazer isso, confira [hosts virtuais](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/), que oferece uma maneira de associar domínios para caminhos de kg maratona específico.

Como alternativa, você pode expor portas diferentes e mapeá-los novamente para o serviço correto atrás maratona kg. Por exemplo:

Azure lb:80 -> maratona-lb:10001 -> mycontainer:233423  
Azure lb:8080 -> maratona-lb:1002 -> mycontainer2:33432


## <a name="next-steps"></a>Próximas etapas

Consulte a documentação de DC/SO para ativado mais [maratona kg](https://dcos.io/docs/1.7/usage/service-discovery/marathon-lb/).
