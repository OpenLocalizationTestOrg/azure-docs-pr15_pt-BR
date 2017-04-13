<properties
   pageTitle="Criar hosts Docker no Azure com máquina Docker | Microsoft Azure"
   description="Descreve o uso da máquina Docker criar hosts docker no Azure."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Criar Docker Hosts no Azure com Docker máquina

Executar [Docker](https://www.docker.com/) contêineres requer um host de máquina virtual executando o daemon docker.
Este tópico descreve como usar o comando [docker-machine](https://docs.docker.com/machine/) para criar novas VMs Linux, configurado com o daemon Docker, em execução no Azure. 

**Observação:** 
- *Este artigo depende da versão de máquina docker 0.7.0 ou maior*
- *Contêineres de Windows serão suportados por meio de máquina docker no futuro próximo*

## <a name="create-vms-with-docker-machine"></a>Criar VMs com Docker máquina

Criar docker VMs de host no Azure com os `docker-machine create` comando usando o `azure` driver. 

O driver Azure precisarão sua ID de assinatura. Você pode usar o [Azure CLI](xplat-cli-install.md) ou o [Portal do Azure](https://portal.azure.com) para recuperar sua assinatura do Azure. 

**Usando o Portal do Azure**
- Selecione assinaturas na página de navegação à esquerda e copie a id da assinatura.

**Usando a CLI do Azure**
- Tipo de ```azure account list``` e copie a id da assinatura.

Tipo de `docker-machine create --driver azure` para ver as opções e seus valores padrão.
Você também pode ver a [documentação do Driver do Azure Docker](https://docs.docker.com/machine/drivers/azure/) para obter mais informações. 

O exemplo a seguir conta com os valores padrão, mas ele, opcionalmente, abra a porta 80 na máquina virtual para acesso à internet. 

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-open-port 80 mydockerhost
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Escolha um host docker com docker máquina
Quando você tiver uma entrada na máquina docker do seu host, você pode definir o host padrão durante a execução de comandos de docker.
##<a name="using-powershell"></a>Usando o PowerShell

```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

##<a name="using-bash"></a>Usando Bash

```bash
eval $(docker-machine env MyDockerHost)
```

Agora você pode executar comandos docker contra o host especificado

```
docker ps
docker info
```

## <a name="run-a-container"></a>Executar um contêiner

Com um host configurado, agora você pode executar um servidor web simples para testar se seu host foi configurado corretamente.
Aqui podemos usar uma imagem de nginx padrão, especificar que ele deve ouvir na porta 80 e que se o host máquina virtual reiniciar, o contêiner também reiniciar (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

A saída deve parecer com o seguinte:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>Testar o contêiner

Examine os contêineres de execução usando `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

E verifique o contêiner em execução, digite `docker-machine ip <VM name>` para localizar o endereço IP para entrar no navegador:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Contêiner de ngnix em execução](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

##<a name="summary"></a>Resumo
Com docker máquina você pode facilmente provisionar hosts docker no Azure para seu validações de host docker individuais.
Para produção hospedagem de contêineres, consulte o [Serviço de contêiner do Azure](http://aka.ms/AzureContainerService)

Para desenvolver aplicativos de núcleo de .NET com o Visual Studio, consulte [Docker ferramentas para Visual Studio](http://aka.ms/DockerToolsForVS)
