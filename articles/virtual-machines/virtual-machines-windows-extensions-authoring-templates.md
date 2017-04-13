<properties
   pageTitle="Criação de modelos com as extensões de máquina virtual do Windows | Microsoft Azure"
   description="Saiba mais sobre a criação de modelos do Gerenciador de recursos do Azure com extensões para VMs do Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="authoring-azure-resource-manager-templates-with-windows-vm-extensions"></a>Criação de modelos do Gerenciador de recursos do Azure com as extensões de máquina virtual do Windows

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

A partir do Azure PowerShell, execute o seguinte cmdlet do PowerShell do Azure:

      Get-AzureVMAvailableExtension


Esse cmdlet retorna o nome do fornecedor, o nome de extensão e a versão da seguinte maneira:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Essas três propriedades mapeiam "publisher", "tipo" e "typeHandlerVersion" respectivamente no trecho de modelo acima.

>[AZURE.NOTE]Sempre recomenda-se para usar a versão mais recente de extensão para obter a funcionalidade mais atualizada.

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>Identificar o esquema para os parâmetros de configuração de extensão

A próxima etapa com a criação de um modelo de extensão é identificar o formato para fornecer parâmetros de configuração. Cada extensão é compatível com seu próprio conjunto de parâmetros.

Para examinar as configurações de amostra para extensões do Windows, consulte [exemplos de extensões do Windows](virtual-machines-windows-extensions-configuration-samples.md).


Consulte o seguinte para obter um modelo totalmente concluído com as extensões de máquina virtual.

[Extensão de Script personalizado em um máquina virtual do Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)


Após a criação do modelo, você pode implantá-lo usando o PowerShell do Azure.
