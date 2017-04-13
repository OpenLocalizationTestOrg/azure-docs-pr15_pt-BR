<properties 
   pageTitle="Planejamento de Gateway VPN e design | Microsoft Azure"
   description="Saiba mais sobre o planejamento de Gateway VPN e design para entre locais, híbrido e conexões de VNet para VNet"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc"/>

# <a name="planning-and-design-for-vpn-gateway"></a>Planejamento e design para Gateway VPN

Planejar e projetar sua entre locais e configurações de VNet para VNet podem ser simples ou complicado, dependendo das suas necessidades de rede. Este artigo conduz você por básicas considerações de planejamento e design.

## <a name="planning"></a>Planejamento


### <a name="compare"></a>Opções de conectividade entre locais

Se você deseja se conectar seus sites de local com segurança uma rede virtual, você tem três maneiras diferentes para fazê-lo:-to-Site, ponto-a-Site e rota expressa. Compare as conexões entre locais diferentes que estão disponíveis. A opção que você escolhe pode depender de várias considerações, tais como:


- Que tipo de produtividade sua solução requer?
- Você deseja se comunicar pela Internet pública via VPN segura, ou uma conexão particular?
- Você tem um endereço IP público disponível para uso?
- Você está planejando usar um dispositivo VPN? Em caso afirmativo, é compatível?
- Você está conectado apenas alguns computadores ou você deseja uma conexão persistente para seu site?
- Que tipo de gateway VPN é necessário para a solução que você deseja criar?
- Quais gateway SKU você deve usar?


A tabela a seguir pode ajudá-lo a decidir a melhor opção de conectividade para sua solução.


[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]



### <a name="gwrequire"></a>Requisitos de gateway por tipo VPN e SKU

[AZURE.INCLUDE [vpn-gateway-gwsku](../../includes/vpn-gateway-gwsku-include.md)]

Para obter mais informações sobre o gateway SKUs, consulte [configurações de Gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

#### <a name="aggregate-throughput-by-sku-and-vpn-type"></a>Transferência agregada por tipo de VPN e SKU

A tabela a seguir mostra os tipos de gateway e a taxa de transferência agregada estimada. A taxa de transferência agregada estimada pode ser um fator de tomada de decisão para o seu design.
Preços diferem entre SKUs de gateway. Para obter informações sobre preços, consulte [Preços de Gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Esta tabela se aplica ao gerente de recursos e modelos de implantação clássico.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

#### <a name="supported-configurations-by-sku-and-vpn-type"></a>Configurações suportadas por tipo de VPN e SKU

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 

### <a name="wf"></a>Fluxo de trabalho

A lista a seguir descreve o fluxo de trabalho comuns para conectividade de nuvem:

1.  Projetar e planejar a topologia de conectividade e listar os espaços de endereço para todas as redes que você deseja se conectar.
2.  Crie uma rede virtual Azure. 
3.  Crie um gateway VPN para a rede virtual.
4.  Criar e configurar conexões com redes local ou outros virtual (conforme necessário).
5.  Criar e configurar uma conexão ponto-a-Site para o gateway de VPN do Azure (conforme necessário).
 

## <a name="design"></a>Design

### <a name="topologies"></a>Topologias de Conexão

Comece examinando os diagramas no artigo [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) . O artigo contém diagramas básicos, os modelos de implantação de cada topologia (Gerenciador de recursos ou clássico) e implantação de quais ferramentas você podem usar para implantar sua configuração.   

### <a name="designbasics"></a>Noções básicas de design

As seções a seguir discutem os fundamentos de gateway VPN. Além disso, considere [limitações de serviços de rede](../articles/azure-subscription-service-limits.md#networking-limits).


#### <a name="subnets"></a>Sobre sub-redes

Quando você está criando conexões, você deve considerar os intervalos de sub-rede. Você não pode ter sobreposição de intervalos de endereços de sub-rede. Uma sub-rede sobreposta é quando uma rede virtual ou local na contém o mesmo espaço de endereço que contém o outro local. Isso significa que você precisa engenheiros sua rede suas redes locais local para reservar um intervalo de uso para o IP do Azure espaço/sub-redes de endereçamento. Você precisa de espaço de endereço que não está sendo usado na rede local no local. 

Evitar sobrepostos sub-redes também é importante quando você estiver trabalhando com conexões de VNet para VNet. Se suas sub-redes sobreposição e um endereço IP constar enviando tanto o destino VNets, conexões de VNet para VNet falhar. Azure não pode rotear os dados para o outro VNet porque o endereço de destino é parte de VNet o envio. 

Gateways VPN exigir uma sub-rede específica chamada uma sub-rede de gateway. Todas as sub-redes de gateway devem ser nomeadas GatewaySubnet funcione corretamente. Certifique-se de não nomear sua sub-rede gateway um nome diferente e não implantar VMs ou qualquer outra coisa à sub-rede gateway. Consulte [sub-redes de Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Sobre os gateways de rede local

O gateway de rede local geralmente se refere ao seu local de local. No modelo clássico de implantação, o gateway de rede local é conhecido como um Site de rede Local. Quando você configura um gateway de rede local, você dê um nome, especifique o endereço IP público do dispositivo VPN local e especifique os prefixos de endereço que estão no local no local. Azure examina os prefixos de endereço de destino para tráfego de rede, consulta a configuração que você especificou para o gateway de rede local e roteia pacotes adequadamente. Você pode modificar esses prefixos de endereço conforme necessário. Para obter mais informações, consulte [os gateways de rede Local](vpn-gateway-about-vpn-gateway-settings.md#lng).


#### <a name="gwtype"></a>Sobre tipos de gateway

Selecionando o tipo de gateway correto para a sua topologia é fundamental. Se você selecionar o tipo errado, seu gateway não funcionarão corretamente. O tipo de gateway Especifica como o gateway em si se conecta e é uma configuração de configuração necessárias para o modelo de implantação do Gerenciador de recursos.

Os tipos de gateway são:

- VPN
- Rota expressa

#### <a name="connectiontype"></a>Sobre tipos de conexão

Cada configuração requer um tipo de conexão específicas. Os tipos de conexão são:

- IPsec
- Vnet2Vnet
- Rota expressa
- VPNClient


#### <a name="vpntype"></a>Sobre tipos VPN

Cada configuração requer um tipo VPN específico. Se você combina duas configurações, como criar uma conexão de Site para Site e uma conexão ponto-a-Site para o mesmo VNet, você deve usar um tipo VPN que satisfaz ambos os requisitos de conexão.

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)] 

As tabelas a seguir mostram o tipo VPN, como ele mapeia para cada configuração de conexão. Verifique se que o tipo VPN para seu gateway corresponde à configuração que você deseja criar. 


[AZURE.INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)] 

### <a name="devices"></a>Dispositivos VPN para conexões to-Site

Para configurar uma conexão de-to-Site, independentemente de modelo de implantação, você precisa dos seguintes itens:

- Um dispositivo VPN que seja compatível com gateways VPN do Azure
- Um endereço de IP IPv4 público que não está atrás de um NAT

Você precisa ter uma experiência com a configuração de seu dispositivo VPN ou ter alguém que pode configurar o dispositivo para você. Para obter mais informações sobre dispositivos VPN, consulte [dispositivos sobre VPN](vpn-gateway-about-vpn-devices.md). O artigo de dispositivos VPN contém informações sobre dispositivos validados, requisitos para dispositivos que não foram validados e links para documentos de configuração de dispositivo, se disponível.

### <a name="forcedtunnel"></a>Considere a possibilidade de roteamento de túnel forçada

Para a maioria das configurações, você pode configurar túnel forçada. Túnel forçada permite redirecionar ou "forçar" todo o tráfego de Internet ligadas de volta para seu local por meio de um túnel de-to-Site VPN para inspeção e auditoria. Esse é um requisito de segurança crítica para a maioria dos enterprise IT políticas. 

Sem túnel forçada, o tráfego de Internet ligadas de suas VMs no Azure sempre será percorrer de infraestrutura de rede Azure diretamente check-out à Internet, sem a opção para permitir que você inspecionar ou o tráfego de auditoria. Acesso não autorizado de Internet potencialmente pode levar a divulgação de informações ou outros tipos de violações de segurança.

**Forçado túnel diagrama**

![Conexão forçado túnel] (./media/vpn-gateway-plan-design/forced-tunnel.png "forçado túnel")

Uma conexão de túnel forçada pode ser configurado em ambos os modelos de implantação e usando ferramentas diferentes. Consulte a tabela a seguir para obter mais informações. Podemos atualizar esta tabela como novos artigos, modelos de implantação de novo e ferramentas adicionais se tornam disponíveis para esta configuração. Quando um artigo estiver disponível, podemos vincular diretamente a ele da tabela.

[AZURE.INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 



## <a name="next-steps"></a>Próximas etapas

Consulte os artigos [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) e [Perguntas Frequentes de Gateway VPN](vpn-gateway-vpn-faq.md) para obter mais informações para ajudá-lo com seu design.

Para obter mais informações sobre as configurações de gateway específico, consulte [Sobre configurações de Gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).




