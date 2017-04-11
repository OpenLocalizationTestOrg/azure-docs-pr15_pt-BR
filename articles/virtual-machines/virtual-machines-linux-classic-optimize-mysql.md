<properties
    pageTitle="Otimizar o desempenho de MySQL em Linux VMs | Microsoft Azure"
    description="Saiba como otimizar MySQL em execução em uma máquina virtual Azure (máquina virtual) executando Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="NingKuang"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="ningk"/>

#<a name="optimizing-mysql-performance-on-azure-linux-vms"></a>Otimização de desempenho de MySQL em VMs Linux Azure

Há muitos fatores que afetam o desempenho de MySQL no Azure, ambos na configuração de seleção e software de hardware virtual. Este artigo se concentra nos otimização de desempenho por meio de armazenamento, sistema e configurações de banco de dados.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


##<a name="utilizing-raid-on-an-azure-virtual-machine"></a>Utilizando RAID em uma máquina virtual Azure
Armazenamento é o principal fator que afeta o desempenho do banco de dados em ambientes de nuvem.  Em comparação com um único disco, RAID pode fornecer acesso mais rápido por meio de concorrência.  Para obter mais detalhes, consulte [Níveis de RAID padrão](http://en.wikipedia.org/wiki/Standard_RAID_levels) .   

Taxa de transferência do disco e/s e tempo de resposta de e/s no Azure podem ser aprimoradas significativamente através de RAID. Nossos testes de laboratório mostram/s de disco produtividade pode ser duplicada e tempo de resposta de e/s pode ser reduzido pela metade em média quando o número de discos RAID é dobrado (de 2 a 4, 4 a 8, etc.). Para obter detalhes, consulte o [Apêndice A](#AppendixA) .  

Além de disco e/s, o desempenho de MySQL melhora quando você aumenta o nível de RAID.  Para obter detalhes, consulte o [Apêndice B](#AppendixB) .  

Você também pode querer considerar o tamanho da parte. Em geral quando você tiver um tamanho maior de bloco, você obterá inferior sobrecarga, especialmente para gravações grandes. No entanto, quando o tamanho da parte é muito grande, pode adicionar uma sobrecarga adicional e você não pode aproveitar o RAID. O tamanho padrão atual é 512KB, que vem provando seja ideal para ambientes de produção mais gerais. Para obter detalhes, consulte o [Apêndice C](#AppendixC) .   

Observe que há limites em quantos discos você pode adicionar para tipos de máquina virtual diferente. Esses limites são detalhados em [tamanhos de serviço de nuvem do Azure e máquina Virtual](http://msdn.microsoft.com/library/azure/dn197896.aspx). Você precisará 4 discos de dados anexados para acompanhar o exemplo RAID neste artigo, embora você pode optar por configurar RAID com menos discos.  

Este artigo pressupõe que você já tiver criado uma máquina virtual Linux e ter MYSQL instalado e configurado. Para obter mais informações sobre como começar consulte como instalar MySQL no Azure.  

###<a name="setting-up-raid-on-azure"></a>Configuração de RAID no Azure
As etapas a seguir mostram como criar RAID no Azure usando o portal de clássico Azure. Você também pode definir RAID usando scripts do Windows PowerShell.
Neste exemplo, podemos configurará RAID 0 com 4 discos.  

####<a name="step-1-add-a-data-disk-to-your-virtual-machine"></a>Etapa 1: Adicionar um disco de dados para sua máquina Virtual  

Na página máquinas virtuais do Azure portal clássico, clique na máquina virtual ao qual você deseja adicionar um disco de dados. Neste exemplo, a máquina virtual é mysqlnode1.  

![][1]

Na página para a máquina virtual, clique em **Painel de controle**.  

![][2]


Na barra de tarefas, clique em **Anexar**.

![][3]

E clique em **disco vazio anexar**.  

![][4]

Para discos de dados, a **Preferência de Cache de Host** deve ser definida como **Nenhum**.  

Isso adicionará um disco vazio em sua máquina virtual. Repita esta etapa três vezes mais para que você tenha 4 discos de dados para RAID.  

Você pode ver as unidades adicionados na máquina virtual examinando o log de mensagens de núcleo. Por exemplo, para ver isso no Ubuntu, use o seguinte comando:  

    sudo grep SCSI /var/log/dmesg

####<a name="step-2-create-raid-with-the-additional-disks"></a>Etapa 2: Criar RAID com os discos adicionais
Siga este artigo para obter etapas detalhadas de configuração RAID:  

[Configurar o software RAID no Linux](virtual-machines-linux-configure-raid.md)

>[AZURE.NOTE] Se você estiver usando o sistema de arquivos XFS, siga as etapas abaixo após ter criado RAID.

Para instalar XFS em Debian, Ubuntu ou Linux Menta, use o seguinte comando:  

    apt-get -y install xfsprogs  

Para instalar o XFS em Fedora, CentOS ou RHEL, use o seguinte comando:  

    yum -y install xfsprogs  xfsdump


####<a name="step-3-set-up-a-new-storage-path"></a>Etapa 3: Configurar um novo caminho de armazenamento
Use o seguinte comando:  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

####<a name="step-4-copy-the-original-data-to-the-new-storage-path"></a>Etapa 4: Copiar os dados originais para o novo caminho de armazenamento
Use o seguinte comando:  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

####<a name="step-5-modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Etapa 5: Modificar permissões para que possa acessar MySQL (leitura e gravação) o disco de dados
Use o seguinte comando:  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


##<a name="adjust-the-disk-io-scheduling-algorithm"></a>Ajustar o algoritmo de agendamento de e/s de disco
Linux implementa quatro tipos de i/o agendamento algoritmos:  

-   Algoritmo NOOP (sem operação)
-   Algoritmo de prazo (data limite)
-   Algoritmo de fila completamente feira (CFQ)
-   Algoritmo de período de orçamento (Anticipatory)  

Você pode selecionar diferentes agendadores de e/s em cenários diferentes para otimizar o desempenho. Em um ambiente de acesso completamente aleatório, não há uma grande diferença entre os algoritmos CFQ e data limite para um desempenho. Geralmente é recomendável para configurar o ambiente de banco de dados MySQL para data limite para estabilidade. Se houver muitos i/o sequencial, CFQ pode reduzir o desempenho de e/s de disco.   

Para SSD e outros equipamentos, usando NOOP ou prazo pode obter melhor desempenho que o agendador padrão.   

Do núcleo 2.5, o algoritmo de agendamento de e/s padrão é data limite. Começando de núcleo 2.6.18, CFQ se tornou o algoritmo de agendamento de i/o padrão.  Você pode especificar essa configuração no momento da inicialização de núcleo ou modificar dinamicamente essa configuração quando o sistema está em execução.  

O exemplo a seguir demonstra como verificar e defina o agendador padrão para o algoritmo NOOP.  

Para a família de distribuição Debian:

###<a name="step-1view-the-current-io-scheduler"></a>Agendador de modo de exibição atual e/s etapa 1.
Use o seguinte comando:  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Você verá seguindo saída, que indica o Agendador atual.  

    noop [deadline] cfq


###<a name="step-2-change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Etapa 2. Alterar o dispositivo atual (/ desenvolvimento/sda) de i/o algoritmo de agendamento
Use os seguintes comandos:  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

>[AZURE.NOTE] Essa configuração para /dev/sda sozinho não é útil. Ele precisa ser definidas em todos os discos de dados onde o banco de dados está localizado.  

Você verá a seguinte saída, indicando que grub.cfg recriado com êxito e que o agendador padrão foi atualizado para NOOP.  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Para a família de distribuição Redhat, você só precisará o seguinte comando:   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

##<a name="configure-system-file-operations-settings"></a>Definir configurações de operações de arquivo do sistema
Uma prática recomendada é desabilitar o recurso de log atime no sistema de arquivos. Atime é a última vez de acesso de arquivo. Sempre que um arquivo é acessado, o sistema de arquivo registra o carimbo de hora no log. No entanto, essas informações raramente são usadas. Você pode desabilitá-lo se não precisar, que irá reduzir o tempo de acesso geral do disco.  

Para desativar o log de atime, é necessário modificar o arquivo sistema configuração arquivo /etc/. /etc/fstab e adicionar a opção **noatime** .  

Por exemplo, edite o arquivo de /etc/fstab vim, adicionando a noatime conforme mostrado abaixo.  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Em seguida, monte novamente o sistema de arquivos com o seguinte comando:  

    mount -o remount /RAID0

Teste o resultado modificado. Observe que, quando você modifica o arquivo de teste, o tempo de acesso não é atualizado.  

Antes de exemplo:     

![][5]

Depois de exemplo:

![][6]

##<a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Aumente o número máximo de identificadores do sistema para alta concorrência
MySQL é banco de dados de concorrência alta. O número padrão de alças simultâneas é 1024 para Linux, que sempre não é suficiente. **Use as seguintes etapas para aumentar as alças simultâneas máximos do sistema para dar suporte a concorrência alta do MySQL**.

###<a name="step-1-modify-the-limitsconf-file"></a>Etapa 1: Modificar o arquivo limits.conf
Adicione as seguintes quatro linhas no arquivo /etc/security/limits.conf para aumentar as alças de simultâneas máximos permitidas. Observe que 65536 é o número máximo que o sistema pode oferecer suporte.   

    * nofile suave 65536
    * disco rígido nofile 65536
    * nproc suave 65536
    * disco rígido nproc 65536

###<a name="step-2-update-the-system-for-the-new-limits"></a>Etapa 2: Atualizar o sistema para os novos limites
Execute os seguintes comandos:  

    ulimit -SHn 65536
    ulimit -SHu 65536

###<a name="step-3-ensure-that-the-limits-are-updated-at-boot-time"></a>Etapa 3: Garantir que os limites são atualizados no momento da inicialização
Coloca os seguintes comandos de inicialização no arquivo /etc/rc.local para que ele serão efetivadas durante toda vez de inicialização.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

##<a name="mysql-database-optimization"></a>Otimização de banco de dados MySQL
Você pode usar a mesma estratégia de ajuste de desempenho para configurar MySQL no Azure como em um computador local.  

As regras de otimização de i/o principais são:   

-   Aumente o tamanho do cache.
-   Reduza o tempo de resposta de e/s.  

Para otimizar as configurações do servidor MySQL, você pode atualizar o arquivo my.cnf, que é o arquivo de configuração padrão para computadores cliente e servidor.  

Os seguintes itens de configuração são os principais fatores que afetam o desempenho de MySQL:  

-   **innodb_buffer_pool_size**: O pool de buffer contém dados armazenados em buffer e o índice. Normalmente, isso é definido para 70% de memória física.
-   **innodb_log_file_size**: Este é o tamanho do log de refazer. Você pode usar logs de refazer para garantir que as operações de gravação rápida, confiável e recuperável após uma falha. Isso é definido para 512MB, que lhe dará bastante espaço para efetuar operações de gravação.
-   **max_connections**: às vezes aplicativos não fechar conexões corretamente. Um valor maior dará ao servidor mais tempo para a Lixeira ociosos conexões. O número máximo de conexões é 10000, mas o máximo recomendado é 5000.
-   **Innodb_file_per_table**: essa configuração Habilitar ou desabilitar a capacidade de InnoDB para armazenar as tabelas em arquivos separados. Ativar a opção garantirá que várias operações de administração avançada podem ser aplicadas com eficiência. Do ponto de vista de desempenho, ele pode acelerar a transmissão de espaço de tabela e otimizar o desempenho de gerenciamento de resíduos. Portanto, a configuração recomendada para isso é ativada.</br>
    Do MySQL 5.6, a configuração padrão é ativado. Portanto, nenhuma ação é necessária. Para outras versões, que é anterior a 5.6, as configurações padrão é desativada. Este está ligando é necessária. E deve aplicá-lo antes de dados são carregados, porque somente recém-criado tabelas são afetadas.
-   **innodb_flush_log_at_trx_commit**: valor padrão é 1, com o escopo definido como 0 ~ 2. O valor padrão é a opção mais adequada para independente do banco de dados MySQL. A configuração de 2 habilita a maioria dos integridade de dados e é adequada para mestre em cluster MySQL. A configuração de 0 permite perda de dados, que pode afetar a confiabilidade, em alguns casos com melhor desempenho e é adequado para auxiliar em cluster MySQL.
-   **Innodb_log_buffer_size**: O buffer de log permite que as transações executar sem ter de liberar o log para o disco antes de confirmar as transações. Entretanto, se houver campos de texto ou objeto binário grande, o cache será consumido rapidamente e/s de disco frequente será acionada. É melhor aumentar o tamanho de buffer se variável de estado de Innodb_log_waits não é 0.
-   **query_cache_size**: A melhor opção é desabilitá-lo desde o início. Defina query_cache_size como 0 (que, agora, é a configuração padrão no MySQL 5.6) e usar outros métodos para acelerar a consultas.  

Consulte [Anexo D](#AppendixD) para comparar desempenho após a otimização.


##<a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>Ativar o log de consulta lenta de MySQL para analisar o gargalo de desempenho
O log de consulta lenta de MySQL pode ajudá-lo a identificar as consultas lentas para MySQL. Depois de habilitar o log de consulta lenta de MySQL, você pode usar ferramentas de MySQL como **mysqldumpslow** para identificar o gargalo de desempenho.  

Observe que por padrão isso não está ativado. Como ativar o log de consulta lenta pode consumir alguns recursos da CPU. Portanto, é recomendável que você ative isso temporariamente para gargalos de desempenho de solução de problemas.

###<a name="step-1-modify-mycnf-file-by-adding-the-following-lines-to-the-end"></a>Etapa 1: Modificar my.cnf arquivo adicionando as seguintes linhas para o final   

    long_query_time = 2
    slow_query_log = 1
    slow_query_log_file = /RAID0/mysql/mysql-slow.log

###<a name="step-2-restart-mysql-server"></a>Etapa 2: Reinicie o servidor mysql
    service  mysql  restart

###<a name="step-3-check-whether-the-setting-is-taking-effect-using-the-show-command"></a>Etapa 3: Verificar se a configuração está demorando efeito usando o comando "show"

![][7]   

![][8]

Neste exemplo, você pode ver que o recurso de consulta lenta foi ativado. Em seguida, você pode usar a ferramenta de **mysqldumpslow** para determinar gargalos de desempenho e otimizar o desempenho, como a adição de índices.





##<a name="appendix"></a>Apêndice

A seguir estão dados de teste de desempenho de exemplo produzidos no ambiente de laboratório alvo, eles fornecem o gerais sobre a tendência de dados de desempenho com diferentes abordagens, de ajuste de desempenho no entanto, os resultados podem variar em versões diferentes do ambiente ou produto.

<a name="AppendixA"></a>Apêndice a:  
**Desempenho de disco (IOPS) com níveis de RAID diferentes**


![][9]

**Comandos de teste:**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

>AZURE. Observação: A carga de trabalho desse teste usa 64 threads, tentando alcançar o limite superior do RAID.

<a name="AppendixB"></a>Apêndice b:  
**Comparação de desempenho (taxa de transferência) MySQL com níveis de RAID diferentes**   
(Sistema de arquivos XFS)


![][10]  
![][11]

**Comandos de teste:**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**Comparação de desempenho (OLTP) MySQL com níveis de RAID diferentes**  
![][12]

**Comandos de teste:**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

<a name="AppendixC"></a>Apêndice c:   
**Comparação de desempenho (IOPS) de disco para tamanhos de bloco diferentes**  
(Sistema de arquivos XFS)


![][13]

**Comandos de teste:**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Observe o tamanho de arquivo usado para esse teste é 30GB e 1GB, respectivamente, com RAID 0(4 disks) XFS CAM sistema.


<a name="AppendixD"></a>Apêndice d:  
**Comparação de desempenho (taxa de transferência) MySQL antes e depois de otimização**  
(Sistema de arquivos XFS)


![][14]

**Comandos de teste:**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**A configuração padrão e otimização é da seguinte maneira:**

|Parâmetros |Padrão    |otimização
|-----------|-----------|-----------
|**innodb_buffer_pool_size**    |Nenhum   |7G
|**innodb_log_file_size**   |5M |512M
|**max_connections**    |100    |5000
|**innodb_file_per_table**  |0  |1
|**innodb_flush_log_at_trx_commit** |1  |2
|**innodb_log_buffer_size** |8M |128M
|**query_cache_size**   |16M    |0


Parâmetros de configuração de otimização mais detalhados, consulte as instruções de oficial mysql.  

[http://dev.MySQL.com/doc/refman/5.6/en/innodb-Configuration.HTML](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)  

[http://dev.MySQL.com/doc/refman/5.6/en/innodb-Parameters.HTML#sysvar_innodb_flush_method](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)

**Ambiente de teste**  

|Hardware   |Detalhes
|-----------|-------
|CPU    |AMD Opteron (tm) processador 4171 HE / 4 cores
|Memória |G 14
|disco   |10g/disco
|sistema operacional |Ubuntu 14.04.1 LTS



[1]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-01.png
[2]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-02.png
[3]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-03.png
[4]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-04.png
[5]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-05.png
[6]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-06.png
[7]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-07.png
[8]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-08.png
[9]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-09.png
[10]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-10.png
[11]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-11.png
[12]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-12.png
[13]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-13.png
[14]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-14.png
