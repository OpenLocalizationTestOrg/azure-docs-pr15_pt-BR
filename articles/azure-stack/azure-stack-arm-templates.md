<properties
    pageTitle="Usar o Gerenciador de recursos do Azure modelos no Azure pilha (desenvolvedores Locatário) | Microsoft Azure"
    description="Saiba como usar modelos do Gerenciador de recursos do Azure na pilha do Azure para implantar e provisionar todos os recursos do aplicativo em uma operação única e coordenado."
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
    ms.date="10/25/2016"
    ms.author="helaw"/>

# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Use modelos do Gerenciador de recursos do Azure na pilha do Azure

Os modelos de Gerenciador de recursos Azure implantar e provisionar todos os recursos do aplicativo em uma operação única e coordenado. Definir os recursos para o aplicativo e como ele será implantado.  Você também pode reimplantar modelos para fazer alterações aos recursos no grupo de recursos.

Esses modelos podem ser implantados com o portal do Microsoft Azure pilha, PowerShell, a linha de comando e Visual Studio.

[AZURE.VIDEO microsoft-azure-stack-tp1--foundational-skills-1-deploying-json-templates]

Os seguintes modelos estão disponíveis no [GitHub](http://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-non-high-availability"></a>Implantar o SharePoint (sem alta disponibilidade)

Use a extensão do PowerShell DSC para criar um farm do SharePoint 2013 que inclui o seguinte:

-   Uma rede virtual

-   Três contas de armazenamento

-   Dois balanceadores de carga externos

-   Uma máquina virtual configurado como um controlador de domínio para uma nova floresta com um único domínio

-   Uma máquina virtual configurado como um servidor autônomo de 2014 do SQL Server

-   Uma máquina virtual configurado como um farm do SharePoint 2013 de um computador

## <a name="deploy-ad-non-high-availability"></a>Implantar AD (sem alta disponibilidade)

Use a extensão do PowerShell DSC para criar um servidor do controlador de domínio AD que inclui o seguinte:

-   Uma rede virtual

-   Uma conta de armazenamento

-   Um balanceador externo

-   Uma máquina virtual configurado como um controlador de domínio para uma nova floresta com um único domínio

## <a name="deploy-adsql-non-high-availability"></a>Implantar AD/SQL (sem alta disponibilidade)

Use a extensão do PowerShell DSC para criar um servidor autônomo de 2014 do SQL Server que inclui o seguinte:

-   Uma rede virtual

-   Duas contas de armazenamento

-   Um balanceador externo

-   Uma máquina virtual configurado como um controlador de domínio para uma nova floresta com um único domínio

-   Uma máquina virtual configurado como um servidor autônomo de 2014 do SQL Server

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Use a extensão de DSC do PowerShell para configurar uma máquina virtual existente Gerenciador de configuração de Local (MMC) e registre-o para um servidor de recepção de DSC conta de automação do Azure.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Criar uma máquina virtual a partir de uma imagem de usuário

Crie uma máquina virtual de uma imagem do usuário personalizada. Este modelo também implanta uma rede virtual (com o DNS), endereço IP público e uma interface de rede.

## <a name="simple-vm"></a>Máquina virtual Simple

Implante uma máquina virtual Windows simples que inclui uma rede virtual (com o DNS), o endereço IP público e uma interface de rede.

## <a name="cancel-a-running-template-deployment"></a>Cancelar uma implantação de modelo em execução

Para cancelar uma implantação de modelo em execução, use o `Stop-AzureRmResourceGroupDeployment` cmdlet do PowerShell.


## <a name="next-steps"></a>Próximas etapas

[Implantar modelos com o portal](azure-stack-deploy-template-portal.md)

[Visão geral do Gerenciador de recursos Azure](../azure-resource-manager/resource-group-overview.md)

