<properties
    pageTitle="Implantar LÂMPADA em um computador virtual Linux | Microsoft Azure"
    description="Saiba como instalar a pilha de LÂMPADA em uma VM Linux"
    services="virtual-machines-linux"
    documentationCenter="virtual-machines"
    authors="jluk"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="NA"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="juluk"/>

# <a name="deploy-lamp-stack-on-azure"></a>Implantar pilha de LÂMPADA no Azure
Este artigo apresentará como implantar um servidor web Apache, MySQL e PHP (a pilha de LÂMPADA) no Azure. Você precisará de uma conta do Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)) e a [CLI do Azure](../xplat-cli-install.md) que está [conectado à sua conta do Azure](../xplat-cli-connect.md).

Há dois métodos para instalar LÂMPADA descrita neste artigo:

## <a name="quick-command-summary"></a>Resumo de comando rápido

1) Implantar LÂMPADA na máquina virtual novo

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

2) Implantar LÂMPADA na máquina virtual existente

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Implantar LÂMPADA no novo passo a passo de máquina virtual

Você pode começar criando um novo [grupo de recursos](../azure-resource-manager/resource-group-overview.md) que conterá a máquina virtual:

    $ azure group create uniqueResourceGroup westus
    info:    Executing command group create
    info:    Getting resource group uniqueResourceGroup
    info:    Creating resource group uniqueResourceGroup
    info:    Created resource group uniqueResourceGroup
    data:    Id:                  /subscriptions/########-####-####-####-############/resourceGroups/uniqueResourceGroup
    data:    Name:                uniqueResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Para criar a máquina virtual em si, você pode usar um modelo do Gerenciador de recursos do Azure já escrito encontrado [aqui no GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

    $ azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json uniqueResourceGroup uniqueLampName

Você deve ver uma resposta solicitando algumas entradas mais:

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    storageAccountNamePrefix: lampprefix
    location: westus
    adminUsername: someUsername
    adminPassword: somePassword
    mySqlPassword: somePassword
    dnsLabelPrefix: azlamptest
    info:    Initializing template configurations and parameters
    info:    Creating a deployment
    info:    Created template deployment "uniqueLampName"
    info:    Waiting for deployment to complete
    data:    DeploymentName     : uniqueLampName
    data:    ResourceGroupName  : uniqueResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          :
    data:    Mode               : Incremental
    data:    CorrelationId      : d51bbf3c-88f1-4cf3-a8b3-942c6925f381
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    DeploymentParameters :
    data:    Name                      Type          Value
    data:    ------------------------  ------------  -----------
    data:    storageAccountNamePrefix  String        lampprefix
    data:    location                  String        westus
    data:    adminUsername             String        someUsername
    data:    adminPassword             SecureString  undefined
    data:    mySqlPassword             SecureString  undefined
    data:    dnsLabelPrefix            String        azlamptest
    data:    ubuntuOSVersion           String        14.04.2-LTS
    info:    group deployment create command OK

Agora que você criou uma VM Linux com LÂMPADA já instalada. Se desejar, você pode verificar a instalação saltar para baixo até [verificar LÂMPADA instalada com êxito].

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Implantar LÂMPADA em passo a passo de máquina virtual existente

Se precisar de ajuda para criar uma VM Linux pode cabeçalho [aqui para saber como criar uma VM Linux] (. / virtual-machines-linux-quick-create-cli.md). Em seguida, você precisará SSH em VM Linux. Se precisar de ajuda com a criação de uma chave SSH você pode cabeçalho [aqui para saber como criar uma chave SSH no Linux/Mac] (. / virtual-machines-linux-mac-create-ssh-keys.md).
Se você já tiver uma chave SSH, vá em frente e SSH em sua VM Linux com `ssh username@uniqueDNS`.

Agora que você estiver trabalhando em sua VM Linux, vamos percorrer instalando a pilha de LÂMPADA em distribuições baseadas em Debian. Os comandos exatos podem diferir para outros distros Linux.

#### <a name="installing-on-debianubuntu"></a>Instalando em Debian/Ubuntu

Você precisará seguintes pacotes instalados: `apache2`, `mysql-server`, `php5`, e `php5-mysql`. Você pode instalar esses segurando esses pacotes diretamente ou usando Tasksel. Instruções para ambas as opções estão listadas abaixo.
Antes de instalar, você precisará baixar e atualizar listas de pacote.

    user@ubuntu$ sudo apt-get update
    
##### <a name="individual-packages"></a>Pacotes individuais
Usando chance-get:

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>Usando Tasksel
Como alternativa, você pode baixar Tasksel, uma ferramenta de Debian/Ubuntu que instala vários pacotes relacionados como uma "tarefa" coordenada em seu sistema.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

Após executar uma das opções acima você será solicitado a instalar esses pacotes e um número de outras dependências. Pressione 'y' e 'Enter' para continuar e siga qualquer outros avisos para definir uma senha de administrador para MySQL. Será instalado com as extensões PHP necessárias mínimas necessárias usar PHP com o MySQL. 

![][1]

Execute o seguinte comando para ver outras extensões PHP que estão disponíveis como pacotes:

    user@ubuntu$ apt-cache search php5


#### <a name="create-infophp-document"></a>Criar info.php documento

Agora você deve ser capaz de verificar qual versão do Apache, MySQL e PHP por meio da linha de comando digitando `apache2 -v`, `mysql -v`, ou `php -v`.

Se você quiser testar ainda mais, você pode criar uma página de informações rápida PHP para exibição em um navegador. Crie um novo arquivo com Nano editor de texto com este comando:

    user@ubuntu$ sudo nano /var/www/html/info.php

Dentro do editor de texto GNU Nano, adicione as seguintes linhas:

    <?php
    phpinfo();
    ?>

Em seguida, salve e feche o editor de texto.

Reinicie o Apache com este comando para que todas as instalações do novo entrarão em vigor.

    user@ubuntu$ sudo service apache2 restart

## <a name="verify-lamp-successfully-installed"></a>Verifique se a LÂMPADA instalada com êxito

Agora você pode verificar a página de informações PHP recém-criado no seu navegador indo para http://youruniqueDNS/info.php, ele deve ser semelhante a esta.

![][2]

Você pode verificar sua instalação do Apache exibindo a página de padrão do Apache2 Ubuntu indo até você http://youruniqueDNS/. Você verá algo parecido com isto.

![][3]

Parabéns, você tem uma pilha de LÂMPADA configuração apenas em sua máquina virtual do Azure!

## <a name="next-steps"></a>Próximas etapas

Confira a documentação de Ubuntu na pilha LÂMPADA:

- [https://help.Ubuntu.com/Community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png
