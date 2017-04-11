<properties
   pageTitle="Aplicativo ou serviço de maratona específicas do usuário | Microsoft Azure"
   description="Criar um aplicativo ou serviço de maratona específicas do usuário"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contêineres, maratona, Microserviços, DC/SO, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>

# <a name="create-an-application-or-user-specific-marathon-service"></a>Criar um aplicativo ou serviço de maratona específicas do usuário

Serviço de contêiner Azure fornece um conjunto de servidores mestres nos quais nós predefinir Apache Mesos e maratona. Elas podem ser usadas para organizar seus aplicativos no cluster, mas é melhor não usar os servidores mestres para essa finalidade. Por exemplo, ajuste a configuração de maratona requer registro em log para os servidores mestres e fazer alterações – isso incentiva exclusivos servidores mestres que estão um pouco diferente do padrão e precisam ser se importaria para e gerenciado independentemente. Além disso, a configuração necessária por uma equipe talvez não seja a configuração ideal para outra equipe.

Neste artigo, explicaremos como adicionar um aplicativo ou serviço de maratona específicas do usuário.

Porque esse serviço será pertencer a um único usuário ou equipe, eles são gratuitos configurá-lo da maneira que desejarem. Além disso, o serviço de contêiner do Azure garantirá que o serviço continuará a ser executado. Se o serviço falhar, o serviço do Azure contêiner será reiniciá-lo para você. Na maioria das vezes você mesmo não perceber que tinha o tempo de inatividade.

## <a name="prerequisites"></a>Pré-requisitos

[Implantar uma instância do serviço de contêiner do Azure](container-service-deployment.md) com tipo de orchestrator DC/sistema operacional e [garantir que seu cliente pode se conectar ao cluster](container-service-connect.md). Além disso, siga estas etapas.

[AZURE.INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>Criar um aplicativo ou serviço de maratona específicas do usuário

Comece criando um arquivo de configuração de JSON que define o nome do serviço do aplicativo que você deseja criar. Aqui, nós usamos `marathon-alice` como o nome de framework. Salve o arquivo como algo parecido com `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Em seguida, use a CLI DC/SO para instalar a instância maratona com as opções que são definidas no arquivo de configuração:

```bash
dcos package install --options=marathon-alice.json marathon
```

Agora você deve ver seu `marathon-alice` serviço em execução na guia Serviços da sua interface de usuário do DC/sistema operacional. A interface do usuário será `http://<hostname>/service/marathon-alice/` se desejar acessá-la diretamente.

## <a name="set-the-dcos-cli-to-access-the-service"></a>Definir a ILC DC/sistema operacional para acessar o serviço

Opcionalmente, você pode configurar sua CLI DC/SO para acessar esse novo serviço definindo a `marathon.url` propriedade para apontar para o `marathon-alice` instância da seguinte maneira:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Você pode verificar qual instância do maratona sua CLI está trabalhando em relação a `dcos config show` comando. Você pode reverter para usar o seu serviço maratona mestre com o comando `dcos config unset marathon.url`.
