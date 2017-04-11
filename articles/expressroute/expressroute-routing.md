<properties
   pageTitle="Requisitos de roteamento para rota expressa | Microsoft Azure"
   description="Esta página fornece requisitos detalhados para configurar e gerenciar o roteamento para rota expressa circuitos."
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="ganesr"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/19/2016"
   ms.author="osamazia"/>


# <a name="expressroute-routing-requirements"></a>Requisitos de roteamento rota expressa  

Para se conectar aos serviços de nuvem da Microsoft usando rota expressa, você precisará configurar e gerenciar o roteamento. Alguns provedores de conectividade oferecem configurar e gerenciar o roteamento como um serviço gerenciado. Verifique com seu provedor de conectividade para ver se eles oferecem esse serviço. Se não tiverem, você deve atender aos seguintes requisitos. 

Consulte o artigo [circuitos e domínios de roteamento](expressroute-circuit-peerings.md) para obter uma descrição das sessões roteamento que precisa ser configurada para facilitar a conectividade.

>[AZURE.NOTE] Microsoft não oferece suporte a qualquer protocolo de redundância de roteador (por exemplo, HSRP, VRRP) para configurações de alta disponibilidade. Contamos com um par redundante de sessões BGP por correspondência para alta disponibilidade.

## <a name="ip-addresses-used-for-peerings"></a>Endereços IP usados para peerings

Você precisa reservar alguns blocos de endereços IP para configurar o roteamento entre sua rede e roteadores de borda (MSEEs) corporativos da Microsoft. Esta seção fornece uma lista de requisitos e descreve as regras sobre como esses endereços IP devem ser adquiridos e usados.

### <a name="ip-addresses-used-for-azure-private-peering"></a>Endereços IP usados para correspondência particular Azure

Você pode usar endereços IP particulares ou endereços IP públicos para configurar o peerings. O intervalo de endereços usado para configurar rotas não deve sobrepor os intervalos de endereços usados para criar redes virtuais no Azure. 

 - Você deve reservar um /29 sub-rede ou sub-redes de dois /30 para interfaces de roteamento.
 - As sub-redes usadas para roteamento podem ser endereços IP particulares ou endereços IP públicos.
 - As sub-redes não devem ter conflito com o intervalo reservado pelo cliente para uso em nuvem da Microsoft.
 - Se um /29 sub-rede é usado, ele será dividido em duas /30 sub-redes. 
     - A primeira /30 sub-rede será usado para o link principal e o segundo/sub-rede 30 serão usadas para o link secundário.
     - Para cada um da /30 sub-redes, você deve usar o primeiro endereço IP do /30 sub-rede em seu roteador. A Microsoft usará o segundo endereço IP do /30 sub-rede para configurar uma sessão BGP.
     - Você deve configurar ambas as sessões de BGP para nosso [disponibilidade SLA](https://azure.microsoft.com/support/legal/sla/) válido.  

#### <a name="example-for-private-peering"></a>Exemplo de correspondência particular

Se você optar por usar a.b.c.d/29 para configurar a correspondência, ele será dividido em duas /30 sub-redes. No exemplo abaixo, veremos como a sub-rede a.b.c.d/29 é usada. 

a.b.c.d/29 serão dividir a.b.c.d/30 e a.b.c.d+4/30 e passada à Microsoft por meio das APIs de provisionamento. Você usará a.b.c.d+1 como o IP VRF para o PE primário e Microsoft irá consumir a.b.c.d+2 como o IP VRF a MSEE principal. Você usará a.b.c.d+5 como o IP VRF para o PE secundário e Microsoft usará a.b.c.d+6 como o IP VRF para o MSEE secundário.

Considere a possibilidade de um caso onde você selecionar 192.168.100.128/29 configurar correspondência particular. 192.168.100.128/29 inclui endereços de 192.168.100.128 a 192.168.100.135, entre os quais:

- 192.168.100.128/30 será atribuída aos link1, com o provedor usando 192.168.100.129 e Microsoft usando 192.168.100.130.
- 192.168.100.132/30 será atribuída aos link2, com o provedor usando 192.168.100.133 e Microsoft usando 192.168.100.134.

### <a name="ip-addresses-used-for-azure-public-and-microsoft-peering"></a>Endereços IP usados para públicas Azure e Microsoft correspondência

Você deve usar endereços IP públicos que você possui para configurar as sessões BGP. Microsoft deve ser capaz de verificar a propriedade dos endereços IP por meio de roteamento de registros de Internet e Internet roteamento de registros. 

- Você deve usar um exclusivo/29 sub-rede ou sub-redes de dois /30 para configurar o correspondências para cada correspondência por rota expressa de BGP circuito (se você tiver mais de um). 
- Se um /29 sub-rede é usado, ele será dividido em duas /30 sub-redes. 
    - A primeira /30 sub-rede será usado para o link principal e o segundo/sub-rede 30 serão usadas para o link secundário.
    - Para cada um da /30 sub-redes, você deve usar o primeiro endereço IP do /30 sub-rede em seu roteador. A Microsoft usará o segundo endereço IP do /30 sub-rede para configurar uma sessão BGP.
    - Você deve configurar ambas as sessões de BGP para nosso [disponibilidade SLA](https://azure.microsoft.com/support/legal/sla/) válido.

## <a name="public-ip-address-requirement"></a>Requisito de endereço IP público 

### <a name="private-peering"></a>Correspondência particular 

Você pode optar por usar endereços IPv4 do públicos ou privados para correspondência particular. Fornecemos isolamento de ponta a ponta do tráfego para que sobreposição de endereços com outros clientes não é possível no caso de correspondência particular. Esses endereços não são anunciados à Internet. 

### <a name="public-peering"></a>Correspondência pública

O caminho de correspondência público Azure permite que você se conectar a todos os serviços hospedados no Azure sobre seus endereços IP públicos. Eles incluem serviços listados nas [Perguntas Frequentes de ExpessRoute](expressroute-faqs.md) e qualquer serviços hospedados por ISVs no Microsoft Azure. Conectividade aos serviços do Microsoft Azure na correspondência público é sempre iniciada da sua rede na rede do Microsoft. Você deve usar endereços IP públicos para o tráfego destinado à rede da Microsoft.

### <a name="microsoft-peering"></a>Microsoft correspondência

O caminho de correspondência do Microsoft permite que você se conectar aos serviços de nuvem da Microsoft que não são compatíveis com o caminho de correspondência público Azure. A lista de serviços inclui serviços do Office 365, como o Exchange Online, SharePoint Online, Skype for Business e CRM Online. A Microsoft oferece suporte bidirecional conectividade a correspondência da Microsoft. Tráfego destinado aos serviços de nuvem da Microsoft deve usar endereços IPv4 públicos válidos antes de entrarem na rede da Microsoft.

Certifique-se de que seu endereço de IP e como número são registrados em um dos registros do listadas abaixo.

- [ARIN](https://www.arin.net/)
- [APNIC](https://www.apnic.net/)
- [AFRINIC](https://www.afrinic.net/)
- [LACNIC](http://www.lacnic.net/)
- [RIPENCC](https://www.ripe.net/)
- [RADB](http://www.radb.net/)
- [ALTDB](http://altdb.net/)

>[AZURE.IMPORTANT] Endereços IP públicos anunciados à Microsoft sobre rota expressa não devem ser anunciados à Internet. Isso pode quebrar conectividade com outros serviços da Microsoft. No entanto, endereços IP públicos usados por servidores em sua rede que se comunicar com pontos de extremidade do Office 365 dentro da Microsoft podem ser anunciados pela rota expressa. 

## <a name="dynamic-route-exchange"></a>Exchange rota dinâmico

Exchange roteamento será sobre protocolo eBGP. Sessões EBGP são estabelecidas entre os MSEEs e seus roteadores. Autenticação de sessões BGP não é um requisito. Se necessário, um hash MD5 pode ser configurado. Consulte o [roteamento de configurar](expressroute-howto-routing-classic.md) e [fluxos de trabalho e estados de circuito de provisionamento de circuito](expressroute-workflows.md) para obter informações sobre como configurar sessões de BGP.

## <a name="autonomous-system-numbers"></a>Números de sistema autônomos

Microsoft usará AS 12076 Azure pública, privada do Azure e Microsoft correspondência. Podemos reservou ASNs de 65515 para 65520 para uso interno. 16 e 32 bits como números são suportados.

Não existem requisitos em torno de simetria de transferência de dados. Os caminhos de Avançar e retorno podem percorrer pares de roteador diferente. Rotas idênticas devem ser anunciadas de qualquer um dos lados entre vários pares de circuito pertencente a você. Métrica não precisam ser idênticos.

## <a name="route-aggregation-and-prefix-limits"></a>Limites de prefixo e de agregação de roteiro

Oferecemos suporte até 4000 prefixos anunciados por meio do Azure correspondência particular. Isso pode ser aumentado até 10.000 prefixos se o complemento de premium rota expressa está ativado. Podemos aceitar até 200 por sessão BGP para Azure público de unidades e prefixos Microsoft correspondência. 

A sessão BGP será ignorada se o número de prefixos exceder o limite. Podemos aceitará rotas padrão em somente o link de correspondência particular. Provedor deve filtrar rota padrão e endereços IP privados (RFC 1918) do Azure público e caminhos de correspondência da Microsoft. 

## <a name="transit-routing-and-cross-region-routing"></a>Trânsito entre região roteamento e

Rota expressa não pode ser configurada como roteadores de trânsito. Você precisará confiar no seu provedor de conectividade para serviços de roteamento de trânsito.

## <a name="advertising-default-routes"></a>Rotas de padrão de anúncios

Rotas padrão são permitidas apenas em Azure sessões aos particulares. Nesse caso, estamos encaminhará todo o tráfego de redes virtuais associados à sua rede. Anunciar rotas padrão em particular correspondência resultará no caminho da internet do Azure sejam bloqueado. Você deve confiar em sua borda corporativa para rotear o tráfego de e para a internet para serviços hospedados no Azure. 

 Para habilitar a conectividade com outros serviços do Azure e serviços de infraestrutura, você deve verificar se um dos seguintes itens está no lugar:

 - Correspondência de público Azure está ativado para rotear o tráfego para pontos de extremidade públicos
 - Use o roteamento de definidas pelo usuário para permitir conectividade com a internet para cada sub-rede que exigem conectividade com a Internet.
 
>[AZURE.NOTE] Anunciar rotas padrão interromperá Windows e outras licenças de máquina virtual. Siga as instruções [aqui](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) para contornar esse problema.

## <a name="support-for-bgp-communities-preview"></a>Suporte para comunidades BGP (prévia)


Esta seção fornece uma visão geral de como comunidades BGP serão usadas com rota expressa. Microsoft anunciará rotas nos público e caminhos de correspondência da Microsoft com rotas marcadas com valores de comunidade apropriado. A lógica para fazê-lo e os detalhes em valores de comunidade estão descritos abaixo. Microsoft, no entanto, não aceita a quaisquer valores de comunidade marcados rotas anunciado à Microsoft.

Se você estiver se conectando à Microsoft por meio de rota expressa em qualquer um dos locais aos dentro de uma região geopolíticas, você terá acesso a todos os serviços de nuvem da Microsoft em todas as regiões dentro do limite geopolíticas. 

Por exemplo, se você estiver conectado à Microsoft no Amsterdam por meio de rota expressa, você terá acesso a todos os serviços de nuvem da Microsoft hospedado na Europa do Norte e Europa Ocidental. 

Consulte a página de [parceiros de rota expressa e locais de correspondência](expressroute-locations.md) para uma lista detalhada de regiões geopolíticas, regiões Azure associadas e rota expressa correspondente correspondência locais.

Você pode adquirir mais de um circuito de rota expressa por região geopolíticas. Ter várias conexões oferece benefícios significativos na alta disponibilidade devido a redundância geográfica. Em casos em que você tiver vários circuitos rota expressa, você receberá o mesmo conjunto de prefixos anunciado da Microsoft sobre a correspondência pública e Microsoft caminhos de correspondência. Isso significa que você terá vários caminhos da sua rede para o Microsoft. Isso pode causar decisões de roteamento ideais a ser feita em sua rede. Como resultado, você pode enfrentar experiências de conectividade ideal para diferentes serviços. 

Microsoft será marca prefixos anunciados por meio de correspondência pública e Microsoft correspondência com valores de comunidade BGP apropriados indicando que a região os prefixos estão hospedados. Você pode confiar nos valores da comunidade para tomar decisões de roteamento apropriadas para oferecer [o roteamento ideal para os clientes](expressroute-optimize-routing.md).

| **Região geopolíticas** | **Região do Microsoft Azure** | **Valor de comunidade BGP** |
|---|---|---|
| **América do Norte** |    |  |
|    | Leste EUA | 12076:51004 |
|    | Leste dos EUA 2 | 12076:51005 |
|    | Oeste EUA | 12076:51006 |
|    | Oeste EUA 2 | 12076:51026 |
|    | Centro-Oeste dos EUA | 12076:51027 |
|    | Centro Norte dos EUA | 12076:51007 |
|    | Centro Sul dos EUA | 12076:51008 |
|    | Centro dos EUA | 12076:51009 |
|    | Canadá Central | 12076:51020 |
|    | Canadá Leste | 12076:51021 |
| **América do Sul** |  |  |
|    | Brasil Sul | 12076:51014 |
| **Europa** |    |  |
|    | Norte da Europa | 12076:51003 |
|    | Europa Ocidental | 12076:51002 |
| **Pacífico** |    |   |
|    | Da Ásia Oriental | 12076:51010 |
|    | Sudeste Asiático | 12076:51011 |
| **Japão** |     |   |
|    | Japão Leste | 12076:51012 |
|    | Japão Oeste | 12076:51013 |
| **Austrália** |    |   | 
|    | Austrália Oriental | 12076:51015 |
|    | Austrália Sudeste | 12076:51016 |
| **Índia** |    |   |
|    | Índia Sul | 12076:51019 |
|    | Índia Oeste | 12076:51018 |
|    | Índia Central | 12076:51017 |

Todas as rotas anunciadas da Microsoft serão marcadas com o valor de comunidade apropriado. 

>[AZURE.IMPORTANT] Prefixos globais serão marcados com um valor de comunidade apropriado e serão anunciados somente quando a rota expressa premium complemento está ativado.


Além dos acima, Microsoft também será marca prefixos baseados no serviço do qual pertencem. Isso só se aplica a correspondência da Microsoft. A tabela a seguir fornece um mapeamento de serviço para o valor de comunidade BGP.

| **Serviço** | **Valor de comunidade BGP** |
|---|---|
| **Exchange** | 12076:5010 |
| **SharePoint** | 12076:5020 |
| **Skype For Business** | 12076:5030 |
| **CRM Online** | 12076:5040 |
| **Outros serviços do Office 365** | 12076:5100 |

>[AZURE.NOTE] Microsoft não honra quaisquer valores de comunidade BGP definido por você em rotas anunciadas à Microsoft.

## <a name="next-steps"></a>Próximas etapas

- Configure sua conexão de rota expressa.

    - [Criar um circuito de rota expressa para o modelo de implantação clássico](expressroute-howto-circuit-classic.md) ou [criar e modificar um circuito de rota expressa usando o Gerenciador de recursos do Azure](expressroute-howto-circuit-arm.md)
    - [Configurar o roteamento para o modelo de implantação clássico](expressroute-howto-routing-classic.md) ou [Configurar o roteamento para o modelo de implantação do Gerenciador de recursos](expressroute-howto-routing-arm.md)
    - [Vincular um VNet clássico para um circuito de rota expressa](expressroute-howto-linkvnet-classic.md) ou [vincular um VNet do Gerenciador de recursos para um circuito de rota expressa](expressroute-howto-linkvnet-arm.md)


