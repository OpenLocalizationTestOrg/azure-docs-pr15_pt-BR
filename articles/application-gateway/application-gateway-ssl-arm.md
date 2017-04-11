<properties
   pageTitle="Configurar um gateway de aplicativo para descarregamento SSL usando o Gerenciador de recursos do Azure | Microsoft Azure"
   description="Esta página fornece instruções para criar um gateway de aplicativo com SSL descarregar usando o Gerenciador de recursos do Azure"
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
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Configurar um gateway de aplicativo para descarregamento SSL usando o Gerenciador de recursos do Azure

> [AZURE.SELECTOR]
-[Portal do Azure](application-gateway-ssl-portal.md)
-[Azure PowerShell do Gerenciador de recursos](application-gateway-ssl-arm.md)
-[Azure PowerShell clássico](application-gateway-ssl.md)

 Azure Application Gateway pode ser configurado para finalizar a sessão de Secure Sockets Layer (SSL) no gateway para evitar dispendiosas tarefas de descriptografia SSL para que ocorram em web farm. Descarregamento SSL também simplifica a configuração de servidor front-end e o gerenciamento do aplicativo da web.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente de cmdlets do PowerShell do Azure usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente da seção **Do Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Crie uma rede virtual e uma sub-rede para o gateway de aplicativo. Certifique-se de que nenhuma máquinas virtuais ou implantações de nuvem estão usando a sub-rede. Gateway do aplicativo deve estar sozinha em uma sub-rede de rede virtual.
3. Os servidores que você configurar para usar o gateway de aplicativo devem existir ou atribuiu seus pontos de extremidade criados na rede virtual ou com um IP/VIP público.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway de aplicativo?


- **Pool de servidor back-end:** A lista de endereços IP de servidores back-end. Os endereços IP listados ou devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público.
- **Configurações de pool do servidor de back-end:** Cada pool tem as configurações como afinidade baseada em cookies, protocolo e porta. Essas configurações estão vinculadas a um pool e são aplicadas a todos os servidores dentro do pool.
- **Porta front-end:** Esta porta é a porta pública que é aberta no gateway do aplicativo. Tráfego visitas esta porta e, em seguida, é redirecionado para um dos servidores back-end.
- **Ouvinte:** O ouvinte tem uma porta de front-end, um protocolo (Http ou Https, essas configurações diferenciam maiusculas de minúsculas) e o nome do certificado SSL (se configurar SSL descarregar).
- **Regra:** A regra associará o ouvinte e o pool de servidor back-end e define qual pool de servidor back-end o tráfego deve ser direcionado para quando ele atinge um determinado ouvinte. Atualmente, somente a regra *básica* é suportada. A regra *básica* é a distribuição de carga round-robin.

**Notas de configuração adicionais**

Para configuração de certificados SSL, o protocolo no **HttpListener** deve mudar para *Https* (diferencia maiusculas de minúsculas). O elemento **SslCertificate** é adicionado à **HttpListener** com o valor da variável configurado para o certificado SSL. A porta front-end deve ser atualizada para 443.

**Para habilitar afinidade baseada em cookie**: um gateway de aplicativo pode ser configurado para garantir que uma solicitação de uma sessão de cliente sempre será direcionada para a máquina virtual mesma na web farm. Este cenário é feito pela inclusão de um cookie de sessão que permite que o gateway para direcionar o tráfego adequadamente. Para habilitar afinidade baseada em cookies, defina **CookieBasedAffinity** como *ativado* no elemento **BackendHttpSettings** .


## <a name="create-an-application-gateway"></a>Criar um gateway de aplicativo

A diferença entre usando o modelo de implantação do Azure clássico e o Gerenciador de recursos do Azure é a ordem em que você criar um gateway de aplicativos e os itens que precisam ser configurados.

Com o Gerenciador de recursos, todos os componentes de um gateway de aplicativo estão configurados individualmente e, em seguida, coloque juntos para criar um recurso de gateway do aplicativo.


Aqui estão as etapas necessárias para criar um gateway de aplicativo:

1. Criar um grupo de recursos para o Gerenciador de recursos
2. Criar uma rede virtual, sub-rede e IP público para o gateway de aplicativo
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

Crie um grupo de recursos (ignorar esta etapa se você estiver usando um grupo existente do recurso).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Gerenciador de recursos de Azure requer que todos os grupos de recursos especificam um local. Esta configuração é usada como o local padrão para os recursos desse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway aplicativo usa o mesmo grupo de recursos.

No exemplo acima, criamos um grupo de recursos chamado "appgw-RG" e local "Oeste EUA".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicativo

O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de recursos:

### <a name="step-1"></a>Etapa 1

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Este exemplo atribui o endereço intervalo 10.0.0.0/24 a uma variável de sub-rede a ser usado para criar uma rede virtual.

### <a name="step-2"></a>Etapa 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Esse exemplo cria uma rede virtual denominada "appgwvnet" no recurso grupo "appgw-rg" para a região Oeste EUA usando o prefixo 10.0.0.0/16 com sub-rede 10.0.0.0/24.

### <a name="step-3"></a>Etapa 3

    $subnet = $vnet.Subnets[0]

Este exemplo atribui o objeto de sub-rede a variável $subnet para as próximas etapas.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Este exemplo cria um recurso IP público "publicIP01" no recurso grupo "appgw-rg" para a região Oeste EUA.


## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway de aplicativo

### <a name="step-1"></a>Etapa 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Esse exemplo cria uma configuração de IP do gateway de aplicativo denominada "gatewayIP01". Quando o Gateway do aplicativo for iniciado, ele seleciona um endereço IP da sub-rede configurado e rotear o tráfego de rede para os endereços IP no pool de IP de back-end. Tenha em mente que cada instância tem um endereço IP.

### <a name="step-2"></a>Etapa 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Este exemplo configura o pool de endereços IP de back-end denominado "pool01" com endereços IP "134.170.185.46, 134.170.188.221,134.170.185.50." Esses valores são os endereços IP que recebem o tráfego de rede que vem do ponto de extremidade IP front-end. Substitua os endereços IP do exemplo anterior os endereços IP dos seus pontos de extremidade do aplicativo web.

### <a name="step-3"></a>Etapa 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Este exemplo configura configuração de gateway do aplicativo "poolsetting01" balanceamento de carga de tráfego de rede do pool de back-end.

### <a name="step-4"></a>Etapa 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Este exemplo configura a porta IP front-end denominada "frontendport01" para o ponto de extremidade IP público.

### <a name="step-5"></a>Etapa 5

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Este exemplo configura o certificado usado para conexão SSL. O certificado precisa estar no formato. pfx e a senha deve estar entre caracteres de 4 a 12.

### <a name="step-6"></a>Etapa 6

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Este exemplo cria a configuração de IP front-end denominada "fipconfig01" e associa o endereço IP público com a configuração de IP front-end.

### <a name="step-7"></a>Etapa 7

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Este exemplo cria o nome do ouvinte "listener01" e associa a porta front-end para a configuração de IP front-end e o certificado.

### <a name="step-8"></a>Etapa 8

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Esse exemplo cria a regra roteamento de Balanceador de carga denominada "rule01" que configura o comportamento de Balanceador de carga.

### <a name="step-9"></a>Etapa 9

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Este exemplo configura o tamanho da instância do gateway aplicativo.

>[AZURE.NOTE]  O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é médio. Você pode escolher entre Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Criar um gateway de aplicativo usando o novo AzureApplicationGateway

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Este exemplo cria um gateway aplicativo com todos os itens de configuração das etapas anteriores. No exemplo, o gateway de aplicativo é chamado "appgwtest".

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

Se desejar configurar um gateway de aplicativo para usar com um balanceador de carga interno (ILB), consulte [criar um gateway de aplicativo com um balanceador de carga interno (ILB)](application-gateway-ilb.md).

Se você desejar obter mais informações sobre opções de balanceamento de carregar em geral, consulte:

- [Balanceador de carga Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gerenciador de tráfego Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
