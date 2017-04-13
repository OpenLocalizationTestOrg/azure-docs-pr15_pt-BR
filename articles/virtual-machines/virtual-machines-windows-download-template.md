<properties
    pageTitle="Criar uma imagem de máquina virtual de uma máquina virtual do Azure | Microsoft Azure"
    description="Aprenda a criar uma imagem de máquina virtual generalizada de uma VM Azure existentes criados no modelo de implantação do Gerenciador de recursos"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>


# <a name="download-the-template-for-a-vm"></a>Baixar o modelo para uma máquina virtual

Quando você cria uma máquina virtual no Azure usando o portal ou PowerShell, um modelo do Gerenciador de recursos é criado automaticamente para você. Você pode usar este modelo para duplicar rapidamente uma implantação. O modelo contém informações sobre todos os recursos em um grupo de recursos. Para uma máquina virtual, isso significa que os contêineres de modelo tudo o que é criado para dar suporte a máquina virtual desse grupo de recursos, incluindo os recursos de rede.

## <a name="download-the-template-using-the-portal"></a>Baixar o modelo usando o portal

1. Faça logon no [portal do Azure](https://portal.azure.com/).
2. Um menu hub, selecione **máquinas virtuais**.
3. Selecione a máquina virtual na lista.
5. Selecione **script de automação**.
6. Selecione **Download** e salve o arquivo. zip em seu computador local.
7. Abra o arquivo. zip e extrair os arquivos para uma pasta. O arquivo. zip irá conter:
    
    - Deploy.ps1
    - Deploy.sh 
    - deployer.RB
    - DeploymentHelper.cs
    - Parameters.JSON
    - Template.JSON

O arquivo .json é o modelo.
    
## <a name="download-the-template-using-powershell"></a>Baixar o modelo usando o PowerShell

Você também pode baixar o arquivo de modelo de .json usando o cmdlet [AzureRMResourceGroup de exportação](https://msdn.microsoft.com/library/mt715427.aspx) . Você pode usar o `-path` parâmetro para fornecer o nome do arquivo e o caminho para o arquivo de .json. Este exemplo mostra como baixar o modelo para o grupo de recursos denominado **myResourceGroup** para a pasta **C:\users\public\downloads** no seu computador local.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como implantar recursos usando modelos, consulte [explicação passo a passo de modelo do Gerenciador de recursos](../resource-manager-template-walkthrough.md).