<properties
   pageTitle="Configurar o Firewall do aplicativo da Web em nova ou existente Gateway de aplicativo | Microsoft Azure"
   description="Este artigo fornece orientação sobre como começar a usar o firewall do aplicativo da web em um gateway de aplicativo novo ou existente."
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
   ms.date="10/25/2016"
   ms.author="gwallace"/>

# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Configurar o Firewall do aplicativo da Web em um Gateway de aplicativo nova ou existente

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-web-application-firewall-portal.md)
- [Azure PowerShell do Gerenciador de recursos](application-gateway-web-application-firewall-powershell.md)

O firewall do aplicativo da web (WAF) no Azure Application Gateway protege aplicativos web contra ataques comuns baseado na web, como a inclusão de SQL, ataques de script entre sites e sequestros de sessão.

Azure Application Gateway é um balanceador de carga de camada-7. Ele oferece failover, roteamento de desempenho solicitações HTTP entre diferentes servidores, independentemente de estarem na nuvem ou local. Aplicativo fornece vários recursos de controlador de entrega de aplicativo (ADC), incluindo balanceamento de carga de HTTP afinidade de sessão baseada em cookies, Secure Sockets Layer (SSL) descarregar, sondagens de integridade personalizados, suporte para vários locais e várias outras. Para localizar uma lista completa de recursos com suporte, visite visão geral de Gateway do aplicativo

O seguinte artigo mostra como [Adicionar firewall do aplicativo da web para um gateway de aplicativo existente](#add-web-application-firewall-to-an-existing-application-gateway) e [criar um gateway de aplicativos que usa o firewall do aplicativo web](#create-an-application-gateway-with-web-application-firewall).

![imagem de cenário][scenario]

## <a name="waf-configuration-differences"></a>Diferenças de configuração de WAF

Se você tiver lido [criar um Gateway de aplicativo com o PowerShell](application-gateway-create-gateway-arm.md), você entender as configurações de SKU configurar ao criar um gateway de aplicativos. WAF fornece configurações adicionais para definir ao configurar o SKU em um gateway de aplicativo. Não há nenhuma alteração adicional que você fizer no gateway do aplicativo em si.

**SKU** - um gateway de aplicativo normal sem WAF suporta **padrão\_pequenas**, **padrão\_médio**, e **padrão\_grande** tamanhos. Com a introdução do WAF, há duas SKUs adicionais, **WAF\_médio** e **WAF\_grande**. WAF não é suportado nos gateways de aplicativo pequeno.

**Nível** - os valores disponíveis são **padrão** ou **WAF**. Ao usar o Firewall do aplicativo da Web, **WAF** deve ser escolhido.

**Modo** - esta configuração é o modo de WAF. valores permitidos são **detecção** e **prevenção**. Quando WAF estiver configurado no modo de detecção, todas as ameaças são armazenadas em um arquivo de log. No modo de prevenção, eventos ainda estão conectados, mas o invasor recebe uma resposta não autorizado 403 do gateway aplicativo.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Adicionar o firewall do aplicativo da web a um gateway de aplicativo existente

Certifique-se de que você está usando a versão mais recente do PowerShell do Azure. Mais informações estão disponíveis em [Usando o Windows PowerShell com o Gerenciador de recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Etapa 1

Faça logon em sua conta do Azure.

    Login-AzureRmAccount

### <a name="step-2"></a>Etapa 2

Selecione a assinatura a ser usada para esse cenário.

    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Etapa 3

Recupere o gateway que você está adicionando Firewall do aplicativo Web.

    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"


### <a name="step-4"></a>Etapa 4

Configure o sku de firewall do aplicativo web. Os tamanhos disponíveis são **WAF\_grande** e **WAF\_médio**. Quando o firewall do aplicativo da web é usado a camada deve ser **WAF**, a capacidade deve ser confirmada ao configurar o sku.

    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2

### <a name="step-5"></a>Etapa 5

Defina as configurações de WAF conforme definido no exemplo a seguir:

Para a configuração de **WafMode** , os valores disponíveis são prevenção e detecção.

    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention

### <a name="step-6"></a>Etapa 6

Atualize o gateway de aplicativo com as configurações definidas na etapa anterior.

    Set-AzureRmApplicationGateway -ApplicationGateway $gw

Este comando atualiza o gateway de aplicativo com Firewall do aplicativo Web. É recomendável exibir [Diagnóstico de Gateway do aplicativo](application-gateway-diagnostics.md) para entender como exibir logs do seu gateway do aplicativo. Devido a natureza segurança WAF, logs precisam ser revisado regularmente para entender a postura de segurança dos seus aplicativos web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Criar um Gateway de aplicativo com o Firewall do aplicativo Web

As etapas a seguir levam você em todo o processo do início ao fim para a criação de um Gateway de aplicativo com o Firewall do aplicativo Web.

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

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-4"></a>Etapa 4

Crie um grupo de recursos (ignorar esta etapa se você estiver usando um grupo existente do recurso).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Gerenciador de recursos de Azure requer que todos os grupos de recursos especificam um local. Esse local é usado como o local padrão para os recursos desse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway aplicativo usa o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos chamado "appgw-RG" e local "Oeste EUA".

>[AZURE.NOTE] Se você precisar configurar um teste personalizado para seu gateway de aplicativo, consulte [criar um gateway de aplicativo com testes personalizados usando o PowerShell](application-gateway-create-probe-ps.md). Confira [testes personalizados e monitoramento de integridade](application-gateway-probe-overview.md) para obter mais informações.

### <a name="step-5"></a>Etapa 5

Atribua um intervalo de endereços para a sub-rede ser usado para o Gateway de aplicativo em si.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] Uma sub-rede para um aplicativo deve ter um mínimo de 28 bits de máscara. Este valor deixa 10 endereços disponíveis na sub-rede por instâncias de Gateway do aplicativo. Com uma sub-rede menor, você não poderá adicionar mais instância do seu gateway de aplicativo no futuro.

### <a name="step-6"></a>Etapa 6

Atribua um intervalo de endereços a ser usado para o pool de endereços de back-end.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-7"></a>Etapa 7

Criar uma rede virtual com as sub-redes anteriores no grupo de recursos criada na etapa: [criar o grupo de recursos](#create-the-resource-group)

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-8"></a>Etapa 8

Recupere o recurso de rede virtual e recursos de sub-rede a ser usado nas etapas a seguir:

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

### <a name="step-9"></a>Etapa 9

Crie um recurso IP público a ser usado para o gateway de aplicativo. Este endereço IP público é usado em uma das seguintes etapas:

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Gateway de aplicativo não suporta o uso de um endereço IP público criado com um rótulo de domínio definido. Há suporte para apenas um endereço IP público com um rótulo de domínio dinamicamente criado. Se você solicitar um nome amigável de dns para o gateway de aplicativo, é recomendável usar um registro cname como um alias.

### <a name="step-10"></a>Etapa 10

Você deve configurar todos os itens de configuração antes de criar o gateway do aplicativo. As etapas a seguir criam os itens de configuração que são necessários para um recurso de gateway do aplicativo.

Criar uma configuração de IP de gateway do aplicativo, isso configura quais sub-rede usa o Gateway do aplicativo. Quando o Gateway do aplicativo for iniciado, ele seleciona um endereço IP da sub-rede configurado e rotear o tráfego de rede para os endereços IP no pool de IP de back-end. Tenha em mente que cada instância tem um endereço IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-11"></a>Etapa 11

Configure o pool de endereço IP de back-end com os endereços IP dos servidores web back-end. Esses endereços IP são os endereços IP que recebem o tráfego de rede que vem do ponto de extremidade IP front-end. Substitua os seguintes endereços IP para adicionar seus próprios pontos de extremidade do endereço IP do aplicativo.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

### <a name="step-12"></a>Etapa 12

Carregar o certificado a ser usada nos recursos do pool de back-end habilitado ssl.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

### <a name="step-13"></a>Etapa 13

Defina as configurações de http de back-end de gateway do aplicativo. Atribua o certificado carregado na etapa anterior para as configurações de http.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-14"></a>Etapa 14

Configure a porta IP front-end para o ponto de extremidade IP público. Esta porta é a porta que os usuários finais se conectam ao.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-15"></a>Etapa 15

Criar uma configuração de IP front-end, essa configuração mapeia um endereço ip particular ou pública para o front-end do gateway aplicativo. A seguinte etapa associa o endereço IP público na etapa anterior a configuração de IP front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-16"></a>Etapa 16

Configure o certificado para o gateway de aplicativo. Este certificado é usado para descriptografar e criptografar novamente o tráfego no gateway do aplicativo.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

### <a name="step-17"></a>Etapa 17

Crie o ouvinte HTTP para o gateway de aplicativo. Atribua o certificado de configuração, porta e ssl de ip front-end para usar.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-18"></a>Etapa 18

Crie uma regra de roteamento de Balanceador de carga que configura o comportamento de Balanceador de carga. Neste exemplo, uma regra básica round robin é criada.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   
### <a name="step-19"></a>Etapa 19

Configure o tamanho de instância do gateway aplicativo.

    $sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

>[AZURE.NOTE]  Você pode escolher entre **WAF\_médio** e **WAF\_grande**, a camada quando usando WAF é sempre **WAF**. Capacidade é qualquer número entre 1 e 10.

### <a name="step-20"></a>Passo 20

Configurar o modo de WAF, valores aceitáveis são **prevenção** e **detecção**.

    $config = New-AzureRmApplicationGatewayWafConfig -Enabled $true -WafMode "Prevention"

### <a name="step-21"></a>Passo 21

Crie um gateway de aplicativo com todos os itens de configuração das etapas anteriores. Neste exemplo, o gateway de aplicativo é chamado "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert

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

Saiba como configurar o log de diagnóstico, para registrar os eventos que são detectados ou impedidos com Firewall do aplicativo Web visitando o [Diagnóstico de Gateway do aplicativo](application-gateway-diagnostics.md)


[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png