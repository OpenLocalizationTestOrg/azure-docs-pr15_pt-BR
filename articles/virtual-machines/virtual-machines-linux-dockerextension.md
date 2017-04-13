<properties
   pageTitle="Usando a extensão de máquina virtual do Azure Docker | Microsoft Azure"
   description="Saiba como usar a extensão de máquina virtual Docker rapidamente e com segurança implantar um ambiente de Docker no Azure usando modelos do Gerenciador de recursos."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/25/2016"
   ms.author="iainfou"/>

# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Criar um ambiente de Docker no Azure usando a extensão de máquina virtual Docker
Docker é uma plataforma de imagens que permite trabalhar rapidamente com contêineres no Linux (e Windows também) e o gerenciamento de contêiner populares. No Azure, há várias maneiras que você pode implantar Docker acordo com suas necessidades. Este artigo se concentra nos usando a extensão de máquina virtual Docker e modelos do Gerenciador de recursos do Azure. 

Para obter mais informações sobre os diferentes métodos de implantação, incluindo o uso de máquina de Docker e serviços de contêiner do Azure, consulte os seguintes artigos:

- Ao protótipo rapidamente um aplicativo, você pode criar um único host Docker usando [Docker máquina](./virtual-machines-linux-docker-machine.md).
- Para ambientes maiores e mais estáveis, você pode usar a extensão de máquina virtual do Azure Docker, que também suporta [Docker compor](https://docs.docker.com/compose/overview/) para gerar implantações de contêiner consistentes. Este detalhes de artigo usando a extensão de máquina virtual do Azure Docker.
- Para criar ambientes de produção-preparar, escaláveis que fornecem ferramentas adicionais de agendamento e gerenciamento, você pode implantar um [cluster Docker Swarm em serviços de contêiner do Azure](../container-service/container-service-deployment.md).


## <a name="azure-docker-vm-extension-overview"></a>Visão geral de extensão do Azure máquina virtual Docker
A extensão de máquina virtual do Azure Docker instala e configura o daemon Docker, Docker cliente e compor Docker na máquina virtual Linux (máquina virtual). Usando a extensão de máquina virtual do Azure Docker, você tem mais controle e recursos que simplesmente usando Docker máquina ou criando o host Docker por conta própria. Esses recursos adicionais, como [Docker redigir](https://docs.docker.com/compose/overview/), verifique a extensão de máquina virtual do Azure Docker adequada para ambientes de produção ou desenvolvedor mais robusto.

Os modelos de Gerenciador de recursos Azure definem toda a estrutura do seu ambiente. Modelos permitem criar e configurar recursos como o host de Docker VMs, armazenamento, controles de acesso baseado em função (RBAC) e diagnóstico. Você pode reutilizar esses modelos para criar implantações adicionais de maneira consistente. Para obter mais informações sobre o Gerenciador de recursos do Azure e modelos, consulte [Visão geral do Gerenciador de recursos](../azure-resource-manager/resource-group-overview.md). 


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Implantar um modelo com a extensão de máquina virtual do Azure Docker
Vamos usar um modelo de início rápido existente para criar uma VM Ubuntu que usa a extensão de máquina virtual do Azure Docker instalar e configurar o host de Docker. Você pode exibir o modelo aqui: [implantação simples de uma VM Ubuntu com Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Você precisa de [Mais recente do Azure CLI](../xplat-cli-install.md) instalados e conectado usando o modo do Gerenciador de recursos da seguinte maneira:

```
azure config mode arm
```

Implante o modelo usando a CLI do Azure, especificando o modelo de URI. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no `WestUS` local. Use seu próprio nome de grupo de recursos e o local da seguinte maneira:

```
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Atenda os prompts para nomear sua conta de armazenamento, forneça um nome de usuário e senha e forneça um nome DNS. A saída é semelhante ao seguinte exemplo:

```
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicip
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

Retorna o Azure CLI para o aviso após apenas alguns segundos, mas seu host Docker ainda está sendo criado e configurado pela extensão de máquina virtual do Azure Docker. Leva alguns minutos para a implantação concluir. Você pode exibir detalhes sobre o status de host de Docker usando o `azure vm show` comando.

O exemplo a seguir verifica o status da máquina virtual chamado `myDockerVM` (o nome padrão do modelo - não alterar esse nome) no grupo de recursos denominado `myResourceGroup`. Digite o nome do grupo de recursos que você criou na etapa anterior:

```bash
azure vm show -g myResourceGroup -n myDockerVM
```

A saída do `azure vm show` comando é semelhante ao seguinte exemplo:

```
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicip.westus.cloudapp.azure.com]
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

Próximo à parte superior da saída, você vê o `ProvisioningState` da máquina virtual. Quando isso exibe `Succeeded`, a implantação terminou e você poderá SSH para a máquina virtual.

Até o final da saída, `FQDN` exibe o nome de domínio totalmente qualificado do seu host de Docker. Este FQDN é o que você usa para SSH em seu host Docker das etapas restantes.


## <a name="deploy-your-first-nginx-container"></a>Implantar o primeiro contêiner de nginx
Quando a implantação tiver terminado, SSH para seu novo host Docker do seu computador local. Insira seu próprio nome de usuário e o FQDN da seguinte maneira:

```bash
ssh ops@mypublicip.westus.cloudapp.azure.com
```

Quando conectado ao host de Docker, vamos executar um contêiner de nginx:

```
sudo docker run -d -p 80:80 nginx
```

A saída é semelhante ao seguinte exemplo conforme a imagem nginx é baixada e um contêiner de Introdução:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Verificar o status dos contêineres em execução no seu host Docker da seguinte maneira:

```
sudo docker ps
```

A saída é semelhante ao seguinte exemplo, mostrando que o contêiner nginx é executado e portas TCP 80 e 443 e sejam encaminhadas:

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Para ver seu contêiner em ação, abra um navegador da web e insira o nome FQDN do seu host Docker:

![Contêiner de ngnix em execução](./media/virtual-machines-linux-dockerextension/nginxrunning.png)


## <a name="azure-docker-vm-extension-template-reference"></a>Referência do modelo da extensão da máquina virtual Docker Azure
O exemplo anterior usa um modelo de início rápido existente. Você também pode implantar a extensão de máquina virtual do Azure Docker com seus próprios modelos do Gerenciador de recursos. Para fazer isso, adicione o seguinte ao seus modelos do Gerenciador de recursos, definindo a `vmName` da sua máquina virtual adequadamente:

```
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Você pode encontrar mais detalhada passo a passo sobre como usar modelos do Gerenciador de recursos lendo [Visão geral do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md).


## <a name="next-steps"></a>Próximas etapas
Talvez você queira [Configurar o daemon Docker porta TCP](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), compreender [segurança Docker](https://docs.docker.com/engine/security/security/)ou implantar contêineres usando [Docker redigir](https://docs.docker.com/compose/overview/). Para obter mais informações sobre a extensão de máquina virtual do Azure Docker em si, consulte o [GitHub projeto](https://github.com/Azure/azure-docker-extension/).

Leia mais informações sobre as opções de implantação de Docker adicionais no Azure:

- [Usar máquina de Docker com o driver do Azure](./virtual-machines-linux-docker-machine.md)  
- [Introdução ao Docker e redigir a definir e executar um aplicativo de contêiner de vários em uma máquina virtual Azure](virtual-machines-linux-docker-compose-quickstart.md).
- [Implantar um cluster de serviço de contêiner do Azure](../container-service/container-service-deployment.md)
