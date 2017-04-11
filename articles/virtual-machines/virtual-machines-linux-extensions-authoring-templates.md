<properties
   pageTitle="Criação de modelos com as extensões de Linux VM | Microsoft Azure"
   description="Saiba mais sobre a criação de modelos do Gerenciador de recursos do Azure com extensões para Linux VMs"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="authoring-azure-resource-manager-templates-with-linux-vm-extensions"></a>Criação de modelos do Gerenciador de recursos do Azure com as extensões de Linux VM

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

A partir do Azure CLI, execute o seguinte comando:

      Azure VM extension list

Esse comando retorna o nome do fornecedor, o nome de extensão e a versão seguinte:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Essas três propriedades mapeiam "publisher", "tipo" e "typeHandlerVersion" respectivamente no trecho de modelo acima.

>[AZURE.NOTE]Sempre recomenda-se para usar a versão mais recente de extensão para obter a funcionalidade mais atualizada.

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>Identificar o esquema para os parâmetros de configuração de extensão

A próxima etapa com a criação de um modelo de extensão é identificar o formato para fornecer parâmetros de configuração. Cada extensão é compatível com seu próprio conjunto de parâmetros.

Para examinar as configurações de amostra para Linux extensões, clique em documentação consulte [exemplos de eExtensions Linux](virtual-machines-linux-extensions-configuration-samples.md).

Consulte o seguinte para obter um modelo totalmente concluído com as extensões de máquina virtual.

[Extensão de script personalizado em uma VM Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Após a criação do modelo, você pode implantá-la usando a CLI do Azure.
