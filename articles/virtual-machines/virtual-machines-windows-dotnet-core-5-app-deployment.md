<properties
   pageTitle="Automatizar a implantação do aplicativo com as extensões de máquina Virtual | Microsoft Azure"
   description="Tutorial do Azure Máquina Virtual DotNet Core"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="application-deployment-with-azure-resource-manager-templates"></a>Implantação de aplicativo com modelos do Gerenciador de recursos do Azure

Depois que todos os requisitos de base Azure foram identificados e traduzidos em um modelo de implantação, a implantação do aplicativo real precisa ser resolvido. Veja a implantação do aplicativo está fazendo referência a instalar os binários do aplicativo reais para recursos Azure. Para o exemplo de armazenamento de música, .net Core e IIS precisa ser instalado e configurado em cada máquina virtual. Os binários do repositório de música precisam estar instalado na máquina virtual e o banco de dados do repositório de música pré-criada.

Este documento detalha como extensões de máquina Virtual podem automatizar configuração Azure máquinas virtuais e implantação do aplicativo. Todas as dependências e configurações exclusivas são realçadas. Para obter a melhor experiência, previamente implante uma instância da solução à sua assinatura do Azure e trabalhar junto com o modelo do Gerenciador de recursos do Azure. O modelo completo pode ser encontrado aqui – [Implantação de repositório de música no Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-Windows).

## <a name="configuration-script"></a>Script de configuração

Extensões de máquina virtual são programas especializados que são executadas em máquinas virtuais para fornecer automação de configuração. Extensões estão disponíveis para muitos fins específicos como antivírus, configuração de registro em log e configuração de Docker. A extensão de Script personalizado pode ser usada para executar qualquer script em uma máquina virtual. Com a amostra de repositório de música, é até a extensão de script personalizado para configurar as máquinas virtuais do Windows e instale o aplicativo de armazenamento de música.

Antes de Detalhar como extensões de máquina virtual são declaradas em um modelo do Gerenciador de recursos do Azure, examine o script que é executado. Esse script configura o computador de virtual do Windows para hospedar o aplicativo do repositório de música. Quando executado, o script instala todos necessário software, instalar o aplicativo de armazenamento de música do controle de origem e preparar o banco de dados. 

> Este exemplo é para fins de demonstração.

```none
Param (
    [string]$user,
    [string]$password,
    [string]$sqlserver
)

# firewall
netsh advfirewall firewall add rule name="http" dir=in action=allow protocol=TCP localport=80

# folders
New-Item -ItemType Directory c:\temp
New-Item -ItemType Directory c:\music

# install iis
Install-WindowsFeature web-server -IncludeManagementTools

# install dot.net core sdk
Invoke-WebRequest http://go.microsoft.com/fwlink/?LinkID=615460 -outfile c:\temp\vc_redistx64.exe
Start-Process c:\temp\vc_redistx64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkID=809122 -outfile c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe
Start-Process c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkId=817246 -outfile c:\temp\DotNetCore.WindowsHosting.exe
Start-Process c:\temp\DotNetCore.WindowsHosting.exe -ArgumentList '/quiet' -Wait

# download / config music app
Invoke-WebRequest  https://github.com/Microsoft/dotnet-core-sample-templates/raw/master/dotnet-core-music-windows/music-app/music-store-azure-demo-pub.zip -OutFile c:\temp\musicstore.zip
Expand-Archive C:\temp\musicstore.zip c:\music
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replaceserver>", $sqlserver } | Set-Content C:\music\config.json
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replaceuser>", $user } | Set-Content C:\music\config.json
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replacepass>", $password } | Set-Content C:\music\config.json

# workaround for db creation bug
Start-Process 'C:\Program Files\dotnet\dotnet.exe' -ArgumentList 'c:\music\MusicStore.dll'

# configure iis
Remove-WebSite -Name "Default Web Site"
Set-ItemProperty IIS:\AppPools\DefaultAppPool\ managedRuntimeVersion ""
New-Website -Name "MusicStore" -Port 80 -PhysicalPath C:\music\ -ApplicationPool DefaultAppPool
& iisreset
```

## <a name="vm-script-extension"></a>Extensão de Script de máquina virtual

Extensões de máquina virtual pode ser executadas em uma máquina virtual em tempo de compilação, incluindo o recurso de extensão no modelo do Gerenciador de recursos do Azure. A extensão pode ser adicionada com o assistente Visual Studio adicionar recursos ou inserindo JSON válido para o modelo. O recurso de extensão do Script estiver aninhado dentro do recurso de máquina Virtual; Isso pode ser visto no exemplo a seguir.

Siga este link para ver a amostra JSON dentro do modelo do Gerenciador de recursos – [Extensão de Script de máquina virtual](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L339). 

Observe o abaixo JSON que o script está armazenado no GitHub. Esse script também pode ser armazenado no armazenamento de Blob do Azure. Além disso, os modelos de Gerenciador de recursos do Azure permitem a cadeia de caracteres de execução de scripts ser construído de forma que os valores de parâmetros de modelo podem ser usados como parâmetros para execução de scripts. Nesse caso os dados são fornecidos ao implantar os modelos e esses valores podem ser usados ao executar o script.

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
  }
}
```

Para obter mais informações sobre como usar a extensão de script personalizado, consulte [extensões de script personalizado com modelos do Gerenciador de recursos](./virtual-machines-windows-extensions-customscript.md).

## <a name="next-step"></a>Próxima etapa

<hr>

[Explorar mais Azure modelos do Gerenciador de recursos](https://github.com/Azure/azure-quickstart-templates)
