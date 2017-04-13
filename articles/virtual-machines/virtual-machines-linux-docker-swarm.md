<properties
   pageTitle="Começando a usar docker com por nuvem no Azure"
   description="Descreve como criar um grupo de VMs com a extensão de máquina virtual Docker e usar por nuvem para criar um cluster de Docker."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="01/04/2016"
   ms.author="rasquill"/>

# <a name="how-to-use-docker-with-swarm"></a>Como usar docker com por nuvem

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Este tópico mostra uma maneira muito simples usar [docker](https://www.docker.com/) com [swarm](https://github.com/docker/swarm) para criar um cluster gerenciado por nuvem no Azure. Ele cria quatro máquinas virtuais no Azure, uma para agir como o Gerenciador de por nuvem e três como parte do cluster de hosts de docker. Quando tiver terminado, você pode usar por nuvem para ver o cluster e, em seguida, começar a usar docker nele. Além disso, as chamadas do Azure CLI neste tópico usam o modo de gerenciamento (asm) do serviço. 

> [AZURE.NOTE] Este tópico usa docker com por nuvem e o Azure CLI *sem* usar **docker máquina** para mostrar como as diferentes ferramentas funcionam juntos, mas permanecem independentes. **docker-máquina** tem **– swarm** opções que permitem que você use **docker máquina** diretamente adicionar nós por uma nuvem. Por exemplo, consulte a documentação de [máquina docker](https://github.com/docker/machine) . Se você perdeu **docker máquina** executando contra VMs do Azure, veja [como usar docker-máquina com o Azure](virtual-machines-linux-docker-machine.md).

## <a name="create-docker-hosts-with-azure-virtual-machines"></a>Criar hosts docker com máquinas virtuais do Azure

Este tópico cria quatro VMs, mas você pode usar qualquer número desejado. Chamar o seguinte com * &lt;senha&gt; * substituídos pela senha que você escolheu.

    azure vm docker create swarm-master -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-1 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-2 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-3 -l "East US" -e 22 $imagename ops <password>

Quando terminar, você poderá usar a **lista de máquina virtual azure** para ver suas VMs Azure:

    $ azure vm list | grep "swarm-[mn]"
    data:    swarm-master     ReadyRole           East US       swarm-master.cloudapp.net                               100.78.186.65
    data:    swarm-node-1     ReadyRole           East US       swarm-node-1.cloudapp.net                               100.66.72.126
    data:    swarm-node-2     ReadyRole           East US       swarm-node-2.cloudapp.net                               100.72.18.47  
    data:    swarm-node-3     ReadyRole           East US       swarm-node-3.cloudapp.net                               100.78.24.68  

## <a name="installing-swarm-on-the-swarm-master-vm"></a>Instalando por nuvem no mestre por nuvem máquina virtual

Este tópico usa o [modelo de contêiner de instalação a partir do docker swarm documentação](https://github.com/docker/swarm#1---docker-image) , mas também é possível SSH no **mestre por nuvem**. Nesse modelo, **swarm** é baixado como um contêiner de docker executando por nuvem. Abaixo, podemos executar esta etapa *remotamente a partir de nosso laptop usando docker* para se conectar ao **mestre por nuvem** máquina virtual e orientá-lo a usar o comando de criação de id de cluster, **Criar por nuvem**. A id de cluster é como **swarm** detecta os membros do grupo por nuvem. (Você também pode clonar o repositório e criá-lo por conta própria, que dão controle total e habilitar a depuração.)

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm create
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    36731c17189fd8f450c395db8437befd

Essa última linha é a id de cluster; copiá-lo em outro porque você usará-lo novamente quando você ingressa o nó VMs ao mestre por nuvem para criar o por "nuvem". Neste exemplo, a id de cluster é **36731c17189fd8f450c395db8437befd**.

> [AZURE.NOTE] Apenas para ser claro, estamos usando nossa instalação docker local para se conectar ao **mestre por nuvem** máquina virtual do Azure e instruções **por nuvem mestre** para baixar, instalar e executar o comando **criar** , que retorna nosso id de cluster que usamos para fins de detecção mais tarde.
<!-- -->
> Para confirmar isso, execute `docker -H tcp://` * &lt;hostname&gt; * ` images` para listar os processos de contêiner na máquina **por nuvem mestre** e em outro nó para comparação (porque podemos executou o comando por nuvem anterior com a opção **- rm** , o contêiner foi removido após ela, isso usando **docker ps - um** não retornará nada).:


        $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        swarm               latest              92d78d321ff2        11 days ago         7.19 MB
        $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        $
<P />
>Se você estiver familiarizado com **docker**, você saberá que outros nós não têm entradas porque não há imagens foram baixadas e ainda executadas.

## <a name="join-the-node-vms-to-our-docker-cluster"></a>Unir o nó VMs a nossa cluster docker

Para cada nó, lista as informações de ponto de extremidade usando a CLI do Azure. Abaixo podemos fazer isso para o host de docker **por nuvem-nó-1** para obter a porta de docker do nó.

    $ azure vm endpoint list swarm-node-1
    info:    Executing command vm endpoint list
    + Getting virtual machines
    data:    Name    Protocol  Public Port  Private Port  Virtual IP      EnableDirectServerReturn  Load Balanced
    data:    ------  --------  -----------  ------------  --------------  ------------------------  -------------
    data:    docker  tcp       2376         2376          138.91.112.194  false                     No
    data:    ssh     tcp       22           22            138.91.112.194  false                     No
    info:    vm endpoint list command OK


Usando o **docker** e o `-H` opção para apontar o cliente docker em sua máquina virtual, nó ingressar nó o por nuvem que você está criando passando a id de cluster e porta de docker do nó (o último usando **- addr**):

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 run -d swarm join --addr=138.91.112.194:2376 token://36731c17189fd8f450c395db8437befd
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    bbf88f61300bf876c6202d4cf886874b363cd7e2899345ac34dc8ab10c7ae924

Parece interessante. Para confirmar que **swarm** está em execução em **por nuvem-nó-1** que digitamos:

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 ps -a
        CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
        bbf88f61300b        swarm:latest        "/swarm join --addr=   13 seconds ago      Up 12 seconds       2375/tcp            angry_mclean

Repita para todos os outros nós no cluster. Em nosso caso, podemos fazer isso para **por nuvem-nó-2** e **por nuvem-nó-3**.

## <a name="begin-managing-the-swarm-cluster"></a>Começar a gerenciar o cluster por nuvem

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run -d -p 2375:2375 swarm manage token://36731c17189fd8f450c395db8437befd
    d7e87c2c147ade438cb4b663bda0ee20981d4818770958f5d317d6aebdcaedd5

e, em seguida, você pode listar seus nós no seu cluster:

    ralph@local:~$ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm list token://73f8bc512e94195210fad6e9cd58986f
    54.149.104.203:2375
    54.187.164.89:2375
    92.222.76.190:2375

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas

Vá executar coisas no seu por nuvem. Para encontrar inspiração, consulte [https://github.com/docker/swarm/](https://github.com/docker/swarm/)ou talvez um [vídeo](https://www.youtube.com/watch?v=EC25ARhZ5bI).

<!-- links -->

[docker-machine-azure]: virtual-machines-linux-docker-machine.md
 
