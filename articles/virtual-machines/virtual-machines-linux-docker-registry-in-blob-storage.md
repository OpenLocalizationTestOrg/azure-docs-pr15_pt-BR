<properties 
  pageTitle="Implantando o seu próprio registro Docker particular no Azure | Microsoft Azure"
  description="Descreve como você pode usar o registro de Docker para hospedar suas imagens de contêiner o serviço de armazenamento de Blob do Azure."
  services="virtual-machines-linux"
  documentationCenter="virtual-machines"
  authors="ahmetalpbalkan"
  editor="squillace"
  manager="timlt"
  tags="azure-service-management,azure-resource-manager" />

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="multiple"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="09/27/2016" 
  ms.author="ahmetb" />

# <a name="deploying-your-own-private-docker-registry-on-azure"></a>Implantando o seu próprio registro Docker particular no Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



Este documento descreve quais Docker registro particular é e mostra como você pode implantar uma imagem de contêiner Docker registro 2.0 para um registro de particular Docker no Microsoft Azure usando o armazenamento de Blob do Azure.

Este documento presume:

1. Você sabe como usar Docker e ter Docker imagens para armazenar. (Você não? [Saiba mais sobre Docker](https://www.docker.com))
2. Você tem um servidor que tenha o mecanismo de Docker instalado. (Você não? [Fazer isso rapidamente no Azure.](https://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/))


## <a name="what-is-a-private-docker-registry"></a>O que é um registro de Docker particular?

Para enviar seus aplicativos contidos na nuvem, você construir uma imagem de contêiner Docker e armazená-la em outro, para que ele pode ser usado por si mesmo e por outras pessoas. 

Embora é fácil criar uma imagem de contêiner e entrega para a nuvem, é um desafio para armazenar a imagem gerada de forma confiável. Por que motivo, Docker oferece um serviço centralizado chamado [Hub de Docker] [ docker-hub] para armazenar contêiner imagens na nuvem e permite que você crie contêineres a qualquer momento usando essas imagens.

Embora o [Hub de Docker] [ docker-hub] é um serviço pago para armazenar seu contêiner particular do aplicativo de imagens, às necessidades dos desenvolvedores de aspectos Docker e fornece um conjunto de ferramentas de código-fonte aberto para armazenar as imagens no seu próprio registro Docker privado atrás de um firewall ou local sem atingir a Internet pública.
Como armazenamento de Blob do Azure é fácil proteger, você pode usá-lo rapidamente para criar e usar um registro de Docker particular no Azure que você controle por conta própria.

## <a name="why-should-you-host-a-docker-registry-on-azure"></a>Por que você deve hospedar um registro de Docker no Azure?

Hospedando sua instância de registro de Docker no Microsoft Azure e armazenando suas imagens no armazenamento de Blob do Azure, você pode ter vários benefícios:

**Segurança:** As imagens de Docker não deixam dos data centers do Azure, para que eles não passam Internet pública como faria se estivesse usando Docker Hub.
  
**Desempenho:** As imagens de Docker são armazenadas no mesmo data center ou região como seus aplicativos. Isso significa que as imagens serão transferidas rápido e mais confiável em comparação com o Hub de Docker.

**Confiabilidade:** Usando o armazenamento de Blob do Microsoft Azure, você pode fazer uso das muitas propriedades de armazenamento como alta disponibilidade, redundância, armazenamento premium SSDs () e assim por diante.

## <a name="configuring-docker-registry-to-use-azure-blob-storage"></a>Configurando o registro de Docker para usar o armazenamento de Blob do Azure

(Recomenda-se para ler a [documentação de Docker registro 2.0][documentos do registro] antes de prosseguir.)

Você pode [Configurar] [ registry-config] seu registro de Docker de duas maneiras diferentes.
É possível:

1. Usar um `config.yml` arquivo. Nesse caso você precisará criar uma imagem de Docker separada na parte superior do `registry` imagem.
2. Substituir o arquivo de configuração padrão por meio de variáveis de ambiente: obtém coisas feitas sem criar e manter uma imagem Docker separada.

Para simplificar, este tópico segue opção 2, usando as variáveis de ambiente.

Para executar um registro de Docker instância que:

* usa sua conta de armazenamento do Azure para armazenar as imagens
* ouve na porta da máquina Virtual 5000
* não tem nenhuma autenticação configurada (não recomendado, consulte a observação abaixo)

Você precisa executar o seguinte comando Docker no terminal bash (substituir `<storage-account>` e `<storage-key>` com credenciais):

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

Depois que o comando sai, você pode ver o contêiner hospedando sua instância de registro de Docker particular executando o `docker ps` comando no seu host:

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT] Configurando a segurança para o registro de Docker não é coberto neste documento e seu registro será acessado por qualquer pessoa sem autenticação por padrão se você abre a porta para a porta de registro no ponto de extremidade de máquina Virtual ou balanceador de carga, se você usar o comando de implantação acima.
>
> Leia o [Registro de Docker Configurando] [ registry-config] documentação para saber como proteger a instância de registro e suas imagens.

## <a name="next-steps"></a>Próximas etapas

Quando você tiver seu registro configurar, é hora de ir usá-lo mais. Comece com o docker [documentos do registro]. 

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[documentos do registro]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/
 
