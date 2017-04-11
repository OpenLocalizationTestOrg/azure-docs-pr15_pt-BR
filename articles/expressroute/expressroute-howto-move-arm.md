<properties
   pageTitle="Mover circuitos rota expressa do clássico ao Gerenciador de recursos | Microsoft Azure"
   description="Esta página descreve como mover um circuito clássico para o modelo de implantação do Gerenciador de recursos."
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


# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Mover rota expressa circuitos do clássico para o modelo de implantação do Gerenciador de recursos

## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração

- Você precisa da versão mais recente dos módulos do PowerShell do Azure (pelo menos versão 1.0).
- Certifique-se de que revisou os [pré-requisitos](expressroute-prerequisites.md), [Roteamento requisitos](expressroute-routing.md)e [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
- Antes anterior ainda mais, examine as informações fornecidas em [movendo um circuito de rota expressa do clássico ao Gerenciador de recursos](expressroute-move.md). Certifique-se de que você tenha compreendido totalmente os limites e as limitações do que é possível.
- Se você deseja mover um circuito de rota expressa do Azure do modelo clássico de implantação para o modelo de implantação do Gerenciador de recursos do Azure, você deve ter o circuito totalmente configurado e operacional no modelo clássico de implantação.
- Certifique-se de que você tenha um grupo de recursos que foi criado no modelo de implantação do Gerenciador de recursos.

## <a name="move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Mover o circuito rota expressa para o modelo de implantação do Gerenciador de recursos

Você deve mover um circuito de rota expressa para o modelo de implantação do Gerenciador de recursos para que você possa usá-lo entre o clássico e os modelos de implantação do Gerenciador de recursos. Você pode fazer isso executando os seguintes comandos do PowerShell.

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Etapa 1: Coletar detalhes de circuito do modelo clássico de implantação

Você precisa reunir informações sobre seu circuito rota expressa primeiro.

Entrar no ambiente clássico do Azure e coletar a chave de serviço. Você pode usar o seguinte trecho do PowerShell para reunir as informações:

    # Sign in to your Azure account
    Add-AzureAccount

    # Select the appropriate Azure subscription
    Select-AzureSubscription "<Enter Subscription Name here>"

    # Import the PowerShell modules for Azure and ExpressRoute
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

    # Get the service keys of all your ExpressRoute circuits
    Get-AzureDedicatedCircuit

Copie a **chave de serviço** do circuito que você deseja mover para o modelo de implantação do Gerenciador de recursos.

### <a name="step-2-sign-in-to-the-resource-manager-environment-and-create-a-new-resource-group"></a>Etapa 2: Entrar no ambiente do Gerenciador de recursos e criar um novo grupo de recursos

Você pode criar um novo grupo de recursos usando o trecho a seguir:

    # Sign in to your Azure Resource Manager environment
    Login-AzureRmAccount

    # Select the appropriate Azure subscription
    Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

    #Create a new resource group if you don't already have one
    New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

Você também pode usar um grupo de recursos existente se você já tiver um.

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Etapa 3: Mover o circuito rota expressa para o modelo de implantação do Gerenciador de recursos

Agora você está pronto para mover pela sua circuito rota expressa do clássico para o modelo de implantação do Gerenciador de recursos. Examine as informações fornecidas em [movendo um circuito de rota expressa do clássico para o modelo de implantação do Gerenciador de recursos](expressroute-move.md) antes de continuar.

Você pode fazer isso executando o trecho a seguir:

    Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

>[AZURE.NOTE] Após a movimentação, o novo nome que está listado no cmdlet anterior será usado para o recurso de endereço. O circuito essencialmente será renomeado.

## <a name="enable-an-expressroute-circuit-for-both-deployment-models"></a>Habilitar um circuito de rota expressa para ambos os modelos de implantação

Você deve mover seu circuito rota expressa para o modelo de implantação do Gerenciador de recursos antes de controlar o acesso ao modelo de implantação.

Execute o cmdlet a seguir para habilitar o acesso a ambos os modelos de implantação:

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to TRUE
    $ckt.AllowClassicOperations = $true

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Após essa operação concluída com êxito, você poderá exibir o circuito no modelo clássico de implantação.

Execute o seguinte para obter os detalhes do circuito rota expressa:

    get-azurededicatedcircuit

Você deve ser capaz de ver a chave de serviço listada. Agora você pode gerenciar links para o circuito de rota expressa usando seus comandos de modelo clássico de implantação padrão para VNets clássico e seus comandos ARM padrão para VNETs ARM. Os artigos a seguir apresentará como gerenciar links para o circuito rota expressa:

- [Vincular sua rede virtual seu circuito rota expressa no modelo de implantação do Gerenciador de recursos](expressroute-howto-linkvnet-arm.md)
- [Vincular sua rede virtual seu circuito rota expressa no modelo clássico de implantação](expressroute-howto-linkvnet-classic.md)


## <a name="disable-the-expressroute-circuit-to-the-classic-deployment-model"></a>Desabilitar o circuito rota expressa para o modelo de implantação clássico

Execute o cmdlet a seguir para desabilitar o acesso ao modelo clássico de implantação:

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to FALSE
    $ckt.AllowClassicOperations = $false

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Após essa operação concluída com êxito, não será possível exibir o circuito no modelo clássico de implantação.

## <a name="next-steps"></a>Próximas etapas

Depois de criar seu circuito, certifique-se de que você faça o seguinte:

- [Criar e modificar o roteamento para seu circuito rota expressa](expressroute-howto-routing-arm.md)
- [Vincular sua rede virtual seu circuito rota expressa](expressroute-howto-linkvnet-arm.md)
