<properties
   pageTitle="Criar, iniciar ou excluir um gateway de aplicativo usando o Gerenciador de recursos do Azure | Microsoft Azure"
   description="Esta página fornece instruções para criar, configurar, iniciar e excluir um gateway de aplicativo do Azure usando o Gerenciador de recursos do Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Criar, iniciar ou excluir um gateway de aplicativo usando o Gerenciador de recursos do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell do Gerenciador de recursos](application-gateway-create-gateway-arm.md)
- [Azure PowerShell clássico](application-gateway-create-gateway.md)
- [Modelo do Gerenciador de recursos Azure](application-gateway-create-gateway-arm-template.md)
- [CLI Azure](application-gateway-create-gateway-cli.md)

Azure Application Gateway é um balanceador de carga de camada-7. Ele oferece failover, roteamento de desempenho solicitações HTTP entre diferentes servidores, independentemente de estarem na nuvem ou local. Gateway do aplicativo fornece vários recursos de controlador de entrega de aplicativo (ADC), incluindo balanceamento de carga de HTTP afinidade de sessão baseada em cookies, Secure Sockets Layer (SSL) descarregar, sondagens de integridade personalizados, suporte para vários locais e várias outras. Para localizar uma lista completa de recursos com suporte, visite [Visão geral de Gateway do aplicativo](application-gateway-introduction.md)

Este artigo explica as etapas para criar, configurar, iniciar e excluir um gateway de aplicativos.

>[AZURE.IMPORTANT] Antes de você trabalha com recursos Azure, é importante entender que o Azure tem atualmente dois modelos de implantação: Gerenciador de recursos e clássico. Certifique-se de que você compreenda [Ferramentas e modelos de implantação](../azure-classic-rm.md) antes de trabalhar com qualquer recurso Azure. Você pode exibir a documentação de ferramentas diferentes clicando nas guias na parte superior deste artigo. Este documento aborda a criação de um gateway de aplicativo usando o Gerenciador de recursos do Azure. Para usar a versão clássica, vá para [criar um gateway clássico implantação usando o PowerShell](application-gateway-create-gateway.md).


## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente de cmdlets do PowerShell do Azure usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente da seção **Do Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Se você tiver uma rede virtual existente, selecione uma sub-rede vazia existente ou criar uma sub-rede na rede virtual existente unicamente para uso pelo gateway de aplicativo. Você não pode implantar o gateway de aplicativo para uma rede virtual diferente os recursos que você pretende implantar atrás do gateway de aplicativo.
3. Os servidores que você configura para usar o gateway de aplicativo devem existir ou atribuiu seus pontos de extremidade criados na rede virtual ou com um IP/VIP público.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway de aplicativo?

- **Pool de servidor back-end:** A lista de endereços IP de servidores back-end. Os endereços IP listados ou devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público.
- **Configurações de pool do servidor de back-end:** Cada pool tem as configurações como afinidade baseada em cookies, protocolo e porta. Essas configurações estão vinculadas a um pool e são aplicadas a todos os servidores dentro do pool.
- **Porta front-end:** Esta porta é a porta pública que é aberta no gateway do aplicativo. Tráfego visitas esta porta e, em seguida, é redirecionado para um dos servidores back-end.
- **Ouvinte:** O ouvinte tem uma porta de front-end, um protocolo (Http ou Https, esses valores diferenciam maiusculas de minúsculas) e o nome do certificado SSL (se configurar SSL descarregar).
- **Regra:** A regra associará o ouvinte, o pool de servidor back-end e define qual pool de servidor back-end o tráfego deve ser direcionado para quando ele atinge um determinado ouvinte.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicativo

A diferença entre o uso clássico do Azure e Gerenciador de recursos do Azure é a ordem em que você criar o gateway de aplicativo e os itens que precisam ser configurados.

Com o Gerenciador de recursos, todos os itens que compõem um gateway de aplicativo estão configurados individualmente e coloque juntos para criar o recurso de gateway do aplicativo.

A seguir estão as etapas que são necessárias para criar um gateway de aplicativo.

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Gerenciador de recursos

Certifique-se de que você está usando a versão mais recente do PowerShell do Azure. Mais informações estão disponíveis em [Usando o Windows PowerShell com o Gerenciador de recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Etapa 1

Faça logon no Azure

    Login-AzureRmAccount

Você será solicitado a autenticar com suas credenciais.

### <a name="step-2"></a>Etapa 2

Verifique as assinaturas para a conta.

    Get-AzureRmSubscription

### <a name="step-3"></a>Etapa 3

Escolha quais das suas assinaturas Azure usar.

    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="step-4"></a>Etapa 4

Crie um grupo de recursos (ignorar esta etapa se você estiver usando um grupo existente do recurso).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Gerenciador de recursos de Azure requer que todos os grupos de recursos especificam um local. Esse local é usado como o local padrão para os recursos desse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway aplicativo usa o mesmo grupo de recursos.

No exemplo acima, criamos um grupo de recursos chamado "appgw-RG" e local "Oeste EUA".

>[AZURE.NOTE] Se você precisar configurar um teste personalizado para seu gateway de aplicativo, consulte [criar um gateway de aplicativo com testes personalizados usando o PowerShell](application-gateway-create-probe-ps.md). Confira [testes personalizados e monitoramento de integridade](application-gateway-probe-overview.md) para obter mais informações.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicativo

O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de recursos.

### <a name="step-1"></a>Etapa 1

Atribua o endereço intervalo 10.0.0.0/24 variável de sub-rede a ser usado para criar uma rede virtual.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Etapa 2

Crie uma rede virtual denominada "appgwvnet" no recurso grupo "appgw-rg" para a região Oeste EUA usando o prefixo 10.0.0.0/16 com sub-rede 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="step-3"></a>Etapa 3

Atribua uma variável de sub-rede para as próximas etapas, que criam um gateway de aplicativos.

    $subnet=$vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso IP público "publicIP01" no recurso grupo "appgw-rg" para a região Oeste EUA.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway de aplicativo

Você deve configurar todos os itens de configuração antes de criar o gateway do aplicativo. As etapas a seguir criam os itens de configuração que são necessários para um recurso de gateway do aplicativo.

### <a name="step-1"></a>Etapa 1

Crie uma configuração de IP do gateway de aplicativo denominada "gatewayIP01". Quando o Gateway do aplicativo for iniciado, ele seleciona um endereço IP da sub-rede configurado e rotear o tráfego de rede para os endereços IP no pool de IP de back-end. Tenha em mente que cada instância tem um endereço IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

### <a name="step-2"></a>Etapa 2

Configurar o pool de endereços IP de back-end denominado "pool01" com endereços IP "134.170.185.46, 134.170.188.221,134.170.185.50". Esses endereços IP são os endereços IP que recebem o tráfego de rede que vem do ponto de extremidade IP front-end. Você substitui os endereços IP anteriores para adicionar seus próprios pontos de extremidade do endereço IP do aplicativo.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

### <a name="step-3"></a>Etapa 3

Configure configuração de gateway do aplicativo "poolsetting01" para o tráfego de rede de balanceamento de carga no pool de back-end.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

### <a name="step-4"></a>Etapa 4

Configure a porta IP front-end denominada "frontendport01" para o ponto de extremidade IP público.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### <a name="step-5"></a>Etapa 5

Crie a configuração de IP front-end denominada "fipconfig01" e associar o endereço IP público a configuração de IP front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### <a name="step-6"></a>Etapa 6

Criar o ouvinte nome "listener01" e associe a porta front-end para a configuração de IP front-end.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### <a name="step-7"></a>Etapa 7

Crie a regra roteamento de Balanceador de carga denominada "rule01" que configura o comportamento de Balanceador de carga.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-8"></a>Etapa 8

Configure o tamanho de instância do gateway aplicativo.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]  O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é médio. Você pode escolher entre Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Criar um gateway de aplicativo usando o novo AzureRmApplicationGateway

Crie um gateway de aplicativo com todos os itens de configuração das etapas anteriores. Neste exemplo, o gateway de aplicativo é chamado "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

### <a name="step-9"></a>Etapa 9

Recupere detalhes DNS e VIP do gateway aplicativo do recurso IP público anexado para o gateway de aplicativo.

    Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  

## <a name="delete-an-application-gateway"></a>Excluir um gateway de aplicativo

Para excluir um gateway de aplicativos, siga estas etapas:

### <a name="step-1"></a>Etapa 1

Obtenha o objeto de gateway do aplicativo e associá-la a uma variável "$getgw".

    $getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Etapa 2

Use **AzureRmApplicationGateway de parar** para interromper o gateway do aplicativo.

    Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

Depois que o gateway de aplicativo estiver em um estado parado, use o cmdlet **AzureRmApplicationGateway remover** para remover o serviço.

    Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force

>[AZURE.NOTE] O **-Forçar** opção pode ser usada para suprimir a mensagem de confirmação de remover.

Para verificar que o serviço foi removido, você pode usar o cmdlet **Get-AzureRmApplicationGateway** . Esta etapa não é necessária.

    Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

## <a name="get-application-gateway-dns-name"></a>Obter o nome DNS de gateway do aplicativo

Depois que o gateway é criado, a próxima etapa é configurar o front-end para comunicação. Ao usar um IP público, o gateway de aplicativo requer um nome DNS atribuído dinamicamente, que não é compatível com. Pode ser usado para garantir que os usuários finais possam atingir o gateway de aplicativo um registro CNAME para apontar para o ponto de extremidade público do gateway aplicativo. [Configurando um nome de domínio personalizado para no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para fazer isso, recupere detalhes do gateway aplicativo e seu nome IP/DNS associado usando o elemento de PublicIPAddress anexado para o gateway de aplicativo. Nome DNS do gateway aplicativo deve ser usado para criar um registro CNAME, que aponta os aplicativos dois web para esse nome DNS. O uso de registros de não é recomendável porque o VIP pode ser alterados na reinicialização do gateway de aplicativo.
    
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

Se você quiser configurar descarregamento SSL, consulte [Configurar um gateway de aplicativo para SSL descarregar](application-gateway-ssl.md).

Se desejar configurar um gateway de aplicativo para usar com um balanceador de carga interno, consulte [criar um gateway de aplicativo com um balanceador de carga interno (ILB)](application-gateway-ilb.md).

Se você desejar obter mais informações sobre opções de balanceamento de carregar em geral, consulte:

- [Balanceador de carga Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gerenciador de tráfego Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
