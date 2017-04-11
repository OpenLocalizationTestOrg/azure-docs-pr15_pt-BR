<properties
   pageTitle="Pré-requisitos técnicos para a criação de uma imagem de máquina virtual do Azure Marketplace | Microsoft Azure"
   description="Compreenda os requisitos para criar e implantar uma imagem de máquina virtual para o Azure Marketplace para que outras pessoas para compra."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="04/29/2016"
  ms.author="hascipio; v-divte"/>

# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Pré-requisitos técnicos para a criação de uma imagem de máquina virtual do Azure Marketplace
Leia o processo completamente antes de começar e entenda onde e por que cada etapa é executada. Tanto quanto possível, você deve preparar informações da sua empresa e outros dados, baixe ferramentas necessárias e/ou criar componentes técnicos antes de iniciar o processo de criação de oferta. Esses itens devem ser claros na revisão deste artigo.  

## <a name="download-needed-tools--applications"></a>Ferramentas de download necessário e aplicativos
Você deve ter os seguintes itens prontos antes de iniciar o processo:

- Dependendo de qual sistema operacional você está destinando, instale a [ferramenta de interface de linha de Linux](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) ou [cmdlets do PowerShell do Azure](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) na página [Downloads do Azure](https://azure.microsoft.com/downloads/) .
- Instale o armazenamento do Azure Explorer do CodePlex.
- Baixe e instale a ferramenta de teste de certificação do Azure certificados:
  - [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Você precisa de um computador baseado no Windows para executar a ferramenta de certificação. Se você não tiver um computador baseado no Windows disponível, você pode executar a ferramenta usando uma máquina virtual baseado no Windows no Azure.

## <a name="platforms-supported"></a>Plataformas suportadas
Você pode desenvolver com base no Azure VMs em Windows ou Linux. Alguns elementos do processo de publicação, como criar um compatível com o Azure disco rígido virtual (VHD) – usar ferramentas e diferentes etapas dependendo de qual sistema operacional você está usando:  

- Se você estiver usando o Linux, consulte a seção "Criar um VHD compatível com o Azure (baseado em Linux)" do [guia de publicação de imagem de máquina Virtual](marketplace-publishing-vm-image-creation.md).
- Se você estiver usando o Windows, consulte a seção "Criar um VHD compatível com o Azure (baseado em Windows)" do [guia de publicação de imagem de máquina Virtual](marketplace-publishing-vm-image-creation.md).

> [AZURE.NOTE] Você precisa de acesso a um computador baseado no Windows para:
- Execute a ferramenta de validação de certificação.
- Crie a URL de assinatura do access VHD compartilhada para o envio de certificação VHD.

## <a name="develop-your-vhd"></a>Desenvolver seu VHD
Você pode desenvolver VHDs Azure na nuvem ou local:

- Desenvolvimento baseado em nuvem significa que todas as etapas de desenvolvimento são executadas remotamente em um VHD residente no Azure.
- Desenvolvimento local requer o download de um VHD e desenvolvendo-la usando a infraestrutura de local. Embora seja possível, não recomendamos-lo. Observe que desenvolvimento para Windows ou SQL local requer que você tenha as chaves de licença do local relevante. Você não pode incluir ou instalar o SQL Server depois de criar uma máquina virtual. Você também deve basear sua oferta em uma imagem SQL aprovada do portal do Azure. Se você decidir desenvolver local, você deverá executar algumas etapas diferente se estivesse desenvolvendo na nuvem. Você pode encontrar informações relevantes em [criar uma imagem de máquina virtual do local](marketplace-publishing-vm-image-creation-on-premise.md).

## <a name="next-steps"></a>Próximas etapas
Agora que você revisar os pré-requisitos e concluir as tarefas necessárias, você pode avançar com a criação de sua oferta de imagem de máquina virtual conforme detalhado no [guia de publicação de imagem de máquina Virtual](marketplace-publishing-vm-image-creation.md).

## <a name="see-also"></a>Consulte também
- [Introdução: como publicar uma oferta do Azure Marketplace](marketplace-publishing-getting-started.md)
- [Criar uma máquina virtual executando o Windows no portal do Azure preview](../virtual-machines/virtual-machines-windows-hero-tutorial.md)


[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
