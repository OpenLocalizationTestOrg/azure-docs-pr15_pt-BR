<properties 
   pageTitle="Configurar túnel forçada para conexões de-to-Site usando o modelo de implantação clássico | Microsoft Azure"
   description="Como redirecionar ou 'force' todo o tráfego de Internet ligadas volta para seu local de local."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Configurar túnel forçada usando o modelo clássico de implantação

> [AZURE.SELECTOR]
- [PowerShell - clássico](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - Gerenciador de recursos](vpn-gateway-forced-tunneling-rm.md)

Túnel forçada permite redirecionar ou "forçar" todo o tráfego de Internet ligadas de volta para seu local por meio de um túnel de-to-Site VPN para inspeção e auditoria. Esse é um requisito de segurança crítica para a maioria dos enterprise IT políticas. 

Sem túnel forçada, o tráfego de Internet ligadas de suas VMs no Azure sempre será percorrer de infraestrutura de rede Azure diretamente check-out à Internet, sem a opção para permitir que você inspecionar ou o tráfego de auditoria. Acesso não autorizado de Internet potencialmente pode levar a divulgação de informações ou outros tipos de violações de segurança.

Este artigo apresentará Configurando forçado túnel para redes virtuais criadas usando o modelo de implantação clássico. 

**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Modelos de implantação e ferramentas para túnel forçada**

Uma conexão de túnel forçada pode ser configurado para o modelo de implantação clássico e o modelo de implantação do Gerenciador de recursos. Consulte a tabela a seguir para obter mais informações. Podemos atualizar esta tabela como novos artigos, modelos de implantação de novo e ferramentas adicionais se tornam disponíveis para esta configuração. Quando um artigo estiver disponível, podemos vincular diretamente a ele da tabela.

[AZURE.INCLUDE [vpn-gateway-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 


## <a name="requirements-and-considerations"></a>Requisitos e considerações

Túnel forçada no Azure está configurado via rotas definidas pelo usuário de rede virtual (UDR). Redirecionar o tráfego para um site local é expresso como uma rota padrão para o gateway VPN do Azure. A seção a seguir apresenta a limitação atual da tabela de roteamento e rotas para uma rede Virtual do Azure:


-  Cada sub-rede de rede virtual tem uma tabela de roteamento interna do sistema. A tabela de roteamento de sistema tem os seguintes três grupos de rotas:

    - **Rotas VNet local:** Diretamente para o destino VMs na mesma rede virtual
    
    - **Em rotas de local:** Ao gateway VPN do Azure
    
    - **Rota padrão:** Diretamente à Internet. Pacotes destinados para os endereços IP particulares não cobertos pelas duas rotas anteriores serão ignorados.


-  Com o lançamento de rotas definidas pelo usuário, você pode criar uma tabela de roteamento para adicionar uma rota padrão e, em seguida, associar a tabela de roteamento para sua subnet(s) VNet para habilitar o túnel forçada nessas sub-redes.

- Você precisa definir um "site padrão" entre sites locais entre locais conectado à rede virtual.

- Túnel forçada deve ser associada um VNet que possui um gateway VPN roteamento dinâmico (não um gateway estático).
 
- Rota expressa forçada túnel não está configurada via esse mecanismo, mas em vez disso, é habilitada por anunciando uma rota padrão via as sessões de correspondência ExpressRoute BGP. Consulte a [Documentação de rota expressa](https://azure.microsoft.com/documentation/services/expressroute/) para obter mais informações.



## <a name="configuration-overview"></a>Visão geral da configuração

No exemplo a seguir, o Frontend sub-rede não está sendo forçado encapsulado. As cargas de trabalho na sub-rede Frontend podem continuar a aceitar e responder a solicitações de cliente da Internet diretamente. As sub-redes intermediário e back-end são obrigadas encapsulado. As conexões de saída dessas duas sub-redes com a Internet serão forçados ou redirecionados para um site local por meio de um nos encapsulamentos VPN S2S.

Isso permite que você restringir e inspecionar acesso à Internet de suas máquinas virtuais ou serviços no Azure, em nuvem enquanto mantém a ativar sua arquitetura de vários níveis de serviço necessária. Você também pode aplicar túnel forçada para as redes virtuais inteiras se houver cargas de trabalho sem voltado para a Internet em suas redes virtuais.


![Forçado túnel](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)



## <a name="before-you-begin"></a>Antes de começar

Verifique se você tem os seguintes itens antes de iniciar a configuração.

- Uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, você pode ativar seus [benefícios de assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou sinal para cima, para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Uma rede virtual configurada. 

- A versão mais recente do cmdlets do PowerShell do Azure. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.


## <a name="configure-forced-tunneling"></a>Configurar túnel forçada

O procedimento a seguir o ajudarão a especificar túnel forçada para uma rede virtual. As etapas de configuração correspondem ao arquivo de configuração de rede VNet.



    <VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>

Neste exemplo, a rede virtual "Várias camadas-VNet" tem três sub-redes: sub-redes *Frontend*, *mercado intermediário*e *back-end* , com conexões de quatro locais cruzada: *DefaultSiteHQ*e três *ramificações*. 

As etapas serão definir o *DefaultSiteHQ* como a conexão de site padrão para forçado túnel e configurar o mercado intermediário e sub-redes de back-end para usar forçada túnel.


1. Crie uma tabela de roteamento. Use o cmdlet a seguir para criar sua tabela de rota.

        New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"

2. Adicione uma rota padrão à tabela de roteamento. 

    O exemplo a seguir adiciona uma rota padrão à tabela de roteamento criada na etapa 1. Observe que a única rota suportada é o prefixo de destino de "0.0.0.0/0" para o próximo salto "VPNGateway".
 
        Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway

3. Associe a tabela de roteamento para as sub-redes. 

    Depois de uma tabela de roteamento é criada e adicionada uma rota, use o exemplo a seguir para adicionar ou associar a tabela de rota a sub-rede VNet. O exemplo adiciona a tabela de rota "MyRouteTable" para as sub-redes mercado intermediário e back-end do VNet várias camadas-VNet.

        Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"

        Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"

4. Atribua um site padrão para forçado túnel. 

    Na etapa anterior, os scripts de exemplo do cmdlet criado a tabela de roteamento em a tabela de rota para duas das sub-redes VNet associada. A etapa restante é selecionar um site local entre as conexões de vários locais da rede virtual como o site padrão ou túnel.

        $DefaultSite = @("DefaultSiteHQ")
        Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"

## <a name="additional-powershell-cmdlets"></a>Cmdlets do PowerShell adicionais

### <a name="to-delete-a-route-table"></a>Para excluir uma tabela de roteiro

    Remove-AzureRouteTable -Name <routeTableName>

### <a name="to-list-a-route-table"></a>Lista de uma tabela de roteiro

    Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]

### <a name="to-delete-a-route-from-a-route-table"></a>Para excluir uma rota de uma tabela de roteiro

    Remove-AzureRouteTable –Name <routeTableName>

### <a name="to-remove-a-route-from-a-subnet"></a>Para remover uma rota de uma sub-rede

    Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Listar a tabela de rota associada a uma sub-rede
    
    Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Para remover um site padrão de um gateway VNet VPN

    Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>






