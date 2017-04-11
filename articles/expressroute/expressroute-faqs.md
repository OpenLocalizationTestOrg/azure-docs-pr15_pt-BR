<properties
   pageTitle="Rota expressa perguntas Frequentes"
   description="Perguntas frequentes sobre o rota expressa contém informações sobre serviços de suporte do Azure, custo, dados e conexões, SLA, provedores e locais, largura de banda e detalhes técnicos adicionais."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="expressroute-faq"></a>Rota expressa perguntas Frequentes


## <a name="what-is-expressroute"></a>O que é a rota expressa?
Rota expressa é um serviço Azure que permite que você crie conexões privadas entre data centers Microsoft e infraestrutura que está em seu local ou em um recurso de colocação. Rota expressa conexões não vá na Internet pública e oferecer maior segurança, a confiabilidade e velocidades com latências mais conexões típicas pela Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Quais são os benefícios de usar conexões de rede privada e rota expressa?
Rota expressa conexões não vá na Internet pública e oferecer maior segurança, a confiabilidade e velocidades com latências inferiores e consistentes mais conexões típicas pela Internet. Em alguns casos, usando conexões de rota expressa para transferir dados entre locais dispositivos e Azure pode gerar benefícios de custo significativas.

### <a name="what-microsoft-cloud-services-are-supported-over-expressroute"></a>Quais serviços de nuvem da Microsoft são suportados pela rota expressa?
Rota expressa é compatível com a maioria dos serviços do Microsoft Azure, incluindo hoje o Office 365.  Procure por atualizações em disponibilidade geral em breve.

### <a name="where-is-the-service-available"></a>Onde o serviço está disponível?
Consulte esta página para disponibilidade e localização de serviço: [locais e parceiros de rota expressa](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Como posso usar rota expressa para se conectar à Microsoft, se não tiver o parceiros com um dos parceiros rota expressa-carrier?
Você pode selecionar uma operadora regional e será levado conexões Ethernet a um do exchange suportado locais do provedor. Você pode, em seguida, ponto com a Microsoft no local do provedor. Verificar a última seção de [locais e parceiros de rota expressa](expressroute-locations.md) para ver se o seu provedor de serviço está presente em qualquer um dos locais de exchange. Em seguida, você pode solicitar um circuito de rota expressa por meio do provedor de serviço para se conectar ao Azure.

### <a name="how-much-does-expressroute-cost"></a>Quanto o custo de rota expressa?
Verificar [preços detalhes](https://azure.microsoft.com/pricing/details/expressroute/) para informações sobre preços.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Se eu pagar por um circuito de rota expressa de uma determinada largura de banda, a conexão VPN para compra do meu provedor de serviço de rede precisa ser a mesma velocidade?
Não. Você pode adquirir uma conexão VPN de qualquer velocidade de seu provedor de serviço. No entanto, sua conexão ao Azure serão limitado a largura de banda de circuito de rota expressa que você comprar.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-required"></a>Se eu pagar por um circuito de rota expressa de uma determinada largura de banda, eu tenho a capacidade de intermitente até velocidades mais altas, se necessário?
Sim. Rota expressa circuitos são configurados para oferecer suporte a casos onde você pode intermitente até duas vezes o limite de largura de banda adquiridas por nenhum custo adicional. Verifique com seu provedor de serviços se eles oferecem suporte a esse recurso.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Posso usar a mesma conexão de rede privada com outros serviços do Azure e de rede Virtual simultaneamente?
Sim. Um circuito de rota expressa, uma vez configuração permitirá que você acesse serviços dentro de uma rede virtual e outros serviços do Azure simultaneamente. Você se conecta para redes virtuais sobre o caminho de correspondência particular e outros serviços no caminho aos público.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Rota expressa oferecem um contrato de nível de serviço (SLA)?
Consulte a [página de SLA rota expressa](https://azure.microsoft.com/support/legal/sla/) para obter mais informações.

## <a name="supported-services"></a>Serviços com suporte
Há suporte para serviços mais Azure sobre rota expressa.

- Conectividade com máquinas virtuais e serviços de nuvem implantados em redes virtuais são suportados no caminho aos particular.
- Sites Azure são suportados no caminho aos público.
- Hub de IoT é suportada no caminho aos público.
- O Office 365 é suportado no caminho de correspondência da Microsoft.
- Todos os outros serviços são acessíveis no caminho aos público. As exceções são da seguinte maneira.

    **Não há suporte para os seguintes serviços:**

    - CDN
    - Testes de carga de serviços de equipe do Visual Studio
    - Autenticação multifator
    - Gerenciador de tráfego

## <a name="data-and-connections"></a>Dados e conexões

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Existem limita a quantidade de dados que eu pode transferir usando rota expressa?
Não podemos definir um limite na quantidade de transferência de dados. Consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter informações sobre as taxas de largura de banda.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Quais velocidades de conexão são suportadas pelo rota expressa?
Há suporte para ofertas de largura de banda:

| 50 Mbps, 100 Mbps, 200Mbps, 500 Mbps, 1Gbps, 2 Gbps, 5 Gbps, 10 Gbps |

### <a name="which-service-providers-are-available"></a>Os provedores de serviços estão disponíveis?
Consulte [locais e parceiros de rota expressa](expressroute-locations.md) para a lista de provedores de serviços e locais.

## <a name="technical-details"></a>Detalhes técnicos

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Quais são os requisitos técnicos para conectar meu local ao Azure?
Consulte a [página de pré-requisitos de rota expressa](expressroute-prerequisites.md) para requisitos.

### <a name="are-connections-to-expressroute-redundant"></a>Conexões rota expressa são redundantes?
Sim. Cada circuito via expressa tem um par redundante de cruzada conexões configurado para oferecer alta disponibilidade.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Posso perderá conectividade se um dos meus links rota expressa falhar?
Você não perderá conectividade se uma das conexões entre falhar. Uma conexão redundante está disponível para dar suporte a carga de sua rede. Além disso, você pode criar vários circuitos em um local diferente de correspondência para obter resiliência de falha.

### <a name="onep2plink"></a>Se eu não sou localizado em uma troca de nuvem e meu provedor de serviços oferece conexão ponto a ponto, eu preciso para solicitar duas conexões físicas entre minha rede local e o Microsoft? 
Não, você só precisará de uma conexão física se seu provedor de serviços pode estabelecer dois circuitos virtuais Ethernet sobre a conexão física. A conexão física (por exemplo, uma fibra óptico) é encerrado em uma camada 1 (L1) dispositivo (consulte a imagem abaixo). Os dois circuitos virtuais de Ethernet estão marcados com diferentes identificações de VLAN, um para o circuito principal e outro para o secundário. Esses IDs de VLAN estão em 802.1 q externa cabeçalho Ethernet. 802.1 q interna cabeçalho Ethernet (não mostrado) é mapeado para um determinado [domínio de roteamento rota expressa](expressroute-circuit-peerings.md). 

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)


### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Posso estender uma das minhas VLANs no Azure usando rota expressa?
Não. Não há suporte para extensões de conectividade 2 camada no Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Pode ter mais de um circuito de rota expressa em minha assinatura?
Sim. Você pode ter mais de um circuito de rota expressa em sua assinatura. O limite padrão no número de circuitos dedicados está definido como 10. Você pode contatar o Microsoft Support para aumentar o limite, se necessário.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Posso ter circuitos rota expressa provedores de serviços diferente?
Sim. Você pode ter rota expressa circuitos com muitos provedores de serviço. Cada circuito rota expressa será associado um provedor de serviços apenas.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Como eu conecto minha redes virtuais a um circuito de rota expressa
As etapas básicas estão descritas abaixo.

- Estabelecer um circuito de rota expressa e deve ter o provedor de serviços ativá-lo.
- Você ou o provedor deve configurar o BGP correspondência (s).
- Você deve vincular a rede virtual ao circuito rota expressa.

Consulte [fluxos de trabalho de rota expressa para provisionamento de circuito e estados de circuito](expressroute-workflows.md) para obter mais informações.

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Existem limites de conectividade para minha circuito rota expressa?
Sim. Página de [locais e parceiros de rota expressa](expressroute-locations.md) fornece uma visão geral sobre os limites de conectividade para um circuito de rota expressa. Conectividade para um circuito de rota expressa está limitada a uma única região geopolíticas. Conectividade pode ser expandida para cross regiões geopolíticas habilitando o recurso de premium rota expressa.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Pode vincular a mais de uma rede virtual a um circuito de rota expressa?
Sim. Você pode vincular até 10 redes virtuais a um circuito de rota expressa.

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Eu tenho várias assinaturas Azure que contêm redes virtuais. Posso me conectar redes virtuais que estão em assinaturas diferentes para um único circuito de rota expressa?
Sim. Você pode autorizar até 10 outras assinaturas Azure para usar um único circuito de rota expressa. Esse limite pode ser aumentado habilitando o recurso de premium rota expressa.

Para obter mais detalhes, consulte [compartilhando um circuito de rota expressa em várias assinaturas](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Redes virtuais estão conectadas ao mesmo circuito isolado uns dos outros?
Não. Todas as redes virtuais vinculadas ao mesmo circuito rota expressa fazem parte do mesmo domínio roteamento e não são isoladas entre si de uma perspectiva de roteamento. Se você precisar isolamento de roteiro, você precisará criar um circuito rota expressa separado.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Posso ter uma rede virtual conectada a mais de uma rota expressa circuito?
Sim. Você pode vincular a uma única rede virtual com até 4 circuitos de rota expressa. Eles devem ser solicitados por meio de 4 diferentes [rota expressa locais](expressroute-locations.md).

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Pode acessar a internet da minha redes virtuais conectadas a rota expressa circuitos?
Sim. Se você não tiver anunciado rotas padrão (0.0.0.0/0) ou prefixos de rota de internet através da sessão BGP, você poderá se conectar à internet de uma rede virtual vinculada a um circuito de rota expressa.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Conectividade com a internet redes virtuais conectados a rota expressa circuitos pode bloquear?
Sim. Você pode anunciar rotas padrão (0.0.0.0/0) para bloquear todos os conectividade com a internet máquinas virtuais implantadas dentro de uma rede virtual e rotear todo o tráfego de check-out por meio de circuito a rota expressa. Observe que, se você anunciar rotas padrão, podemos irá forçar tráfego aos serviços oferecidos sobre público voltar aos (como armazenamento do Azure e SQL DB) para o seu local. Você precisará configurar seus roteadores para retornar o tráfego ao Azure através do caminho de correspondência público ou pela internet.

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Redes virtuais vinculadas ao mesmo circuito rota expressa podem conversar com os outros?
Sim. Máquinas virtuais implantadas em redes virtuais conectados ao mesmo circuito rota expressa podem se comunicar com os outros.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Pode usar-to-site conectividade para redes virtuais em conjunto com rota expressa?
Sim. Rota expressa pode coexistir com VPNs site-to-site.

### <a name="can-i-move-a-virtual-network-from-site-to-site--point-to-site-configuration-to-use-expressroute"></a>Pode mover uma rede virtual da configuração-to-site / ponto-a-site para usar a rota expressa?
Sim. Você precisará criar um gateway rota expressa dentro de sua rede virtual. Haverá um tempo de inatividade pequeno associado com o processo.

### <a name="what-do-i-need-to-connect-to-azure-storage-over-expressroute"></a>O que eu preciso se conectar ao armazenamento do Azure pela rota expressa?
Você deve estabelecer um circuito de rota expressa e configurar rotas para correspondência público.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Há limites no número de rotas que posso pode anunciar?
Sim. Podemos aceitar até 4000 prefixos de rota de correspondência particular e 200 para correspondência pública e Microsoft correspondência. Você pode aumentar esse 10.000 rotas para correspondência particular se você habilitar o recurso de premium rota expressa.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Existem restrições sobre intervalos de IP que posso pode anunciar sobre a sessão BGP?
Podemos não aceitam prefixos particulares (RFC1918) da sessão de BGP aos pública e Microsoft.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>O que acontece se eu exceder o BGP limita?
Sessões de BGP serão ignoradas. Elas serão redefinidas após a contagem de prefixo vai abaixo do limite.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>O que é o tempo de espera ExpressRoute BGP? Pode ser ajustado?
O tempo de espera é 180. As mensagens de manutenção de funcionamento são enviadas a cada 60 segundos. Estes são corrigidos configurações no lado de Microsoft não pode ser alterado.

### <a name="after-i-advertise-the-default-route-00000-to-my-virtual-networks-i-cant-activate-windows-running-on-my-azure-vms-how-to-i-fix-this"></a>Depois de eu anunciar a rota padrão (0.0.0.0/0) à minha redes virtuais, para não é possível ativar o Windows em execução no meu VMs do Azure. Como a para corrigir isso?
As etapas a seguir ajudará Azure reconhece a solicitação de ativação:

1. Estabelece a correspondência pública para seu circuito rota expressa.
2. Realizar uma pesquisa DNS e localizar o endereço IP do **kms.core.windows.net**
3. Siga um dos dois itens a seguir para que o serviço de gerenciamento de chaves reconhecerá que a solicitação de ativação vem do Azure e aceita a solicitação.
    - Na sua rede local, rotear o tráfego destinado para o endereço IP (obtido na etapa 2) voltar ao Azure via a correspondência público.
    - Seu pin de forma do provedor NSP têm o tráfego de volta ao Azure via a correspondência público.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Posso alterar a largura de banda de um circuito de rota expressa?
Sim. Você pode aumentar a largura de banda de um circuito de rota expressa sem precisar subdividi-lo. Você terá de acompanhamento com seu provedor de conectividade para garantir que elas sejam atualizadas as limitações em suas redes para oferecer suporte ao aumento de largura de banda. No entanto não será capaz de reduzir a largura de banda de um circuito de rota expressa. Precisar inferior que a largura de banda significará que um desgastado para baixo e a recriação de um circuito de rota expressa.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Como posso alterar a largura de banda de um circuito de rota expressa?
Você pode atualizar a largura de banda do circuito rota expressa usando o cmdlet update dedicada circuito API e PowerShell.

## <a name="expressroute-premium"></a>Rota expressa Premium

### <a name="what-is-expressroute-premium"></a>O que é a rota expressa premium?
Rota expressa premium é uma coleção de recursos listados abaixo.

 - Aumentado roteamento limite de tabela de 4000 rotas para 10.000 rotas para correspondência particular.
 - Maior número de VNets que podem ser conectados ao circuito rota expressa (o padrão é 10). Consulte a tabela abaixo para obter mais detalhes.
 - Conectividade global através da rede de núcleo da Microsoft. Agora, você poderá vincular um VNet em uma região geopolíticas com um circuito de rota expressa em outra região. **Exemplo:** Você pode vincular um VNet criada na Europa Ocidental para um circuito de rota expressa criado no Vale do Silício.
 - Conectividade aos serviços do Office 365 e CRM Online.

### <a name="how-many-vnets-can-i-link-to-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a>Quantos VNets pode vincular a um circuito de rota expressa se eu habilitado rota expressa premium?
As tabelas a seguir mostram os limites de rota expressa e do número de VNets por rota expressa circuito.


[AZURE.INCLUDE [expressroute-limits](../../includes/expressroute-limits.md)]


### <a name="how-do-i-enable-expressroute-premium"></a>Como habilitar o rota expressa premium?
Rota expressa os recursos premium podem ser habilitados quando o recurso é habilitado e pode ser desligado atualizando o estado de circuito. Você pode habilitar a rota expressa premium no momento da criação de circuito ou pode ligar para o circuito de atualização dedicada API / cmdlet do PowerShell para habilitar a rota expressa premium.

### <a name="how-do-i-disable-expressroute-premium"></a>Como posso desabilitar rota expressa premium?
Você pode desabilitar a rota expressa premium chamando a atualização dedicada circuito API / cmdlet do PowerShell você deve certificar-se de que você tenha dimensionada sua conectividade precisa atender os limites padrão antes de desabilitar rota expressa premium. Podemos falhará solicitação para desativar a rota expressa premium se escalas de utilização do além dos limites padrão.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Pode posso escolher os recursos que desejo para o conjunto de recursos premium?
Não. Você não poderá escolher os recursos que necessários. Podemos habilitar todos os recursos quando você ativar a rota expressa premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Quanto o custo do rota expressa premium?
Consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) de custo.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Pagar para premium rota expressa além dos encargos de rota expressa padrão?
Sim. Serão cobrados encargos de premium rota expressa na parte superior de tarifas de circuito de rota expressa e cobranças solicitadas pelo provedor de conectividade.

## <a name="expressroute-and-office-365-services-and-crm-online"></a>Rota expressa e serviços do Office 365 e CRM Online

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services-and-crm-online"></a>Como posso criar um circuito de rota expressa para se conectar aos serviços do Office 365 e CRM Online?

1. Revise a página de [página de pré-requisitos de rota expressa](expressroute-prerequisites.md) para garantir que você atende aos requisitos.
2. Examine a lista de provedores de serviços e localizações de [parceiros de rota expressa e locais](expressroute-locations.md) para garantir que suas necessidades de conectividade sejam atendidas.
3. Planeje seus requisitos de capacidade revisando o [planejamento de rede e ajuste de desempenho do Office 365](http://aka.ms/tune/).
4. Siga as etapas indicadas em fluxos de trabalho abaixo para conectividade [fluxos de trabalho de rota expressa para provisionamento de circuito e estados de circuito](expressroute-workflows.md)de instalação.

>[AZURE.IMPORTANT] Certifique-se de que você ativou complemento premium de rota expressa ao configurar a conectividade com o CRM Online e serviços do Office 365.

### <a name="do-i-need-to-enable-azure-public-peering-to-connect-to-office-365-services-and-crm-online"></a>Preciso habilitar Azure correspondência pública para se conectar aos serviços do Office 365 e CRM Online?
Não, você só precisa ativar Microsoft Peering. Tráfego de autenticação para Azure AD será enviado por meio de Microsoft Peering. 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-crm-online"></a>Meu circuitos rota expressa existentes podem oferecer suporte a conectividade com os serviços do Office 365 e CRM Online?
Sim. Seu circuito rota expressa existente pode ser configurado para oferecer suporte a conectividade com os serviços do Office 365. Certifique-se de que você tenha capacidade suficiente para se conectar aos serviços do Office 365 e certifique-se de que você ativou o complemento premium. [Planejamento de rede e ajuste de desempenho do Office 365](http://aka.ms/tune/) ajudará você a planejar suas necessidades de conectividade. Consulte também [criar e modificar um circuito de rota expressa](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Quais serviços podem ser acessados através de uma conexão de rota expressa do Office 365?

Consulte a página de [URLs do Office 365 e intervalos de endereços IP](http://aka.ms/o365endpoints) para uma lista atualizada dos serviços de suporte ao longo de rota expressa.

### <a name="how-much-does-expressroute-for-office-365-services-and-crm-online-cost"></a>Quanto o rota expressa para serviços do Office 365 e o custo CRM Online?
Serviços do Office 365 e CRM Online requer o complemento premium sejam habilitadas. A [página de detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) fornece detalhes de custos para rota expressa.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Quais regiões rota expressa para Office 365 é suportada no?
Consulte [locais e parceiros de rota expressa](expressroute-locations.md) para obter mais informações sobre a lista de parceiros e locais onde haja suporte para a rota expressa.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>É possível acessar Office 365 pela internet mesmo se rota expressa foi configurada para minha organização?
Sim. Pontos de extremidade do Office 365 serviço estão acessíveis através da internet, apesar de rota expressa foi configurada para sua rede. Se você estiver em um local que está configurado para se conectar aos serviços do Office 365 por meio de rota expressa, você se conecta por meio de rota expressa.

### <a name="can-dynamics-ax-online-be-accessed-over-an-expressroute-connection"></a>Podem Dynamics AX Online ser acessado por uma conexão de rota expressa?
Não, ela não é suportada.
