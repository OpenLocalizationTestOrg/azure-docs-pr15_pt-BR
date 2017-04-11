<properties
 pageTitle="Recursos e extensões de máquina virtual | Microsoft Azure"
 description="Saiba quais extensões estão disponíveis para Azure máquinas virtuais, agrupadas por quais eles fornecem ou melhorar."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="neilpeterson"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/22/2016"
 ms.author="nepeters"/>

# <a name="about-virtual-machine-extensions-and-features"></a>Sobre os recursos e extensões de máquina virtual

## <a name="azure-vm-extensions"></a>Extensões de máquina virtual Azure

Extensões de máquina Virtual Azure são pequenos aplicativos que fornecem tarefas de configuração e automação de implantação de postagem em máquinas virtuais do Azure. Por exemplo, se uma máquina Virtual requer software a ser instalada, proteção antivírus ou configuração de Docker, uma extensão de máquina virtual pode ser usada para concluir essas tarefas. Extensões de máquina virtual Azure podem ser executadas utilizando a CLI do Azure, PowerShell, recurso Gerenciar modelos e o portal do Azure. Extensões podem ser fornecidas com uma nova implantação de máquina virtual ou executar qualquer sistema existente.

Este documento fornece os pré-requisitos para extensão de máquina de Virtual do Azure e orientações sobre como detectar extensões de máquina virtual disponíveis. 

## <a name="azure-vm-agent"></a>Agente de máquina virtual Azure

O agente de máquina virtual do Azure gerencia interação entre uma máquina de Virtual do Azure e o controlador de tecidos do Azure. O agente de máquina virtual é responsável por muitos aspectos funcionais de implantação e gerenciamento de máquinas virtuais do Azure, incluindo executando as extensões de máquina virtual. O agente de máquina virtual do Azure vem pré-instalado em imagens de galeria do Azure e pode ser instalado em sistemas operacionais compatíveis. 

Para obter informações sobre sistemas operacionais compatíveis e instruções de instalação, consulte [Guia de usuário de agente do Azure Linux](./virtual-machines-linux-agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Descubra as extensões de máquina virtual

Muitas extensões de máquina virtual diferentes estão disponíveis para uso com máquinas virtuais do Azure. Para ver uma lista completa, execute o seguinte comando da CLI do Azure, substituindo o local com a localização da opção.

```none
azure vm extension-image list westus
```

<br />

## <a name="common-vm-extensions"></a>Extensões de máquina virtual comuns

|Nome de extensão   |Descrição   |Mais informações   |
|---|---|---|
|Extensão de Script personalizado para Linux  | Executar scripts em relação a uma máquina Virtual do Azure  |[Extensão de Script personalizado para Linux](./virtual-machines-linux-extensions-customscript.md)   |
|Extensão de docker |Instala o daemon Docker para oferecer suporte a comandos Docker remotos.  | [Extensão de máquina virtual docker](./virtual-machines-linux-dockerextension.md)  |
|Extensão de acesso de máquina virtual | Recuperar o acesso para máquina Virtual do Azure  |[Extensão de acesso de máquina virtual](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
|Extensão do diagnóstico do Azure |Gerenciar o diagnóstico do Azure |[Extensão do diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |

