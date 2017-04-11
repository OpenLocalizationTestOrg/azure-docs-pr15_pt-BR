<properties 
    pageTitle="Implantar QuickBooks no Azure RemoteApp | Microsoft Azure" 
    description="Saiba como compartilhar QuickBooks com RemoteApp do Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-do-you-deploy-quickbooks-in-azure-remoteapp"></a>Como implantar o QuickBooks no Azure RemoteApp?

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Use as seguintes informações para compartilhar QuickBooks como um aplicativo no Azure RemoteApp.


Você pode compartilhar QuickBooks 2015 Enterprise com o Azure RemoteApp no conjunto de um híbrido ou nuvem. O arquivo de empresa deve residem em uma máquina virtual que esteja executando o servidor de banco de dados do QuickBooks separada dos servidores RemoteApp do Azure. Nunca armazenar o arquivo de empresa na sua imagem do Azure RemoteApp - perda de dados é esperada se você fizer isso. Somente QuickBooks Enterprise é compatível com o arquivo do QuickBooks em um compartilhamento externo com o servidor de banco de dados do QuickBooks acessível por meio de rede do Windows padrão de hospedagem.   

> [AZURE.IMPORTANT] O servidor de banco de dados do QuickBooks que hospeda o arquivo de empresa deve residem em uma máquina virtual separada dentro do mesmo VNET como a coleção de RemoteApp do Azure.  

## <a name="steps-to-deploy-quickbooks"></a>Etapas para implantar o QuickBooks

1. Criar uma máquina virtual do Azure e instale o QuickBooks, servidor de banco de dados do QuickBooks e coloque o arquivo da empresa em uma máquina virtual do Azure.  Certifique-se de configurar regras de firewall corretamente.
2. Instale o QuickBooks em uma [imagem personalizada](remoteapp-imageoptions.md) e criar um [conjunto de Azure RemoteApp](remoteapp-collections.md), nuvem ou híbridos, dentro do VNET mesmo exato onde reside a máquina virtual hospedando o servidor de banco de dados do QuickBooks com arquivos de empresa. 
3.  [Publicar](remoteapp-publish.md) Aplicativo do QuickBooks aos usuários
4.  Abrir o cliente do QuickBooks hospedado no Azure RemoteApp, navegue usando a rede padrão do Windows para a máquina virtual hospedando o servidor de banco de dados do QuickBooks e abra o arquivo da empresa. 

## <a name="documentation-references"></a>Referências de documentação

- QuickBooks [Configurações suportadas](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top)
- [Opções de implantação](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/) do QuickBooks

Você também pode verificar minha Ignite apresentação, [conceitos básicos do Microsoft Azure RemoteApp gerenciamento e administração](https://channel9.msdn.com/Events/Ignite/2015/BRK3868) - avanço para 1:02:45 para ir para a parte do QuickBooks.

## <a name="deployment-architecture"></a>Arquitetura de implantação

![QuickBooks + implantação do Azure RemoteApp](./media/remoteapp-quickbooks/ra-quickbooks.png)