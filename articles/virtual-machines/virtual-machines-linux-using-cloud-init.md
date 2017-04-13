<properties
    pageTitle="Usando a inicialização de nuvem para personalizar uma VM Linux durante a criação | Microsoft Azure"
    description="Usando a inicialização de nuvem para personalizar uma VM Linux durante a criação."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="v-livech"
/>

# <a name="using-cloud-init-to-customize-a-linux-vm-during-creation"></a>Usando a inicialização de nuvem para personalizar uma VM Linux durante a criação

Este artigo mostra como fazer um script de inicialização de nuvem para definir o nome do host, pacotes de atualização instalada e gerenciar contas de usuário.  Os scripts de inicialização de nuvem são chamados durante a criação de máquina virtual do Azure CLI.  O artigo requer:

- uma conta do Microsoft Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- o [Azure CLI](../xplat-cli-install.md) conectado com `azure login`.

- o modo de Gerenciador de recursos do Azure _deve estar no_ Azure CLI `azure config mode arm`.

## <a name="quick-commands"></a>Comandos rápidos

Crie um script de nuvem init.txt que define o nome do host, atualiza todos os pacotes e adiciona um usuário sudo Linux.

```bash
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
Crie um grupo de recursos para iniciar VMs em.

```bash
azure group create myResourceGroup westus
```

Crie uma VM Linux usando nuvem-inicialização configurá-lo durante a inicialização.

```bash
azure vm create \
-g myResourceGroup \
-n myVM \
-l westus \
-y Linux \
-f myVMnic \
-F myVNet \
-P 10.0.0.0/22 \
-j mySubnet \
-k 10.0.0.0/24 \
-Q canonical:ubuntuserver:14.04.2-LTS:latest \
-M ~/.ssh/id_rsa.pub \
-u myAdminUser \
-C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Explicação passo a passo detalhada

### <a name="introduction"></a>Introdução

Quando você inicia uma nova VM Linux, você está obtendo um padrão Linux VM com nada personalizados ou pronto para as suas necessidades. [Inicialização de nuvem](https://cloudinit.readthedocs.org) é uma maneira padrão para inserir um script ou configurações dessa VM Linux como ele está sendo inicializado pela primeira vez.

No Azure, há um três maneiras diferentes para fazer alterações em uma VM Linux como ele está sendo implantado ou inicializado.

- Inserir scripts usando a inicialização de nuvem.
- Inserir scripts usando a [Extensão de VMAccess](virtual-machines-linux-using-vmaccess-extension.md)do Azure.
- Um modelo Azure usando inicialização de nuvem.
- Um modelo Azure usando [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md).

Inserir scripts a qualquer momento após a inicialização:

- SSH para executar comandos diretamente
- Inserir scripts usando a [Extensão de VMAccess](virtual-machines-linux-using-vmaccess-extension.md)Azure, imperativamente ou em um modelo do Azure
- Ferramentas de gerenciamento de configuração como Ansible, valor falso, chefe e Puppet.

>[AZURE.NOTE]: VMAccess Extension executes a script as root in the same way using SSH can.  However, using the VM extension enables several features that Azure offers that can be useful depending upon your scenario.

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Disponibilidade de nuvem-inicialização na máquina virtual do Azure rápido-criar aliases de imagem:

| Alias     | Publisher | Oferta        | SKU         | Versão | inicialização de nuvem |
|:----------|:----------|:-------------|:------------|:--------|:-----------|
| CentOS    | OpenLogic | CentOS       | 7.2         | mais recente  | Não         |
| CoreOS    | CoreOS    | CoreOS       | Estável      | mais recente  | Sim        |
| Debian    | credativ  | Debian       | 8           | mais recente  | Não         |
| openSUSE  | SUSE      | openSUSE     | 13.2        | mais recente  | Não         |
| RHEL      | RedHat    | RHEL         | 7.2         | mais recente  | Não         |
| UbuntuLTS | Canônico | UbuntuServer | 14.04.4-LTS | mais recente  | Sim        |

Microsoft está trabalhando com nossos parceiros para obter nuvem-inicialização incluídos e trabalhar nas imagens que elas fornecem no Azure.

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Adicionando um script de inicialização de nuvem para a criação de máquina virtual com a CLI do Azure

Para iniciar um script de inicialização de nuvem ao criar uma máquina virtual no Azure, especifique o arquivo de inicialização de nuvem usando a CLI Azure `--custom-data` alternar.

Crie um grupo de recursos para iniciar VMs em.

```bash
azure group create myResourceGroup westus
```

Crie uma VM Linux usando nuvem-inicialização configurá-lo durante a inicialização.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Criar um script de inicialização de nuvem para definir o nome de host de uma VM Linux

Uma das configurações mais simples e mais importantes para qualquer VM Linux seria o nome do host. Podemos facilmente definir isso usando nuvem-inicialização com esse script.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Script de inicialização de nuvem de exemplo chamado `cloud_config_hostname.txt`.

``` bash
#cloud-config
hostname: myservername
```

Durante a inicialização da máquina virtual, esse script de inicialização de nuvem define o nome de host para `myservername`.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_hostname.txt
```

Faça logon e verifique se o nome de host da nova máquina virtual.

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>Criar um script de inicialização de nuvem para atualizar Linux

Para segurança, desejar que sua VM Ubuntu atualizar na primeira inicialização.  Usando a nuvem-inicialização, que é possível fazer isso com o script de acompanhamento, dependendo da distribuição de Linux que você está usando.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Exemplo de script de inicialização de nuvem `cloud_config_apt_upgrade.txt` para a família Debian

```bash
#cloud-config
apt_upgrade: true
```

Depois de Linux tem inicializado, todos os pacotes instalados são atualizados por meio `apt-get`.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_apt_upgrade.txt
```

Faça logon e verifique se todos os pacotes são atualizados.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>Criar um script de inicialização de nuvem para adicionar um usuário ao Linux

Uma das primeiras tarefas em qualquer novo VM Linux é para adicionar um usuário para si mesmo ou para evitar o uso de `root`. Chaves SSH são práticas recomendadas para segurança e usabilidade e eles são adicionados à `~/.ssh/authorized_keys` arquivo com este script de inicialização de nuvem.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Exemplo de script de inicialização de nuvem `cloud_config_add_users.txt` para a família Debian

```bash
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Depois de Linux tem inicializado, todos os usuários listados são criados e adicionados ao grupo sudo.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_add_users.txt
```

Faça logon e verifique se o usuário recém-criado.

```bash
ssh myVM
cat /etc/group
```

Saída

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Próximas etapas

Inicialização de nuvem está se tornando uma maneira padrão para modificar sua VM Linux na inicialização. Azure também tem extensões de máquina virtual, que permitem que você modifique seu LinuxVM na inicialização ou enquanto ele é executado. Por exemplo, você pode usar o VMAccessExtension do Azure para redefinir as informações de SSH ou usuário enquanto a máquina virtual está em execução. Com inicialização de nuvem, seria necessário reinicializar para redefinir a senha.

[Sobre os recursos e extensões de máquina virtual](virtual-machines-linux-extensions-features.md)

[Gerenciar usuários, SSH e seleção ou reparar discos em Azure Linux VMs usando a extensão de VMAccess](virtual-machines-linux-using-vmaccess-extension.md)
