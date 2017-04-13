<properties
   pageTitle="Navegue e selecione imagens de máquina virtual do Windows | Microsoft Azure"
   description="Saiba como determinar o publisher, oferta e SKU para imagens ao criar uma máquina virtual Windows com o modelo de implantação do Gerenciador de recursos."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell-or-the-cli"></a>Navegue e selecione imagens de máquina virtual do Windows no Azure com PowerShell ou CLI

Este tópico descreve como localizar fornecedores de imagem de máquina virtual, ofertas, skus e versões para cada local no qual você pode implantar. Para dar um exemplo, algumas imagens de máquina virtual do Windows comumente usadas são:

## <a name="table-of-commonly-used-windows-images"></a>Tabela comumente usadas de imagens do Windows


| PublisherName                        | Oferta                                 | SKU                         |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| MicrosoftDynamicsNAV             | DynamicsNAV                                | 2015                             |
| MicrosoftSharePoint              | MicrosoftSharePointServer                  | 2013                             |
| MicrosoftSQLServer               | SQL2014-WS2012R2                           | Enterprise-otimizado-para-DW      |
| MicrosoftSQLServer               | SQL2014-WS2012R2                           | Enterprise-otimizado-para-OLTP    |
| MicrosoftWindowsServer           | WindowsServer                              | 2012 R2-Data Center                  |
| MicrosoftWindowsServer           | WindowsServer                              | Data Center 2012               |
| MicrosoftWindowsServer           | WindowsServer                              | 2008-R2-SP1 |
| MicrosoftWindowsServer           | WindowsServer                              | Windows Server-Technical Preview |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials                    | WindowsServerEssentials          |
| MicrosoftWindowsServerHPCPack    | WindowsServerHPCPack                       | 2012R2                           |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]
