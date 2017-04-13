<properties
    pageTitle="Configurar chave cofre para máquinas virtuais no Gerenciador de recursos do Azure | Microsoft Azure"
    description="Como configurar a chave do cofre para uso com uma máquina virtual de Gerenciador de recursos do Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="singhkay"/>

# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configurar chave cofre para máquinas virtuais no Gerenciador de recursos do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico

No Gerenciador de recursos do Azure pilha, segredos/certificados são modelados como recursos que são fornecidos pelo provedor de recurso de chave cofre. Para saber mais sobre Cofre de chave, consulte [o que é Azure chave cofre?](../key-vault/key-vault-whatis.md)

>[AZURE.NOTE] 
>
>1. Ordem de Cofre de chave a ser usado com máquinas virtuais de Gerenciador de recursos do Azure, a propriedade **EnabledForDeployment** Cofre de chave deve ser definida como true. Você pode fazer isso em vários clientes.
>
>2. O Cofre de chave precisa ser criado na mesma assinatura e local da máquina Virtual.

## <a name="use-powershell-to-set-up-key-vault"></a>Usar o PowerShell para configurar a chave do cofre
Para criar um chave cofre usando o PowerShell, consulte [Introdução ao Azure chave cofre](../key-vault/key-vault-get-started.md#vault).

Para novos compartimentos de chave, você pode usar esse cmdlet do PowerShell:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Para compartimentos de chave existentes, você pode usar esse cmdlet do PowerShell:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="us-cli-to-set-up-key-vault"></a>Conosco CLI para configurar a chave do cofre
Para criar um chave cofre usando a interface de linha de comando (CLI), consulte [Gerenciar cofre de chave usando CLI](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

Para CLI, você precisa criar o cofre chave antes de atribuir a política de implantação. Você pode fazer isso usando o seguinte comando:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Use modelos para configurar a chave do cofre
Enquanto você usa um modelo, você precisa definir o `enabledForDeployment` propriedade `true` para o recurso de chave cofre.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Para obter outras opções que você pode configurar quando você cria um cofre chave usando modelos, consulte [criar um cofre chave](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
