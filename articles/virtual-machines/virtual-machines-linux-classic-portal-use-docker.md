<properties
    pageTitle="Usando a extensão de máquina virtual Docker para Linux | Microsoft Azure"
    description="Descreve como criar máquinas virtuais do Azure que são hosts docker usando a CLI Azure no modelo clássico de implantação e as extensões de máquinas virtuais do Azure e Docker."
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
    ms.date="05/27/2016"
    ms.author="rasquill"/>


# <a name="using-the-docker-vm-extension-with-the-azure-classic-portal"></a>Usando a extensão de máquina virtual do Docker com o Azure portal clássico

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


[Docker](https://www.docker.com/) é uma das abordagens virtualização mais populares que usa [contêineres de Linux](http://en.wikipedia.org/wiki/LXC) em vez de máquinas virtuais como uma forma de isolamento de dados e a computação em recursos compartilhados. Você pode usar a extensão de máquina virtual Docker gerenciada pelo [Agente de Linux do Azure] para criar uma máquina virtual Docker que hospeda qualquer número de contêineres para seus aplicativos no Azure.

> [AZURE.NOTE] Este tópico descreve como criar uma máquina virtual Docker do portal de clássico do Azure. Para ver como criar uma máquina virtual Docker na linha de comando, veja [como usar a extensão de máquina virtual Docker a Interface do Azure de linha (Azure comando)]. Para ver uma discussão de alto nível de contêineres e suas vantagens, consulte o [Quadro de comunicações Docker de alto nível](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

## <a name="create-a-new-vm-from-the-image-gallery"></a>Criar uma nova VM da Galeria de imagem
A primeira etapa exige uma VM Azure a partir de uma imagem de Linux que ofereça suporte a extensão de máquina virtual Docker, usando uma imagem Ubuntu 14.04 LTS da Galeria de imagem como uma imagem de servidor de exemplo e área de trabalho do Ubuntu 14.04 como um cliente. No portal do, clique em **+ novo** no canto inferior esquerdo para criar uma nova instância de máquina virtual e selecione uma imagem de Ubuntu 14.04 LTS das seleções disponíveis ou na Galeria de imagem completa, conforme mostrado abaixo.

> [AZURE.NOTE] Atualmente, somente imagens de Ubuntu 14.04 LTS mais recentes que julho de 2014 suportam a extensão de máquina virtual Docker.

![Criar uma nova imagem Ubuntu](./media/virtual-machines-linux-classic-portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Criar certificados de Docker

Depois que a máquina virtual tiver sido criada, certifique-se de que Docker está instalado no computador cliente. (Para obter detalhes, consulte [as instruções de instalação do Docker](https://docs.docker.com/installation/#installation).)

Criar os arquivos de certificado e a chave para comunicação de Docker de acordo com a [Execução Docker com https] e colocá-los na **`~/.docker`** diretório no computador cliente.

> [AZURE.NOTE] A extensão de máquina virtual Docker no portal atualmente requer credenciais de codificação base64.

Na linha de comando, use **`base64`** ou outra ferramenta de codificação favorita para criar tópicos codificado na Base 64. Isso com um conjunto simples de arquivos de certificado e chave pode ser semelhante a esta:

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Adicionar a extensão de máquina virtual Docker
Para adicionar a extensão de máquina virtual Docker, localize a instância de máquina virtual que você criou e role para baixo até **extensões** e clique nele para exibir extensões de máquina virtual, conforme mostrado abaixo.
> [AZURE.NOTE] Essa funcionalidade é suportada no portal de visualização somente: https://portal.azure.com/

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickExtensions.png)
### <a name="add-an-extension"></a>Adicionar uma extensão
Clique no **+ Adicionar** para exibir as extensões de máquina virtual possíveis, você pode adicionar a esta máquina virtual.

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickAdd.png)
### <a name="select-the-docker-vm-extension"></a>Selecione a extensão de máquina virtual Docker
Escolha a extensão de máquina virtual Docker, que traz a descrição de Docker e links importantes, e clique em **criar** na parte inferior para iniciar o procedimento de instalação.

![](./media/virtual-machines-linux-classic-portal-use-docker/ChooseDockerExtension.png)

![](./media/virtual-machines-linux-classic-portal-use-docker/CreateButtonFocus.png)
### <a name="add-your-certificate-and-key-files"></a>Adicione seu certificado e arquivos de chave:

Nos campos do formulário, insira as versões codificado na Base 64 do seu certificado de autoridade de certificação, o certificado de servidor e sua chave de servidor, conforme mostrado no gráfico a seguir.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddExtensionFormFilled.png)

> [AZURE.NOTE] Observe que (como a imagem anterior) a 2376 está preenchido por padrão. Você pode inserir qualquer ponto de extremidade aqui, mas a próxima etapa será abrir o ponto de extremidade correspondente. Se você alterar o padrão, verifique se a abrir o ponto de extremidade correspondente na próxima etapa.

## <a name="add-the-docker-communication-endpoint"></a>Adicionar o ponto de extremidade de comunicação de Docker
Ao exibir o grupo de recursos que você criou, selecione o grupo de segurança de rede associada a sua máquina virtual e clique em **Regras de segurança de entrada** para exibir as regras, como mostrado aqui.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddingEndpoint.png)

Clique em **+ Adicionar** para adicionar outra regra e no caso padrão, insira um nome para o ponto de extremidade (no exemplo, **Docker**) e 2376 'intervalo porta de destino'. Defina o valor de protocolo mostrando **TCP**e, em seguida, clique em **Okey** para criar a regra.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddEndpointFormFilledOut.png)


## <a name="test-your-docker-client-and-azure-docker-host"></a>Teste seu cliente de Docker e o Host do Azure Docker
Localize e copie o nome de domínio da sua VM e na linha de comando do computador cliente, digite `docker --tls -H tcp://` *dockerextension* `.cloudapp.net:2376 info` (onde *dockerextension* é substituído pelo subdomínio para sua máquina virtual).

O resultado deve aparecer semelhante a esta:

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

Depois de concluir as etapas acima, agora você tem um host Docker totalmente funcional em execução em uma máquina virtual do Azure, configurado para ser conectadas remotamente de outros clientes.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas

Você está pronto para ir para o [Guia do usuário Docker] e usar sua máquina virtual Docker. Se você quiser automatizar a criação hosts Docker Azure VMs por meio da interface de linha de comando, veja [como usar a extensão de máquina virtual Docker a Interface do Azure de linha (Azure comando)]

<!--Anchors-->
[Create a new VM from the Image Gallery]: #createvm
[Create Docker Certificates]: #dockercerts
[Add the Docker VM Extension]: #adddockerextension
[Test Docker Client and Azure Docker Host]: #testclientandserver
[Next steps]: #next-steps

<!--Image references-->
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Como usar a extensão de máquina virtual Docker a Interface do Azure de linha (Azure comando)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Agente de Linux Azure]: virtual-machines-linux-agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

[Executando Docker com https]: http://docs.docker.com/articles/https/
[Guia do usuário docker]: https://docs.docker.com/userguide/
