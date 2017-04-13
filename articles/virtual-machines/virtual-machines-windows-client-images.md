<properties
   pageTitle="Usando imagens de cliente do Windows para cenários de desenvolvimento/teste | Microsoft Azure"
   description="Como usar os benefícios de assinatura do Visual Studio para implantar o Windows 7/8/10 no Azure para cenários de desenvolvimento/teste"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="iainfou"/>

# <a name="using-windows-client-in-azure-for-devtest-scenarios"></a>Usando o cliente do Windows no Azure para cenários de desenvolvimento/teste

Você pode usar o Windows 7, Windows 8, ou Windows 10 no Azure para cenários de desenvolvimento/teste desde que tenha uma assinatura do Visual Studio (antigo MSDN) apropriada. Este artigo descreve os requisitos de qualificação para o cliente do Windows em execução no Azure e usar as imagens de galeria do Azure.


## <a name="subscription-eligibility"></a>Qualificação de assinatura
Assinantes ativos do Visual Studio (pessoas que tem adquirido uma licença de assinatura do Visual Studio) podem usar o cliente do Windows para fins de testes e desenvolvimento. Cliente do Windows pode ser usado em seu próprio hardware e Azure máquinas virtuais executando qualquer tipo de assinatura do Azure. Cliente do Windows pode não ser implantado ou usado no Azure para uso de produção normal ou por pessoas que não são assinantes ativos do Visual Studio.

Para sua conveniência, fizemos certas imagens do Windows 10 disponíveis na Galeria de Azure dentro de [desenvolvimento/teste qualificado oferece](#eligible-offers). Os assinantes do Visual Studio dentro de qualquer tipo de oferta também podem [preparar e criar adequadamente](virtual-machines-windows-prepare-for-upload-vhd-image.md) 64-bit Windows 7, Windows 8, ou Windows 10 imagem e, em seguida, [carregar no Azure](virtual-machines-windows-upload-image.md). O uso permanece limitado a desenvolvimento/teste por assinantes ativos do Visual Studio.


## <a name="eligible-offers"></a>Ofertas qualificadas
A tabela a seguir detalha a oferta IDs que estão qualificados para implantar o Windows 10 por meio da Galeria do Azure. As imagens do Windows 10 são visíveis apenas para as seguintes ofertas. Os assinantes do Visual Studio que precisam para executar o cliente do Windows em um tipo de oferta diferentes exigem que você [sejam adequados para preparar](virtual-machines-windows-prepare-for-upload-vhd-image.md) e criar uma imagem do Windows 7, Windows 8 ou Windows 10 de 64 bits e [, em seguida, carregue no Azure](virtual-machines-windows-upload-image.md).

| Nome da oferta | Número da oferta | Imagens de cliente disponíveis |
|:-----------|:------------:|:-----------------------:|
| [Pré-pago desenvolvimento/teste](https://azure.microsoft.com/offers/ms-azr-0023p/)                          | 0023P | Windows 10 |
| [Assinantes do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)      | 0029P | Windows 10 |
| [Assinantes do Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)          | 0059P | Windows 10 |
| [Assinantes do Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)     | 0060P | Windows 10 |
| [Visual Premium Studio com MSDN (benefício)](https://azure.microsoft.com/offers/ms-azr-0061p/)       | 0061P | Windows 10 |
| [Assinantes do Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)            | 0063P | Windows 10 |
| [Assinantes do Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) | 0064P | Windows 10 |
| [Enterprise desenvolvimento/teste](https://azure.microsoft.com/ofers/ms-azr-0148p/)                              | 0148P | Windows 10 |


## <a name="check-your-azure-subscription"></a>Verificar a sua assinatura do Azure
Se você não souber sua ID de oferta, você pode obtê-lo por meio do portal do Azure ou o portal de conta.

A ID de oferta de assinatura é indicada na lâmina 'Assinaturas' dentro do Azure portal:

![Detalhes de ID da oferta do portal do Azure](./media/virtual-machines-windows-client-images/offer_id_azure_portal.png) 

Você também pode exibir a ID de oferta a partir da [guia 'Inscrições'](http://account.windowsazure.com/Subscriptions) do portal da conta do Azure:

![Detalhes de ID de oferta a partir do portal de conta do Azure](./media/virtual-machines-windows-client-images/offer_id_azure_account_portal.png) 


## <a name="next-steps"></a>Próximas etapas
Agora você pode implantar suas VMs usando o [PowerShell](virtual-machines-windows-ps-create.md), [modelos do Gerenciador de recursos](virtual-machines-windows-ps-template.md)ou [Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
