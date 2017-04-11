<properties
   pageTitle="Criar e modificar um circuito de rota expressa usando o Gerenciador de recursos e PowerShell | Microsoft Azure"
   description="Este artigo descreve como criar, provisionar, verifique se, atualizar, excluir e desprovisionamento um circuito de rota expressa."
   documentationCenter="na"
   services="expressroute"
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
   ms.author="ganesr"/>


# <a name="create-and-modify-an-expressroute-circuit"></a>Criar e modificar um circuito de rota expressa

> [AZURE.SELECTOR]
[Portal Azure - Gerenciador de recursos](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Gerenciador de recursos](expressroute-howto-circuit-arm.md)
[PowerShell - clássico](expressroute-howto-circuit-classic.md)


Este artigo descreve como criar um circuito de rota expressa do Azure usando cmdlets do Windows PowerShell e o modelo de implantação do Gerenciador de recursos do Azure. Este artigo também mostrará a você como verificar o status do circuito, atualizá-lo, ou excluir e desprovisione-lo.

**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Antes de começar


- Obter a versão mais recente dos módulos Azure PowerShell (pelo menos versão 1.0). Para obter orientação passo a passo sobre como configurar seu computador para usar os módulos do PowerShell, siga as instruções em [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

- Examine os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

## <a name="create-and-provision-an-expressroute-circuit"></a>Criar e provisionar um circuito de rota expressa

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. entre na sua conta do Azure e selecione sua assinatura

Para começar a configuração, entre sua conta do Azure. Para obter mais informações sobre o PowerShell, consulte [Usando o Windows PowerShell com o Gerenciador de recursos](../powershell-azure-resource-manager.md). Use os exemplos a seguir para ajudá-lo a se conectar:

    Login-AzureRmAccount

Verifique as assinaturas para a conta:

    Get-AzureRmSubscription

Selecione a assinatura que você deseja criar um circuito de rota expressa para:

    Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. obter a lista de provedores compatíveis, locais e larguras

Antes de criar um circuito de rota expressa, é necessário na lista de provedores de conectividade com suporte, locais e opções de largura de banda.

O cmdlet do PowerShell `Get-AzureRmExpressRouteServiceProvider` retorna essas informações, que você usará em etapas posteriores:

    Get-AzureRmExpressRouteServiceProvider

Verifique se o seu provedor de conectividade está listado lá. Anote as informações a seguir pois você precisará-la mais tarde quando você cria um circuito:

- Nome

- PeeringLocations

- BandwidthsOffered

Agora você está pronto para criar um circuito de rota expressa.   

### <a name="3-create-an-expressroute-circuit"></a>3. criar um circuito de rota expressa

Se você ainda não tiver um grupo de recursos, você deve criar uma antes de criar seu circuito rota expressa. Você pode fazer isso, execute o seguinte comando:


    New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"


O exemplo a seguir mostra como criar um 200-Mbps circuito de rota expressa por meio de Equinix no Vale do Silício. Se você estiver usando um provedor diferente e configurações diferentes, substitua essas informações quando você faz sua solicitação. Este é um exemplo de solicitação para uma nova chave de serviço:

    New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

Certifique-se de que você especificar o nível SKU correto e família SKU:

- Nível SKU determina se um padrão de rota expressa ou um complemento de premium rota expressa está ativado. Você pode especificar *padrão* para obter o SKU ou *Premium* padrão para o complemento premium.

- Família SKU determina o tipo de cobrança. Você pode especificar *Metereddata* para um plano de dados limitada e *Unlimiteddata* para um plano de dados ilimitado. Observe que você pode alterar o tipo de cobrança da *Metereddata* para *Unlimiteddata*, mas você não pode alterar o tipo de *Unlimiteddata* para *Metereddata*.


>[AZURE.IMPORTANT] Seu circuito rota expressa será cobrado no momento em que uma chave de serviço é emitida. Certifique-se de que você executar essa operação quando o provedor de conectividade está pronto para provisionar o circuito.

A resposta contém a chave de serviço. Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte:


    get-help New-AzureRmExpressRouteCircuit -detailed


### <a name="4-list-all-expressroute-circuits"></a>4. listar todos os circuitos de rota expressa

Para obter uma lista de todos os circuitos de rota expressa que você criou, execute o `Get-AzureRmExpressRouteCircuit` comando:


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

A resposta terá aparência semelhante ao seguinte exemplo:


    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

Você pode recuperar essas informações a qualquer momento usando o `Get-AzureRmExpressRouteCircuit` cmdlet. Fazendo a chamada sem parâmetros lista todos os circuitos. Sua chave de serviço será listado no campo *ServiceKey* :


    Get-AzureRmExpressRouteCircuit


A resposta terá aparência semelhante ao seguinte exemplo:


    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []


Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte:


    get-help Get-AzureRmExpressRouteCircuit -detailed

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. enviar a chave de serviço para seu provedor de conectividade para provisionamento

*ServiceProviderProvisioningState* fornece informações sobre o estado atual de provisionamento do lado do provedor de serviço. Status fornece o estado do lado de Microsoft. Para obter mais informações sobre circuito estados de provisionamento, consulte o artigo de [fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando você cria um novo circuito de rota expressa, o circuito será no estado seguinte:


    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



O circuito mudará para o estado seguinte quando o provedor de conectividade está em processo de ativação dele para você:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Para que você seja capaz de usar um circuito de rota expressa, ele deve estar no estado seguinte:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. periodicamente verificar o status e o estado da chave de circuito

Verificar o status e o estado da chave de circuito permite saber quando seu provedor habilitou seu circuito. Após o circuito foi configurado, *ServiceProviderProvisioningState* aparece como *provisionado*, conforme mostrado no exemplo a seguir:


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


A resposta terá aparência semelhante ao seguinte exemplo:


    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                    }
    ServiceKey                       : **************************************
    Peerings                         : []

### <a name="7-create-your-routing-configuration"></a>7. Crie sua configuração de roteamento

Para obter instruções passo a passo, consulte o artigo de [configuração de roteamento de circuito de rota expressa](expressroute-howto-routing-arm.md) para criar e modificar peerings de circuito.


>[AZURE.IMPORTANT] Estas instruções se aplicam apenas às circuitos criados com provedores de serviço que oferecem serviços de conectividade de 2 de camada. Se você estiver usando um provedor de serviços que oferece gerenciado camada 3 serviços (normalmente um IP VPN, como MPLS), seu provedor de conectividade irá configurar e gerenciar o roteamento para você.

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. vincular a uma rede virtual a um circuito de rota expressa

Em seguida, vincule a uma rede virtual a seu circuito rota expressa. Use o artigo de [vinculação redes virtuais a rota expressa circuitos](expressroute-howto-linkvnet-arm.md) ao trabalhar com o modelo de implantação do Gerenciador de recursos.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obtendo o status de um circuito de rota expressa

Você pode recuperar essas informações a qualquer momento usando o `Get-AzureRmExpressRouteCircuit` cmdlet. Fazendo a chamada sem parâmetros lista todos os circuitos.

    Get-AzureRmExpressRouteCircuit


A resposta será semelhante ao seguinte exemplo:


    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []


Você pode obter informações sobre um circuito rota expressa específico, passando o nome do grupo de recursos e o nome de circuito como um parâmetro à chamada:


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


A resposta terá aparência semelhante ao seguinte exemplo:


    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []


Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte:

    get-help get-azurededicatedcircuit -detailed


## <a name="modify"></a>Modificando um circuito de rota expressa

Você pode modificar determinadas propriedades de um circuito de rota expressa sem afetar a conectividade.

Você pode fazer o seguinte sem tempo de inatividade:

- Habilitar ou desabilitar um complemento premium de rota expressa para seu circuito rota expressa.
- Aumente a largura de banda de circuito sua rota expressa. Observe que não é suportado downgrade a largura de banda de um circuito.
- Altere o plano de medição de dados limitadas para dados ilimitado. Observe que não é suportado alterando o plano de medição de dados ilimitado para dados limitadas.
-  Você pode ativar e desativar *Permitir operações clássico*.

Para obter mais informações sobre limites e limitações, consulte as [Perguntas Frequentes de rota expressa](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para habilitar o complemento premium rota expressa

Você pode ativar o complemento premium rota expressa para seu circuito existente usando o seguinte trecho do PowerShell:

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Tier = "Premium"
    $ckt.sku.Name = "Premium_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


O circuito agora terá os rota expressa complemento os recursos premium do habilitado. Observe que começaremos cobrança você para o recurso de complemento premium assim que o comando foi executado com êxito.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para desabilitar o complemento premium rota expressa

>[AZURE.IMPORTANT] Essa operação pode falhar se você estiver usando recursos que sejam maiores que o que é permitido para o circuito padrão.

Observe o seguinte:

- Antes do downgrade do premium padrão, você deve garantir que o número de redes virtuais que estão vinculados ao circuito for menor que 10. Se você não fizer isso, sua solicitação de atualização falha e será cobrado com taxas de premium.

- Você deve desvincular todas as redes virtuais em outras regiões geopolíticas. Se você não fizer isso, sua solicitação de atualização falhará e será cobrado com taxas de premium.

- Sua tabela de rota deve ser menor que 4.000 rotas para correspondência particular. Se o tamanho da sua tabela de rota for maior que 4.000 rotas, a sessão BGP descarta e não ser habilitado novamente até que o número de prefixos anunciados fica abaixo de 4.000.

Você pode desabilitar o complemento de premium rota expressa para o circuito existente usando o seguinte cmdlet do PowerShell:


    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Tier = "Standard"
    $ckt.sku.Name = "Standard_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para atualizar a largura de banda de circuito de rota expressa

Para opções de largura de banda com suporte do seu provedor, verifique a [Rota expressa FAQ](expressroute-faqs.md). Você pode escolher qualquer tamanho maior do que o tamanho da sua circuito existente.

>[AZURE.IMPORTANT] Você não pode reduzir a largura de banda de um circuito de rota expressa sem interrupções. Fazer downgrade largura de banda requer que você desprovisione o circuito rota expressa e, em seguida, reaprovisionamento um novo circuito de rota expressa.

Depois que decidir qual o tamanho que você precisa, use o seguinte comando para redimensionar sua circuito:


    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.ServiceProviderProperties.BandwidthInMbps = 1000

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


Seu circuito será dimensionado para cima no lado de Microsoft. Em seguida, você deve contatar seu provedor de conectividade para atualizar as configurações de seu lado para corresponder a essa alteração. Após fazer essa notificação, podemos começará a cobrança você para a opção de largura de banda atualizadas.


### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Para mover o SKU de medidas para ilimitado

Você pode alterar o SKU de circuito uma rota expressa usando o seguinte trecho do PowerShell:

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Family = "UnlimitedData"
    $ckt.sku.Name = "Premium_UnlimitedData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Para controlar o acesso aos ambientes de gerente de recursos e o clássico  

Revise as instruções em [rota expressa mover circuitos do clássico para o modelo de implantação do Gerenciador de recursos](expressroute-howto-move-arm.md).  


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Desprovisionamento e excluindo um circuito de rota expressa

Observe o seguinte:

- Você deve desvincular todas as redes virtuais de circuito a rota expressa. Se essa operação falhar, verifique se as redes virtuais estão vinculados ao circuito.

- Se o estado provisionamento provedor de serviço rota expressa circuito é **provisionamento** ou **provisionado** você deve trabalhar com seu provedor de serviço para desprovisionamento o circuito de seu lado. Podemos continuará a reservar recursos e cobrar você até o provedor de serviço conclui desprovisionamento o circuito e notifica conosco.

- Se o provedor de serviços tem desprovisionada o circuito (o estado de provisionamento de provedor de serviço é definido como **não provisionado**) você pode excluir o circuito. Isso interromperá cobrança para o circuito

Você pode excluir seu circuito rota expressa executando o seguinte comando:

    Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"



## <a name="next-steps"></a>Próximas etapas

Depois de criar seu circuito, certifique-se de que você faça o seguinte:

- [Criar e modificar o roteamento para seu circuito rota expressa](expressroute-howto-routing-arm.md)
- [Vincular sua rede virtual seu circuito rota expressa](expressroute-howto-linkvnet-arm.md)
