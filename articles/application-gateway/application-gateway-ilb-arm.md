<properties
   pageTitle="Criar e configurar um gateway de aplicativo com um balanceador de carga interno (ILB) usando o Gerenciador de recursos do Azure | Microsoft Azure"
   description="Esta página fornece instruções para criar, configurar, iniciar e excluir um gateway de aplicativo do Azure com balanceador de carga interno (ILB) para o Gerenciador de recursos do Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2016"
   ms.author="gwallace"/>


# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb-by-using-azure-resource-manager"></a>Criar um gateway de aplicativo com um balanceador de carga interno (ILB) usando o Gerenciador de recursos do Azure

> [AZURE.SELECTOR]
- [Azure etapas clássicas](application-gateway-ilb.md)
- [Etapas do PowerShell do Gerenciador de recursos](application-gateway-ilb-arm.md)

Azure Application Gateway pode ser configurado com um VIP voltado para a Internet ou com um ponto de extremidade interno que não é exposto à Internet, também conhecido como um ponto extremo (ILB) de Balanceador de carga interno. Configuração do gateway com um ILB é útil para aplicativos de linha de negócios internos que não estão expostos na Internet. Também é útil para serviços e níveis dentro de um aplicativo de várias camado que fique em um limite de segurança que não é exposto à Internet, mas ainda exigem alternada carregam distribuição, adesão de sessão ou encerramento Secure Sockets Layer (SSL).

Este artigo conduz você pelas etapas para configurar um gateway de aplicativo com um ILB.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente de cmdlets do PowerShell do Azure usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente da seção **Do Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Você cria uma rede virtual e uma sub-rede para Gateway do aplicativo. Certifique-se de que nenhuma máquinas virtuais ou implantações de nuvem estão usando a sub-rede. Gateway do aplicativo deve estar sozinha em uma sub-rede de rede virtual.
3. Os servidores que você configura para usar o gateway de aplicativo devem existir ou atribuiu seus pontos de extremidade criados na rede virtual ou com um IP/VIP público.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway de aplicativo?


- **Pool de servidor back-end:** A lista de endereços IP de servidores back-end. Os endereços IP listados ou devem pertencer à rede virtual, mas em uma sub-rede diferente para o gateway de aplicativo ou devem ser um IP/VIP público.
- **Configurações de pool do servidor de back-end:** Cada pool tem as configurações como afinidade baseada em cookies, protocolo e porta. Essas configurações estão vinculadas a um pool e são aplicadas a todos os servidores dentro do pool.
- **Porta front-end:** Esta porta é a porta pública que é aberta no gateway do aplicativo. Tráfego visitas esta porta e, em seguida, é redirecionado para um dos servidores back-end.
- **Ouvinte:** O ouvinte tem uma porta de front-end, um protocolo (Http ou Https, estes são diferencia maiusculas de minúsculas) e o nome do certificado SSL (se configurar SSL descarregar).
- **Regra:** A regra associará o ouvinte e o pool de servidor back-end e define qual pool de servidor back-end o tráfego deve ser direcionado para quando ele atinge um determinado ouvinte. Atualmente, somente a regra *básica* é suportada. A regra *básica* é a distribuição de carga round-robin.



## <a name="create-an-application-gateway"></a>Criar um gateway de aplicativo

A diferença entre o uso clássico do Azure e Gerenciador de recursos do Azure é a ordem em que você criar o gateway de aplicativo e os itens que precisam ser configurados.
Com o Gerenciador de recursos, todos os itens que compõem um gateway de aplicativo é configurado individualmente e, em seguida, coloque juntos para criar o recurso de gateway do aplicativo.


Aqui estão as etapas que são necessárias para criar um gateway de aplicativo:

1. Criar um grupo de recursos para o Gerenciador de recursos
2. Criar uma rede virtual e uma sub-rede para o gateway de aplicativo
3. Criar um objeto de configuração do gateway de aplicativo
4. Criar um recurso de gateway do aplicativo


## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Gerenciador de recursos

Certifique-se de que você alterna modo do PowerShell para usar os cmdlets do Gerenciador de recursos do Azure. Mais informações estão disponíveis em [Usando o Windows PowerShell com o Gerenciador de recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Etapa 1

    Login-AzureRmAccount

### <a name="step-2"></a>Etapa 2

Verifique as assinaturas para a conta.

    Get-AzureRmSubscription

Você será solicitado a autenticar com suas credenciais.<BR>

### <a name="step-3"></a>Etapa 3

Escolha quais das suas assinaturas Azure usar. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Etapa 4

Crie um novo grupo de recursos (ignorar esta etapa se você estiver usando um grupo existente do recurso).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Gerenciador de recursos de Azure requer que todos os grupos de recursos especificam um local. Isso é usado como o local padrão para os recursos desse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway aplicativo usa o mesmo grupo de recursos.

No exemplo acima, criamos um grupo de recursos chamado "appgw-rg" e local "Oeste EUA".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicativo

O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de recursos:

### <a name="step-1"></a>Etapa 1

    $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Isso atribui o endereço intervalo 10.0.0.0/24 a uma variável de sub-rede a ser usado para criar uma rede virtual.

### <a name="step-2"></a>Etapa 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

Isso cria uma rede virtual denominada "appgwvnet" no recurso grupo "appgw-rg" para a região Oeste EUA usando o prefixo 10.0.0.0/16 com sub-rede 10.0.0.0/24.

### <a name="step-3"></a>Etapa 3

    $subnet = $vnet.subnets[0]

Isso atribui o objeto de sub-rede a variável $subnet para as próximas etapas.

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway de aplicativo

### <a name="step-1"></a>Etapa 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Isso cria uma configuração de IP do gateway de aplicativo denominada "gatewayIP01". Quando o Gateway do aplicativo for iniciado, ele seleciona um endereço IP da sub-rede configurado e rotear o tráfego de rede para os endereços IP no pool de IP de back-end. Tenha em mente que cada instância tem um endereço IP.


### <a name="step-2"></a>Etapa 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Isso configura o pool de endereços IP de back-end denominado "pool01" com endereços IP "134.170.185.46, 134.170.188.221,134.170.185.50". Esses são os endereços IP que recebem o tráfego de rede que vem do ponto de extremidade IP front-end. Você substitui os endereços IP acima para adicionar seus próprios pontos de extremidade do endereço IP do aplicativo.

### <a name="step-3"></a>Etapa 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Isso configura o tráfego de rede do gateway configuração "poolsetting01" para o carregamento equilibrada de aplicativo do pool de back-end.

### <a name="step-4"></a>Etapa 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Isso configura a porta IP front-end denominada "frontendport01" para o ILB.

### <a name="step-5"></a>Etapa 5

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Isso cria a configuração de IP front-end chamada "fipconfig01" e associa um IP particular da sub-rede rede virtual atual.

### <a name="step-6"></a>Etapa 6

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Isso cria o ouvinte chamado "listener01" e associa a porta front-end para a configuração de IP front-end.

### <a name="step-7"></a>Etapa 7

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Isso cria a regra roteamento de Balanceador de carga chamada "rule01" que configura o comportamento de Balanceador de carga.

### <a name="step-8"></a>Etapa 8

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Isso configura o tamanho da instância do gateway aplicativo.

>[AZURE.NOTE]  O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é médio. Você pode escolher entre Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Criar um gateway de aplicativo usando o novo AzureApplicationGateway

Cria um gateway aplicativo com todos os itens de configuração das etapas acima. Neste exemplo, o gateway de aplicativo é chamado "appgwtest".


    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Isso cria um gateway aplicativo com todos os itens de configuração das etapas acima. No exemplo, o gateway de aplicativo é chamado "appgwtest".


## <a name="delete-an-application-gateway"></a>Excluir um gateway de aplicativo

Para excluir um gateway de aplicativo, você precisará fazer o seguinte ordem:

1. Use o cmdlet **AzureRmApplicationGateway de parar** para interromper o gateway.
2. Use o cmdlet **AzureRmApplicationGateway remover** para remover o gateway.
3. Verifique se que o gateway foi removido usando o cmdlet **Get-AzureApplicationGateway** .


### <a name="step-1"></a>Etapa 1

Obtenha o objeto de gateway do aplicativo e associá-la a uma variável "$getgw".

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Etapa 2

Use **AzureRmApplicationGateway de parar** para interromper o gateway do aplicativo. Este exemplo mostra o cmdlet **AzureRmApplicationGateway parar** na primeira linha, seguido de saída.

    PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Depois que o gateway de aplicativo estiver em um estado parado, use o cmdlet **AzureRmApplicationGateway remover** para remover o serviço.


    PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE] O **-Forçar** opção pode ser usada para suprimir a mensagem de confirmação de remover.


Para verificar que o serviço foi removido, você pode usar o cmdlet **Get-AzureRmApplicationGateway** . Esta etapa não é necessária.


    PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>Próximas etapas

Se você quiser configurar descarregamento SSL, consulte [Configurar um gateway de aplicativo para SSL descarregar](application-gateway-ssl.md).

Se desejar configurar um gateway de aplicativo para usar com um ILB, consulte [criar um gateway de aplicativo com um balanceador de carga interno (ILB)](application-gateway-ilb.md).

Se você desejar obter mais informações sobre opções de balanceamento de carregar em geral, consulte:

- [Balanceador de carga Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gerenciador de tráfego Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
