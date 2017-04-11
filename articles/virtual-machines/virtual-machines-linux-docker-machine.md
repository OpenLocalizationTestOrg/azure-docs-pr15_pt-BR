<properties
    pageTitle="Criar hosts Docker no Azure com máquina Docker | Microsoft Azure"
    description="Descreve o uso da máquina Docker criar hosts docker no Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="07/22/2016"
    ms.author="rasquill"/>

# <a name="use-docker-machine-with-the-azure-driver"></a>Usar máquina de Docker com o driver do Azure

[Docker](https://www.docker.com/) é uma das abordagens virtualização mais populares que usa contêineres de Linux em vez de máquinas virtuais como uma forma de isolamento de dados de aplicativo e computação em recursos compartilhados. Este tópico descreve quando e como usar [Docker máquina](https://docs.docker.com/machine/) (o `docker-machine` comando) para criar novas VMs Linux no Azure habilitado como um host de docker para seus contêineres de Linux.


## <a name="create-vms-with-docker-machine"></a>Criar VMs com Docker máquina

Criar docker VMs de host no Azure com os `docker-machine create` comando usando o `azure` argumento de driver para a opção driver (`-d`) e os outros argumentos. 

O exemplo a seguir conta com os valores padrão, mas ele Abra porta 80 na máquina virtual à internet para teste com um contêiner de nginx, torna `ops` o usuário de logon para SSH e chamadas a máquina virtual novo `machine`. 

Tipo de `docker-machine create --driver azure` para ver as opções e seus valores padrão; Você também pode ler a [documentação do Driver do Azure Docker](https://docs.docker.com/machine/drivers/azure/). (Observe que se você tiver habilitada de autenticação de dois fatores, você será solicitado a autenticar usando o segundo fator.)

```bash
docker-machine create -d azure \
  --azure-ssh-user ops \
  --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> \
  --azure-open-port 80 \
  machine
```

A saída deve parecer como este, dependendo se você tiver configurada em sua conta de autenticação de dois fatores.

```
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(machine) Microsoft Azure: To sign in, use a web browser to open the page https://aka.ms/devicelogin. Enter the code <code> to authenticate.
(machine) Completed machine pre-create checks.
Creating machine...
(machine) Querying existing resource group.  name="machine"
(machine) Creating resource group.  name="machine" location="eastus"
(machine) Configuring availability set.  name="docker-machine"
(machine) Configuring network security group.  name="machine-firewall" location="eastus"
(machine) Querying if virtual network already exists.  name="docker-machine-vnet" location="eastus"
(machine) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(machine) Creating public IP address.  name="machine-ip" static=false
(machine) Creating network interface.  name="machine-nic"
(machine) Creating storage account.  name="vhdsolksdjalkjlmgyg6" location="eastus"
(machine) Creating virtual machine.  name="machine" location="eastus" size="Standard_A2" username="ops" osImage="canonical:UbuntuServer:15.10:latest"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env machine
```

## <a name="configure-your-docker-shell"></a>Configurar o shell de docker

Agora, digite `docker-machine env <VM name>` para ver o que você precisa fazer para configurar o shell. 

```bash
docker-machine env machine
```

Que imprime as informações de ambiente, que pode ter esta aparência. Observe que o endereço IP tiver sido atribuído, que você precisará testar a máquina virtual.

```
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://191.237.46.90:2376"
export DOCKER_CERT_PATH="/Users/rasquill/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env machine)
```

Você pode executar o comando de configuração sugerida ou você pode definir as variáveis de ambiente. 

## <a name="run-a-container"></a>Executar um contêiner

Agora você pode executar um servidor web simples para testar se tudo está funcionando corretamente. Aqui podemos usar uma imagem padrão nginx especificar que ele deve ouvir na porta 80 e que se a máquina virtual reiniciar o contêiner deve reiniciar também (`--restart=always`). 

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

E verifique o contêiner em execução, digite `docker-machine ip <VM name>` para localizar o endereço IP (se você esqueceu do `env` comando):

![Contêiner de ngnix em execução](./media/virtual-machines-linux-docker-machine/nginxsuccess.png)

## <a name="next-steps"></a>Próximas etapas

Se você estiver interessado, você pode experimentar o Azure [Docker máquina virtual extensão](virtual-machines-linux-dockerextension.md) fazer a mesma operação usando a CLI Azure ou modelos de Gerenciador de recurso Azure. 

Para obter mais exemplos de como trabalhar com Docker, consulte [Trabalhando com Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 conectar [demonstração](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Para mais guias de início rápido da demonstração de HealthClinic.biz, consulte [Início rápido de ferramentas de desenvolvedor do Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

