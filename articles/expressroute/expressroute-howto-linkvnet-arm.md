<properties 
   pageTitle="Vincular uma rede virtual a um circuito de rota expressa usando o PowerShell | Microsoft Azure"
   description="Este documento fornece uma visão geral de como vincular redes virtuais (VNets) a rota expressa circuitos usando o modelo de implantação do Gerenciador de recursos e PowerShell."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr" />

# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Vincular uma rede virtual a um circuito de rota expressa

> [AZURE.SELECTOR]
- [Portal Azure - Gerenciador de recursos](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Gerenciador de recursos](expressroute-howto-linkvnet-arm.md)
- [PowerShell - clássico](expressroute-howto-linkvnet-classic.md)


Este artigo ajudará você a vincular redes virtuais (VNets) a rota expressa do Azure circuitos usando o modelo de implantação do Gerenciador de recursos e PowerShell. Redes virtuais podem ser na mesma assinatura ou parte de outra assinatura.

**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração

- Você precisa da versão mais recente dos módulos do PowerShell do Azure (pelo menos versão 1.0). Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.
- Você precisa analisar os [pré-requisitos](expressroute-prerequisites.md), [requisitos de roteamento](expressroute-routing.md)e [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
- Você deve ter um circuito rota expressa ativo. 
    - Siga as instruções para [criar um circuito de rota expressa](expressroute-howto-circuit-arm.md) e ter o circuito habilitado pelo seu provedor de conectividade. 
    - Certifique-se de que você tenha correspondência particular Azure configurado para seu circuito. Consulte o artigo [Configurar o roteamento](expressroute-howto-routing-arm.md) para instruções de circulação. 
    - Certifique-se de que correspondência particular Azure está configurado e a correspondência de BGP entre sua rede e da Microsoft para cima para que você pode habilitar a conectividade de ponta a ponta.
    - Certifique-se de que você tem uma rede virtual e um gateway de rede virtual criado e totalmente provisionado. Siga as instruções para criar um [gateway VPN](../articles/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), mas certifique-se de usar `-GatewayType ExpressRoute`.

Você pode vincular até 10 redes virtuais a um circuito de rota expressa padrão. Todas as redes virtuais devem ser na mesma região geopolíticas quando usando um circuito rota expressa padrão. 

Você pode vincular um redes virtuais fora da região geopolíticas do circuito rota expressa ou conectar um número maior de redes virtuais ao seu circuito rota expressa se você ativou o complemento premium rota expressa. Verifique as [perguntas Frequentes](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Conectar uma rede virtual na mesma assinatura a um circuito

Você pode se conectar a um gateway de rede virtual para um circuito de rota expressa usando o cmdlet a seguir. Certifique-se de que o gateway de rede virtual é criado e está pronto para a vinculação antes de executar o cmdlet:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Conectar uma rede virtual em uma assinatura diferente a um circuito

Você pode compartilhar um circuito de rota expressa em várias assinaturas. A figura a seguir mostra um simples esquemáticos de como funciona o compartilhamento para circuitos rota expressa em várias assinaturas.

Cada um das nuvens menores dentro da nuvem grande é usada para representar assinaturas que pertencem a diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização pode usar sua próprias assinatura para implantar os serviços, mas eles podem compartilhar um único circuito de rota expressa para conectar-se novamente à sua rede local. Um único departamento (neste exemplo: IT) podem ter o circuito rota expressa. Outras assinaturas dentro da organização podem usar o circuito rota expressa.

>[AZURE.NOTE] Encargos de conectividade e largura de banda para o circuito dedicado serão aplicados para o proprietário de circuito rota expressa. Todas as redes virtuais compartilham a mesma largura de banda.

![Conectividade entre-assinatura](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administração

O *proprietário de circuito* é um usuário de energia autorizados do recurso circuito rota expressa. O proprietário de circuito pode criar autorizações que podem ser trocadas pelos *usuários de circuito*. *Usuários de circuito* são proprietários de gateways de rede virtual (que não estão na mesma assinatura como o circuito rota expressa). *Usuários de circuito* podem resgatar autorizações (uma autorização por rede virtual).

O *proprietário de circuito* tem a capacidade de modificar e revogar autorizações a qualquer momento. Revogando um resultados de autorização em todas as conexões de link está sendo excluídos da assinatura cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações de proprietário de circuito 

#### <a name="creating-an-authorization"></a>Criando uma autorização
    
O proprietário de circuito cria uma autorização. Isso resulta na criação de uma chave de autorização que pode ser usada por um usuário de circuito conectem seus gateways de rede virtual para o circuito rota expressa. Uma autorização é válida para apenas uma conexão.

O trecho de cmdlet a seguir mostra como criar uma autorização:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

        $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
        

A resposta a esta conterá a chave de autorização e o status:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded

        

#### <a name="reviewing-authorizations"></a>Revisando autorizações

O proprietário de circuito pode examinar todas as autorizações que são emitidas em um determinado circuito executando o seguinte cmdlet:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
    

#### <a name="adding-authorizations"></a>Adicionando autorizações

O proprietário de circuito pode adicionar autorizações usando o seguinte cmdlet:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    
    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit

    
#### <a name="deleting-authorizations"></a>Excluindo autorizações

O proprietário de circuito pode revogar/excluir autorizações ao usuário executando o seguinte cmdlet:

    Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit    

### <a name="circuit-user-operations"></a>Operações de usuário de circuito

O usuário de circuito precisa a ID de ponto e uma chave de autorização do proprietário circuito. A chave de autorização é um GUID.

ID de ponto for, pode ser verificado do comando a seguir.

    Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

#### <a name="redeeming-connection-authorizations"></a>Recuperando autorizações de conexão

O usuário de circuito pode executar o seguinte cmdlet para resgatar uma autorização de link:

    $id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"  
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### <a name="releasing-connection-authorizations"></a>Liberar autorizações de conexão

Você pode liberar uma autorização excluindo a conexão que vincula o circuito rota expressa para a rede virtual.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a rota expressa, consulte as [Perguntas Frequentes de rota expressa](expressroute-faqs.md).
