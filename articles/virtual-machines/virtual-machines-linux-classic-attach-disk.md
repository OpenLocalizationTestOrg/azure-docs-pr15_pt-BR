<properties
    pageTitle="Anexar um disco a uma VM Linux | Microsoft Azure"
    description="Saiba como anexar um disco de dados a uma máquina virtual Azure executando Linux e inicializá-lo para que ele está pronto para uso."
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
    ms.date="08/23/2016"
    ms.author="iainfou"/>

# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Como anexar um disco de dados a um computador Virtual Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Veja como [anexar um disco de dados usando o modelo de implantação do Gerenciador de recursos](virtual-machines-linux-add-disk.md).

Você pode anexar tanto vazios discos e que contêm dados em suas VMs do Azure. Os dois tipos de discos são arquivos. vhd que residem em uma conta de armazenamento do Azure. Como adicionando qualquer disco a um computador Linux, depois de anexar o disco você precisa inicializar e formatá-lo para que ele está pronto para uso. Este detalhes de artigo anexando tanto vazios discos e já que contém os dados para suas VMs, bem como para então inicializar e formatar um novo disco.

> [AZURE.NOTE]É uma prática recomendada usar um ou mais discos separados para armazenar dados de uma máquina virtual. Quando você cria uma máquina virtual Azure, ele tem um sistema operacional e um disco temporário. **Não use o disco temporário para armazenar dados persistentes.** Como o nome indica, ele oferece somente armazenamento temporário. Ele oferece sem redundância ou backup porque ela não reside no armazenamento do Azure.
> O disco temporário normalmente gerenciado pelo agente de Linux do Azure e automaticamente montado **/mnt/resource** (ou **/mnt** em imagens Ubuntu). Por outro lado, um disco de dados pode ser nomeado de acordo com o núcleo Linux algo parecido com `/dev/sdc`, e você precisa partição, formatar e montar este recurso. Consulte o [Guia do usuário do Azure Linux agente] [ Agent] para obter detalhes.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>Inicializar um novo disco de dados no Linux

1. SSH para sua máquina virtual. Para obter mais detalhes, consulte [como fazer logon em uma máquina virtual executando Linux][Logon].

2. Avançar para encontrar o identificador de dispositivo para o disco de dados ao inicializar. Há duas maneiras de fazer isso:

    a) Grep para dispositivos SCSI nos logs, tais como o seguinte comando:

            $sudo grep SCSI /var/log/messages

    Para distribuições Ubuntu recentes, você pode precisar usar `sudo grep SCSI /var/log/syslog` porque o registro em log para `/var/log/messages` podem estar desabilitados por padrão.

    Você pode encontrar o identificador do último disco de dados que foi adicionado nas mensagens que são exibidas.

    ![O disco de mensagens](./media/virtual-machines-linux-classic-attach-disk/scsidisklog.png)

    OU

    b) use o `lsscsi` comando para descobrir a identificação do dispositivo. `lsscsi`pode ser instalado por qualquer um `yum install lsscsi` (em vermelho é o seu baseado distribuições) ou `apt-get install lsscsi` (em Debian baseado distribuições). Você pode encontrar o disco que você está procurando pelo seu _lun_ ou o **número de unidade lógica**. Por exemplo, o _lun_ para os discos anexado pode ser visto facilmente em `azure vm disk list <virtual-machine>` como:

            ~$ azure vm disk list TestVM
            info:    Executing command vm disk list
            + Fetching disk images
            + Getting virtual machines
            + Getting VM disks
            data:    Lun  Size(GB)  Blob-Name                         OS
            data:    ---  --------  --------------------------------  -----
            data:         30        TestVM-2645b8030676c8f8.vhd  Linux
            data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
            info:    vm disk list command OK

    Comparar esses dados com a saída de `lsscsi` para a mesma máquina virtual de exemplo:

            ops@TestVM:~$ lsscsi
            [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
            [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
            [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
            [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc

    O último número na tupla em cada linha é o _lun_. Consulte `man lsscsi` para obter mais informações.

3. No prompt, digite o seguinte comando para criar o seu dispositivo:

        $sudo fdisk /dev/sdc


4. Quando solicitado, digite **n** para criar uma nova partição.


    ![Criar dispositivo](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartition.png)

5. Quando solicitado, digite **p** para tornar a partição partição primária. Digite **1** para torná-lo a primeira partição e, em seguida, digite enter para aceitar o valor padrão para o cilindro. Em alguns sistemas, ele pode mostrar os valores padrão da primeira e as últimos setores, em vez de cilindro. Você pode optar por aceitar esses padrões.


    ![Criar partição](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartdetails.png)



6. Digite **p** para ver os detalhes sobre o disco que está sendo particionadas.


    ![Informações de disco de lista](./media/virtual-machines-linux-classic-attach-disk/fdiskpartitiondetails.png)



7. Digite **w** para gravar as configurações para o disco.


    ![Gravar as alterações de disco](./media/virtual-machines-linux-classic-attach-disk/fdiskwritedisk.png)

8. Agora você pode criar o sistema de arquivos na nova partição. Acrescentar o número da partição a identificação do dispositivo (no exemplo a seguir `/dev/sdc1`). O exemplo a seguir cria uma partição de ext4 em /dev/sdc1:

        # sudo mkfs -t ext4 /dev/sdc1

    ![Criar sistema de arquivos](./media/virtual-machines-linux-classic-attach-disk/mkfsext4.png)

    >[AZURE.NOTE] Sistemas SuSE Linux Enterprise 11 só oferece suporte para acesso somente leitura para sistemas de arquivos ext4. Para esses sistemas, é recomendável para formatar o novo sistema de arquivos como ext3 em vez de ext4.


9. Crie um diretório para montar o novo sistema de arquivos, da seguinte maneira:

        # sudo mkdir /datadrive


10. Finalmente você pode montar a unidade, da seguinte maneira:

        # sudo mount /dev/sdc1 /datadrive

    O disco de dados agora está pronto para usar como **/datadrive**.
    
    ![Crie o diretório e monte o disco](./media/virtual-machines-linux-classic-attach-disk/mkdirandmount.png)


11. Adicione a nova unidade a /etc/fstab:

    Para garantir que a unidade é remontada automaticamente após a reinicialização ele deve ser adicionado ao arquivo /etc/fstab. Além disso, é altamente recomendável que o UUID (Universal identificador exclusivo) é usado no /etc/fstab para se referir a unidade em vez de apenas o nome do dispositivo (ou seja, /dev/sdc1). Usar o UUID evita o disco incorreto sendo montado em um determinado local se o sistema operacional detecta um erro de disco durante a inicialização e qualquer disco de dados restantes sendo atribuído essas identificações de dispositivo. Para localizar o UUID da nova unidade, você pode usar o utilitário de **blkid** :

        # sudo -i blkid

    A saída é semelhante ao seguinte:

        /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
        /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
        /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


    >[AZURE.NOTE] O arquivo **/etc/fstab** de edição incorretamente pode resultar em um sistema de impossibilidade. Se não tiver certeza, consulte a documentação da distribuição para obter informações sobre como editar corretamente este arquivo. Também é recomendável que um backup do arquivo /etc/fstab for criado antes da edição.

    Em seguida, abra o arquivo **/etc/fstab** em um editor de texto:

        # sudo vi /etc/fstab

    Neste exemplo, usamos o valor UUID para o novo dispositivo **/dev/sdc1** criado em etapas anteriores e o ponto de montagem **/datadrive**. Adicione a seguinte linha ao final do arquivo **/etc/fstab.** :

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2

    Ou, em sistemas com base em SuSE Linux talvez você precise usar um formato ligeiramente diferentes:

        /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    
    >[AZURE.NOTE] O `nofail` opção garante que a máquina virtual começa mesmo que o sistema de arquivos está corrompido ou o disco não existir no momento da inicialização. Sem essa opção, talvez você encontre comportamento conforme descrito em [Possível SSH para máquina virtual Linux devido a erros de /etc/fstab](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    Agora você pode testar se o sistema de arquivos é montado corretamente por desmontar e, em seguida, remontando o sistema de arquivos, ou seja usando o exemplo de ponto de montagem `/datadrive` criou nas etapas anteriores:

        # sudo umount /datadrive
        # sudo mount /datadrive

    Se o `mount` comando produz um erro, verifique o/arquivo /etc/fstab sintaxe correta. Se as unidades de dados adicionais ou partições são criadas, inseri-las nas/etc//etc/fstab separadamente também.

    Verifique a unidade gravável usando este comando:

        # sudo chmod go+w /datadrive

>[AZURE.NOTE] Remover subsequentemente um disco de dados sem editar /etc/fstab pode causar a máquina virtual falhe na inicialização. Se esta for uma ocorrência comum, a maioria das distribuições fornecem a `nofail` e/ou `nobootwait` tempo de inicialização de opções de /etc/fstab que permitem que um sistema inicializa mesmo se o disco não conseguir montar em. Consulte a documentação da sua distribuição para obter mais informações sobre esses parâmetros.

### <a name="trimunmap-support-for-linux-in-azure"></a>Suporte a CORTAR/UNMAP Linux no Azure
Alguns kernels Linux suportam para operações de CORTAR/UNMAP para descartar blocos não utilizados no disco. Essas operações são úteis principalmente armazenamento padrão para informar Azure que páginas excluídas não são mais válida e poderá ser descartada. Descartar páginas pode salvar o custo se você criar arquivos grandes e, em seguida, excluí-las.

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
Você pode ler mais sobre como usar sua VM Linux nos seguintes artigos:

- [Como fazer logon em uma máquina virtual executando Linux][Logon]

- [Como desanexar um disco de um computador virtual Linux](virtual-machines-linux-classic-detach-disk.md)

- [Usando a CLI Azure com o modelo clássico de implantação](../virtual-machines-command-line-tools.md)

<!--Link references-->
[Agent]: virtual-machines-linux-agent-user-guide.md
[Logon]: virtual-machines-linux-mac-create-ssh-keys.md
