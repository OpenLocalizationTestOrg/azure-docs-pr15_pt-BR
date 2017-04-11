<properties
    pageTitle="Visão geral do IPv6 para balanceador de carga Azure | Microsoft Azure"
    description="Noções básicas sobre o suporte a IPv6 balanceador de carga do Azure e VMs balanceamento de carga."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    keywords="IPv6, balanceador de carga azure, pilha dupla, ip público, ipv6 nativo, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Visão geral do IPv6 para balanceador de carga Azure

Balanceadores de carga de voltado para a Internet podem ser implantados com um endereço IPv6. Além de conectividade IPv4, isso permite que os seguintes recursos:

* Ponta a ponta conectividade IPv6 nativa entre clientes de Internet públicos e Azure VMs (máquinas virtuais) por meio de Balanceador de carga.
* Ponta a ponta conectividade IPv6 nativa saída entre VMs e clientes habilitados para Internet IPv6 públicos.

A figura a seguir ilustra a funcionalidade IPv6 de Balanceador de carga do Azure.

![Balanceador de carga Azure com o IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Uma vez implantado, um cliente IPv4 ou Internet IPv6 ativado possa se comunicar com os endereços IPv4 ou IPv6 público (ou nomes de host) de Balanceador de carga voltado para a Internet do Azure. O balanceador de carga rotear os pacotes IPv6 para os endereços IPv6 particulares de VMs usando conversão de endereço de rede (NAT). O cliente de Internet IPv6 não consegue se comunicar diretamente com o endereço IPv6 do VMs.

## <a name="features"></a>Recursos

Suporte a IPv6 nativo para VMs implantadas por meio do Gerenciador de recursos do Azure fornece:

1. Balanceamento de carga de serviços de IPv6 para clientes IPv6 na Internet
2. Pontos de extremidade de IPv4 e IPv6 nativo em VMs ("dupla empilhada")
3. Entrada e saída iniciadas conexões IPv6 nativas
4. Protocolos suportados como TCP, UDP e HTTP (S) habilitar uma ampla variedade de arquiteturas de serviços

## <a name="benefits"></a>Benefícios

Esta funcionalidade permite que os seguintes benefícios principais:

* Cumprir normas governamentais exigir que novos aplicativos ser acessadas por clientes somente IPv6
* Habilitar mobile e Internet dos desenvolvedores de coisas (IOT) usar empilhadas dupla máquinas virtuais do (IPv4 + IPv6) para Azure para o celular crescente & IOT mercados de endereço

## <a name="details-and-limitations"></a>Detalhes e limitações

Detalhes

* O serviço DNS Azure contém A IPv4 e IPv6 AAAA registros de nome e responde com os dois registros de Balanceador de carga. O cliente escolhe qual endereço (IPv4 ou IPv6) para se comunicar com.
* Quando uma máquina virtual inicia uma conexão para um dispositivo conectado à Internet IPv6 público, endereço IPv6 de origem da VM é rede endereço traduzido (NAT) para o endereço IPv6 público de Balanceador de carga.
* VMs executando o sistema operacional Linux deverá ser configuradas para receber um endereço IP IPv6 por meio de DHCP. Muitas das imagens Linux na Galeria Azure já estão configuradas para oferecer suporte a IPv6 sem modificação. Para obter mais informações, consulte [Configurando DHCPv6 para Linux VMs](load-balancer-ipv6-for-linux.md)
* Se você optar por usar um teste de integridade com seu balanceador de carga, crie um teste de IPv4 e usá-lo com pontos de extremidade de IPv4 e IPv6. Se o serviço em sua máquina virtual falhar, pontos de extremidade de IPv4 e IPv6 são feitos fora rotação.

Limitações

* Você não pode adicionar regras de balanceamento de carga de IPv6 no portal do Azure. As regras só podem ser criadas através do modelo, CLI, PowerShell.
* Você não pode atualizar VMs existentes para usar endereços IPv6. Você deve implantar novas VMs.
* Um único endereço IPv6 pode ser atribuído a uma interface de rede em cada máquina virtual.
* Os endereços IPv6 públicos não podem ser atribuídos a uma máquina virtual. Eles só podem ser atribuídos a um balanceador de carga.
* Você não pode configurar a pesquisa reversa de DNS para seus endereços IPv6 públicos.
* As VMs com os endereços IPv6 não podem ser membros de um serviço de nuvem do Azure. Eles podem ser conectados a uma rede Virtual do Azure (VNet) e se comunicar com os outros em seus endereços IPv4.
* Endereços IPv6 particular podem ser implantados em VMs individuais em um grupo de recursos, mas não podem ser implantados em um grupo de recursos por meio de conjuntos de escala.
* VMs Azure não consegue se conectar em IPv6 para outras VMs, outros serviços do Azure ou dispositivos locais. Eles só possam se comunicar com o balanceador de carga Azure em IPv6. No entanto, eles podem se comunicar com esses outros recursos usando IPv4.
* Proteção de grupo de segurança de rede (NSG) para IPv4 é suportada em implantações de pilha dupla (IPv4 + IPv6). NSGs não se aplicam a pontos de extremidade de IPv6.
* O ponto de extremidade de IPv6 na máquina virtual não é exposto diretamente à internet. Ele está atrás de um balanceador de carga. Somente as portas especificadas nas regras de Balanceador de carga são acessíveis em IPv6.
* Alterar o parâmetro IdleTimeout para IPv6 é **não suportadas atualmente**. O padrão é quatro minutos.

## <a name="next-steps"></a>Próximas etapas

Saiba como implantar um balanceador de carga com o IPv6.

* [Disponibilidade do IPv6 por região](https://go.microsoft.com/fwlink/?linkid=828357)
* [Implantar um balanceador de carga com o IPv6 usando um modelo](load-balancer-ipv6-internet-template.md)
* [Implantar um balanceador de carga com o IPv6 usando o PowerShell do Azure](load-balancer-ipv6-internet-ps.md)
* [Implantar um balanceador de carga com o IPv6 usando CLI do Azure](load-balancer-ipv6-internet-cli.md)
