<properties
   pageTitle="Criar uma VM Linux no Azure usando a CLI | Microsoft Azure"
   description="Crie uma VM Linux no Azure usando a CLI."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="v-livech"/>


# <a name="create-a-linux-vm-on-azure-by-using-the-cli"></a>Criar uma VM Linux no Azure usando a CLI

Este artigo mostra como implantar rapidamente uma Linux virtuais de máquina no Azure usando o `azure vm quick-create` comando na interface do Azure de linha de comando (CLI). O `quick-create` comando implanta uma máquina virtual dentro de uma infraestrutura básica e segura que você pode usar para criar um protótipo ou testar um conceito rapidamente. O artigo requer:

- uma conta do Microsoft Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- o [Azure CLI](../xplat-cli-install.md) conectado com `azure login`.

- o modo de Gerenciador de recursos do Azure _deve estar no_ Azure CLI `azure config mode arm`.

Você pode implantar uma VM Linux também rapidamente usando o [portal do Azure](virtual-machines-linux-quick-create-portal.md).

## <a name="quick-commands"></a>Comandos rápidos

O exemplo a seguir mostra como implantar uma VM CoreOS e anexe sua chave SSH (Secure Shell) (seus argumentos podem ser diferentes):

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>Explicação passo a passo detalhada

A instrução a seguir tem uma VM UbuntuLTS sendo implantado, passo a passo, com explicações do que está fazendo cada etapa.

## <a name="vm-quick-create-aliases"></a>Máquina virtual rápido criar aliases

Uma maneira rápida de escolher uma distribuição é usar os aliases de CLI Azure mapeados para as distribuições mais comuns do sistema operacional. A tabela a seguir lista os aliases (a partir do Azure CLI versão 0,10). Todas as implantações que usam `quick-create` padrão para VMs que contam com o armazenamento de unidade de estado sólido (SSD), que oferece acesso de disco mais rápido provisionamento e de alto desempenho. (Esses aliases representam uma parte pequena das distribuições disponíveis no Azure. Encontrar mais imagens do Azure Marketplace, [procurando uma imagem no PowerShell](virtual-machines-linux-cli-ps-findimage.md), [na web](https://azure.microsoft.com/marketplace/virtual-machines/)ou [carregar sua própria imagem personalizada](virtual-machines-linux-create-upload-generic.md).)

| Alias     | Publisher | Oferta        | SKU         | Versão |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | CentOS       | 7.2         | mais recente  |
| CoreOS    | CoreOS    | CoreOS       | Estável      | mais recente  |
| Debian    | credativ  | Debian       | 8           | mais recente  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | mais recente  |
| RHEL      | É o seu vermelho    | RHEL         | 7.2         | mais recente  |
| UbuntuLTS | Canônico | Servidor de Ubuntu | 14.04.4-LTS | mais recente  |

Os seguintes seções usar o `UbuntuLTS` alias para a opção de **ImageURN** (`-Q`) implantar um servidor de LTS Ubuntu 14.04.4.

Anterior `quick-create` exemplo apenas destacados a `-M` sinalizador para identificar a chave pública SSH para carregar durante a desativação SSH senhas, para que você for solicitado para os seguintes argumentos:

- nome do grupo de recursos (qualquer cadeia de caracteres é geralmente funciona bem para seu grupo de recursos Azure primeiro)
- Nome de máquina virtual
- local (`westus` ou `westeurope` são bons padrões)
- Linux (para permitir que o Azure saibam qual sistema operacional desejado)
- nome de usuário

O exemplo a seguir especifica todos os valores para que nenhuma ainda mais solicitando que seja necessário. Desde que tenha um `~/.ssh/id_rsa.pub` como um ssh rsa formato arquivo chave pública, ele funciona como é:

```bash
azure vm quick-create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--admin-username myAdminUser \
--ssh-public-file ~/.ssh/id_rsa.pub \
--image-urn UbuntuLTS
```

A saída deve parecer com o seguinte bloco de saída:

```bash
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "myVM"
+ Looking up the VM "myVM"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

## <a name="log-in-to-the-new-vm"></a>Faça logon na máquina virtual novo

Faça logon em sua máquina virtual usando o endereço IP público listado na saída. Você também pode usar o nome de domínio totalmente qualificado (FQDN) que está listado:

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
```

O processo de login deve parecer com o seguinte bloco de saída:

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>Próximas etapas

O `azure vm quick-create` comando é a maneira de implantar rapidamente uma máquina virtual para que possa fazer logon em um shell bash e comece a trabalhar. No entanto, usando `vm quick-create` não lhe dá controle abrangente nem oferece permitem criar um ambiente mais complexo.  Para implantar uma VM Linux que é personalizado para sua infraestrutura, você pode seguir qualquer um dos seguintes artigos:

- [Usar um modelo do Gerenciador de recursos do Azure para criar uma implantação específica](virtual-machines-linux-cli-deploy-templates.md)
- [Criar seu próprio ambiente personalizado para uma VM Linux usando comandos do Azure diretamente](virtual-machines-linux-create-cli-complete.md)
- [Criar um SSH protegido Linux máquina virtual no Azure usando modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

Você também pode [usar o `docker-machine` driver Azure com vários comandos para criar rapidamente uma VM Linux como um host de docker](virtual-machines-linux-docker-machine.md).
