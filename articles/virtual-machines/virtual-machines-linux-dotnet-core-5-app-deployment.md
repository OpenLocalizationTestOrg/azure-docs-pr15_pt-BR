<properties
   pageTitle="Automatizar a implantação do aplicativo com as extensões de máquina Virtual | Microsoft Azure"
   description="Tutorial do Azure Máquina Virtual DotNet Core"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# <a name="application-deployment-with-azure-resource-manager-templates"></a>Implantação de aplicativo com modelos do Gerenciador de recursos do Azure

Depois que todos os requisitos de base Azure foram identificados e traduzidos em um modelo de implantação, a implantação do aplicativo real precisa ser resolvido. Veja a implantação do aplicativo está fazendo referência a instalar os binários do aplicativo reais para recursos Azure. Para o exemplo de armazenamento de música, .net principal, NGINX e Supervisor precisam ser instalado e configurado em cada máquina virtual. Os binários do repositório de música precisam estar instalado na máquina virtual e o banco de dados do repositório de música pré-criada.

Este documento detalha como extensões de máquina Virtual podem automatizar configuração Azure máquinas virtuais e implantação do aplicativo. Todas as dependências e configurações exclusivas são realçadas. Para obter a melhor experiência, previamente implante uma instância da solução à sua assinatura do Azure e trabalhar junto com o modelo do Gerenciador de recursos do Azure. O modelo completo pode ser encontrado aqui – [Implantação de repositório de música em Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="configuration-script"></a>Script de configuração

Extensões de máquina virtual são programas especializados que são executadas em máquinas virtuais para fornecer automação de configuração. Extensões estão disponíveis para muitos fins específicos como antivírus, configuração de registro em log e configuração de Docker. Uma extensão de script personalizado pode ser usada para executar qualquer script em uma máquina virtual. Com a amostra de repositório de música, é até a extensão de script personalizado para configurar máquinas virtuais Ubuntu e instalar o aplicativo do repositório de música.

Antes de Detalhar como extensões de máquina virtual são declaradas em um modelo do Gerenciador de recursos do Azure, examine o script que é executado. Esse script configura máquina virtual Ubuntu para hospedar o aplicativo do repositório de música. Quando executado, o script instala todos necessário software, instalar o aplicativo de armazenamento de música do controle de origem e preparar o banco de dados. 

Para saber mais sobre um .net de hospedagem Core aplicativo no Linux, consulte [Publicar para um ambiente de produção Linux](https://docs.asp.net/en/latest/publishing/linuxproduction.html). 

> Este exemplo é para fins de demonstração.

```none
#!/bin/bash

# install dotnet core
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
sudo apt-get update
sudo apt-get install -y dotnet-dev-1.0.0-preview2-003121

# download application
sudo wget https://raw.github.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/music-store-azure-demo-pub.tar /
sudo mkdir /opt/music
sudo tar -xf music-store-azure-demo-pub.tar -C /opt/music

# install nginx, update config file
sudo apt-get install -y nginx
sudo service nginx start
sudo touch /etc/nginx/sites-available/default
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/nginx-config/default -O /etc/nginx/sites-available/default
sudo cp /opt/music/nginx-config/default /etc/nginx/sites-available/
sudo nginx -s reload

# update and secure music config file
sed -i "s/<replaceserver>/$1/g" /opt/music/config.json
sed -i "s/<replaceuser>/$2/g" /opt/music/config.json
sed -i "s/<replacepass>/$3/g" /opt/music/config.json
sudo chown $2 /opt/music/config.json
sudo chmod 0400 /opt/music/config.json

# config supervisor
sudo apt-get install -y supervisor
sudo touch /etc/supervisor/conf.d/music.conf
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/supervisor/music.conf -O /etc/supervisor/conf.d/music.conf
sudo service supervisor stop
sudo service supervisor start

# pre-create music store database
/usr/bin/dotnet /opt/music/MusicStore.dll &
```

## <a name="vm-script-extension"></a>Extensão de Script de máquina virtual

Extensões de máquina virtual pode ser executadas em uma máquina virtual em tempo de compilação, incluindo o recurso de extensão no modelo do Gerenciador de recursos do Azure. A extensão pode ser adicionada com o assistente Visual Studio adicionar recursos ou inserindo JSON válido para o modelo. O recurso de extensão do Script estiver aninhado dentro do recurso de máquina Virtual; Isso pode ser visto no exemplo a seguir.

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [Extensão de Script de máquina virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L359). 

Observe o abaixo JSON que o script está armazenado no GitHub. Esse script também pode ser armazenado no armazenamento de Blob do Azure. Além disso, os modelos de Gerenciador de recursos do Azure permitem a cadeia de caracteres de execução de scripts para construído de forma que os valores de parâmetros de modelo podem ser usados como parâmetros para execução de scripts. Nesse caso os dados são fornecidos ao implantar os modelos e esses valores podem ser usados ao executar o script.

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Para obter mais informações sobre como usar a extensão de script personalizado, consulte [extensões de script personalizado com modelos do Gerenciador de recursos](./virtual-machines-linux-extensions-customscript.md).

## <a name="next-step"></a>Próxima etapa

<hr>

[Explorar mais Azure modelos do Gerenciador de recursos](https://github.com/Azure/azure-quickstart-templates)
