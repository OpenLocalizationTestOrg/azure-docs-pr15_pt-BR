<properties
   pageTitle="Criar um teste personalizado para o Gateway de aplicativo usando o PowerShell no Gerenciador de recursos | Microsoft Azure"
   description="Saiba como criar um teste personalizado para o Gateway de aplicativo usando o PowerShell no Gerenciador de recursos"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Criar um teste personalizado para o Gateway de aplicativo do Azure usando o PowerShell para o Gerenciador de recursos do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-probe-portal.md)
- [Azure PowerShell do Gerenciador de recursos](application-gateway-create-probe-ps.md)
- [Azure PowerShell clássico](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implantação clássico](application-gateway-create-probe-classic-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

### <a name="step-1"></a>Etapa 1

Use AzureRmAccount de logon para autenticar.

    Login-AzureRmAccount

### <a name="step-2"></a>Etapa 2

Verifique as assinaturas para a conta.

    Get-AzureRmSubscription

### <a name="step-3"></a>Etapa 3

Escolha quais das suas assinaturas Azure usar. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Etapa 4

Crie um grupo de recursos (ignorar esta etapa se você estiver usando um grupo existente do recurso).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Gerenciador de recursos de Azure requer que todos os grupos de recursos especificam um local. Esse local é usado como o local padrão para os recursos desse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway aplicativo usam o mesmo grupo de recursos.

No exemplo acima, criamos um grupo de recursos chamado "appgw-RG" e local "Oeste EUA".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicativo

As etapas a seguir criam uma rede virtual e uma sub-rede para o gateway de aplicativo.

### <a name="step-1"></a>Etapa 1


Atribua o endereço intervalo 10.0.0.0/24 a uma variável de sub-rede a ser usado para criar uma rede virtual.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Etapa 2

Crie uma rede virtual denominada "appgwvnet" no recurso grupo "appgw-rg" para a região Oeste EUA usando o prefixo 10.0.0.0/16 com sub-rede 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### <a name="step-3"></a>Etapa 3

Atribua uma variável de sub-rede para as próximas etapas, que criam um gateway de aplicativos.

    $subnet = $vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end


Crie um recurso IP público "publicIP01" no recurso grupo "appgw-rg" para a região Oeste EUA.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location "West US" -AllocationMethod Dynamic


## <a name="create-an-application-gateway-configuration-object-with-a-custom-probe"></a>Criar um objeto de configuração do gateway de aplicativo com um teste personalizado

Você pode configurar todos os itens de configuração antes de criar o gateway do aplicativo. As etapas a seguir criam os itens de configuração que são necessários para um recurso de gateway do aplicativo.

### <a name="step-1"></a>Etapa 1

Crie uma configuração de IP do gateway de aplicativo denominada "gatewayIP01". Quando o Gateway do aplicativo for iniciado, ele seleciona um endereço IP da sub-rede configurado e rotear o tráfego de rede para os endereços IP no pool de IP de back-end. Tenha em mente que cada instância tem um endereço IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### <a name="step-2"></a>Etapa 2


Configurar o pool de endereços IP de back-end denominado "pool01" com endereços IP "134.170.185.46, 134.170.188.221,134.170.185.50". Esses valores são os endereços IP que recebem o tráfego de rede que vem do ponto de extremidade IP front-end. Você substitui os endereços IP acima para adicionar seus próprios pontos de extremidade do endereço IP do aplicativo.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### <a name="step-3"></a>Etapa 3


O teste personalizado está configurado nesta etapa.

Os parâmetros usados são:

- **Intervalo** - configura as verificações de intervalo de teste em segundos.
- **Tempo limite** - define o tempo de limite de teste para uma verificação de resposta HTTP.
- **-Hostname e caminho** - caminho de URL completo que é chamado pelo Gateway de aplicativo para determinar a integridade da instância. Por exemplo, se você tiver um site http://contoso.com/, em seguida, o teste personalizado pode ser configurado para "http://contoso.com/path/custompath.htm" para verificações de teste ter uma resposta HTTP bem-sucedida.
- **UnhealthyThreshold** - o número de respostas HTTP falhas necessário para sinalizar a instância de back-end como *não íntegra*.

<BR>

    $probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8

### <a name="step-4"></a>Etapa 4

Configure a configuração do gateway aplicativo "poolsetting01" para o tráfego do pool de back-end. Essa etapa também tem uma configuração de tempo limite para a resposta de pool de back-end para uma solicitação de gateway do aplicativo. Quando uma resposta de back-end atinge um limite, Application Gateway cancela a solicitação. Esse valor é diferente de um tempo de limite de teste que só está disponível para a resposta de back-end para teste verificações.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

### <a name="step-5"></a>Etapa 5

Configure a porta IP front-end denominada "frontendport01" para o ponto de extremidade IP público.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80

### <a name="step-6"></a>Etapa 6

Crie a configuração de IP front-end denominada "fipconfig01" e associar o endereço IP público a configuração de IP front-end.


    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### <a name="step-7"></a>Etapa 7

Criar o ouvinte nome "listener01" e associe a porta front-end para a configuração de IP front-end.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### <a name="step-8"></a>Etapa 8

Crie a regra roteamento de Balanceador de carga denominada "rule01" que configura o comportamento de Balanceador de carga.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-9"></a>Etapa 9

Configure o tamanho de instância do gateway aplicativo.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]  O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é médio. Você pode escolher entre Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Criar um gateway de aplicativo usando o novo AzureRmApplicationGateway

Crie um gateway de aplicativo com todos os itens de configuração das etapas acima. Neste exemplo, o gateway de aplicativo é chamado "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Adicionar um teste a um gateway de aplicativo existente

Você tem quatro etapas para adicionar um teste personalizado a um gateway de aplicativo existente.

### <a name="step-1"></a>Etapa 1

Carrega o recurso de gateway do aplicativo em uma variável do PowerShell usando **Get-AzureRmApplicationGateway**.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Etapa 2

Adicione um teste a configuração do gateway existente.

    $getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


No exemplo, o teste personalizado está configurado para verificar URL caminho contoso.com/path/custompath.htm cada 30 segundos. Um limite de tempo limite de 120 segundos está configurado com um número máximo de 8 solicitações de sondagem falha.

### <a name="step-3"></a>Etapa 3

Adicionar o teste para a configuração do pool de back-end e tempo limite usando **-Configurar-AzureRmApplicationGatewayBackendHttpSettings**.


     $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### <a name="step-4"></a>Etapa 4

Salve a configuração do gateway de aplicativo usando **Set-AzureRmApplicationGateway**.

    Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Remover um teste de um gateway de aplicativo existente

Estas são as etapas para remover um teste personalizado de um gateway de aplicativo existente.

### <a name="step-1"></a>Etapa 1

Carrega o recurso de gateway do aplicativo em uma variável do PowerShell usando **Get-AzureRmApplicationGateway**.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### <a name="step-2"></a>Etapa 2

Remova a configuração de teste do gateway aplicativo usando **AzureRmApplicationGatewayProbeConfig remover**.

    $getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### <a name="step-3"></a>Etapa 3

Atualizar a configuração de pool de back-end para remover o teste e a configuração de tempo limite usando **-Configurar-AzureRmApplicationGatewayBackendHttpSettings**.


     $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### <a name="step-4"></a>Etapa 4

Salve a configuração do gateway de aplicativo usando **Set-AzureRmApplicationGateway**. 

    Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## <a name="get-application-gateway-dns-name"></a>Obter o nome DNS de gateway do aplicativo

Depois que o gateway é criado, a próxima etapa é configurar o front-end para comunicação. Ao usar um público IP, o gateway de aplicativo requer um nome DNS atribuído dinamicamente, que não é compatível com. Pode ser usado para garantir que os usuários finais possam atingir o gateway de aplicativo um registro CNAME para apontar para o ponto de extremidade público do gateway aplicativo. [Configurando um nome de domínio personalizado para no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para fazer isso, recupere detalhes do gateway aplicativo e seu nome IP/DNS associado usando o elemento de PublicIPAddress anexado para o gateway de aplicativo. Nome DNS do gateway aplicativo deve ser usado para criar um registro CNAME, que aponta os aplicativos dois web para esse nome DNS. O uso de registros de não é recomendável porque o VIP pode ser alterados na reinicialização do gateway de aplicativo.
    
    Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
        
    Name                     : publicIP01
    ResourceGroupName        : appgw-RG
    Location                 : westus
    Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
    Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
    ResourceGuid             : 00000000-0000-0000-0000-000000000000
    ProvisioningState        : Succeeded
    Tags                     : 
    PublicIpAllocationMethod : Dynamic
    IpAddress                : xx.xx.xxx.xx
    PublicIpAddressVersion   : IPv4
    IdleTimeoutInMinutes     : 4
    IpConfiguration          : {
                                 "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                               Configurations/frontend1"
                               }
    DnsSettings              : {
                                 "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                               }

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar SSL transferindo visitando [Configurar SSL descarregar](application-gateway-ssl-arm.md)

