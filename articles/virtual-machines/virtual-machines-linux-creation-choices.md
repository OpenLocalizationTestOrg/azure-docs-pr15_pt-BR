<properties
    pageTitle="Maneiras diferentes de criar uma VM Linux | Microsoft Azure"
    description="Aprenda as diferentes maneiras de criar uma máquina virtual Linux no Azure, incluindo links para ferramentas e tutoriais para cada método."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>Maneiras diferentes de criar uma máquina virtual Linux no Azure

Você tem a flexibilidade no Azure para criar uma máquina virtual Linux (máquina virtual) usando ferramentas e fluxos de trabalho confortáveis para você. Este artigo resume essas diferenças e exemplos para criar suas VMs Linux.


## <a name="azure-cli"></a>CLI Azure 

A CLI do Azure está disponível em plataformas por meio de um pacote de npm, pacotes fornecidos distribuição ou contêiner de Docker. Você pode ler mais sobre [como instalar e configurar o CLI do Azure](../xplat-cli-install.md). Os seguintes tutoriais fornecem exemplos sobre como usar a CLI do Azure. Leia cada artigo para obter mais detalhes sobre os comandos de início rápido mostrados:

- [Criar uma VM Linux da CLI do Azure para desenvolvimento e teste](virtual-machines-linux-quick-create-cli.md)
    - O exemplo a seguir cria uma VM CoreOS usando uma chave pública denominada `azure_id_rsa.pub`:

    ```bash
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
        --image-urn CoreOS
    ```

- [Criar uma VM Linux protegido usando um modelo do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
    - O exemplo a seguir cria uma máquina virtual usando um modelo armazenado no GitHub:

    ```bash
    azure group create --name TestRG --location WestUS 
        --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```

- [Criar um ambiente Linux completo utilizando a CLI do Azure](virtual-machines-linux-create-cli-complete.md)
    - Inclui a criação de um balanceador de carga e várias VMs em um conjunto de disponibilidade.

- [Adicionar um disco a uma VM Linux](virtual-machines-linux-add-disk.md)
    - O exemplo a seguir adiciona um disco de 5Gb para uma chamada de VM existente `TestVM`:

    ```bash
    azure vm disk attach-new --resource-group TestRG --vm-name TestVM \
        --size-in-GB 5
    ```

## <a name="azure-portal"></a>Portal do Azure

O [portal do Azure](https://portal.azure.com) permite criar rapidamente uma máquina virtual, desde que nada instalar em seu sistema. Use o portal do Azure para criar a máquina virtual:

- [Criar uma VM Linux usando o portal do Azure](virtual-machines-linux-quick-create-portal.md) 
- [Anexar um disco usando o portal do Azure](virtual-machines-linux-attach-disk-portal.md)


## <a name="operating-system-and-image-choices"></a>Sistema operacional e opções de imagem
Ao criar uma máquina virtual, você pode escolher uma imagem com base no sistema operacional que você deseja executar. Azure e seus parceiros oferecem várias imagens, algumas delas incluem aplicativos e ferramentas pré-instaladas. Ou carregar uma das suas próprias imagens (consulte [a seção a seguir](#use-your-own-image)).

### <a name="azure-images"></a>Imagens Azure
Use o `azure vm image` comandos para ver o que está disponível, publisher, versão de distribuição e compilações.

Lista de fornecedores disponíveis da seguinte maneira:

```bash
azure vm image list-publishers --location WestUS
```

Lista de produtos disponíveis (oferece) para um determinado editor da seguinte maneira:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Liste SKUs disponíveis (versões de distribuição) de uma oferta de determinado da seguinte maneira:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Lista todas as imagens disponíveis para uma determinada versão segue:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Para obter mais exemplos de navegação e o uso de imagens disponíveis, consulte [navegar e selecionar máquina virtual Azure imagens com a CLI do Azure](virtual-machines-linux-cli-ps-findimage.md).

O `azure vm quick-create` e `azure vm create` comandos têm aliases que você pode usar para acessar rapidamente o distros mais comuns e suas versões mais recentes. Usando aliases geralmente é mais rápido que especifica o publisher, oferta, SKU e versão cada vez que você criar uma máquina virtual:

| Alias     | Publisher | Oferta        | SKU         | Versão |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | CentOS       | 7.2         | mais recente  |
| CoreOS    | CoreOS    | CoreOS       | Estável      | mais recente  |
| Debian    | credativ  | Debian       | 8           | mais recente  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | mais recente  |
| RHEL      | RedHat    | RHEL         | 7.2         | mais recente  |
| SLES      | SLES      | SLES         | 12-SP1      | mais recente  |
| UbuntuLTS | Canônico | UbuntuServer | 14.04.4-LTS | mais recente  |

### <a name="use-your-own-image"></a>Usar sua própria imagem

Se você precisar personalizações específicas, você pode usar uma imagem com base em uma VM existente do Azure *capturando* que máquina virtual. Você também pode carregar uma imagem criada no local. Para obter mais informações sobre distros com suporte e como usar suas próprias imagens, consulte os seguintes artigos:

- [Azure aprovado distribuições](virtual-machines-linux-endorsed-distros.md)

- [Informações para as distribuições não aprovado](virtual-machines-linux-create-upload-generic.md)

- [Como capturar uma máquina virtual de Linux como um modelo do Gerenciador de recursos](virtual-machines-linux-capture-image.md).
    - Comandos de exemplo para capturar uma máquina virtual existente de início rápido:

    ```bash
    azure vm deallocate --resource-group TestRG --vm-name TestVM
    azure vm generalize --resource-group TestRG --vm-name TestVM
    azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
    ```

## <a name="next-steps"></a>Próximas etapas

- Crie uma VM Linux a partir do [portal](virtual-machines-linux-quick-create-portal.md), com a [CLI](virtual-machines-linux-quick-create-cli.md), ou usando um [modelo do Gerenciador de recursos do Azure](virtual-machines-linux-cli-deploy-templates.md).

- Depois de criar uma VM Linux, [Adicionar um disco de dados](virtual-machines-linux-add-disk.md).

- Etapas rápidas para [Redefinir uma senha ou teclas de SSH e gerenciar usuários](virtual-machines-linux-using-vmaccess-extension.md)
