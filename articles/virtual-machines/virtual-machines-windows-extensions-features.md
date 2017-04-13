<properties
 pageTitle="Recursos e extensões de máquina virtual | Microsoft Azure"
 description="Saiba quais extensões estão disponíveis para Azure máquinas virtuais, agrupadas por quais eles fornecem ou melhorar."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="neilpeterson"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/30/2016"
 ms.author="nepeters"/>

# <a name="about-virtual-machine-extensions-and-features"></a>Sobre os recursos e extensões de máquina virtual

## <a name="azure-vm-extensions"></a>Extensões de máquina virtual Azure

Extensões de máquina Virtual Azure são pequenos aplicativos que fornecem tarefas de configuração e automação de implantação de postagem em máquinas virtuais do Azure. Por exemplo, se uma máquina Virtual requer software a ser instalada, proteção antivírus ou configuração de Docker, uma extensão de máquina virtual pode ser usada para concluir essas tarefas. Extensões de máquina virtual Azure podem ser executadas utilizando a CLI do Azure, PowerShell, recurso Gerenciar modelos e o portal do Azure. Extensões podem ser fornecidas com uma nova implantação de máquina virtual ou executar qualquer sistema existente.

Este documento fornece os pré-requisitos para extensão de máquina de Virtual do Azure e orientações sobre como detectar extensões de máquina virtual disponíveis. 

## <a name="azure-vm-agent"></a>Agente de máquina virtual Azure

O agente de máquina virtual do Azure gerencia interação entre uma máquina de Virtual do Azure e o controlador de tecidos do Azure. O agente de máquina virtual é responsável por muitos aspectos funcionais de implantação e gerenciamento de máquinas virtuais do Azure, incluindo executando as extensões de máquina virtual. O agente de máquina virtual do Azure vem pré-instalado em imagens de galeria do Azure e pode ser instalado em sistemas operacionais compatíveis. 

Para obter informações sobre sistemas operacionais compatíveis e instruções de instalação, consulte [Agente de máquina Virtual do Azure](./virtual-machines-windows-classic-agents-and-extensions.md).

## <a name="discover-vm-extensions"></a>Descubra as extensões de máquina virtual

Muitas extensões de máquina virtual diferentes estão disponíveis para uso com máquinas virtuais do Azure. Para ver uma lista completa, execute o seguinte comando da CLI do Azure, substituindo o local com a localização da opção.

```none
Get-AzureVMAvailableExtension | Select ExtensionName, Version
```

<br />

## <a name="common-vm-extensions"></a>Extensões de máquina virtual comuns

|Nome de extensão   |Descrição   |Mais informações   |
|---|---|---|
|Extensão de Script personalizado para Windows  | Executar scripts em relação a uma máquina Virtual do Azure  |[Extensão de Script personalizado para Windows](./virtual-machines-windows-extensions-customscript.md)   |
|Extensão de DSC para Windows | Extensão do PowerShell DSC (configuração de estado desejada).  | [Extensão de máquina virtual docker](./virtual-machines-windows-extensions-dsc-overview.md)  |
|Extensão do diagnóstico do Azure | Gerenciar o diagnóstico do Azure |[Extensão do diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
