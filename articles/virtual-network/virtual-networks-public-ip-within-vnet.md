<properties 
   pageTitle="Como usar endereços IP públicos em uma rede virtual"
   description="Saiba como configurar uma rede virtual para usar endereços IP públicos"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="public-ip-address-space-in-a-virtual-network-vnet"></a>Espaço de endereço IP público em uma rede Virtual (VNet)

Redes virtuais (VNets) podem conter espaços de endereço IP (blocos de endereço RFC 1918) públicos e privados. Quando você adiciona um intervalo de endereços IP público, ele será tratado como parte do espaço de endereço particular do VNet IP que só está acessível dentro do VNet, VNets interconectados e em seu local de local.

A imagem abaixo mostra um VNet que inclui os espaços de endereço IP públicos e privados.

![IP público conceitual](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## <a name="how-do-i-add-a-public-ip-address-range"></a>Como posso adicionar um intervalo de endereços IP público?

Adicionar um intervalo de endereços IP público da mesma maneira que você adiciona um intervalo de endereços IP particular; usando um arquivo de *netcfg* , ou adicionando a configuração no [portal do Azure](http://portal.azure.com). Você pode adicionar um intervalo de endereços IP público quando você cria sua VNet, ou você pode voltar e adicioná-la posteriormente. O exemplo a seguir mostra públicos e privados espaços de endereço IP configurados no mesmo VNet.

![Endereço IP público no Portal](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## <a name="are-there-any-limitations"></a>Existem quaisquer limitações?

Existem alguns intervalos de endereços IP que não são permitidos:

- 224.0.0.0/4 (difusão seletiva)

- 255.255.255.255/32 (difusão)

- 127.0.0.0/8 (loopback)

- 169.254.0.0/16 (link-local)

- 168.63.129.16/32 (DNS interno)

## <a name="next-steps"></a>Próximas etapas

[Como gerenciar servidores DNS usados por um VNet](virtual-networks-manage-dns-in-vnet.md)