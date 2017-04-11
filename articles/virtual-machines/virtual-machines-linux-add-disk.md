<properties
    pageTitle="Adicionar um disco ao Linux VM | Microsoft Azure"
    description="Saiba como adicionar um disco persistente a sua VM Linux"
    keywords="computador de virtual Linux, adicionar disco do recurso"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rickstercdn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.topic="article"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.date="09/06/2016"
    ms.author="rclaus"/>

# <a name="add-a-disk-to-a-linux-vm"></a>Adicionar um disco a uma VM Linux

Este artigo mostra como anexar um disco persistente para sua máquina virtual para que você possa preservar seus dados - mesmo se sua máquina virtual está provisionado novamente devido a manutenção ou redimensionando. Para adicionar um disco, você precisará [CLI Azure](../xplat-cli-install.md) configurado no modo do Gerenciador de recursos (`azure config mode arm`).  

## <a name="quick-commands"></a>Comandos rápidos

Os seguintes exemplos de comando, substitua os valores entre &lt; e &gt; com os valores do seu ambiente.

```bash
azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>
```

## <a name="attach-a-disk"></a>Anexar um disco

Anexar um novo disco é rápido. Tipo de `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>` para criar e anexar um novo disco GB para sua máquina virtual. Se você não identificar explicitamente uma conta de armazenamento, qualquer disco que você criar é colocado na mesma conta de armazenamento onde seu disco de sistema operacional está localizado.  Ele deve ser algo parecido com o seguinte:

```bash
azure vm disk attach-new myuniquegroupname myuniquevmname 5
```

Saída

```bash
info:    Executing command vm disk attach-new
+ Looking up the VM "myuniquevmname"
info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
+ Updating VM "myuniquevmname"
info:    vm disk attach-new command OK
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Conectar-se à VM Linux para montar o novo disco

> [AZURE.NOTE] Este tópico se conecta a uma máquina virtual usando nomes de usuário e senhas. Para usar pares de chaves públicos e privados para se comunicar com sua máquina virtual, veja [como usar SSH com Linux no Azure](virtual-machines-linux-mac-create-ssh-keys.md). Você pode modificar a conectividade **SSH** de VMs criada com a `azure vm quick-create` comando usando o `azure vm reset-access` comando para redefinir o acesso **SSH** completamente, adicionar ou remover usuários ou adicionar arquivos de chave públicos para proteger o acesso.

Você precisa para SSH em sua máquina de virtual do Azure para partição, formatar e montar seu novo disco para que sua VM Linux pode usá-lo. Se não estiver familiarizado com a conexão com o **ssh**, o comando assume a forma `ssh <username>@<FQDNofAzureVM> -p <the ssh port>`e parece com o seguinte:

```bash
ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
```

Saída

```bash
The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Fri May 22 21:02:32 UTC 2015

System load: 0.37              Memory usage: 2%   Processes:       207
Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

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

ops@myuniquevmname:~$
```

Agora que você está conectado à sua máquina virtual, você está pronto para anexar um disco.  Primeiro, encontre o disco, usando `dmesg | grep SCSI` (o método usado para descobrir o novo disco pode variar). Nesse caso, ele aparência a seguir:

```bash
dmesg | grep SCSI
```

Saída

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

e no caso de neste tópico, o `sdc` disco é aquele que queremos. Agora partição do disco com `sudo fdisk /dev/sdc` – presumindo que seu caso o disco estava `sdc`, torná-lo um disco primário partição 1 e aceitar os outros padrões.

```bash
sudo fdisk /dev/sdc
```

Saída

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Criar a partição digitando `p` no prompt:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

E escrever um sistema de arquivos para a partição usando o comando **mkfs** , especificando seu tipo de sistema de arquivos e o nome do dispositivo. Neste tópico, estamos usando o `ext4` e `/dev/sdc1` acima:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Saída

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Agora, podemos criar uma pasta para montar o sistema de arquivos usando `mkdir`:

```bash
sudo mkdir /datadrive
```

E você monta o diretório usando `mount`:

```bash
sudo mount /dev/sdc1 /datadrive
```

O disco de dados está pronto para usar como `/datadrive`.

```bash
ls
```

Saída

```bash
bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var
```

Para garantir que a unidade é remontada automaticamente após a reinicialização ele deve ser adicionado ao arquivo /etc/fstab. Além disso, é altamente recomendável que o UUID (Universal identificador exclusivo) é usado em/etc/fstab para se referir a unidade em vez de apenas o nome do dispositivo (como `/dev/sdc1`). Se o sistema operacional detecta um erro de disco durante a inicialização, usar o UUID evita o disco incorreto sendo montado em um determinado local. Restante discos de dados seria então atribuído essas mesmas IDs de dispositivo. Para localizar o UUID da nova unidade, use o utilitário de **blkid** :

```bash
sudo -i blkid
```

A saída é semelhante ao seguinte:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

>[AZURE.NOTE] O arquivo **/etc/fstab** de edição incorretamente pode resultar em um sistema de impossibilidade. Se não tiver certeza, consulte a documentação da distribuição para obter informações sobre como editar corretamente este arquivo. Também é recomendável que um backup do arquivo /etc/fstab for criado antes da edição.

Em seguida, abra o arquivo **/etc/fstab** em um editor de texto:

```bash
sudo vi /etc/fstab
```

Neste exemplo, usamos o valor UUID para o novo dispositivo **/dev/sdc1** criado em etapas anteriores e o ponto de montagem **/datadrive**. Adicione a seguinte linha ao final do arquivo **/etc/fstab.** :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

>[AZURE.NOTE] Remover posteriormente um disco de dados sem editar /etc/fstab pode causar a máquina virtual falhe na inicialização. A maioria das distribuições fornecem a `nofail` e/ou `nobootwait` /etc/fstab opções. Essas opções permitem um inicialização mesmo se o disco falhar montar no momento da inicialização do sistema. Consulte a documentação da sua distribuição para obter mais informações sobre esses parâmetros.

>[AZURE.NOTE] A opção **nofail** assegura que a máquina virtual começa mesmo que o sistema de arquivos está corrompido ou o disco não existir no momento da inicialização. Sem essa opção, você pode encontrar o comportamento conforme descrito em [Possível SSH para máquina virtual Linux devido a erros de /etc/fstab](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)


### <a name="trimunmap-support-for-linux-in-azure"></a>Suporte a CORTAR/UNMAP Linux no Azure
Alguns kernels Linux suportam para operações de CORTAR/UNMAP para descartar blocos não utilizados no disco. Isso é especialmente útil em armazenamento padrão para informar Azure que páginas excluídas não são mais válida e poderá ser descartada. Isso pode poupar custo se você criar arquivos grandes e, em seguida, excluí-las.

Há duas maneiras de habilitar CORTAR suportam em sua VM Linux. Como de costume, consulte sua distribuição para a abordagem recomendada:

- Use o `discard` montar opção no `/etc/fstab`, por exemplo:

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2

- Como alternativa, você pode executar o `fstrim` comando manualmente da linha de comando ou adicioná-lo ao seu crontab para executar regularmente:

    **Ubuntu**

        # sudo apt-get install util-linux
        # sudo fstrim /datadrive

    **RHEL/CentOS**

        # sudo yum install util-linux
        # sudo fstrim /datadrive

## <a name="troubleshooting"></a>Solução de problemas
[AZURE.INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Próximas etapas

- Lembre-se de que o novo disco não está disponível para a máquina virtual se ele reinicializar, a menos que você escreve essas informações para o seu arquivo [/etc/fstab](http://en.wikipedia.org/wiki/Fstab) .
- Para garantir que sua VM Linux está configurado corretamente, examine as recomendações de [otimizar o desempenho da máquina Linux](virtual-machines-linux-optimization.md) .
- Expanda sua capacidade de armazenamento adicionando discos adicionais e [configurar RAID](virtual-machines-linux-configure-raid.md) para desempenho adicional.
