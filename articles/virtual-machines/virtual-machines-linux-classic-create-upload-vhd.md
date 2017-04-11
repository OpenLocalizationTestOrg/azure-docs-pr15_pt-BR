<properties
    pageTitle="Criar e carregar um VHD Linux | Microsoft Azure"
    description="Criar e carregar um disco rígido virtual (VHD) Azure com o modelo clássico de implantação que contém o sistema operacional Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Criar e carregar um disco rígido Virtual que contém o sistema operacional Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Você também pode [carregar uma imagem de disco personalizado usando o Gerenciador de recursos do Azure](virtual-machines-linux-upload-vhd.md).

Este artigo mostra como criar e carregar um disco rígido virtual (VHD) para poder usá-lo como sua própria imagem para criar máquinas virtuais no Azure. Saiba como preparar o sistema operacional para poder usá-lo para criar várias máquinas virtuais com base em imagem. 

>  [AZURE.NOTE] Se você tiver alguns momentos, ajude-na melhorar a documentação de máquina virtual do Azure Linux fazendo esta [pesquisa rápida](https://aka.ms/linuxdocsurvey) de suas experiências. Cada resposta nos ajudam a ajuda você executar seu trabalho.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você tenha os seguintes itens:

- **Sistema operacional Linux instalado em um arquivo. vhd** - você instalou uma [distribuição Linux aprovado Azure](virtual-machines-linux-endorsed-distros.md) (ou ver [informações para as distribuições não aprovado](virtual-machines-linux-create-upload-generic.md)) em um disco virtual no formato VHD. Existem várias ferramentas para criar uma máquina virtual e VHD:
    - Instalar e configurar [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), tomando cuidado para usar VHD como seu formato de imagem. Se necessário, você pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert`.
    - Você também pode usar Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Não há suporte para o formato mais recente do VHDX no Azure. Quando você cria uma máquina virtual, especifique VHD como o formato. Se necessário, você pode converter discos VHDX VHD usando [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou o [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) cmdlet do PowerShell. Além disso, Azure não suporta Carregando VHDs dinâmicos, então você precisa converter esses discos em VHDs estáticos antes de carregar. Você pode usar ferramentas como [Utilitários de VHD Azure para ir](https://github.com/Microsoft/azure-vhd-utils-for-go) para converter discos dinâmicos durante o processo de carregar Azure.

- **Interface de linha de comando do azure** - instalar a [Interface de linha de comando do Azure](../virtual-machines-command-line-tools.md) mais recente para carregar o VHD.

<a id="prepimage"> </a>
## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Etapa 1: Preparar a imagem a ser carregado

Azure suporta várias distribuições Linux (consulte [Distribuições aprovado](virtual-machines-linux-endorsed-distros.md)). Os artigos a seguir o guiará como preparar as várias distribuições Linux que têm suporte no Azure. Depois de concluir as etapas nos seguintes guias, vir volta aqui depois que você tiver um arquivo VHD que está pronto para carregar no Azure:

- **[Distribuições baseadas em centOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Outras - distribuições não aprovado](virtual-machines-linux-create-upload-generic.md)**

> [AZURE.NOTE] Plataforma Windows Azure SLA aplica às máquinas virtuais executando o sistema operacional Linux somente quando um das distribuições endossadas é usado com os detalhes de configuração conforme especificado em 'suporte 'versões no [Linux em Azure-Endorsed distribuições](virtual-machines-linux-endorsed-distros.md). Todas as distribuições Linux na Galeria de imagem Azure são endossadas distribuições com a configuração necessária.

Consulte também as **[Notas de instalação do Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** para dicas mais gerais sobre Preparando imagens de Linux Azure.


<a id="connect"> </a>
## <a name="step-2-prepare-the-connection-to-azure"></a>Etapa 2: Preparar a conexão para o Azure

Verifique se você estiver usando o CLI Azure no modelo clássico de implantação (`azure config mode asm`), faça logon sua conta:

```
azure login
```


<a id="upload"> </a>
## <a name="step-3-upload-the-image-to-azure"></a>Etapa 3: Carregar a imagem do Azure

Você precisa de uma conta de armazenamento para carregar seu arquivo VHD. Você pode selecionar uma conta de armazenamento existente ou [Crie um novo](../storage/storage-create-storage-account.md).

Use a CLI do Azure para carregar a imagem usando o seguinte comando:

```bash
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

No exemplo anterior:

- **BlobStorageURL** é a URL para a conta de armazenamento que você planeja usar
- **YourImagesFolder** é o recipiente em armazenamento de blob onde você deseja armazenar as imagens
- **VHDName** é o rótulo que aparece no portal para identificar o disco rígido virtual.
- **PathToVHDFile** é o caminho completo e o nome do arquivo. vhd em sua máquina.

A seguir mostra um exemplo completo:

```bash
azure vm image create UbuntuLTS `
    --blob-url https://teststorage.blob.core.windows.net/vhds/UbuntuLTS.vhd `
    --os Linux /home/ahmet/UbuntuLTS.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Etapa 4: Criar uma máquina virtual da imagem
Você cria uma máquina virtual usando `azure vm create` da mesma maneira como uma máquina virtual regular. Especifique o nome que você deu sua imagem na etapa anterior. No exemplo a seguir, usamos o nome de imagem de **UbuntuLTS** atribuído na etapa anterior:

```bash
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "DeployedUbuntu" UbuntuLTS
```

Para criar suas próprias VMs, forneça seu próprio nome de usuário + senha local, nome DNS e nome da imagem.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [referência de CLI do Azure para o modelo de implantação clássico Azure](../virtual-machines-command-line-tools.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload
