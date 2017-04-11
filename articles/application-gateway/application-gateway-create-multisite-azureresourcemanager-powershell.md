<properties
   pageTitle="Criar um gateway de aplicativo para hospedar vários sites | Microsoft Azure"
   description="Esta página fornece instruções para criar, configurar um gateway de aplicativo do Azure para hospedar vários aplicativos da web no mesmo gateway."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="amsriva"/>


# <a name="create-an-application-gateway-for-hosting-multiple-web-applications"></a>Criar um gateway de aplicativo para hospedar vários aplicativos da web

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-multisite-portal.md)
- [Azure PowerShell do Gerenciador de recursos](application-gateway-create-multisite-azureresourcemanager-powershell.md)

Várias opções de hospedagem de site permite que você implantar mais de um aplicativo da web no mesmo gateway aplicativo. Ele depende de presença do cabeçalho de host na solicitação de HTTP recebida, para determinar quais ouvinte deve receber o tráfego. O ouvinte depois direciona o tráfego ao pool de back-end apropriado conforme configurado na definição de regras do gateway. Em aplicativos da web SSL habilitado, o gateway de aplicativo depende a extensão de nome de servidor indicação (SNI) para escolher o ouvinte correto para o tráfego da web. Um uso comum para várias opções de hospedagem de site é carregar solicitações de saldo para domínios da web diferente para pools de servidor de back-end diferente. Da mesma forma vários subdomínios do mesmo domínio raiz também poderiam ser hospedados no mesmo gateway aplicativo.

## <a name="scenario"></a>Cenário

No exemplo a seguir, o gateway de aplicativo está servindo tráfego para contoso.com e fabrikam.com com dois grupos de servidor back-end: contoso pool de servidor e pool de servidor de fabrikam. Configuração semelhante pode ser usadas para subdomínios de host como app.contoso.com e blog.contoso.com.

![imageURLroute](./media/application-gateway-create-multisite-azureresourcemanager-powershell/multisite.png)

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente de cmdlets do PowerShell do Azure usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente da seção **Do Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Os servidores adicionados ao pool de back-end para usar o gateway de aplicativo devem existir ou seus pontos de extremidade criou em rede virtual em uma sub-rede separada ou com um IP/VIP público atribuído.

## <a name="requirements"></a>Requisitos

- **Pool de servidor back-end:** A lista de endereços IP de servidores back-end. Os endereços IP listados ou devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público. FQDN também pode ser usado.
- **Configurações de pool do servidor de back-end:** Cada pool tem as configurações como afinidade baseada em cookies, protocolo e porta. Essas configurações estão vinculadas a um pool e são aplicadas a todos os servidores dentro do pool.
- **Porta front-end:** Esta porta é a porta pública que é aberta no gateway do aplicativo. Tráfego visitas esta porta e, em seguida, é redirecionado para um dos servidores back-end.
- **Ouvinte:** O ouvinte tem uma porta de front-end, um protocolo (Http ou Https, esses valores diferenciam maiusculas de minúsculas) e o nome do certificado SSL (se configurar SSL descarregar). Para gateways de aplicativo habilitado de vários locais, nome do host e indicadores SNI também são adicionados.
- **Regra:** A regra associará o ouvinte, o pool de servidor back-end e define qual pool de servidor back-end o tráfego deve ser direcionado para quando ele atinge um determinado ouvinte.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicativo

A seguir estão as etapas necessárias para criar um gateway de aplicativo:

1. Crie um grupo de recursos para o Gerenciador de recursos.
2. Crie uma rede virtual, sub-redes e IP público para o gateway de aplicativo.
3. Crie um objeto de configuração do gateway de aplicativo.
4. Crie um recurso de gateway do aplicativo.

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

    Select-AzureRmSubscription -SubscriptionName "Name of subscription"

### <a name="step-4"></a>Etapa 4

Crie um grupo de recursos (ignorar esta etapa se você estiver usando um grupo existente do recurso).

    New-AzureRmResourceGroup -Name appgw-RG -location "West US"

Alternativamente você também pode criar marcas para um grupo de recursos para o gateway de aplicativo:

    $resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"}

Gerenciador de recursos de Azure requer que todos os grupos de recursos especificam um local. Esse local é usado como o local padrão para os recursos desse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway aplicativo usam o mesmo grupo de recursos.

No exemplo acima, criamos um grupo de recursos chamado "appgw-RG" com um local de "Oeste EUA".

>[AZURE.NOTE] Se você precisar configurar um teste personalizado para seu gateway de aplicativo, consulte [criar um gateway de aplicativo com testes personalizados usando o PowerShell](application-gateway-create-probe-ps.md). Visite [testes personalizados e monitoramento de integridade](application-gateway-probe-overview.md) para obter mais informações.

## <a name="create-a-virtual-network-and-subnets"></a>Criar uma rede virtual e sub-redes

O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de recursos. Duas sub-redes são criadas nesta etapa. A primeira sub-rede é para o gateway de aplicativo em si. Gateway de aplicativo requer sua própria sub-rede para armazenar suas instâncias. Somente outros gateways de aplicativo podem ser implantados nessa sub-rede. A segunda sub-rede é usada para manter os servidores de back-end do aplicativo.

### <a name="step-1"></a>Etapa 1

Atribua o endereço intervalo 10.0.0.0/24 variável de sub-rede a ser usado para manter o gateway do aplicativo.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Etapa 2

Atribua o endereço intervalo 10.0.1.0/24 variável de subnet2 a ser usado para os pools de back-end.

    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -AddressPrefix 10.0.1.0/24

### <a name="step-3"></a>Etapa 3

Criar uma rede virtual denominada "appgwvnet" no recurso grupo "appgw-rg" para a região Oeste EUA usando o prefixo 10.0.0.0/16 com sub-rede 10.0.0.0/24 e 10.0.1.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet,$subnet2


### <a name="step-4"></a>Etapa 4

Atribua uma variável de sub-rede para as próximas etapas, que cria um gateway de aplicativos.

    $appgatewaysubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -VirtualNetwork $vnet
    $backendsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -VirtualNetwork $vnet

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso IP público "publicIP01" no recurso grupo "appgw-rg" para a região Oeste EUA.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic

Um endereço IP é atribuído para o gateway de aplicativo quando o serviço é iniciado.

## <a name="create-application-gateway-configuration"></a>Criar a configuração de gateway do aplicativo

Você deve configurar todos os itens de configuração antes de criar o gateway do aplicativo. As etapas a seguir criam os itens de configuração que são necessários para um recurso de gateway do aplicativo.

### <a name="step-1"></a>Etapa 1

Crie uma configuração de IP do gateway de aplicativo denominada "gatewayIP01". Quando o gateway do aplicativo for iniciado, ele seleciona um endereço IP da sub-rede configurado e rotear o tráfego de rede para os endereços IP no pool de IP de back-end. Tenha em mente que cada instância tem um endereço IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnet

### <a name="step-2"></a>Etapa 2

Configurar o pool de endereços IP de back-end denominada "pool01" e "pool2" com endereços IP "10.0.1.100, 10.0.1.101,10.0.1.102" para "pool1" e "10.0.1.103, 10.0.1.104, 10.0.1.105" para "pool2".

    $pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.1.100, 10.0.1.101, 10.0.1.102
    $pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.1.103, 10.0.1.104, 10.0.1.105

Neste exemplo, há dois pools de back-end para rotear o tráfego de rede com base no site solicitado. Um pool recebe o tráfego de site "contoso.com" e outro pool recebe o tráfego de site "fabrikam.com". Você precisa substituir os endereços IP anteriores para adicionar seus próprios pontos de extremidade do endereço IP do aplicativo. No lugar de endereços IP internos, você também pode usar endereços IP públicos, FQDN ou NIC de uma máquina virtual para instâncias de back-end. Use "-BackendFQDNs" parâmetro no PowerShell para especificar FQDNs em vez de IPs.

### <a name="step-3"></a>Etapa 3

Configure a configuração de gateway do aplicativo "poolsetting01" e "poolsetting02" para o tráfego de rede de balanceamento de carga no pool de back-end. Neste exemplo, você definir configurações de grupo de back-end diferente para os pools de back-end. Cada pool de back-end pode ter sua própria configuração de pool de back-end.

    $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
    $poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### <a name="step-4"></a>Etapa 4

Configure o IP front-end com ponto de extremidade IP público.

    $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### <a name="step-5"></a>Etapa 5

Configure a porta front-end para um gateway de aplicativo.

    $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443

### <a name="step-6"></a>Etapa 6

Configure dois certificados SSL para os dois sites que vamos suporte neste exemplo. Um certificado é para o tráfego de contoso.com e a outra é para o tráfego de fabrikam.com. Esses certificados devem ser uma autoridade de certificação emitidos certificados para seus sites. Certificados autoassinados são suportados, mas não recomendados para tráfego de produção.

    $cert01 = New-AzureRmApplicationGatewaySslCertificate -Name contosocert -CertificateFile <file path> -Password <password>
    $cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>


### <a name="step-7"></a>Etapa 7

Configure dois ouvintes para os dois sites neste exemplo. Esta etapa configura os ouvintes para público endereço IP, porta e host usado para receber tráfego de entrada. Parâmetro de nome do host é necessário para o suporte a vários sites e deve ser definido para o site apropriado para o qual o tráfego for recebido. Parâmetro RequireServerNameIndication deve ser definido como true para sites que precisam de suporte para SSL em um cenário com vários host. Se o suporte SSL for necessário, você também precisa especificar o certificado SSL que é usado para proteger o tráfego para o aplicativo web. A combinação de FrontendIPConfiguration, FrontendPort e HostName deve ser exclusiva para um ouvinte. Cada ouvinte pode oferecer suporte a um certificado.

    $listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
    $listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02

### <a name="step-8"></a>Etapa 8

Crie dois configuração de regra para os aplicativos dois web neste exemplo. Uma regra vincula ouvintes, pools de back-end e configurações de http. Esta etapa configura o gateway de aplicativo para usar a regra de roteamento básica, uma para cada site. O tráfego para cada site é recebido pelo seu ouvinte configurado e será direcionado para seu pool de back-end configurado, usando as propriedades especificadas na BackendHttpSettings.

    $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
    $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2

### <a name="step-9"></a>Etapa 9

Configure o número de instâncias e o tamanho para o gateway de aplicativo.

    $sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2

## <a name="create-application-gateway"></a>Criar o gateway de aplicativo

Crie um gateway de aplicativo com todos os objetos de configuração das etapas anteriores.

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02


>[AZURE.IMPORTANT] Provisionamento de Gateway do aplicativo é uma operação de execução demorada e pode levar algum tempo para ser concluída.

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

Saiba como proteger seus sites com [Application Gateway - Firewall do aplicativo Web](application-gateway-webapplicationfirewall-overview.md)