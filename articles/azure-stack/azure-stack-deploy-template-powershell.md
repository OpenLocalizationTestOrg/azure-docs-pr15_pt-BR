<properties
    pageTitle="Implantar modelos com o PowerShell na pilha do Azure | Microsoft Azure"
    description="Saiba como implantar uma máquina virtual usando um modelo do Gerenciador de recursos e o PowerShell."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-powershell"></a>Implantar modelos na pilha de Azure usando o PowerShell

Use o PowerShell para implantar modelos de Gerenciador de recursos do Azure para o Azure pilha VDC.  Modelos do Gerenciador de recursos implantar e provisionar todos os recursos do aplicativo em uma operação única e coordenado.

## <a name="run-azurerm-powershell-cmdlets"></a>Execute cmdlets do AzureRM PowerShell

Neste exemplo, você executa um script para implantar uma máquina virtual do Azure pilha VDC usando um modelo do Gerenciador de recursos.  Antes de prosseguir, certifique-se de que ter [instalado e configurado o PowerShell](azure-stack-connect-powershell.md)  

O VHD usado neste modelo de exemplo é uma imagem padrão do marketplace (WindowsServer 2012-R2-Data Center).

1.  Vá para <http://aka.ms/AzureStackGitHub>, procure o modelo de **101-simples-windows-máquina virtual** e salvá-lo no seguinte local: c:\\modelos\\azuredeploy-101-simples-windows-vm.json.

2.  No PowerShell, execute o seguinte script de implantação. Substitua o *nome de usuário* e *senha* com seu nome de usuário e senha. Em usos subsequentes, incremente o valor para o parâmetro *$myNum* para evitar substituir sua implantação.

    ```PowerShell
        # Set Deployment Variables
        $myNum = "001" #Modify this per deployment
        $RGName = "myRG$myNum"
        $myLocation = "local"

        # Create Resource Group for Template Deployment
        New-AzureRmResourceGroup -Name $RGName -Location $myLocation

        # Deploy Simple IaaS Template
        New-AzureRmResourceGroupDeployment `
            -Name myDeployment$myNum `
            -ResourceGroupName $RGName `
            -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
            -NewStorageAccountName mystorage$myNum `
            -DnsNameForPublicIP mydns$myNum `
            -AdminUsername <username> `
            -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
            -VmName myVM$myNum `
            -WindowsOSVersion 2012-R2-Datacenter
    ```

3.  Abra o portal do Azure pilha, clique em **Procurar**, clique em **máquinas virtuais**e procure por sua nova máquina virtual (*myDeployment001*).

## <a name="video-example-hybrid-virtual-machine-deployment"></a>Exemplo de vídeo: implantação híbrida do máquina virtual

[AZURE.VIDEO microsoft-azure-stack-tp1-poc-hybrid-vm-deployment]

## <a name="next-steps"></a>Próximas etapas

[Implantar modelos com o Visual Studio](azure-stack-deploy-template-visual-studio.md)
