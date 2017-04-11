<properties
   pageTitle="Rota expressa locais | Microsoft Azure"
   description="Este artigo fornece uma visão geral detalhada dos locais onde os serviços são oferecidos e como se conectar a regiões Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="cherylmc" />

# <a name="expressroute-partners-and-peering-locations"></a>Rota expressa parceiros e aos locais

As tabelas neste artigo fornecem informações sobre a rota expressa conectividade provedores, rota expressa cobertura geográfica, serviços de nuvem da Microsoft com suporte sobre rota expressa e rota expressa SIs (integradores).

## <a name="partners"></a>Rota expressa provedores de conectividade

Rota expressa é suportada em todas as regiões Azure e locais. O mapa a seguir fornece uma lista de regiões Azure e rota expressa locais. Rota expressa locais se referir aos onde Microsoft correspondentes com vários provedores de serviço.

![Mapa de localização][0]

Você terá acesso aos serviços Azure em todas as regiões dentro de uma região geopolíticas se você estiver conectado a pelo menos um local de rota expressa dentro da região geopolíticas. A tabela a seguir fornece um mapa de regiões Azure para locais de rota expressa dentro de uma região geopolíticas.

|**Região geopolíticas**|**Regiões Azure**|**Rota expressa locais**|
|---|---|---|
|**América do Norte**|Leste EUA, oeste EUA, Leste dos EUA 2, centro dos EUA, Centro Sul dos EUA, Centro Norte dos EUA, Canadá Central, Canadá Leste|Atlanta, Chicago, Dallas, Las Vegas, Los Angeles, Nova York, Seattle, vale do Silício, Washington DC, Montreal +, cidade de Quebec +, curitiba|
|**América do Sul**|Brasil Sul|São Paulo|
|**Europa**|Europa do Norte, Europa Ocidental, Reino Unido Oeste, Sul do Reino Unido|Amsterdam, Dublin, Londres, Newport(Wales) +, Paris|
|**Ásia**|Leste Asiático, Sudeste Asiático|Hong Kong, Cingapura|
|**Japão**|Japão Oeste Japão Leste|Osaka, Tóquio|
|**Austrália**|Austrália Sudeste, Austrália Oriental|Melbourne, Sidney|
|**Índia**|Índia Oeste, Índia Central, Índia Sul|Chennai, Mumbai|



A tabela a seguir fornece informações sobre regiões e limites geopolíticas para nuvens nacionais.

|**Região geopolíticas**|**Regiões Azure**|**Rota expressa locais**|
|---|---|---|---|
|**Nuvem do governo dos EUA**|EUA Gov Iowa, EUA Gov Virgínia|Chicago, Dallas, Nova York, Washington DC|
|**China**|China Norte, China Leste|Pequim, Shanghai|
|**Alemanha**|Alemanha Central, Alemanha Oriental|Berlim, Frankfurt|


Conectividade entre regiões geopolíticas não é suportada no SKU ExpressRoute padrão. Você precisará habilitá-lo rota expressa premium dar suporte a conectividade global. Não há suporte para a conectividade aos ambientes de nuvem nacional. Você pode trabalhar com seu provedor de conectividade se tal uma necessidade surgir.


## <a name="connectivity-provider-locations"></a>Locais de provedor de serviços de conectividade

> [AZURE.SELECTOR]
[Locais pelo provedor](expressroute-locations.md#connectivity-provider-locations)
[provedores por local](expressroute-locations-providers.md#connectivity-provider-locations)

### <a name="production-azure"></a>Azure de produção
| **Local**  | **Provedores de serviço** |
|---------------|-----------------------|
| **Amsterdam** | Redes Aryaka, AT & T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, soluções de Internet - nuvem conectar, Interxion, Verizon de comunicações, laranja, Tata comunicações, TeleCity grupo Telenor, de nível 3 |
| **Atlanta** | Equinix |
| **Madras** | Comunicações de Tata |
| **Chicago** | AT & T NetBond, Comcast, Equinix, comunicações de nível 3, grupo de Zayo |
| **Dallas** | AT & T NetBond, Cologix, Equinix, nível 3 comunicações, Megaport |
| **Dublin** | COLT, grupo de Telecity |
| **Hong Kong** | British Telecom, China Telecom Global, Equinix, Megaport, laranja, PCCW Global limitado, clique em comunicações Tata Verizon |
| **Londres** | AT & T NetBond, British Telecom, Colt, Equinix, InterCloud, soluções de Internet - nuvem conectar, Interxion, Jisc, nível 3 comunicações, para Mountain, NTT comunicações, laranja, Tata comunicações, Telecity grupo Telenor, Verizon, Vodafone |
| **Las Vegas** | Nível 3 comunicações +, Megaport
| **Los Angeles** | CoreSite, Equinix, Megaport, NTT, Zayo grupo |
| **Melbourne** | AARNet, Equinix, Megaport, NEXTDC, Telstra Corporation |
| **Nova York** | Equinix, Megaport, Zayo grupo |
| **Newport(Wales) +** | Próxima geração dados + |
| **Montreal** | Cologix + |
| **Bombaim** | Comunicações de Tata |
| **Osaka** | Equinix, Internet iniciativa Japão Inc. - IIJ, NTT comunicações, Softbank |
| **Paris** | Interxion, Equinix + |
| **São Paulo** | Equinix, Telefonica |
| **Seattle** | Equinix, comunicações de nível 3, Megaport |
| **Vale do silício** | Redes de Aryaka, AT & T NetBond, British Telecom, CenturyLink +, Comcast, Equinix, nível 3 comunicações, laranja, Tata comunicações, Verizon, Zayo grupo |
| **Cingapura** | Redes de Aryaka, AT & T NetBond, British Telecom, Equinix, InterCloud, Megaport, laranja, SingTel, Tata comunicações, Verizon |
| **Sidney** | AARNet, AT & T NetBond, British Telecom, Equinix, Megaport, NEXTDC, laranja, Telstra Corporation, Verizon |
| **Tóquio** | Aryaka redes, British Telecom, Colt, Equinix, Internet iniciativa Japão Inc. - IIJ, NTT comunicações, Softbank, Verizon |
| **Curitiba** | Cologix, Equinix, Zayo grupo |
| **Washington DC** | Redes de Aryaka, AT & T NetBond, British Telecom, Comcast, Equinix, InterCloud, nível 3, Megaport, laranja, Tata comunicações, Verizon, Zayo grupo de comunicações |

 **+**Indica em breve

### <a name="national-cloud-environments"></a>Ambientes de nuvem nacional

#### <a name="us-government-cloud"></a>Nuvem do governo dos EUA

| **Local**  |**Provedores de serviço** |
|---------------|--------------------|
| **Chicago** | AT & T NetBond, Equinix, nível 3 comunicações, Verizon |
| **Dallas** |  Equinix, Verizon + |
| **Nova York** | Equinix, nível 3 comunicações +, Verizon |
| **Washington DC** | AT & T NetBond, Equinix, nível 3 comunicações, Verizon |

#### <a name="china"></a>China

| **Local**  | **Provedores de serviço** |
|---------------|-----------------------|
| **Pequim** | China Telecom |
| **Xangai** |  China Telecom |
Para saber mais, consulte [rota expressa na China](http://www.windowsazure.cn/home/features/expressroute/)

#### <a name="germany"></a>Alemanha

| **Local**  | **Provedores de serviço** |
|---------------|-----------------------|
| **Berlim** | COLT +, e-shelter |
| **Frankfurt** | COLT, Equinix, Interxion |

## <a name="nonpartners"></a>Conectividade por meio de provedores de serviço não listado

Se o seu provedor de conectividade não estiver listado nas seções anteriores, você ainda pode criar uma conexão.

- Verifique com seu provedor de conectividade para ver se eles estiverem conectados a qualquer uma das trocas na tabela acima. Você pode verificar os links a seguir para obter mais informações sobre serviços oferecidas por provedores de exchange. Vários provedores de conectividade já estão conectados a trocas de Ethernet.

    - [Nuvem de Equinix Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [InterXion](http://www.interxion.com/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Ter o seu provedor de conectividade estender sua rede para o local de correspondência de preferência.
    - Certifique-se de que seu provedor de conectividade estende a conectividade de maneira altamente disponível para que sejam sem pontos únicos de falha.
- Solicite um circuito de rota expressa com o exchange como seu provedor de conectividade para se conectar à Microsoft.
    - Siga as etapas em [criar um circuito de rota expressa](expressroute-howto-circuit-classic.md) para configurar a conectividade.

|**Local**|**Exchange**|**Provedores de conectividade**|
|-------------|------------|-------------------------|
| **Nova York** | Equinix | Lightower |
| **Seattle** | Equinix | Comunicações de Alasca |
| **Vale do silício** | Equinix | Comunicações de XO |
| **Cingapura** | Equinix | 1CLOUDSTAR |
| **Washington DC** | Equinix | Lightower |

## <a name="expressroute-system-integrators"></a>Rota expressa integradores

Habilitar conectividade particular para atender às suas necessidades pode ser desafiador, com base na escala de sua rede. Você pode trabalhar com qualquer um dos integradores listados na tabela a seguir para ajudá-lo com integração a rota expressa.

|**Continente**|**Integradores**|
|-------------|---------------------|
| **Ásia** | Avanade Inc., OneAs1a|
| **Europa** | Inc. Avanade, Dotnet soluções|
| **CONOSCO** | Avanade Inc, serviços profissionais de Equinix, Perficient, liderança de projeto|

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre a rota expressa, consulte as [Perguntas Frequentes de rota expressa](expressroute-faqs.md).
- Certifique-se de que todos os pré-requisitos forem atendidos. Consulte [rota expressa pré-requisitos](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de localização"
