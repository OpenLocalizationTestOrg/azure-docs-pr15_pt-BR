<properties
    pageTitle="Executando um cluster de MariaDB (MySQL) no Azure"
    description="Criar um MariaDB + Galera MySQL cluster em máquinas virtuais do Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="sabbour"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="04/15/2015"
    ms.author="v-ahsab"/>

# <a name="mariadb-mysql-cluster---azure-tutorial"></a>MariaDB (MySQL) cluster - tutorial Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

> [AZURE.NOTE]  Cluster MariaDB Enterprise agora está disponível do Azure Marketplace.  A nova oferta implantará automaticamente um cluster MariaDB Galera em ARM. Você deve usar a nova oferta da https://azure.microsoft.com/en-us/marketplace/partners/mariadb/cluster-maxscale/ 

Estamos criando um cluster de [Galera](http://galeracluster.com/products/) vários mestres de [MariaDBs](https://mariadb.org/en/about/), substituto para soltar robusta, flexível e confiável para MySQL, trabalhar em um ambiente altamente disponível em máquinas virtuais do Azure.

## <a name="architecture-overview"></a>Visão geral da arquitetura

Este tópico executa as seguintes etapas:

1. Criar um cluster de 3 nós
2. Separe os discos de dados do disco SO
3. Criar os discos de dados na configuração RAID-0/distribuídos para aumentar o IOPS
4. Use o balanceador de carga do Azure para saldo a carga para os 3 nós
5. Para minimizar o trabalho repetitivo, crie uma imagem de máquina virtual contendo MariaDB + Galera e usá-lo para criar outro cluster VMs.

![Arquitetura](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Setup.png)

> [AZURE.NOTE]  Este tópico usa as ferramentas de [CLI do Azure](../xplat-cli-install.md) , portanto, verifique se baixá-los e conectá-los à sua assinatura do Azure acordo com as instruções. Se precisar de uma referência para os comandos disponíveis no CLI Azure, confira este link para a [referência de comandos de CLI do Azure](../virtual-machines-command-line-tools.md). Você também precisa [criar uma chave SSH para autenticação] e anote o **local do arquivo de .pem**.


## <a name="creating-the-template"></a>Criar o modelo

### <a name="infrastructure"></a>Infraestrutura

1. Criar um grupo de afinidade para manter os recursos juntos

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"

2. Criar uma rede Virtual

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet

3. Crie uma conta de armazenamento para hospedar todos os nossos discos. Observe que você não deve ser colocando mais de 40 muito usado discos na mesma conta de armazenamento para evitar atingir o limite de conta de armazenamento IOPS 20.000. Nesse caso, estamos longe desse número para nós vai armazenar tudo na mesma conta para simplicidade

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster

3. Localize o nome da imagem CentOS 7 Virtual Machine

        azure vm image list | findstr CentOS
Isso irá gerar uma saída algo parecido com `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`. Use o nome na etapa seguinte.

4. Criar o modelo de máquina virtual substituindo **/path/to/key.pem** com o caminho onde você armazenou chave SSH .pem gerado

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser

5. Anexar discos de dados de 4 x 500GB para a máquina virtual para uso na configuração RAID

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd

6. SSH no modelo de máquina virtual que você criou em **mariadbhatemplate.cloudapp.net:22** e conecte-se usando sua chave privada.

### <a name="software"></a>Software

1. Obter raiz

        sudo su

2. Instale o suporte RAID:

     - Instalar o mdadm

                yum install mdadm

     - Criar a configuração RAID0/faixa com um sistema de arquivo EXT4

                mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
                mdadm --detail --scan >> /etc/mdadm.conf
                mkfs -t ext4 /dev/md0

     - Criar o diretório de ponto de montagem

                mkdir /mnt/data

     - Recuperar o UUID do dispositivo RAID recém-criado

                blkid | grep /dev/md0

     - Editar /etc/fstab.

                vi /etc/fstab

     - Adicionar o dispositivo lá mouting automático na reinicialização substituindo o UUID pelo valor obtido o comando **blkid** antes de habilitar

                UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2

     - Montar a nova partição

                mount /mnt/data

3. Instale o MariaDB:

     - Crie o arquivo de MariaDB.repo:

                vi /etc/yum.repos.d/MariaDB.repo

     - Preenchê-lo com o abaixo de conteúdo

                [mariadb]
                name = MariaDB
                baseurl = http://yum.mariadb.org/10.0/centos7-amd64
                gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
                gpgcheck=1

     - Remover o sufixo existente e mariadb-as bibliotecas para evitar conflitos

            yum remove postfix mariadb-libs-*

     - Instalar o MariaDB com Galera

            yum install MariaDB-Galera-server MariaDB-client galera

4. Mover o diretório de dados MySQL para o dispositivo de bloco RAID

     - Copie a pasta atual do MySQL para seu novo local e remover diretório antigo

            cp -avr /var/lib/mysql /mnt/data  
            rm -rf /var/lib/mysql

     - Definir permissões no novo diretório adequadamente

            chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/  

     - Criar um symlink apontando diretório antigo para o novo local na partição RAID

            ln -s /mnt/data/mysql /var/lib/mysql

5. Porque [que SELinux irá interferir nas operações de cluster](http://galeracluster.com/documentation-webpages/configuration.html#selinux), é necessário desabilitá-lo para a sessão atual (até que apareça uma versão compatível). Editar `/etc/selinux/config` para desabilitá-lo para reiniciar subsequentes:

            setenforce 0

       then editing `/etc/selinux/config` to set `SELINUX=permissive`

6. Validar executa MySQL

    - Iniciar o MySQL

            service mysql start

    - Proteger a instalação do MySQL, definir a senha raiz, remover usuários anônimos, desabilitando login raiz remota e removendo o banco de dados de teste

            mysql_secure_installation

    - Criar um usuário no banco de dados para operações de cluster e, opcionalmente, seus aplicativos

            mysql -u root -p
            GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
            exit

   - Parar MySQL

            service mysql stop

7. Criar o espaço reservado de configuração

    - Edite a configuração de MySQL para criar um espaço reservado para as configurações de cluster. Não substitua a **`<Vairables>`** ou não comente agora. Que acontecerá depois que criamos uma máquina virtual com este modelo.

            vi /etc/my.cnf.d/server.cnf

    - Editar a seção **[galera]** e limpe-o

    - Editar a seção **[mariadb]**

            wsrep_provider=/usr/lib64/galera/libgalera_smm.so
            binlog_format=ROW
            wsrep_sst_method=rsync
            bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
            default_storage_engine=InnoDB
            innodb_autoinc_lock_mode=2

            wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
            #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
            #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
            #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
            #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server

8. Abrir portas necessárias no firewall (usando FirewallD em CentOS 7)

    - MySQL:`firewall-cmd --zone=public --add-port=3306/tcp --permanent`
    - GALERA:`firewall-cmd --zone=public --add-port=4567/tcp --permanent`
    - GALERA IST:`firewall-cmd --zone=public --add-port=4568/tcp --permanent`
    - RSYNC:`firewall-cmd --zone=public --add-port=4444/tcp --permanent`
    - Recarregar o firewall:`firewall-cmd --reload`

9.  Otimize o sistema para o desempenho. Consulte este artigo sobre [estratégia de ajuste de desempenho](virtual-machines-linux-classic-optimize-mysql.md) para obter mais detalhes

    - Editar o arquivo de configuração de MySQL novamente

            vi /etc/my.cnf.d/server.cnf

    - Editar a seção **[mariadb]** e acrescentar a abaixo

    > [AZURE.NOTE] É recomendável que **innodb\_buffer\_pool_size** ser 70% de memória do sua máquina virtual. Ele foi definido como 2,45 GB aqui para a máquina virtual do Azure de médio com 3,5 GB de RAM.

            innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70% of physical memory.
            innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
            max_connections = 5000 # A larger value will give the server more time to recycle idled connections
            innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
            innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
            innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
            query_cache_size = 0

10. Parar MySQL, desabilitar o serviço MySQL executem em inicialização para evitar perder a cluster ao adicionar um novo nó e desprovisionamento máquina.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision

11. Capture a máquina virtual por meio do portal. (Atualmente, ferramentas de [problema 1268 # no CLI Azure] descreve o fato de que imagens capturadas pelas ferramentas do Azure CLI não captura os discos de dados anexados.)

    - Desligar o computador por meio do portal
    - Clique em captura e especifique o nome de imagem como **mariadb-galera-imagem** e forneça uma descrição e verifique "Tiver executar waagent".
    ![Capturar a máquina Virtual](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture.png)
    ![capturar a máquina Virtual](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture2.PNG)

## <a name="creating-the-cluster"></a>Criar o cluster

Crie 3 VMs fora o modelo que acabou de criar e, em seguida, configurar e iniciar o cluster.

1. Criar a primeira máquina virtual 7 de CentOS da imagem **mariadb de galera de imagem** que você criou, fornecendo o nome de rede virtual **mariadbvnet** e a sub-rede **mariadb**, tamanho **Médio**passando no serviço de nuvem da máquina nome seja **mariadbha** (ou qualquer nome que você deseja ser acessado por meio de mariadbha.cloudapp.net), definindo o nome dessa máquina para ser **mariadb1** e o nome de usuário sejam **azureuser**e habilitar o acesso SSH e passando o SSH certificados .pem arquivo e substituindo **/path/to/key.pem** com o caminho onde você armazenados chave SSH .pem gerado.

    > [AZURE.NOTE] Comandos a seguir são divididos em várias linhas para clareza, mas você deve digitar cada como uma linha.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser

2. Criar 2 mais máquinas virtuais _Conectando_ -los para o criadas atualmente **mariadbha** serviço em nuvem, alterando o **nome da máquina virtual** , bem como a **porta SSH** para uma porta exclusiva não em conflito com outras VMs no serviço de nuvem do mesmo.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
e para MariaDB3

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser

3. Você precisará obter o endereço IP interno de cada um dos 3 VMs para a próxima etapa:

    ![Obtendo o endereço de IP](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/IP.png)

4. SSH em 3 VMs e e editar o arquivo de configuração em cada

        sudo vi /etc/my.cnf.d/server.cnf

    uncommenting **`wsrep_cluster_name`** e **`wsrep_cluster_address`** , removendo o **#** no início e validação são realmente o que você deseja.
    Além disso, substituir **`<ServerIP>`** na **`wsrep_node_address`** e **`<NodeName>`** no **`wsrep_node_name`** com IP a máquina virtual de endereço e nome respectivamente e não comentar essas linhas também.

5. Iniciar o cluster em MariaDB1 e permita a executar na inicialização

        sudo service mysql bootstrap
        chkconfig mysql on

6. Iniciar o MySQL em MariaDB2 e MariaDB3 e permita a executar na inicialização

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balancing-the-cluster"></a>Cluster de balanceamento de carga
Quando você criou as VMs agrupadas, você adicionou-las em uma disponibilidade definido chamado **clusteravset** para garantir que eles são colocados em diferentes falhas e atualizar domínios e que Azure nunca faz manutenção em todos os computadores ao mesmo tempo. Essa configuração atende aos requisitos de suporte pelo contrato de nível de serviço (SLA) que Azure.

Agora você usa o balanceador de carga do Azure para saldo solicitações entre nossos 3 nós.

Executar o abaixo comandos em sua máquina utilizando a CLI do Azure.
A estrutura de parâmetros do comando é:`azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

Finalmente, desde que a CLI define o intervalo de sondagem de Balanceador de carga de 15 segundos (que pode ser um pouco longos demais), alterá-lo no portal em **pontos de extremidade** para qualquer uma das VMs

![Editar o ponto de extremidade](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint.PNG)

Clique no conjunto de Reconfigure The Load-Balanced e vá próximo

![Reconfigure carregar conjunto equilibrado](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint2.PNG)

em seguida, alterar o intervalo de teste para 5 segundos e salvar

![Intervalo de sondagem de alteração](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validating-the-cluster"></a>Validando cluster

O trabalho pesado é feito. O cluster deve ser agora acessível no `mariadbha.cloudapp.net:3306` qual será o balanceador de carga de visitas e rotear solicitações entre 3 VMs tranquilamente e eficiente.

Use seu cliente MySQL favorito para conectar ou conectar-se apenas uma das VMs para verificar que esse cluster está funcionando.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Crie um novo banco de dados e preenchê-lo com alguns dados

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

Resultará na tabela abaixo

    +----+--------+
  	| id | value  |
    +----+--------+
  	|  1 | Value1 |
  	|  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um 3 nó MariaDB + cluster altamente disponível de Galera em máquinas virtuais do Azure executando CentOS 7. VMs são carga equilibrada com o balanceador de carga do Azure.

Talvez você queira dar uma olhada [outra maneira de cluster MySQL no Linux](virtual-machines-linux-classic-mysql-cluster.md) e formas para [otimizar e testar desempenho MySQL em VMs do Azure Linux](virtual-machines-linux-classic-optimize-mysql.md).

<!--Anchors-->
[Architecture overview]: #architecture-overview
[Creating the template]: #creating-the-template
[Creating the cluster]: #creating-the-cluster
[Load balancing the cluster]: #load-balancing-the-cluster
[Validating the cluster]: #validating-the-cluster
[Next steps]: #next-steps

<!--Image references-->

<!--Link references-->
[Galera]: http://galeracluster.com/products/
[MariaDBs]: https://mariadb.org/en/about/
[criar uma chave SSH para autenticação]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[problema #1268 na CLI do Azure]:https://github.com/Azure/azure-xplat-cli/issues/1268
