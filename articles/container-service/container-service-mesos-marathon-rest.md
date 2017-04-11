<properties
   pageTitle="Gerenciamento de contêiner de serviço de contêiner Azure por meio da API REST | Microsoft Azure"
   description="Implante contêineres a um cluster de Mesos de serviço do Azure contêiner usando a API REST maratona."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contêineres, Microserviços, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="timlt"/>

# <a name="container-management-through-the-rest-api"></a>Gerenciamento de contêiner por meio da API REST

DC/sistema operacional fornece um ambiente para a implantação e dimensionamento cargas de trabalho agrupadas, enquanto abstrair o hardware subjacente. Na parte superior de DC/sistema operacional, não há uma estrutura que gerencia o planejamento e realização de cargas de trabalho de computação.

Embora estruturas estão disponíveis para muitas cargas de trabalho populares, este documento descreve como você pode criar e dimensionar implantações de contêiner usando maratona. Antes de realizar esses exemplos, é necessário um cluster de DC/SO que está configurado no serviço de contêiner do Azure. Você também precisa ter conectividade remota a esse cluster. Para obter mais informações sobre esses itens, consulte os seguintes artigos:

- [Implantando um cluster de serviço de contêiner do Azure](container-service-deployment.md)
- [Conectando a um cluster de serviço de contêiner do Azure](container-service-connect.md)

Depois que você estiver conectado ao cluster Azure contêiner serviço, você pode acessar o DC/SO e as APIs REST relacionadas através de porta http://localhost:local. Os exemplos neste documento pressupõem que você está túnel na porta 80. Por exemplo, o ponto de extremidade maratona possa ser encontrado `http://localhost/marathon/v2/`. Para obter mais informações sobre as diversas APIs, consulte a documentação Mesosphere [Maratona API](https://mesosphere.github.io/marathon/docs/rest-api.html) e a [API Chronos](https://mesos.github.io/chronos/docs/api.html)e documentação Apache a [API do Agendador de Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Reunir informações do DC/SO e maratona

Antes de implantar contêineres ao cluster DC/SO, colete informações sobre o cluster DC/sistema operacional, como os nomes e o status atual dos agentes DC/sistema operacional. Para fazer isso, consultar a `master/slaves` ponto de extremidade da API REST DC/sistema operacional. Se tudo correr bem, você verá uma lista de agentes de DC/sistema operacional e várias propriedades para cada um.

```bash
curl http://localhost/mesos/master/slaves
```

Agora, use a maratona `/apps` ponto de extremidade para verificar se há atuais implantações de aplicativo ao cluster DC/sistema operacional. Se esse for um novo cluster, você verá uma matriz vazia para os aplicativos.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Implantar um contêiner formatados como Docker

Você implanta contêineres formatados como Docker por meio de maratona usando um arquivo JSON que descreve a implantação pretendida. O exemplo a seguir implantará o contêiner Nginx, porta de vinculação 80 do agente DC/SO à porta 80 do contêiner. Observe também que a propriedade 'acceptedResourceRoles' está definida como 'slave_public'. Isso implantará o contêiner para um agente do conjunto de escala do agente de público.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Para implantar um contêiner formatados como Docker, criar seu próprio arquivo JSON ou use a amostra fornecida na [demonstração de serviço de contêiner do Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Armazená-lo em um local acessível. Em seguida, para implantar o contêiner, execute o seguinte comando. Especifique o nome do arquivo JSON.

```
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

O resultado será semelhante ao seguinte:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Agora, se você consulta maratona para aplicativos, este novo aplicativo mostrará na saída.

```
curl localhost/marathon/v2/apps
```

## <a name="scale-your-containers"></a>Dimensionar seus contêineres

Você também pode usar a API maratona para dimensionar ou escalar em implantações de aplicativos. No exemplo anterior, você distribuiu uma instância de um aplicativo. Vamos dimensionar este out três instâncias de um aplicativo. Para fazer isso, crie um arquivo JSON usando o seguinte texto JSON e armazená-la em um local acessível.

```json
{ "instances": 3 }
```

Execute o seguinte comando para dimensionar o aplicativo.

>[AZURE.NOTE] O URI será http://localhost/marathon/v2/apps/ e, em seguida, a ID do aplicativo para dimensionar. Se você estiver usando a amostra de Nginx fornecidos aqui, o URI seria http://localhost/marathon/v2/apps/nginx.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Por fim, consulte o ponto de extremidade maratona para aplicativos. Você verá que agora há três dos contêineres Nginx.

```
curl localhost/marathon/v2/apps
```

## <a name="use-powershell-for-this-exercise-marathon-rest-api-interaction-with-powershell"></a>Usar o PowerShell para este exercício: interação de API REST de maratona com PowerShell

Você pode executar essas mesmas ações usando os comandos do PowerShell em um sistema Windows.

Para obter informações sobre o cluster DC/SO, como nomes de agente e status do agente, execute o seguinte comando.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Você implanta contêineres formatados como Docker por meio de maratona usando um arquivo JSON que descreve a implantação pretendida. O exemplo a seguir implantará o contêiner Nginx, porta de vinculação 80 do agente DC/SO à porta 80 do contêiner.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Criar seu próprio arquivo JSON ou use a amostra fornecida na [demonstração de serviço de contêiner do Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Armazená-lo em um local acessível. Em seguida, para implantar o contêiner, execute o seguinte comando. Especifique o nome do arquivo JSON.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Você também pode usar a API maratona para dimensionar ou escalar em implantações de aplicativos. No exemplo anterior, você distribuiu uma instância de um aplicativo. Vamos dimensionar este out três instâncias de um aplicativo. Para fazer isso, crie um arquivo JSON usando o seguinte texto JSON e armazená-la em um local acessível.

```json
{ "instances": 3 }
```

Execute o seguinte comando para dimensionar o aplicativo.

> [AZURE.NOTE] O URI será http://localhost/marathon/v2/apps/ e, em seguida, a ID do aplicativo para dimensionar. Se você estiver usando a amostra de Nginx fornecida aqui, o URI seria http://localhost/marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Próximas etapas

- [Leia mais sobre os pontos de extremidade de HTTP Mesos]( http://mesos.apache.org/documentation/latest/endpoints/).
- [Leia mais sobre a API REST maratona]( https://mesosphere.github.io/marathon/docs/rest-api.html).
