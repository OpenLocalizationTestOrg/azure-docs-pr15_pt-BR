<properties
    pageTitle="Sobre imagens para máquinas virtuais do Windows | Microsoft Azure"
    description="Saiba mais sobre como as imagens são usadas com máquinas virtuais do Windows no Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/21/2016"
    ms.author="cynthn"/>

# <a name="about-images-for-windows-virtual-machines"></a>Sobre imagens para máquinas virtuais do Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]



## <a name="working-with-images"></a>Trabalhar com imagens

Você pode usar o módulo do PowerShell do Azure para gerenciar as imagens disponíveis para sua assinatura do Azure. Você também pode usar o portal de clássico do Azure para algumas tarefas de imagem, mas a linha de comando oferece mais opções.


-   **Obter todas as imagens**:`Get-AzureVMImage`retorna uma lista de todas as imagens disponíveis em sua assinatura atual: suas imagens, bem como aquelas fornecidas pelo Azure ou parceiros. Isso significa que você pode receber uma lista grande. Os exemplos mostram como obter uma lista mais curta.
-   **Obter famílias de imagem**:`Get-AzureVMImage | select ImageFamily` obtém uma lista de famílias de imagem mostrando cadeias de caracteres de propriedade de **ImageFamily** .
-   **Obter todas as imagens em uma família específica**:`Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-   **Encontrar imagens de máquina virtual**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` isso funciona filtrando a propriedade DataDiskConfiguration, que se aplica somente às imagens de máquina virtual. Este exemplo também filtra a saída para apenas o nome do rótulo e imagem.
-   **Salvar uma imagem generalizada**:`Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-   **Salvar uma imagem especializada**:`Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip] O parâmetro OSState é necessário se você quiser criar uma imagem de máquina virtual, que inclui discos de dados, bem como o disco de sistema operacional. Se você não usa o parâmetro, o cmdlet cria uma imagem de sistema operacional. O valor do parâmetro indica se a imagem é generalizada ou especializada, baseada no se o disco de sistema operacional foi preparado para reutilização.
-   **Excluir uma imagem**:`Remove-AzureVMImage –ImageName "MyOldVmImage"`


## <a name="next-steps"></a>Próximas etapas

Você também pode [criar um computador Windows usando o portal de clássico](virtual-machines-windows-classic-tutorial.md)

