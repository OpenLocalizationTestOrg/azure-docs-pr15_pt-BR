<properties
    pageTitle="Redefinir acesso no Azure Linux VMs usando a extensão de VMAccess | Microsoft Azure"
    description="Redefina acesso no Azure Linux VMs usando a extensão de VMAccess."
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
    ms.date="10/25/2016"
    ms.author="v-livech"
/>

# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension"></a>Gerenciar usuários, SSH e seleção ou reparar discos em Azure Linux VMs usando a extensão de VMAccess

Este artigo mostra como usar a extensão de VMAcesss do Azure para verificar ou reparar um disco, redefinir o acesso do usuário, gerenciar contas de usuário ou redefinir a configuração de SSHD no Linux. O artigo requer:

- uma conta do Microsoft Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- o [Azure CLI](../xplat-cli-install.md) conectado com `azure login`.

- o modo de Gerenciador de recursos do Azure _deve estar no_ Azure CLI `azure config mode arm`.

## <a name="quick-commands"></a>Comandos rápidos

Há duas maneiras de usar VMAccess em suas VMs Linux:

- Usando a CLI do Azure e os parâmetros necessários.
- Usando arquivos JSON brutos que VMAccess processa e depois agir em.

Para a seção de comando rápido, vamos usar a CLI Azure `azure vm reset-access` método. Os seguintes exemplos de comando, substitua os valores que contêm "exemplo" com os valores do seu ambiente.

## <a name="create-a-resource-group-and-linux-vm"></a>Criar um grupo de recursos e máquina virtual Linux

```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Criar uma máquina virtual Debian

```bash
azure vm quick-create \
-M ~/.ssh/id_rsa.pub \
-u myAdminUser \
-g myResourceGroup \
-l westus \
-y Linux \
-n myVM \
-Q Debian
```

## <a name="reset-root-password"></a>Redefinir senha raiz

Para redefinir a senha raiz:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u root \
-p myNewPassword
```

## <a name="ssh-key-reset"></a>Redefinição de chave SSH

Para redefinir a chave SSH de um usuário não raiz:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>Criar um usuário

Para criar um usuário:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u myAdminUser \
-p myAdminUserPassword
```

## <a name="remove-a-user"></a>Remover um usuário

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-R myRemovedUser
```

## <a name="reset-sshd"></a>Redefinir SSHD

Para redefinir a configuração de SSHD:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM
-r
```


## <a name="detailed-walkthrough"></a>Explicação passo a passo detalhada

### <a name="vmaccess-defined"></a>VMAccess definidos:

O disco na sua VM Linux está mostrando erros. Você alguma forma redefinir a senha raiz para sua VM Linux ou excluída acidentalmente sua chave privada SSH. Se o que aconteceu de volta aos dias da data center, você precisaria unidade lá e, em seguida, abra o KVM obter no console do servidor. Pense a extensão do Azure VMAccess como comutador KVM que permite que você acesse o console para redefinir o acesso para Linux ou executar manutenção de nível de disco.

Para obter detalhes, vamos usar a forma longa de VMAccess, que usa os arquivos JSON brutos.  Esses arquivos VMAccess JSON também podem ser chamados de modelos Azure.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>Usando VMAccess para verificar ou reparar o disco de uma VM Linux

Usando o VMAccess você pode fazer um fsck executar no disco em sua VM Linux.  Você também pode fazer uma verificação de disco e um reparo de disco usando um VMAccess.

Para verificar e reparar o disco usar este script de VMAccess:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Execute o script VMAccess com:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>Usando VMAccess para redefinir o acesso do usuário para Linux

Se você tiver perdido acesso à raiz na sua VM Linux, você pode iniciar um script de VMAccess para redefinir a senha raiz.

Para redefinir a senha raiz, use este script VMAccess:

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword",   
}
```

Execute o script VMAccess com:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_root_password.json
```

Para redefinir a chave SSH de um usuário não raiz, use este script VMAccess:

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM",   
}
```

Execute o script VMAccess com:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>Usando VMAccess para gerenciar contas de usuário no Linux

VMAccess é um script de Python que pode ser usado para gerenciar usuários em sua VM Linux sem fazer logon e usando sudo ou conta raiz.

Para criar um usuário, use este script VMAccess:

`create_new_user.json`

```json
{
"username":"myNewUser",
"ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
"password":"myNewUserPassword",
}
```

Execute o script VMAccess com:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

Para excluir um usuário, use este script VMAccess:

`remove_user.json`

```json
{
"remove_user":"myDeletedUser",
}
```

Execute o script VMAccess com:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>Usando VMAccess para redefinir a configuração de SSHD

Se você fizer alterações na configuração de Linux VMs SSHD e feche a conexão SSH antes de verificar as alterações, você pode ser impedido de SSH'ing novamente.  VMAccess pode ser usado para redefinir a configuração de SSHD para uma boa configuração conhecida sem estar conectado via SSH.

Para redefinir a configuração de SSHD use este script VMAccess:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Execute o script VMAccess com:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

## <a name="next-steps"></a>Próximas etapas

Atualizando Linux usando extensões de VMAccess do Azure é um método para fazer alterações em uma VM Linux em execução.  Você também pode usar ferramentas como nuvem-inicialização e modelos do Azure para modificar sua VM Linux na inicialização.

[Sobre os recursos e extensões de máquina virtual](virtual-machines-linux-extensions-features.md)

[Criação de modelos do Gerenciador de recursos do Azure com as extensões de Linux VM](virtual-machines-linux-extensions-authoring-templates.md)

[Usando a inicialização de nuvem para personalizar uma VM Linux durante a criação](virtual-machines-linux-using-cloud-init.md)
