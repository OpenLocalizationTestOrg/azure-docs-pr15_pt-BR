<properties 
   pageTitle="Introdução a rota expressa | Microsoft Azure"
   description="Esta página fornece uma visão geral do serviço rota expressa, incluindo como uma conexão de rota expressa funciona."
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

# <a name="expressroute-technical-overview"></a>Visão geral técnica de rota expressa

Rota expressa do Microsoft Azure lhe permite estender suas redes locais em nuvem da Microsoft através de uma conexão privada dedicada facilitada por um provedor de conectividade. Com a rota expressa, você pode estabelecer conexões com serviços de nuvem da Microsoft, como Microsoft Azure, Office 365 e CRM Online. Conectividade pode ser uma rede de (IP VPN) para qualquer, uma rede de Ethernet de ponto a ponto ou uma conexão entre virtual através de um provedor de conectividade em uma instalação de localização conjunta. Rota expressa conexões não vá na Internet pública. Isso permite conexões de rota expressa oferecer mais confiabilidade, rapidez, latências menores e maior segurança que conexões típicas pela Internet.

![](./media/expressroute-introduction/expressroute-basic.png)

**Principais benefícios incluem:**

- Camada 3 conectividade entre sua rede local e o Microsoft Cloud através de um provedor de conectividade. Conectividade pode ser de uma rede (IPVPN) para qualquer, uma conexão de Ethernet de ponto a ponto, ou por meio de uma conexão entre virtual por meio de uma troca de Ethernet.
- Conectividade com serviços de nuvem da Microsoft em todas as regiões na região geopolíticas.
- Conectividade global serviços da Microsoft em todas as regiões com complemento do rota expressa premium.
- Dinâmico roteamento entre a rede e Microsoft através de protocolos padrão do setor (BGP).
- Redundância em cada local para maior confiabilidade.
- Atividade de Conexão [SLA](https://azure.microsoft.com/support/legal/sla/).
- QoS e suporte para várias classes de serviço para aplicativos especiais, como o Skype for Business.

Consulte as [Perguntas Frequentes de rota expressa](expressroute-faqs.md) para obter mais detalhes.

## <a name="howtoconnect"></a>Como posso me conectar minha rede à Microsoft usando a rota expressa?

Você pode criar uma conexão entre sua rede local e a nuvem da Microsoft de três maneiras diferentes:

### <a name="co-located-at-a-cloud-exchange"></a>Localizada no exchange nuvem

Se você estiver localizada em um recurso com uma troca de nuvem, você pode solicitar conexões entre virtuais para a nuvem da Microsoft por meio do exchange de Ethernet do provedor localização conjunta. Provedores de localização conjunta podem oferecer entre-conexões de camada 2 ou gerenciado Layer 3 entre conexões entre sua infraestrutura no recurso localização conjunta e nuvem da Microsoft.

### <a name="point-to-point-ethernet-connections"></a>Conexões de Ethernet de ponto a ponto 

Você pode conectar sua data centers/escritórios locais na nuvem da Microsoft por meio de links de Ethernet de ponto a ponto. Provedores de Ethernet de ponto a ponto podem oferecer conexões de camada 2 ou gerenciado conexões de camada 3 entre seu site e de nuvem da Microsoft.

### <a name="any-to-any-ipvpn-networks"></a>Para qualquer redes (IPVPN)

Você pode integrar seu WAN com a nuvem da Microsoft. Provedores de IPVPN (normalmente MPLS VPN) oferecem a qualquer conectividade entre seus escritórios de ramificação e dos data centers. Nuvem da Microsoft pode ser interconectada para sua WAN para torná-lo como qualquer outra filial. Provedores de WAN normalmente oferecem conectividade de camada 3 gerenciada. Rota expressa recursos estão todos idênticos em todos os modelos de conectividade acima. 

Provedores de conectividade podem oferecer um ou mais modelos de conectividade. Você pode trabalhar com seu provedor de conectividade para escolher o modelo que funciona melhor para você.

![](./media/expressroute-introduction/expressroute-connectivitymodels.png)



## <a name="expressroute-features"></a>Recursos de rota expressa

Rota expressa compatível com os recursos e os recursos a seguir: 

### <a name="layer-3-connectivity"></a>Conectividade de camada 3

Microsoft usa protocolo padrão da indústria dinâmico roteamento (BGP) para trocar rotas entre sua rede local, suas instâncias no Azure e Microsoft endereços públicos.  Podemos estabelecer várias sessões BGP com a sua rede para perfis de tráfego diferente. Mais detalhes podem ser encontrados no artigo [circuito de rota expressa e roteamento de domínios](expressroute-circuit-peerings.md) .

### <a name="redundancy"></a>Redundância

Cada circuito rota expressa consiste em duas conexões para dois roteadores de borda do Microsoft Enterprise (MSEEs) do provedor de conectividade / sua borda de rede. Microsoft exigirá dupla conexão de BGP do provedor de conectividade / seu lado – um para cada MSEE. Você pode optar por não implantar dispositivos redundantes / Ethernet circuitos e seu final. No entanto, provedores de conectividade usam dispositivos redundantes para garantir que suas conexões são enviadas à Microsoft de maneira redundante. Uma configuração de conectividade de camada 3 redundante é um requisito para o nosso [SLA](https://azure.microsoft.com/support/legal/sla/) válido. 

### <a name="connectivity-to-microsoft-cloud-services"></a>Conectividade aos serviços de nuvem da Microsoft

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Rota expressa conexões habilitam o acesso aos seguintes serviços:

- Serviços do Microsoft Azure
- Serviços do Microsoft Office 365
- Serviços do Microsoft CRM Online 
 
Você pode visitar a página de [Perguntas Frequentes de rota expressa](expressroute-faqs.md) para uma lista detalhada dos serviços de suporte ao longo de rota expressa.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Conectividade com todas as regiões dentro de uma região geopolíticas

Você pode se conectar à Microsoft em um dos nossos [aos locais](expressroute-locations.md) e tem acesso a todas as regiões dentro da região geopolíticas. 

Por exemplo, se você estiver conectado à Microsoft no Amsterdam por meio de rota expressa, você terá acesso a todos os serviços de nuvem da Microsoft hospedado na Europa Norte e Europa Ocidental. Consulte o artigo de [parceiros de rota expressa e locais de correspondência](expressroute-locations.md) para obter uma visão geral das regiões geopolíticas, regiões de nuvem Microsoft associadas e locais de correspondência rota expressa correspondentes.

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>Conectividade global com complemento premium de rota expressa

Você pode habilitar o recurso de complemento premium rota expressa estender a conectividade geopolíticas limites. Por exemplo, se você estiver conectado à Microsoft no Amsterdam por meio de rota expressa, você terá acesso a todos os serviços de nuvem da Microsoft hospedado em todas as regiões em todo o mundo (nuvens nacionais são excluídos). Você pode acessar serviços implantados na América do Sul ou Austrália da mesma maneira que você acessa as regiões Norte e Europa Oeste.

### <a name="rich-connectivity-partner-ecosystem"></a>Ecossistema de parceiros de conectividade avançada

Rota expressa tem um ecossistema constantemente crescente de provedores de conectividade e parceiros de SI. Você pode consultar o artigo [locais e provedores de rota expressa](expressroute-locations.md) para as informações mais recentes.

### <a name="connectivity-to-national-clouds"></a>Conectividade com nuvens nacionais

Microsoft opera ambientes de nuvem isolada para regiões geopolíticas especiais e segmentos de cliente. Consulte a página [locais e provedores de rota expressa](expressroute-locations.md) para obter uma lista de nuvens nacionais e provedores.

### <a name="supported-bandwidth-options"></a>Opções de largura de banda com suporte

Você pode adquirir circuitos rota expressa para uma ampla variedade de larguras. Lista de larguras compatíveis está listada abaixo. Certifique-se de verificar com seu provedor de conectividade para determinar a lista de larguras compatíveis fornecem.

- 50 Mbps
- 100 Mbps
- 200 Mbps
- 500 Mbps
- 1 Gbps
- 2 Gbps
- 5 Gbps
- 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>Dimensionamento dinâmico de largura de banda

Você tem a capacidade de aumentar a largura de banda de circuito de rota expressa (melhor esforço com base em) sem precisar subdividir suas conexões. 

### <a name="flexible-billing-models"></a>Modelos flexíveis de cobrança

Você pode escolher um modelo de cobrança que funciona melhor para você. Escolha entre os modelos de cobranças listados abaixo. Consulte a página de [Perguntas Frequentes de rota expressa](expressroute-faqs.md) para obter mais detalhes. 

- **Dados ilimitado**. O circuito rota expressa é cobrado com base em uma taxa mensal e todos os dados de entrada e saída transferência é incluída gratuitamente. 
- **Dados de limitadas**. O circuito rota expressa é cobrado com base em uma taxa mensal. Todas as transferências de dados de entrada é gratuita. Transferência de dados de saída é cobrada por GB de transferência de dados. Taxas de transferência de dados variam por região.
- **Complemento do rota expressa premium**. A rota expressa premium é um complemento sobre o circuito rota expressa. O complemento de premium rota expressa fornece os seguintes recursos: 
    - Limites de rota aumento do Azure públicas e Azure particular correspondência de 4.000 rotas 10.000 rotas.
    - Conectividade global para serviços. Um circuito de rota expressa criado em qualquer região (excluindo nuvens nacionais) terá acesso aos recursos em qualquer outra região do mundo. Por exemplo, uma rede virtual criada na Europa Oeste pode ser acessada por meio de um circuito de rota expressa provisionado no Vale do Silício.
    - Maior número de links de VNet por circuito de rota expressa de 10 para um limite maior, dependendo da largura de banda do circuito.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre conexões de rota expressa e domínios de roteamento. Consulte [rota expressa circuitos e domínios de roteamento](expressroute-circuit-peerings.md).
- Encontre um provedor de serviço. Consulte [parceiros de rota expressa e locais de correspondência](expressroute-locations.md).
- Certifique-se de que todos os pré-requisitos forem atendidos. Consulte [rota expressa pré-requisitos](expressroute-prerequisites.md).
- Consulte os requisitos para [Roteamento](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
- Configure sua conexão de rota expressa.
    - [Criar um circuito de rota expressa](expressroute-howto-circuit-classic.md)
    - [Configurar o roteamento](expressroute-howto-routing-classic.md)
    - [Vincular um VNet a um circuito de rota expressa](expressroute-howto-linkvnet-classic.md)
