<properties
    pageTitle="Serviços de PaaS e ferramentas para pilha Azure | Microsoft Azure"
    description="Saiba como começar a usar os serviços de PaaS na pilha do Azure."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="tools-for-azure-stack"></a>Ferramentas para pilha Azure

Você pode baixar as ferramentas descritas abaixo. Se quiser ser notificado sobre ferramentas e novos serviços, siga #AzureStack no Twitter.

## <a name="template-tools"></a>Ferramentas de modelo

### <a name="azure-stack-github-templates"></a>Modelos de pilha Github Azure
Explore coleção crescente do [Azure pilha GitHub modelos](https://github.com/Azure/AzureStack-QuickStart-Templates). Assim como [Azure](https://github.com/Azure/azure-quickstart-templates), esses modelos de "Quick Start" Gerenciador de recursos do Azure objetivo é começar com blocos de construção simples e exemplos, prontos para implantar no ambiente da Microsoft Azure pilha Technical Preview prova de conceito. Estão incluídos exemplos de carga de trabalho de festa primeiro para [ad-Não ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha), [sql-2014-não ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha), [sharepoint 2013-Não-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha), como bem como vários modelos de 101 simples como [101-simples-windows-máquina virtual](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm).


### <a name="marketplace-item-packaging-tool-and-sample"></a>Amostra e ferramenta de embalagem de item do marketplace
[Baixar e usar a ferramenta de embalagem](http://www.aka.ms/azurestackmarketplaceitem) para criar itens de marketplace para seus próprios modelos personalizados adicionar ao marketplace Azure pilha. Instruções sobre como criar um item de marketplace e disponibilizá-lo ao seu locatários podem ser encontradas no [Marketplace criar item](azure-stack-create-and-publish-marketplace-item.md).

## <a name="developer-tools"></a>Ferramentas de desenvolvedor


### <a name="use-visual-studio-and-azure-stack-tp2-on-the-mas-con01-virtual-machine"></a>Use o Visual Studio e Azure pilha TP2 na máquina virtual MAS-CON01
Se você quiser usar o Visual Studio no console de máquina virtual para trabalhar com modelos de pilha do Azure, é necessário instalar as versões corretas das ferramentas necessárias. Use o procedimento a seguir para instalar as versões com suporte para TP2.

1. Use Conexão de área de trabalho remota para fazer logon máquina virtual CON01 MAS com as credenciais de azurestack\azurestackadmin.
2. Instale e abra Web Platform Installer.
3. Localizar e instalar o **Visual Studio comunidade de 2015 com o SDK do Microsoft Azure - 2.9.5**.
4. Usando **Adicionar ou remover programas**, desinstale o **Microsoft Azure PowerShell (setembro)** que é instalado como parte do SDK.
5. Abra o Web Platform Installer.
6. Encontre e instale o **Microsoft Azure PowerShell - 2 de visualização técnica do Azure pilha**. 
7. Reinicie a máquina virtual MAS-CON01.
8. Use Conexão de área de trabalho remota para fazer logon máquina virtual CON01 MAS com as credenciais de azurestack\azurestackadmin.
9. Abra o Visual Studio e validar que você pode conectar-se para o ambiente do Azure pilha, obtenha modelos e assim por diante. 

### <a name="azure-powershell-sdk"></a>Azure SDK do PowerShell
PowerShell Azure é um módulo que fornece cmdlets para gerenciar Azure e pilha de Azure com o Windows PowerShell. Você pode usar os cmdlets para criar, testar, implantar e gerenciar soluções e serviços prestados na plataforma de pilha do Azure.
[Baixe o SDK do PowerShell do Azure](http://aka.ms/azStackPsh) e [Instale o PowerShell](azure-stack-connect-powershell.md).

### <a name="azure-cross-platform-command-line-interfaces"></a>Azure entre interfaces de linha de comando de plataforma
Instale rapidamente a Interface de linha do Azure (Azure comando) para usar um conjunto de código-fonte aberto baseado em shell comandos para criar e gerenciar recursos do Microsoft Azure pilha.

[Baixar o Windows CLI](http://aka.ms/azstack-windows-cli)

[Baixar o Mac CLI](http://aka.ms/azstack-linux-cli)

[Baixar o CLI Linux](http://aka.ms/azstack-mac-cli)

>[AZURE.NOTE]
>
> + Se você estiver em um computador Mac ou Linux, você também pode chegar a CLI usando o comando`npm install -g azure-cli@0.9.11`</br>
> + Se você estiver recebendo problemas de validação de certificado, execute o comando`set NODE_TLS_REJECT_UNAUTHORIZED=0`
