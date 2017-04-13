<properties
    pageTitle="Atualizar o agente Linux Azure do GitHub | Microsoft Azure"
    description="Saiba como a atualização do agente do Azure Linux para sua VM Linux no Azure para a versão lateset do Github"
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
    ms.date="12/14/2015"
    ms.author="mingzhan"/>


# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>Como atualizar o agente de Linux Azure em uma máquina virtual para a versão mais recente do GitHub

Para atualizar seu [Agente do Azure Linux](https://github.com/Azure/WALinuxAgent) em uma VM Linux no Azure, você já deve ter:

1. Uma VM Linux em execução no Azure.
2. Uma conexão para essa VM Linux usando SSH.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [AZURE.NOTE] Se você executará esta tarefa de um computador com Windows, você pode usar acabamento SSH em sua máquina Linux. Para obter mais informações, consulte [como faça logon em uma máquina Virtual executando Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Aprovado Azure Linux distros tiver colocar o pacote do agente do Azure Linux em seus repositórios, portanto, verifique e instale a versão mais recente desse repositório de distribuição primeiro se possível.  

Para Ubuntu, digite:

    #sudo apt-get install walinuxagent

E na CentOS, digite:

    #sudo yum install waagent


Para Oracle Linux, certifique-se de que o `Addons` repositório está ativado. Escolha Editar o arquivo `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) e altere a linha `enabled=0` para `enabled=1` em **[ol6_addons]** ou **[ol7_addons]** neste arquivo.

Em seguida, para instalar a versão mais recente do Azure Linux Agent, digite:


    #sudo yum install WALinuxAgent

Se você não encontrar o repositório do complemento você pode simplesmente adicionar essas linhas no final do seu arquivo de .repo de acordo com a sua versão do Oracle Linux:

Para as máquinas virtuais Oracle Linux 6:

    [ol6_addons]
    name=Add-Ons for Oracle Linux $releasever ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
    gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
    gpgcheck=1
    enabled=1

Para as máquinas virtuais Oracle Linux 7:

    [ol7_addons]
    name=Oracle Linux $releasever Add ons ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
    gpgcheck=1
    enabled=0

Em seguida, digite:

    #sudo yum update WALinuxAgent

Normalmente, isso é tudo que você precisa, mas se por algum motivo você precisar instalá-lo https://github.com diretamente, use as etapas a seguir.


## <a name="install-wget"></a>Instalar o wget

Faça login para sua máquina virtual usando SSH.

Instalar o wget (há alguns distros que não instalá-lo por padrão como Redhat, CentOS e Oracle Linux versões 6.4 e 6.5) digitando `#sudo yum install wget` na linha de comando.


## <a name="download-the-latest-version"></a>Baixar a versão mais recente

Abra [a versão do agente de Linux Azure no GitHub](https://github.com/Azure/WALinuxAgent/releases) em uma página da web e descobrir o número da versão mais recente. (Você pode localizar sua versão atual digitando `#waagent --version`.)

### <a name="for-version-20x-type"></a>Para a versão 2.0. x, digite:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   A seguinte linha usa versão 2.0.14 como um exemplo:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

### <a name="for-version-21x-or-later-type"></a>Para versão 2.1. x ou posterior, digite:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   A seguinte linha usa versão 2.1.0 como um exemplo:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

## <a name="install-the-azure-linux-agent"></a>Instalar o agente de Linux Azure

### <a name="for-version-20x-use"></a>Para a versão 2.0. x, use:

 Verifique waagent executável:

    #chmod +x waagent

 Copie o executável novo para /usr//usr/sbin /.

  Para a maioria do Linux, use:

    #sudo cp waagent /usr/sbin

  Para CoreOS, use:

    #sudo cp waagent /usr/share/oem/bin/

  Se esta for uma nova instalação do Azure Linux Agent, execute:
 
    #sudo /usr/sbin/waagent -install -verbose

### <a name="for-version-21x-use"></a>Para versão 2.1. x, use:

Talvez seja necessário instalar o pacote `setuptools` primeiro, veja [aqui](https://pypi.python.org/pypi/setuptools). Execute a:

    #sudo python setup.py install

## <a name="restart-the-waagent-service"></a>Reinicie o serviço de waagent

Para a maioria dos linux Distros:

    #sudo service waagent restart

Para Ubuntu, use:

    #sudo service walinuxagent restart

Para CoreOS, use:

    #sudo systemctl restart waagent

## <a name="confirm-the-azure-linux-agent-version"></a>Confirmar a versão do agente de Linux do Azure

    #waagent -version

Para CoreOS, o comando acima pode não funcionar.

Você verá que a versão do agente do Azure Linux foi atualizada para a nova versão.

Para obter mais informações sobre o agente de Linux do Azure, consulte [Azure Linux agente Leiame](https://github.com/Azure/WALinuxAgent).
