<properties
   pageTitle="Docker e redigir em uma máquina virtual | Microsoft Azure"
   description="Rápida introdução a trabalhar com redigir e Docker no Linux máquinas virtuais do Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="iainfou"/>

# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-on-an-azure-virtual-machine"></a>Introdução ao Docker e redigir definir e executar um aplicativo de contêiner vários em uma máquina virtual Azure

Introdução ao uso Docker e [Redigir](http://github.com/docker/compose) definir e executar um aplicativo complexo em um computador de virtual Linux no Azure. Com redigir, você pode usar um arquivo de texto simples para definir um aplicativo que consiste em vários contêineres de Docker. Você e rotação seu aplicativo em um único comando que faz tudo para implantar seu ambiente definido. 

Como exemplo, este artigo mostra como configurar rapidamente um blog WordPress com um banco de dados do SQL MariaDB em uma VM Ubuntu de back-end. Você também pode usar redigir configurar aplicativos mais complexos.


## <a name="step-1-set-up-a-linux-vm-as-a-docker-host"></a>Etapa 1: Configurar uma VM Linux como um host de Docker

Você pode usar vários procedimentos Azure e imagens disponíveis ou modelos do Gerenciador de recursos do Azure Marketplace para criar uma VM Linux e configurá-lo como um host de Docker. Por exemplo, consulte [usando a extensão de máquina virtual Docker para implantar o seu ambiente](virtual-machines-linux-dockerextension.md) para criar rapidamente uma VM Ubuntu com a extensão de máquina virtual do Azure Docker usando um [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Quando você usa a extensão de máquina virtual Docker, sua máquina virtual é definido automaticamente como um host de Docker e redigir já está instalado. O exemplo nesse artigo mostra como usar a [interface de linha de comando Azure para Mac, Linux e Windows](../xplat-cli-install.md) do Azure no modo do Gerenciador de recursos para criar a máquina virtual.

O comando básico do documento anterior cria um grupo de recursos denominado `myResourceGroup` no `West US` local e implanta uma máquina virtual com a extensão de máquina virtual do Azure Docker instalada:

```bash
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

## <a name="step-2-verify-that-compose-is-installed"></a>Etapa 2: Confirmar que redigir está instalado

Depois que a implantação for concluída, SSH para seu novo host Docker usando o DNS nome que você forneceu durante a implantação. Você pode usar `azure vm show -g myDockerResourceGroup -n myDockerVM` para exibir os detalhes da sua máquina virtual, incluindo o nome DNS.

Para verificar que redigir está instalado na máquina virtual, execute o seguinte comando:

```bash
docker-compose --version
```

Você verá a saída semelhante ao `docker-compose 1.6.2, build 4d72027`.

>[AZURE.TIP] Se você usou outro método para criar um host Docker e precisa instalar redigir por conta própria, consulte a [documentação de redigir](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="step-3-create-a-docker-composeyml-configuration-file"></a>Etapa 3: Criar um arquivo de configuração de docker-compose.yml

Em seguida você cria uma `docker-compose.yml` arquivo, que é apenas um texto arquivo de configuração para definir os contêineres de Docker para executar na máquina virtual. O arquivo Especifica a imagem para executar em cada contêiner (ou pode ser uma compilação de um Dockerfile), variáveis de ambiente necessárias e dependências, portas e os links entre contêineres. Para obter detalhes sobre a sintaxe do arquivo yml, consulte [referência de arquivo de redigir](http://docs.docker.com/compose/yml/).

Criar a `docker-compose.yml` arquivo da seguinte maneira:

```bash
touch docker-compose.yml
```

Use o editor de texto preferido para adicionar alguns dados para o arquivo. O exemplo a seguir usa a `vi` editor:

```bash
vi docker-compose.yml
```

Cole o exemplo a seguir em seu arquivo de texto. Essa configuração usa imagens do [Registro de DockerHub](https://registry.hub.docker.com/_/wordpress/) instalar WordPress (o código-fonte aberto blogs e conteúdo sistema de gerenciamento) e um banco de dados MariaDB SQL de back-end vinculado. Inserir suas próprias `MYSQL_ROOT_PASSWORD` da seguinte maneira:

```bash
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="step-4-start-the-containers-with-compose"></a>Etapa 4: Iniciar os contêineres com redigir

Na mesma pasta que sua `docker-compose.yml` arquivo, execute o seguinte comando (dependendo do seu ambiente, talvez seja necessário executar `docker-compose` usando `sudo`.):

```bash
docker-compose up -d

```

Este comando inicia os contêineres de Docker especificados em `docker-compose.yml`. Leva um ou dois minutos para concluir esta etapa. Você verá a saída semelhante ao seguinte exemplo:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

>[AZURE.NOTE] Certifique-se de usar a opção **-d** na inicialização para que os contêineres executado em segundo plano continuamente.

Para verificar se os contêineres são para cima, digite `docker-compose ps`. Você verá algo como:

```bash
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

Agora, você pode conectar a WordPress diretamente sobre a máquina virtual na porta 80. Abra um navegador da web e digite o nome DNS da sua máquina virtual (tais como `http://myresourcegroup.westus.cloudapp.azure.com`). Agora você deve ver o WordPress tela inicial, onde você pode concluir a instalação e começar a usar o aplicativo.

![Na tela inicial do WordPress][wordpress_start]


## <a name="next-steps"></a>Próximas etapas

* Ir para o [Guia do usuário de extensão de máquina virtual Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) para obter mais opções configurar Docker e redigir em sua máquina virtual Docker. Por exemplo, uma opção é colocar o arquivo de yml redigir (convertido em JSON) diretamente na configuração da extensão máquina virtual Docker.
* Consulte o [Guia do usuário](http://docs.docker.com/compose/) para obter mais exemplos de criação e implantação contêiner de vários aplicativos e a [referência de linha de comando de redigir](http://docs.docker.com/compose/reference/) .
* Usar um modelo do Gerenciador de recursos do Azure, seu próprio ou um contribuiu da [comunidade](https://azure.microsoft.com/documentation/templates/), para implantar uma VM Azure com Docker e um aplicativo configurado com redigir. Por exemplo, o modelo de [implantar um blog WordPress com Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) usa Docker e redigir para implantar rapidamente WordPress com um back-end MySQL em uma VM Ubuntu.
* Tente Docker compor a integração com um cluster [Docker Swarm](virtual-machines-linux-docker-swarm.md) . Consulte [Usando Redigir com por nuvem](https://docs.docker.com/compose/swarm/) para cenários.

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png
