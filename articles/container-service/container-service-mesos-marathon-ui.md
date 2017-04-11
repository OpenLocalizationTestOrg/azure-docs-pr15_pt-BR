<properties
   pageTitle="Gerenciamento de contêiner de serviço de contêiner Azure por meio de web UI | Microsoft Azure"
   description="Implante contêineres em um serviço de cluster do serviço de contêiner do Azure usando web maratona UI."
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
   ms.date="09/19/2016"
   ms.author="timlt"/>

# <a name="container-management-through-the-web-ui"></a>Gerenciamento de contêiner através do interface do usuário da web

DC/sistema operacional fornece um ambiente para a implantação e dimensionamento cargas de trabalho agrupadas, enquanto abstrair o hardware subjacente. Na parte superior de DC/sistema operacional, não há uma estrutura que gerencia o planejamento e realização de cargas de trabalho de computação.

Enquanto estruturas estão disponíveis para muitos cargas de trabalho populares, este documento descreverá como você pode criar e dimensionar implantações de contêiner com maratona. Antes de realizar esses exemplos, você precisará de um cluster de DC/SO que está configurado no serviço de contêiner do Azure. Você também precisa ter conectividade remota a esse cluster. Para obter mais informações sobre esses itens, consulte os seguintes artigos:

- [Implantar um cluster de serviço de contêiner do Azure](container-service-deployment.md)
- [Conectar a um cluster de serviço de contêiner do Azure](container-service-connect.md)

## <a name="explore-the-dcos-ui"></a>Explorar o DC/SO interface do usuário

Com um túnel SSH (Secure Shell) estabelecido, navegue até http://localhost/. Isso carrega web DC/SO UI e mostra informações sobre o cluster, como recursos usados, agentes de ativos e serviços em execução.

![INTERFACE DE USUÁRIO DE DC/SO](media/dcos/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Explorar a maratona interface do usuário

Para ver a interface do usuário maratona, navegue até http://localhost/Marathon. Nessa tela, você pode iniciar um novo recipiente ou outro aplicativo no cluster Service para contêiner Azure DC/sistema operacional. Você também pode ver informações sobre como executar contêineres e aplicativos.  

![Maratona interface do usuário](media/dcos/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Implantar um contêiner formatados como Docker

Para implantar um novo contêiner usando maratona, clique no botão **Criar aplicativo** e insira as seguintes informações no formulário:

Campo           | Valor
----------------|-----------
ID              | nginx
Imagem           | nginx
Rede         | Uma ponte
Porta de host       | 80
Protocolo        | TCP

![Novo aplicativo UI - geral](media/dcos/dcos4.png)

![Novo aplicativo de interface do usuário – Docker contêiner](media/dcos/dcos5.png)

![Novo aplicativo de interface do usuário – portas e descoberta de serviço](media/dcos/dcos6.png)

Se você quiser mapear estática a porta de contêiner para uma porta do agente, você precisa usar o modo de JSON. Para fazer isso, alterne o Assistente de novo aplicativo para **O modo de JSON** usando o botão de alternância. Insira o seguinte sob o `portMappings` seção da definição de aplicativo. Este exemplo vincula a porta 80 do contêiner para a porta 80 do agente DC/sistema operacional. Você pode alternar esse assistente sair do modo de JSON após fazer essa alteração.

```none
"hostPort": 80,
```

![Novo aplicativo de interface do usuário – exemplo de porta 80](media/dcos/dcos13.png)

Cluster DC/SO é implantado com o conjunto de agentes públicos e particulares. Para o cluster poder acessar os aplicativos da Internet, você precisa implantar os aplicativos para um agente de público. Para fazer isso, selecione a guia **opcional** do Assistente para novo aplicativo e insira **slave_public** para as **Funções de recurso aceitas**.

![Novo aplicativo de interface do usuário – público agente de configuração](media/dcos/dcos14.png)

Volte à página principal maratona, você pode ver o status de implantação do contêiner.

![Página principal do maratona UI – status de implantação do contêiner](media/dcos/dcos7.png)

Quando você alterna para a web DC/SO UI (http://localhost/), você verá que uma tarefa (neste caso, um contêiner formatados como Docker) está sendo executado no cluster DC/sistema operacional.

![Interface do usuário – tarefa em execução no cluster de web DC/SO](media/dcos/dcos8.png)

Você também pode ver o nó de cluster que a tarefa estiver em execução no.

![Interface do usuário – nó de cluster de tarefa da web DC/SO](media/dcos/dcos9.png)

## <a name="scale-your-containers"></a>Dimensionar seus contêineres

Você pode usar a interface do usuário maratona dimensionar a contagem de instância de um contêiner. Para fazer isso, navegue até a página **maratona** , selecione o recipiente que você deseja dimensionar e clique no botão de **escala** . Na caixa de diálogo **Escala de aplicativo** , insira o número de instâncias do contêiner desejado e selecione **Escala de aplicativo**.

![Maratona UI - caixa de diálogo de aplicativo de escala](media/dcos/dcos10.png)

Após concluir a operação de escala, você verá várias instâncias da mesma tarefa difundir através de agentes de DC/sistema operacional.

![Painel de interface do usuário do web DC/SO - disseminação de tarefas entre agentes](media/dcos/dcos11.png)

![Interface do usuário – nós de web DC/SO](media/dcos/dcos12.png)

## <a name="next-steps"></a>Próximas etapas

- [Trabalhar com DC/sistema operacional e a API maratona](container-service-mesos-marathon-rest.md)

Detalhamento do serviço de contêiner do Azure com Mesos

> [AZURE. Azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos de vídeo]]
