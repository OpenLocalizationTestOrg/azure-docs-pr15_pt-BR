<properties
    pageTitle="Baixe o SDK do Azure para PHP"
    description="Saiba como baixar e instalar o SDK do Azure para PHP."
    documentationCenter="php"
    services="app-service\web"
    authors="allclark"
    manager="douge"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="06/01/2016"
    ms.author="allclark;yaqiyang"/>

#<a name="download-the-azure-sdk-for-php"></a>Baixe o SDK do Azure para PHP

## <a name="overview"></a>Visão geral

O SDK do Azure para PHP inclui componentes que permitem que você desenvolver, implantar e gerenciar aplicativos de PHP para Azure. Especificamente, o SDK do Azure para PHP inclui o seguinte:

* **Bibliotecas de cliente PHP o Azure**. Essas bibliotecas de classe fornecem uma interface para acessar recursos Azure, como os serviços de gerenciamento de dados e serviços em nuvem.  
* **A Interface de linha Azure para Mac, Linux e Windows (Azure CLI)**. Este é um conjunto de comandos para implantar e gerenciar serviços Azure, como sites do Azure e máquinas virtuais do Azure. O trabalho de CLI do Azure em qualquer plataforma, inclusive Mac, Linux e Windows.
* **PowerShell azure (somente Windows)**. Este é um conjunto de cmdlets do PowerShell para implantar e gerenciar serviços do Azure, como os serviços de nuvem e máquinas virtuais.
* **Os emuladores Azure (somente Windows)**. Os emuladores de computação e armazenamento são emuladores locais de serviços de nuvem e serviços de gerenciamento de dados que permitem testar um aplicativo localmente. Os emuladores Azure executados apenas no Windows.

As seções a seguir descrevem como baixar e instalar os componentes descritos acima.

As instruções neste tópico pressupõem que você tenha [PHP] [ install-php] instalado.

> [AZURE.NOTE] Você deve ter PHP 5,5 ou superior para usar as bibliotecas de cliente do PHP para Azure.

##<a name="php-client-libraries-for-azure"></a>Bibliotecas de cliente PHP do Azure

Bibliotecas de cliente do PHP para Azure fornecem uma interface para acessar recursos Azure, como os serviços de gerenciamento de dados e serviços, de qualquer sistema operacional em nuvem. Essas bibliotecas podem ser instaladas via o compositor.

Para obter informações sobre como usar as bibliotecas de cliente do PHP para Azure, veja [como usar o serviço de Blob][blob-service], [como usar o serviço de tabela] [ table-service] e [como usar o serviço de fila][queue-service].

###<a name="install-via-composer"></a>Instalar via compositor

1. [Instalar gito][install-git].


    > [AZURE.NOTE] No Windows, você também precisa adicionar o gito executável a variável de ambiente PATH.

2. Crie um arquivo denominado **composer.json** na raiz do seu projeto e adicione o seguinte código para ele:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Baixar ** [composer.phar] [ composer-phar] ** na raiz do seu projeto.

4. Abra um prompt de comando e execute isso na raiz do seu projeto

        php composer.phar install

##<a name="azure-powershell-and-azure-emulators"></a>PowerShell Azure e emuladores Azure

PowerShell Azure é um conjunto de cmdlets do PowerShell para implantar e gerenciar serviços do Azure (como os serviços de nuvem e máquinas virtuais). Os emuladores Azure são emuladores de serviços de nuvem e serviços de gerenciamento de dados que permitem testar um aplicativo localmente. Esses componentes são suportados apenas no Windows.

A maneira recomendada de instalar os emuladores do Azure e o Azure PowerShell é usar o [Microsoft Web Platform Installer][download-wpi]. Observe que você também pode optar por instalar outros componentes de desenvolvimento, como PHP, SQL Server, os Drivers Microsoft SQL Server para PHP e WebMatrix.

Para obter informações sobre como usar o PowerShell do Azure, veja [como usar o PowerShell do Azure][powershell-tools].

##<a name="azure-cli"></a>CLI Azure

O Azure é um conjunto de comandos para implantar e gerenciar serviços Azure, como sites do Azure e máquinas virtuais do Azure. Para obter informações sobre como instalar o Azure CLI, consulte [instalar o CLI do Azure](xplat-cli-install.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte o [PHP Developer Center](/develop/php/).


[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
