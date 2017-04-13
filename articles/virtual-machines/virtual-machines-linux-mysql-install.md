<properties
    pageTitle="Configurar o MySQL em uma VM Linux | Microsoft Azure "
    description="Saiba como instalar a pilha de MySQL no computador virtual Linux (Ubuntu RedHat família ou SO) no Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/01/2016"
    ms.author="mingzhan"/>


#<a name="how-to-install-mysql-on-azure"></a>Como instalar o MySQL no Azure


Neste artigo, você aprenderá como instalar e configurar o MySQL em uma máquina virtual Azure executando Linux.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


##<a name="install-mysql-on-your-virtual-machine"></a>Instalar MySQL em sua máquina virtual

> [AZURE.NOTE] Você já deve ter uma máquina virtual do Microsoft Azure executando Linux para concluir este tutorial. Consulte o [tutorial de máquina virtual do Azure Linux](virtual-machines-linux-quick-create-cli.md) para criar e configurar uma VM Linux com `mysqlnode` como o nome de máquina virtual e `azureuser` como usuário antes de continuar.

Nesse caso, use 3306 porta como a porta de MySQL.  

Conecte a máquina virtual que você criou via acabamento Linux. Se esta for a primeira vez que você usa máquina virtual do Azure Linux, veja como usar acabamento conectar a uma VM Linux [aqui](virtual-machines-linux-mac-create-ssh-keys.md).

Usaremos o pacote do repositório instalar MySQL5.6 como exemplo neste artigo. Na verdade, MySQL5.6 tem mais de melhoria no desempenho que MySQL5.5.  Mais informações [aqui](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).


###<a name="how-to-install-mysql56-on-ubuntu"></a>Como instalar MySQL5.6 no Ubuntu
Usaremos Linux VM com Ubuntu do Azure aqui.

- Etapa 1: Instalar MySQL Server 5.6 alternar para `root` usuário:

            #[azureuser@mysqlnode:~]sudo su -

    Instale o servidor mysql 5.6:

            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6

    Durante a instalação, você verá um poping da janela de diálogo até peça para definir a senha de raiz de MySQL abaixo e você precisa definir a senha aqui.

    ![imagem](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p1.png)


    Inserir a senha novamente para confirmar.

    ![imagem](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p2.png)

- Etapa 2: Login MySQL Server

    Quando terminar de instalação do servidor MySQL, serviço MySQL será iniciado automaticamente. Você pode fazer logon servidor MySQL com `root` usuário.
    Use o abaixo comando a senha de logon e entrada.

             #[root@mysqlnode ~]# mysql -uroot -p

- Etapa 3: Gerenciar o serviço MySQL em execução

    (a) obter o status do serviço de MySQL

             #[root@mysqlnode ~]# service mysql status

    (b) iniciar o serviço MySQL

             #[root@mysqlnode ~]# service mysql start

    (c) parar o serviço MySQL

             #[root@mysqlnode ~]# service mysql stop

    (d) reinicie o serviço MySQL

             #[root@mysqlnode ~]# service mysql restart


###<a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Como instalar MySQL em família vermelho é o seu SO como CentOS, Oracle Linux
Usaremos Linux VM com CentOS ou Oracle Linux aqui.

- Etapa 1: Adicionar o repositório de MySQL Yum alternar para `root` usuário:

            #[azureuser@mysqlnode:~]sudo su -

    Baixe e instale o pacote de lançamento de MySQL:

            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm

- Etapa 2: Edite abaixo de arquivo para habilitar o repositório de MySQL para baixar o pacote de MySQL5.6.

            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo

    Atualize a cada valor deste arquivo abaixo:

        \# *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- Etapa 3: MySQL de instalação do repositório de MySQL instalar MySQL:

           #[root@mysqlnode ~]#yum install mysql-community-server

    Pacote RPM MySQL e todos os pacotes relacionados serão instalados.

- Etapa 4: Gerenciar o serviço MySQL em execução

    (a) verificar o status do serviço do servidor MySQL:

           #[root@mysqlnode ~]#service mysqld status

    (b) verificar se o servidor de porta de MySQL padrão está em execução:

           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306


    (c) inicie o servidor MySQL:

           #[root@mysqlnode ~]#service mysqld start

    (d) pare o servidor MySQL:

           #[root@mysqlnode ~]#service mysqld stop

    (e) MySQL conjunto para iniciar quando a inicialização do sistema para:

           #[root@mysqlnode ~]#chkconfig mysqld on


###<a name="how-to-install-mysql-on-suse-linux"></a>Como instalar MySQL em SUSE Linux
Usaremos Linux VM com OpenSUSE aqui.

- Etapa 1: Baixar e instalar o servidor MySQL

    Alternar para `root` usuário pelas abaixo comando:  

           #sudo su -

    Baixe e instale o pacote de MySQL:

           #[root@mysqlnode ~]# zypper update

           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql

- Etapa 2: Gerenciar o serviço MySQL em execução

    (a) verificar o status do servidor MySQL:

           #[root@mysqlnode ~]# rcmysql status

    (b) verificar se a porta padrão do servidor MySQL:

           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306


    (c) inicie o servidor MySQL:

           #[root@mysqlnode ~]# rcmysql start

    (d) pare o servidor MySQL:

           #[root@mysqlnode ~]# rcmysql stop

    (e) MySQL conjunto para iniciar quando a inicialização do sistema para:

           #[root@mysqlnode ~]# insserv mysql

###<a name="next-step"></a>Próxima etapa
Encontre mais informações sobre MySQL [aqui](https://www.mysql.com/)e uso.
