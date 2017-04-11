<properties
   pageTitle="Pré-requisitos para adoção de rota expressa | Microsoft Azure"
   description="Esta página fornece uma lista dos requisitos seja atendida antes de você pode solicitar um circuito de rota expressa do Azure."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>


# <a name="expressroute-prerequisites--checklist"></a>Lista de verificação & rota expressa pré-requisitos  

Para se conectar aos serviços de nuvem da Microsoft usando rota expressa, você precisará verificar se os seguintes requisitos listados nas seções a seguir foram atendidos.

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Conta do Azure

- Uma conta da Microsoft Azure válida e ativa. Isso é necessário configurar o circuito rota expressa. Rota expressa circuitos são recursos dentro de assinaturas do Azure. Uma assinatura do Azure é um requisito, mesmo se a conectividade está limitada aos serviços de nuvem não é do Microsoft Azure, como serviços do Office 365 e CRM online.
- Uma assinatura ativa do Office 365 (se usando os serviços do Office 365). Consulte a seção [requisitos específicos do Office 365](#office-365-specific-requirements) deste artigo para obter mais informações.

## <a name="connectivity-provider"></a>Provedor de serviços corporativos de conectividade
- Você pode trabalhar com um [parceiro de conectividade de rota expressa](expressroute-locations.md#partners) para se conectar à nuvem da Microsoft. Você pode configurar uma conexão entre sua rede local e a Microsoft de [três maneiras](expressroute-introduction.md#howtoconnect). 
- Se seu provedor não for um parceiro de conectividade rota expressa, você ainda pode se conectar à nuvem da Microsoft através de um [provedor de nuvem do exchange](expressroute-locations.md#nonpartners).

## <a name="network-requirements"></a>Requisitos de rede
- **Conectividade redundante**: há nenhuma exigência de redundância em conectividade física entre você e seu provedor. Microsoft exigem sessões de BGP redundantes ser configuradas entre roteadores da Microsoft e os roteadores correspondência, mesmo quando você tiver apenas [uma conexão física ao exchange nuvem](expressroute-faqs.md#onep2plink). 
- **Roteamento**: dependendo de como você se conecta para os Microsoft Cloud, você ou seu provedor precisa configurar e gerenciar as sessões BGP para [roteamento de domínios](expressroute-circuit-peerings.md). Alguns provedor de conectividade Ethernet ou provedor de nuvem do exchange pode oferecer gerenciamento de BGP como um serviço de valor agregado.
- **NAT**: Microsoft aceita somente endereços IP públicos por meio de correspondência da Microsoft. Se você estiver usando endereços IP particulares da sua rede local, você ou sua necessidade de provedor para traduzir os endereços IP particulares para o IP público endereços [usando o NAT](expressroute-nat.md).
- **QoS**: Skype para Business tem vários serviços (por exemplo, vídeo, o texto de voz) que exigem diferenciados tratamento de QoS. Você e seu provedor devem seguir os [requisitos de QoS](expressroute-qos.md).
- **Segurança de rede**: você deve considerar a [segurança de rede](../best-practices-network-security.md) ao conectar com o Microsoft Cloud via rota expressa.
 
## <a name="office-365"></a>Office 365

Se você planeja habilitar o Office 365 em rota expressa, examine os documentos a seguir para obter mais informações sobre os requisitos do Office 365.


- [Visão geral de rota expressa para Office 365](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
- [Roteamento com rota expressa para Office 365](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
- [Intervalos de endereços IP e URLs do Office 365](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
- [Planejamento de rede e ajuste de desempenho do Office 365](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
- [Ferramentas e calculadoras de largura de banda de rede](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
- [Integração do Office 365 com ambientes locais](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)

## <a name="crm-online"></a>CRM Online 
Se você planeja habilitar CRM Online na rota expressa, revise os seguintes documentos para obter mais informações sobre o CRM Online

- [CRM Online URLs](https://support.microsoft.com/kb/2655102) e [intervalos de endereços IP](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre a rota expressa, consulte as [Perguntas Frequentes de rota expressa](expressroute-faqs.md).
- Localize um provedor de conectividade rota expressa. Consulte [parceiros de rota expressa e locais de correspondência](expressroute-locations.md).
- Consulte requisitos de [Roteamento](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
- Configure sua conexão de rota expressa.
    - [Criar um circuito de rota expressa](expressroute-howto-circuit-classic.md)
    - [Configurar o roteamento](expressroute-howto-routing-classic.md)
    - [Vincular um VNet a um circuito de rota expressa](expressroute-howto-linkvnet-classic.md)

