<properties
   pageTitle="Vincular uma rede virtual a um circuito de rota expressa usando o modelo clássico de implantação e PowerShell | Microsoft Azure"
   description="Este documento fornece uma visão geral de como vincular redes virtuais (VNets) a rota expressa circuitos usando o modelo clássico de implantação e PowerShell."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
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



Este artigo ajudará você a vincular redes virtuais (VNets) a rota expressa do Azure circuitos usando o modelo clássico de implantação e PowerShell. Redes virtuais podem ser na mesma assinatura ou podem fazer parte de outra assinatura.

**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração

1. É necessário a versão mais recente dos módulos do PowerShell do Azure. Você pode baixar os mais recentes módulos do PowerShell da seção PowerShell da [página de Downloads do Azure](https://azure.microsoft.com/downloads/). Siga as instruções em [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md) para obter orientações passo a passo sobre como configurar seu computador para usar os módulos do PowerShell do Azure.
2. Você precisa analisar os [pré-requisitos](expressroute-prerequisites.md), [requisitos de roteamento](expressroute-routing.md)e [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
3. Você deve ter um circuito rota expressa ativo.
    - Siga as instruções para [criar um circuito de rota expressa](expressroute-howto-circuit-classic.md) e fazer com que o seu provedor de conectividade habilitar o circuito.
    - Certifique-se de que você tenha correspondência particular Azure configurado para seu circuito. Consulte o artigo [Configurar o roteamento](expressroute-howto-routing-classic.md) para instruções de circulação.
    - Certifique-se de que correspondência particular Azure está configurado e a correspondência de BGP entre sua rede e da Microsoft para cima para que você pode habilitar a conectividade de ponta a ponta.
    - Você deve ter uma rede virtual e um gateway de rede virtual criado e totalmente provisionado. Siga as instruções para [Configurar uma rede virtual para rota expressa](expressroute-howto-vnet-portal-classic.md).

Você pode vincular até 10 redes virtuais a um circuito de rota expressa. Todas as redes virtuais devem estar na mesma região geopolíticas. Você pode vincular um número maior de virtuais redes ao seu circuito rota expressa, ou link virtual que estão em outras regiões geopolíticas se você ativou o complemento premium rota expressa. Verifique as [perguntas Frequentes](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Conectar uma rede virtual na mesma assinatura a um circuito

Você pode vincular a uma rede virtual a um circuito de rota expressa usando o cmdlet a seguir. Certifique-se de que o gateway de rede virtual é criado e está pronto para a vinculação antes de executar o cmdlet.

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
    Provisioned

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Conectar uma rede virtual em uma assinatura diferente a um circuito

Você pode compartilhar um circuito de rota expressa em várias assinaturas. A figura a seguir mostra um simples esquemáticos de como funciona o compartilhamento para circuitos rota expressa em várias assinaturas.

Cada um das nuvens menores dentro da nuvem grande é usada para representar assinaturas que pertencem a diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização pode usar sua próprias assinatura para implantar os serviços, mas os departamentos pode compartilhar um único circuito de rota expressa para conectar-se novamente à sua rede local. Um único departamento (neste exemplo: IT) podem ter o circuito rota expressa. Outras assinaturas dentro da organização podem usar o circuito rota expressa.

>[AZURE.NOTE] Encargos de conectividade e largura de banda para o circuito dedicado serão aplicados para o proprietário de circuito rota expressa. Todas as redes virtuais compartilham a mesma largura de banda.

![Conectividade entre-assinatura](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administração

O *proprietário de circuito* é o administrador/coadministrator da assinatura no qual o circuito rota expressa é criado. O proprietário de circuito pode autorizar administradores/coadministrators de outras assinaturas, chamadas de *usuários de circuito*, para usar o circuito dedicado que eles possuem. Os usuários de circuito que estão autorizados a usar circuito de rota expressa da organização podem vincular a rede virtual na sua assinatura ao circuito rota expressa depois que eles estão autorizados.

O proprietário de circuito tem a capacidade de modificar e revogar autorizações a qualquer momento. Revogar uma autorização resultará em todos os links que está sendo excluídos da assinatura cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações de proprietário de circuito

#### <a name="creating-an-authorization"></a>Criando uma autorização

O proprietário de circuito autoriza os administradores de outras assinaturas para usar o circuito especificado. No exemplo a seguir, o administrador do circuito (TI Contoso) permite que o administrador da outra assinatura (teste de desenvolvimento) para vincular até duas redes virtuais ao circuito. O administrador de TI da Contoso permite isso, especificando a ID da Microsoft de desenvolvimento de teste. O cmdlet não envia email para a ID da Microsoft especificada. O proprietário de circuito precisa explicitamente notifique o proprietário da assinatura outros que a autorização foi concluída.

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

#### <a name="reviewing-authorizations"></a>Revisando autorizações

O proprietário de circuito pode examinar todas as autorizações que são emitidas em um determinado circuito executando o seguinte cmdlet:

    Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

    Description         : EngineeringTeam
    Limit               : 3
    LinkAuthorizationId : ####################################
    MicrosoftIds        : engadmin@contoso.com
    Used                : 1

    Description         : MarketingTeam
    Limit               : 1
    LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    MicrosoftIds        : marketingadmin@contoso.com
    Used                : 0

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : salesadmin@contoso.com
    Used                : 2


#### <a name="updating-authorizations"></a>Atualizando autorizações

O proprietário de circuito pode modificar autorizações usando o seguinte cmdlet:

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


#### <a name="deleting-authorizations"></a>Excluindo autorizações

O proprietário de circuito pode revogar/excluir autorizações ao usuário executando o seguinte cmdlet:

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>Operações de usuário de circuito

#### <a name="reviewing-authorizations"></a>Revisando autorizações

O usuário de circuito pode revisar autorizações usando o seguinte cmdlet:

    Get-AzureAuthorizedDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

#### <a name="redeeming-link-authorizations"></a>Recuperando autorizações de link

O usuário de circuito pode executar o seguinte cmdlet para resgatar uma autorização de link:

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a rota expressa, consulte as [Perguntas Frequentes de rota expressa](expressroute-faqs.md).
