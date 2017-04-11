<properties
    pageTitle="Usar a extensão de CustomScript em uma máquina virtual Linux | Microsoft Azure"
    description="Saiba como usar a extensão de CustomScript para implantar aplicativos em máquinas virtuais Linux no Azure criado usando o modelo de implantação clássico."
    editor="tysonn"
    manager="timlt"
    documentationCenter=""
    services="virtual-machines-linux"
    authors="gbowerman"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="multiple"
    ms.tgt_pltfrm="linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="guybo"/>

#<a name="deploy-a-lamp-app-using-the-azure-customscript-extension-for-linux"></a>Implantar um aplicativo de LÂMPADA usando a extensão de CustomScript do Azure para Linux#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


A extensão do Microsoft Azure CustomScript para Linux fornece uma maneira para personalizar suas máquinas virtuais (VMs) executando aleatório código escrito em qualquer linguagem de script compatível com a máquina virtual (por exemplo, Python e Bash). Isso fornece uma maneira muito flexível para automatizar a implantação do aplicativo para vários computadores.

Você pode implantar a extensão de CustomScript usando o portal de clássico Azure, o Windows PowerShell ou a Interface de linha do Azure (Azure comando).

Neste artigo, que vamos usar a CLI do Azure para implantar um aplicativo de LÂMPADA simples para uma VM Ubuntu criado usando o modelo clássico de implantação.

## <a name="prerequisites"></a>Pré-requisitos

Neste exemplo, primeiro crie duas VMs Azure executando Ubuntu 14.04 ou posterior. VMs são chamadas *script-máquina virtual* e *lâmpada-máquina virtual*. Use nomes exclusivos ao criar VMs. Uma é usada para executar os comandos e uma é usada para implantar o aplicativo de LÂMPADA.

Você também precisa de uma conta de armazenamento do Azure e uma chave para acessá-lo (você pode obter isso a partir do portal clássico Azure).

Se precisar de ajuda para criar VMs Linux no Azure consulte [criar uma máquina Virtual executando Linux](virtual-machines-linux-classic-createportal.md).

Os comandos de instalação presumem Ubuntu, mas você pode adaptá a instalação para qualquer distribuição Linux com suporte.

A máquina virtual script-máquina virtual precisa ter CLI Azure instalado, com uma conexão de trabalho para o Azure. Para obter ajuda sobre isso consulte [instalar e configurar a Interface de linha do Azure](../xplat-cli-install.md).

## <a name="upload-a-script"></a>Carregar um script

Usaremos a extensão CustomScript executem um script em uma máquina virtual remota para instalar a pilha de LÂMPADA e criar uma página PHP. Para acessar o script em qualquer lugar podemos carregará-lo como um blob do Microsoft Azure.

### <a name="script-overview"></a>Visão geral de script

O exemplo de script instala uma pilha de LÂMPADA Ubuntu (incluindo a configuração uma instalação silenciosa do MySQL), grava um arquivo PHP simples e inicia Apache.

    #!/bin/bash
    # set up a silent install of MySQL
    dbpass="mySQLPassw0rd"

    export DEBIAN_FRONTEND=noninteractive
    echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
    echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

    # install the LAMP stack
    apt-get -y install apache2 mysql-server php5 php5-mysql  

    # write some PHP
    echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
    echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

    # restart Apache
    apachectl restart

### <a name="upload-script"></a>Carregar o script

Salve o script como um arquivo de texto, por exemplo *install_lamp.sh*e depois carregue-o ao armazenamento do Azure. Você pode fazer isso facilmente com CLI do Azure. O exemplo a seguir carrega o arquivo em um contêiner de armazenamento denominado "scripts". Se o contêiner não existir, você precisará criá-lo primeiro.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Também crie um arquivo JSON que descreve como baixar o script do armazenamento do Azure. Salvá-lo como *public_config.json* (substituindo "mystorage" com o nome da sua conta de armazenamento):

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## <a name="deploy-the-extension"></a>Implantar a extensão

Agora você pode usar o próximo comando para implantar a extensão de CustomScript Linux para a remota máquina virtual usando a CLI do Azure.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScript Microsoft.Azure.Extensions 2.0

O comando anterior downloads e executa o script *install_lamp.sh* na máquina virtual chamada *lâmpada-máquina virtual*.

Como o aplicativo inclui um servidor web, lembre-se abrir uma porta ouvinte HTTP na VM remoto com o próximo comando.

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## <a name="monitoring-and-troubleshooting"></a>Monitoramento e solução de problemas

Você pode verificar quanto o script personalizado é executado examinando o arquivo de log na VM remoto. SSH *lâmpada-máquina virtual* e o arquivo de log com o próximo comando de extremidade.

    cd /var/log/azure/customscript
    tail -f handler.log

Depois de executar a extensão de CustomScript, você pode navegar para a página PHP que você criou para obter informações. A página PHP para que o exemplo neste artigo é *http://lamp-vm.cloudapp.net/phpinfo.php*.

## <a name="additional-resources"></a>Recursos adicionais

Você pode usar as mesmas etapas básicas para implantar aplicativos mais complexos. Neste exemplo, o script de instalação foi salva como um blob pública no armazenamento do Azure. Uma opção mais segura seria armazenar o script de instalação como um blob seguro com uma [Assinatura de acesso seguro](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS).

Recursos adicionais para CLI do Azure, Linux e a extensão de CustomScript são listados a seguir.

[Automatizar tarefas de personalização de máquina virtual Linux usando a extensão de CustomScript](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Extensões de Linux Azure (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Linux e código-fonte aberto computação no Azure](virtual-machines-linux-opensource-links.md)
