<properties
   pageTitle="Criar e modificar um circuito de rota expressa usando o modelo clássico de implantação e PowerShell | Microsoft Azure"
   description="Este artigo conduz você pelas etapas para criar e provisionar um circuito de rota expressa. Este artigo mostra como verificar o status, atualizar ou excluir e desprovisionamento seu circuito."
   documentationCenter="na"
   services="expressroute"
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
   ms.author="ganesr;cherylmc"/>

# <a name="create-and-modify-an-expressroute-circuit"></a>Criar e modificar um circuito de rota expressa

> [AZURE.SELECTOR]
[Portal Azure - Gerenciador de recursos](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Gerenciador de recursos](expressroute-howto-circuit-arm.md)
[PowerShell - clássico](expressroute-howto-circuit-classic.md)

Este artigo conduz você pelas etapas para criar um circuito de rota expressa do Azure usando cmdlets do PowerShell e o modelo de implantação clássico. Este artigo também mostrará a você como verificar o status, atualizar ou excluir e desprovisionamento um circuito de rota expressa.

**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="before-you-begin"></a>Antes de começar

### <a name="1-review-the-prerequisites-and-workflow-articles"></a>1. examine os pré-requisitos e artigos de fluxo de trabalho

Certifique-se de que revisou os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.  


### <a name="2-install-the-latest-versions-of-the-azure-powershell-modules"></a>2. instalar as últimas versões dos módulos do PowerShell do Azure 

Siga as instruções em [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md) para obter orientações passo a passo sobre como configurar seu computador para usar os módulos do PowerShell do Azure.

### <a name="3-log-in-to-your-azure-account-and-select-a-subscription"></a>3. Faça logon em sua conta do Azure e selecione uma assinatura

1. Execute o cmdlet a seguir usando um prompt do Windows PowerShell:

        Add-AzureAccount
2. Na entrada tela que aparece, entre sua conta.

3. Obter uma lista das suas assinaturas.

        Get-AzureSubscription
4. Selecione a assinatura que você deseja usar.
    
        Select-AzureSubscription -SubscriptionName "mysubscriptionname"

## <a name="create-and-provision-an-expressroute-circuit"></a>Criar e provisionar um circuito de rota expressa

### <a name="1-import-the-powershell-modules-for-expressroute"></a>1. importar os módulos do PowerShell para rota expressa

 Se você ainda não fez isso, você deve importar os módulos do Azure e rota expressa para a sessão do PowerShell para começar a usar os cmdlets rota expressa. Importe os módulos do local que eles tenham sido instalados no computador local. Dependendo do método usado para instalar os módulos, o local pode ser diferente do que mostra o exemplo a seguir. Modificar o exemplo se necessário.  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. obter a lista de provedores compatíveis, locais e larguras

Antes de criar um circuito de rota expressa, é necessário na lista de provedores de conectividade com suporte, locais e opções de largura de banda.

O cmdlet do PowerShell `Get-AzureDedicatedCircuitServiceProvider` retorna essas informações, que você usará em etapas posteriores:

    Get-AzureDedicatedCircuitServiceProvider

Verifique se o seu provedor de conectividade está listado lá. Anote as informações a seguir pois você precisará-la mais tarde quando você cria um circuito:

- Nome

- PeeringLocations

- BandwidthsOffered

Agora você está pronto para criar um circuito de rota expressa.         

### <a name="3-create-an-expressroute-circuit"></a>3. criar um circuito de rota expressa

O exemplo a seguir mostra como criar um 200-Mbps circuito de rota expressa por meio de Equinix no Vale do Silício. Se você estiver usando um provedor diferente e configurações diferentes, substitua essas informações quando você faz sua solicitação.

>[AZURE.IMPORTANT] Seu circuito rota expressa será cobrado no momento em que uma chave de serviço é emitida. Certifique-se de que você executar essa operação quando o provedor de conectividade está pronto para provisionar o circuito.


Este é um exemplo de solicitação para uma nova chave de serviço:

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

Ou, se você quiser criar um circuito de rota expressa com o complemento premium, use o exemplo a seguir. Consulte as [Perguntas Frequentes de rota expressa](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


A resposta conterá a chave de serviço. Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte:

    get-help new-azurededicatedcircuit -detailed

### <a name="4-list-all-the-expressroute-circuits"></a>4. listar todos os circuitos de rota expressa

Você pode executar o `Get-AzureDedicatedCircuit` comando para obter uma lista de todos os circuitos de rota expressa que você criou:


    Get-AzureDedicatedCircuit

A resposta será algo semelhante ao seguinte exemplo:

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Você pode recuperar essas informações a qualquer momento usando o `Get-AzureDedicatedCircuit` cmdlet. Fazendo a chamada sem parâmetros lista todos os circuitos. Sua chave de serviço será listado no campo *ServiceKey* .

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte:

    get-help get-azurededicatedcircuit -detailed

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. enviar a chave de serviço para seu provedor de conectividade para provisionamento


*ServiceProviderProvisioningState* fornece informações sobre o estado atual de provisionamento do lado do provedor de serviço. *Status* fornece o estado do lado de Microsoft. Para obter mais informações sobre circuito estados de provisionamento, consulte o artigo de [fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando você cria um novo circuito de rota expressa, o circuito será no estado seguinte:

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


O circuito irão para o estado seguinte quando o provedor de conectividade está em processo de ativação dele para você:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Um circuito de rota expressa deve ser no estado seguinte para você poder usá-lo:

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. periodicamente verificar o status e o estado da chave de circuito

Isso permite saber quando seu provedor habilitou seu circuito. Após o circuito tiver sido configurado, *ServiceProviderProvisioningState* será exibido como *provisionado* conforme mostrado no exemplo a seguir:

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="7-create-your-routing-configuration"></a>7. Crie sua configuração de roteamento

Consulte o [rota expressa circuito configuração de roteamento (criar e modificar peerings de circuito)](expressroute-howto-routing-classic.md) artigo para obter instruções passo a passo.

>[AZURE.IMPORTANT] Estas instruções se aplicam apenas às circuitos criados com provedores de serviço que oferecem serviços de conectividade de 2 de camada. Se você estiver usando um provedor de serviços que oferece gerenciado camada 3 serviços (normalmente um IP VPN, como MPLS), seu provedor de conectividade irá configurar e gerenciar o roteamento para você.

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. vincular a uma rede virtual a um circuito de rota expressa

Em seguida, vincule a uma rede virtual a seu circuito rota expressa. Consulte [circuitos rota expressa vinculação para redes virtuais](expressroute-howto-linkvnet-classic.md) para instruções passo a passo. Se você precisar criar uma rede virtual usando o modelo de implantação clássico para rota expressa, consulte [criar uma rede virtual para rota expressa](expressroute-howto-vnet-portal-classic.md) para obter instruções.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obtendo o status de um circuito de rota expressa

Você pode recuperar essas informações a qualquer momento usando o `Get-AzureCircuit` cmdlet. Fazendo a chamada sem parâmetros lista todos os circuitos.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

    Bandwidth                        : 1000
    CircuitName                      : MyAsiaCircuit
    Location                         : Singapore
    ServiceKey                       : #################################
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Você pode obter informações sobre um circuito de rota expressa específico, passando a chave do serviço como um parâmetro à chamada:

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte:

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>Modificando um circuito de rota expressa

Você pode modificar determinadas propriedades de um circuito de rota expressa sem afetar a conectividade.

Você pode fazer o seguinte sem tempo de inatividade:

- Habilitar ou desabilitar um complemento premium de rota expressa para seu circuito rota expressa.
- Aumente a largura de banda de circuito sua rota expressa. Observe que não é suportado downgrade a largura de banda de um circuito.
- Altere o plano de medição de dados limitadas para dados ilimitado. Observe que não é suportado alterando o plano de medição de dados ilimitado para dados limitadas.
- Você pode ativar e desativar *Permitir operações clássico*.

Consulte as [Perguntas Frequentes de rota expressa](expressroute-faqs.md) para obter mais informações sobre limitações e limites.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para habilitar o complemento premium rota expressa

Você pode ativar o complemento premium rota expressa para seu circuito existente usando o seguinte cmdlet do PowerShell:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

Seu circuito agora terá os rota expressa complemento os recursos premium do habilitado. Observe que começaremos cobrança você para o recurso de complemento premium assim que o comando foi executado com êxito.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para desabilitar o complemento premium rota expressa

>[AZURE.IMPORTANT] Essa operação pode falhar se você estiver usando recursos que sejam maiores que o que é permitido para o circuito padrão.

Observe o seguinte:

- Você deve garantir que o número de redes virtuais vinculado ao circuito for menor que 10 antes do downgrade do premium padrão. Se você não fizer isso, sua solicitação de atualização falhará e você será cobrado as taxas premium.

- Você deve desvincular todas as redes virtuais em outras regiões geopolíticas. Se você não fizer isso, sua solicitação de atualização falhará e você será cobrado as taxas premium.

- Sua tabela de rota deve ser menor que 4.000 rotas para correspondência particular. Se o tamanho da sua tabela de rota for maior que 4.000 rotas, a sessão BGP descarta e não ser habilitado novamente até que o número de prefixos anunciados fica abaixo de 4.000.

Você pode desabilitar o complemento premium rota expressa para seu circuito existente usando o seguinte cmdlet do PowerShell:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para atualizar a largura de banda de circuito de rota expressa

Verifique a [Rota expressa FAQ](expressroute-faqs.md) para as opções de largura de banda com suporte do seu provedor. Você pode escolher qualquer tamanho que seja maior que o tamanho da sua circuito existente contanto que permite que a porta física (no qual seu circuito é criado).

>[AZURE.IMPORTANT] Você não pode reduzir a largura de banda de um circuito de rota expressa sem interrupções. Fazer downgrade largura de banda exigirão que você desprovisionamento o circuito rota expressa e, em seguida, reaprovisionamento um novo circuito de rota expressa.

Depois que decidir qual o tamanho que você precisa, você pode usar o seguinte comando para redimensionar sua circuito:

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Seu circuito será tiver sido dimensionado para cima no lado de Microsoft. Você deve contatar o seu provedor de conectividade para atualizar as configurações de seu lado para corresponder a essa alteração. Observe que começaremos cobrança você para a opção de largura de banda atualizadas deste ponto em.

Se você vir o erro a seguir ao aumentar a largura de banda de circuito, significa lá não resta nenhuma largura de banda suficiente na porta física onde seu circuito existente é criado. Você precisa excluir este circuito e criar um novo circuito do tamanho que você precisa. 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
    

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Desprovisionamento e excluindo um circuito de rota expressa

Observe o seguinte:

- Você deve desvincular todas as redes virtuais de circuito a rota expressa para essa operação ter êxito. Verifique se você tem qualquer redes virtuais que estão vinculados ao circuito se essa operação falhar.

- Se o estado provisionamento provedor de serviço rota expressa circuito é **provisionamento** ou **provisionado** você deve trabalhar com seu provedor de serviço para desprovisionamento o circuito de seu lado. Podemos continuará a reservar recursos e cobrar você até o provedor de serviço conclui desprovisionamento o circuito e notifica conosco.

- Se o provedor de serviços tem desprovisionada o circuito (o estado de provisionamento de provedor de serviço é definido como **não provisionado**) você pode excluir o circuito. Isso interromperá cobrança para o circuito.

Você pode excluir seu circuito rota expressa executando o seguinte comando:

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>Próximas etapas

Depois de criar seu circuito, certifique-se de que você faça o seguinte:

- [Criar e modificar o roteamento para seu circuito rota expressa](expressroute-howto-routing-classic.md)
- [Vincular sua rede virtual seu circuito rota expressa](expressroute-howto-linkvnet-classic.md)
