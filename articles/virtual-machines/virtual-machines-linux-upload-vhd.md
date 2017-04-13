<properties
    pageTitle="Criar e carregar uma imagem personalizada do Linux | Microsoft Azure"
    description="Criar e carregar um disco rígido virtual (VHD) no Azure com uma imagem de Linux personalizada usando o modelo de implantação do Gerenciador de recursos."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="iainfou"/>

# <a name="upload-and-create-a-linux-vm-from-custom-disk-image"></a>Carregar e criar uma VM Linux da imagem de disco personalizada

Este artigo mostra como carregar um disco rígido virtual (VHD) no Azure usando o modelo de implantação do Gerenciador de recursos e criar VMs Linux partir dessa imagem personalizada. Esta funcionalidade permite instalar e configurar uma distribuição Linux às suas necessidades e, em seguida, use esse VHD para criar rapidamente o Azure VMs (máquinas virtuais).

## <a name="quick-commands"></a>Comandos rápidos
Se você precisar realizar a tarefa, os seguintes detalhes de seção rapidamente a base comandos para carregar uma máquina virtual no Azure. Informações mais detalhadas e o contexto para cada etapa podem ser encontradas o restante do documento, [começando aqui](#requirements).

Certifique-se de que você tenha [A CLI Azure](../xplat-cli-install.md) conectado e usando o modo do Gerenciador de recursos:

```bash
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Nomes de parâmetro de exemplo incluídos `myResourceGroup`, `mystorageaccount`, e `myimages`.

Primeiro, crie um grupo de recursos. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no `WestUs` local:

```bash
azure group create myResourceGroup --location "WestUS"
```

Crie uma conta de armazenamento para armazenar seus discos virtuais. O exemplo a seguir cria uma conta de armazenamento denominada `mystorageaccount`:

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

Lista as teclas de acesso para sua conta de armazenamento. Tome nota dos `key1`:

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Crie um contêiner dentro de sua conta de armazenamento usando a chave de armazenamento que você obteve. O exemplo a seguir cria um contêiner nomeado `myimages` usando o valor de chave de armazenamento do `key1`:

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Por fim, carregue seu VHD para o recipiente que você criou. Especifique o caminho local para seu VHD em `/path/to/disk/mydisk.vhd`:

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

Agora você pode criar uma máquina virtual do seu disco virtual carregados [usando um modelo do Gerenciador de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). Você também pode usar a CLI, especificando o URI para o seu disco (`--image-urn`). O exemplo a seguir cria uma máquina virtual chamada `myVM` utilizando o disco virtual carregados anteriormente no:

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

A conta de armazenamento de destino deve ser a mesma que onde você carregou seu disco virtual. Você também precisará especificar ou resposta solicita, todos os parâmetros adicionais necessários para o `azure vm create` comando como uma rede virtual, endereço IP público, nome de usuário e chaves SSH. Você pode ler mais sobre os [parâmetros disponíveis do Gerenciador de recursos de CLI](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Requisitos
Para concluir as etapas a seguir, você precisa:

- **Sistema operacional Linux instalado em um arquivo. vhd** - instalar uma [distribuição Linux aprovado Azure](virtual-machines-linux-endorsed-distros.md) (ou ver [informações para as distribuições não aprovado](virtual-machines-linux-create-upload-generic.md)) em um disco virtual no formato VHD. Existem várias ferramentas para criar uma máquina virtual e VHD:
    - Instalar e configurar [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), tomando cuidado para usar VHD como seu formato de imagem. Se necessário, você pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert`.
    - Você também pode usar Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Não há suporte para o formato mais recente do VHDX no Azure. Quando você cria uma máquina virtual, especifique VHD como o formato. Se necessário, você pode converter discos VHDX VHD usando [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou o [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) cmdlet do PowerShell. Além disso, Azure não suporta Carregando VHDs dinâmicos, então você precisa converter esses discos em VHDs estáticos antes de carregar. Você pode usar ferramentas como [Utilitários de VHD Azure para ir](https://github.com/Microsoft/azure-vhd-utils-for-go) para converter discos dinâmicos durante o processo de carregar Azure.

- VMs criadas a partir de sua imagem personalizada devem residem na mesma conta de armazenamento como a própria imagem
    - Criar uma conta de armazenamento e o contêiner para manter sua imagem personalizada tanto VMs criadas
    - Após ter criado todos os seus VMs, você pode excluir com segurança sua imagem

Certifique-se de que você tenha [A CLI Azure](../xplat-cli-install.md) conectado e usando o modo do Gerenciador de recursos:

```bash
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Nomes de parâmetro de exemplo incluídos `myResourceGroup`, `mystorageaccount`, e `myimages`.


<a id="prepimage"> </a>
## <a name="prepare-the-image-to-be-uploaded"></a>Preparar a imagem a ser carregado

Azure suporta várias distribuições Linux (consulte [Distribuições aprovado](virtual-machines-linux-endorsed-distros.md)). Os artigos a seguir o guiará como preparar as várias distribuições Linux que têm suporte no Azure:

- **[Distribuições baseadas em centOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Outras - distribuições não aprovado](virtual-machines-linux-create-upload-generic.md)**

Consulte também as **[Notas de instalação do Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** para dicas mais gerais sobre Preparando imagens de Linux Azure.

> [AZURE.NOTE] A [plataforma Windows Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) aplica às VMs executando Linux somente quando um das distribuições endossadas é usado com os detalhes de configuração conforme especificado em 'suporte 'versões no [Linux em Azure-Endorsed distribuições](virtual-machines-linux-endorsed-distros.md).


## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Grupos de recursos logicamente reúnem todos os recursos Azure para dar suporte a suas máquinas virtuais, como a rede virtual e o armazenamento. Leia mais sobre [grupos de recursos Azure aqui](../azure-resource-manager/resource-group-overview.md). Antes de carregar a imagem de disco personalizada e criar VMs, primeiro é necessário criar um grupo de recursos. 

O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no `WestUS` local:

```bash
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
VMs são armazenadas como blobs de página dentro de uma conta de armazenamento. Leia mais sobre o [armazenamento de blob do Microsoft Azure aqui](../storage/storage-introduction.md#blob-storage). Criar uma conta de armazenamento para sua imagem de disco personalizada e VMs. Qualquer VMs que você cria a partir de sua imagem de disco personalizada precisam estar na mesma conta de armazenamento como imagem.

O exemplo a seguir cria uma conta de armazenamento denominada `mystorageaccount` no grupo de recursos criado anteriormente:

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Lista de teclas de conta de armazenamento
Azure gera duas teclas de acesso de 512 bits para cada conta de armazenamento. Estas teclas de acesso são usadas ao autenticar a conta de armazenamento, como para executar operações de gravação. Leia mais sobre como [Gerenciar o acesso ao armazenamento aqui](../storage/storage-create-storage-account.md#manage-your-storage-account). Você pode exibir as teclas de acesso com o `azure storage account keys list` comando.

Exiba as teclas de acesso para a conta de armazenamento que você criou:

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

A saída é semelhante a:

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
Tome nota dos `key1` como você utilizará para interagir com sua conta de armazenamento nas próximas etapas.

## <a name="create-a-storage-container"></a>Criar um contêiner de armazenamento
Da mesma forma que você criar diferentes pastas para organizar logicamente a seu sistema de arquivos local, crie contêineres dentro de uma conta de armazenamento para organizar discos virtuais e imagens. Uma conta de armazenamento pode conter qualquer número de contêineres. 

O exemplo a seguir cria um contêiner nomeado `myimages`, especificando a tecla de acesso obtido na etapa anterior (`key1`):

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Carregar VHD
Agora, na verdade, você pode carregar sua imagem de disco personalizada. Como com nenhum disco virtual usado pelo VMs, você carrega e armazena sua imagem de disco personalizada como um blob de página.

Especifique sua chave de acesso, o recipiente que você criou na etapa anterior e, em seguida, o caminho para a imagem de disco personalizada em seu computador local:

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Criar máquina virtual de imagem personalizada
Quando você cria VMs da sua imagem de disco personalizada, especifique o URI para a imagem de disco. Certifique-se de que as correspondências de conta de armazenamento de destino onde a imagem de disco personalizada está armazenada. Você pode criar sua máquina virtual usando o modelo de CLI Azure ou JSON do Gerenciador de recursos.


### <a name="create-a-vm-using-the-azure-cli"></a>Criar uma máquina virtual usando a CLI do Azure
Você especificar o `--image-urn` parâmetro com o `azure vm create` comando para apontar para a imagem de disco personalizada. Certifique-se de que `--storage-account-name` corresponde a conta de armazenamento onde a imagem de disco personalizada está armazenada. Você não precisa usar o mesmo contêiner como a imagem de disco personalizada para armazenar suas VMs. Verifique se criar qualquer contêineres adicionais da mesma maneira como as etapas anteriores antes de carregar as imagens de disco personalizada.

O exemplo a seguir cria uma máquina virtual chamada `myVM` de sua imagem de disco personalizada:

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

Você ainda precisa especificar ou resposta solicita, todos os parâmetros adicionais necessários para o `azure vm create` comando como uma rede virtual, endereço IP público, nome de usuário e chaves SSH. Leia mais sobre os [parâmetros disponíveis do Gerenciador de recursos de CLI](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>Criar uma máquina virtual usando um modelo JSON
Modelos de Gerenciador de recursos Azure são arquivos de JavaScript Object Notation (JSON) que definem o ambiente que você deseja criar. Os modelos são divididos provedores de recurso diferente como computação ou rede. Você pode usar modelos existentes ou escrever suas próprias. Leia mais sobre como [usar o Gerenciador de recursos e modelos](../azure-resource-manager/resource-group-overview.md).

Dentro do `Microsoft.Compute/virtualMachines` provedor do modelo, você tem uma `storageProfile` nó que contém os detalhes de configuração para sua máquina virtual. Os dois parâmetros principais para editar são os `image` e `vhd` URIs que apontam para sua imagem de disco personalizada e disco virtual da VM seu novo. A seguir mostra um exemplo do JSON para usar uma imagem de disco personalizada:

```bash
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Você pode usar [Este modelo existente para criar uma máquina virtual de uma imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) ou ler sobre como [criar seus próprios modelos do Gerenciador de recursos do Azure](../resource-group-authoring-templates.md). 

Quando você tiver um modelo configurado, você criar suas VMs usando o `azure group deployment create` comando. Especificar o URI do seu modelo JSON com a `--template-uri` parâmetro:

```bash
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Se você tiver um arquivo JSON armazenado localmente em seu computador, você pode usar o `--template-file` parâmetro em vez disso:

```bash
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Próximas etapas
Depois de ter preparado e carregados no seu disco virtual personalizado, você pode ler mais sobre como [usar o Gerenciador de recursos e modelos](../azure-resource-manager/resource-group-overview.md). Você talvez também queira para [Adicionar um disco de dados](virtual-machines-linux-add-disk.md) suas novas VMs. Se você tiver aplicativos executados em suas VMs que você precisa acessar, certifique-se de [Abrir portas e pontos de extremidade](virtual-machines-linux-nsg-quickstart.md).