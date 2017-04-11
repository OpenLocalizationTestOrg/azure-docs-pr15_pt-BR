<properties
    pageTitle="Usando a extensão de máquina virtual Docker para Linux no Azure"
    description="Descreve Docker e as extensões de máquinas virtuais do Azure e mostra como criar programaticamente máquinas virtuais no Azure que são hosts de docker da linha de comando utilizando a CLI do Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="08/29/2016"
    ms.author="rasquill"/>

# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>Usando a extensão de máquina virtual Docker do Azure de linha de comando a Interface (Azure CLI)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]



Este tópico descreve como criar uma máquina virtual com a extensão de máquina virtual Docker do modo de gerenciamento (asm) de serviço no Azure CLI em qualquer plataforma. [Docker](https://www.docker.com/) é uma das abordagens virtualização mais populares que usa [contêineres de Linux](http://en.wikipedia.org/wiki/LXC) em vez de máquinas virtuais como uma forma de isolamento de dados e a computação em recursos compartilhados. Você pode usar a extensão de máquina virtual Docker e o [Azure Linux agente](virtual-machines-linux-agent-user-guide.md) para criar uma máquina virtual Docker que hospeda qualquer número de contêineres para seus aplicativos no Azure. Para ver uma discussão de alto nível de contêineres e suas vantagens, consulte o [Quadro de comunicações Docker de alto nível](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).


##<a name="how-to-use-the-docker-vm-extension-with-azure"></a>Como usar a extensão de máquina virtual Docker com o Azure
Para usar a extensão de máquina virtual Docker com o Azure, é necessário instalar uma versão da [Interface de linha de comando Azure](https://github.com/Azure/azure-sdk-tools-xplat) de Azure maior do que 0.8.6 (como da redação deste artigo, a versão atual é 0.10.0). Você pode instalar o CLI Azure no Mac, Linux e Windows.


O processo completo para usar Docker no Azure é simple:

+ Instalar a CLI do Azure e suas dependências no computador do qual você deseja controlar Azure (no Windows, este será uma distribuição Linux executado como uma máquina virtual)
+ Use os comandos do Azure CLI Docker para criar um host de máquina virtual Docker no Azure
+ Use os comandos de Docker locais para gerenciar seus contêineres de Docker em sua máquina virtual de Docker no Azure.


### <a name="install-the-azure-command-line-interface-azure-cli"></a>Instalar o Azure Interface de linha (comando Azure)

Para instalar e configurar o CLI do Azure, veja [como instalar a Interface de linha do Azure](../xplat-cli-install.md). Para confirmar a instalação, digite `azure` no prompt de comando e após um breve momento em que você deve ver a arte ASCII de CLI do Azure, que lista os comandos básicos disponíveis para você. Se a instalação trabalhou corretamente, você deve ser capaz de fazer `azure help vm` e se um dos comandos listados está "docker".

> [AZURE.NOTE] Docker tem ferramentas para Windows, [Docker máquina](https://docs.docker.com/installation/windows/), que você também pode usar para automatizar a criação de um cliente de docker que você pode usar para trabalhar com o Azure VMs como hosts de docker.

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Conectar a ILC Azure para a sua conta do Azure
Antes de poder usar a CLI Azure você deve associar suas credenciais de conta do Microsoft Azure CLI Azure em sua plataforma. A seção [como se conectar à sua assinatura do Azure](../xplat-cli-connect.md) explica como baixar e importar o arquivo **. publishsettings** ou associar seu CLI Azure com uma id organizacional.

> [AZURE.NOTE] Há algumas diferenças no comportamento ao usar um ou outros métodos de autenticação, portanto, não deixe de ler o documento acima para entender a funcionalidade de diferentes.

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Instalar Docker e usar a extensão de máquina virtual Docker do Azure
Siga as [instruções de instalação do Docker](https://docs.docker.com/installation/#installation) para instalar o Docker localmente em seu computador.

Para usar Docker com uma máquina Virtual do Azure, a imagem de Linux usada para a máquina virtual deve ter o [Agente de máquina virtual do Azure Linux](virtual-machines-linux-agent-user-guide.md) instalado. Atualmente, há apenas dois tipos de imagens que fornecem isso:

+ Uma imagem de Ubuntu da Galeria de imagem Azure ou

+ Uma imagem de Linux personalizada que você criou com o agente de máquina virtual do Azure Linux instalado e configurado. Consulte [Agente de máquina virtual do Azure Linux](virtual-machines-linux-agent-user-guide.md) para obter mais informações sobre como criar uma VM Linux personalizado com o agente de máquina virtual do Azure.

### <a name="using-the-azure-image-gallery"></a>Usando a Galeria de imagens do Azure

De uma Bash ou sessão de Terminal, use o seguinte comando CLI do Azure para localizar a imagem mais recente do Ubuntu na Galeria de máquina virtual usar digitando

`azure vm image list | grep Ubuntu-14_04`

e selecione um dos nomes de imagem, como `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`e use o comando a seguir para criar uma nova máquina virtual usando a imagem.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

onde:

+ * &lt;nome de máquina virtual-cloudservice&gt; * é o nome da máquina virtual que se tornará o computador de host do contêiner Docker no Azure

+  * &lt;username&gt; * é o nome de usuário do usuário raiz padrão da máquina virtual

+ * &lt;senha&gt; * é a senha da conta de *nome de usuário* que atende aos padrões de complexidade do Azure

> [AZURE.NOTE] Atualmente, uma senha deve ter pelo menos 8 caracteres, contêm um letras minúsculas e um maiusculas caractere, um número e um caractere especial como um dos seguintes caracteres: `!@#$%^&+=`. Não, o período no final da sentença anterior não é um caractere especial.

Se o comando foi bem-sucedido, você verá algo parecido com o seguinte, dependendo dos argumentos precisos e opções usadas:

![](./media/virtual-machines-linux-classic-cli-use-docker/dockercreateresults.png)

> [AZURE.NOTE] Criando uma máquina virtual pode levar alguns minutos, mas após ela foi provisionada (o valor de estado é `ReadyRole`) inicia o daemon Docker (o serviço de Docker) e você pode se conectar ao host de contêiner de Docker.

Para testar a máquina virtual de Docker que você criou no Azure, digite

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

onde * &lt;máquina virtual-nome-você-usado&gt; * é o nome da máquina virtual usadas em sua chamada para `azure vm docker create`. Você verá algo semelhante ao seguinte, que indica a sua máquina virtual Host de Docker para cima e em execução no Azure e aguardando seus comandos. 

Agora você pode tentar se conectar usando o cliente de docker para obter informações (em algumas configurações de cliente Docker, como o que no Mac, você talvez precise usar `sudo`):

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

Apenas para ter certeza de que se trata de funcionar, você pode examinar a máquina virtual para a extensão de Docker:

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Autenticação de máquina virtual do Host de docker

Além de criar a máquina virtual Docker, o `azure vm docker create` comando também cria automaticamente os certificados necessários para permitir que o computador de cliente Docker para se conectar ao host de contêiner Azure usando HTTPS e os certificados são armazenados em máquinas o cliente e o host, conforme apropriado. Em tentativas subsequentes, os certificados existentes são reutilizados e compartilhados com o novo host.

Por padrão, os certificados são colocados na `~/.docker`, e Docker será configurado para executar em porta **2376**. Se você gostaria de usar uma porta diferente ou diretório, você pode usar um dos seguintes `azure vm docker create` opções de linha de comando para configurar seu contêiner Docker hospedam máquina virtual para usar uma porta diferente ou certificados diferentes para conectando clientes:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

O daemon Docker no host está configurado para escutar e autenticar conexões de cliente na porta especificada usando os certificados gerados pela `azure vm docker create` comando. O computador cliente deve ter esses certificados para obter acesso ao host de Docker.

> [AZURE.NOTE] Um host de rede executando sem esses certificados será vulnerável a qualquer pessoa que pode para conectar à máquina. Antes de modificar a configuração padrão, certifique-se de que você compreenda os riscos para seus computadores e aplicativos.

## <a name="next-steps"></a>Próximas etapas

* Você está pronto para ir para o [Guia do usuário Docker] e usar sua máquina virtual Docker. Para criar uma máquina virtual habilitados para Docker no novo portal, veja [como usar a extensão de máquina virtual Docker com o Portal].

* A extensão de máquina virtual do Azure Docker também suporta Docker compor, que usa um arquivo YAML declarativo para tomar um aplicativo modelados desenvolvedor em todo o ambiente e gerar uma implantação consistente. Consulte [Introdução ao Docker e redigir a definir e executar um aplicativo de contêiner de vários em uma máquina virtual Azure].  

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

[How to use the Docker VM Extension with Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]: #Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
[Como usar a extensão de máquina virtual Docker com o Portal]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Guia do usuário docker]: https://docs.docker.com/userguide/
 
[Introdução ao Docker e redigir definir e executar um aplicativo de contêiner vários em uma máquina virtual Azure]:virtual-machines-linux-docker-compose-quickstart.md