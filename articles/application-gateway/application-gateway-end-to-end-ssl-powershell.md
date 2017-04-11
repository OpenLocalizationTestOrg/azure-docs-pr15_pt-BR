<properties
    pageTitle="Configurar política de SSL e SSL de ponta a ponta com Application Gateway | Microsoft Azure"
    description="Este artigo descreve como configurar SSL de ponta a ponta com Application Gateway usando o PowerShell do Gerenciador de recursos do Azure"
    services="application-gateway"
    documentationCenter="na"
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

# <a name="configure-ssl-policy-and-end-to-end-ssl-with-application-gateway-using-powershell"></a>Configurar política de SSL e SSL de ponta a ponta com Application Gateway usando o PowerShell

## <a name="overview"></a>Visão geral

Gateway de aplicativo dá suporte à criptografia de ponta a ponta de tráfego. Gateway de aplicativo faz isso encerrando a conexão SSL no gateway de aplicativo. O gateway, em seguida, aplica as regras de roteamento para o tráfego, criptografa novamente o pacote e encaminha o pacote para o back-end apropriado com base nas regras de roteamento definidas. Qualquer resposta do servidor web percorre o mesmo processo de volta para o usuário final.

Outro recurso gateway desse aplicativo suporta está desabilitando certas versões de protocolo SSL. Gateway de aplicativo oferece suporte para desabilitar a seguinte versão de protocolo; TLSv1.0, TLSv1.1 e TLSv1.2.

> [AZURE.NOTE] SSL 2.0 e SSL 3.0 estão desabilitadas por padrão e não podem ser ativado. Elas são consideradas não seguras e não são capazes de ser usada com o Gateway de aplicativo

![imagem de cenário][scenario]

## <a name="scenario"></a>Cenário

Neste cenário, você saiba como criar um gateway de aplicativo usando SSL de ponta a ponta usando o PowerShell.

Este cenário irá:

- Criar um grupo de recursos denominado "appgw-rg"
- Crie uma rede virtual denominada "appgwvnet" com um bloco CIDR reservado de 10.0.0.0/16.
- Crie duas sub-redes chamado "appgwsubnet" e "appsubnet".
- Crie um gateway de aplicativo pequeno criptografia de SSL de ponta a ponta que desativa determinados protocolos SSL de suporte.

## <a name="before-you-begin"></a>Antes de começar

Para configurar SSL de ponta a ponta com um gateway de aplicativos, um certificado é necessário para o gateway e os certificados são necessários para os servidores de back-end. O certificado de gateway é usado para criptografar e descriptografar o tráfego enviado a ele usando SSL. O certificado de gateway precisa estar no formato de troca de informações pessoais (pfx). Este formato de arquivo permite a chave privada a ser exportado que é requerido pelo gateway de aplicativo para executar a criptografia e descriptografia de tráfego.

Para criptografia ssl de ponta a ponta back-end deve estar na lista branca com application gateway. Isso é feito carregando o certificado pública dos back-ends para o gateway de aplicativo. Isso garante que o gateway de aplicativo somente se comunica com instâncias de back-end conhecidos. Isso protege ainda mais a comunicação de ponta a ponta.

Esse processo é descrito nas etapas a seguir:

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Esta seção o orienta a criação de um grupo de recursos, que contém o gateway do aplicativo.

### <a name="step-1"></a>Etapa 1

Faça logon em sua conta do Azure.

    Login-AzureRmAccount

### <a name="step-2"></a>Etapa 2

Selecione a assinatura a ser usada para esse cenário.

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Etapa 3

Crie um grupo de recursos (ignorar esta etapa se você estiver usando um grupo existente do recurso).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicativo

O exemplo a seguir cria uma rede virtual e duas sub-redes. Uma sub-rede é usada para manter o gateway do aplicativo. A outra sub-rede é usado para o back-ends hospeda o aplicativo web.

### <a name="step-1"></a>Etapa 1

Atribua um intervalo de endereços para a sub-rede ser usado para o Gateway de aplicativo em si.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] Sub-redes configurados para o gateway de aplicativo devem ser ajustados corretamente. Um gateway de aplicativo pode ser configurado para até 10 instâncias. Cada instância leva 1 endereço IP da sub-rede. Muito pequeno de uma sub-rede pode afetar negativamente dimensionamento de um gateway de aplicativos.

### <a name="step-2"></a>Etapa 2

Atribua um intervalo de endereços a ser usado para o pool de endereços de back-end.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-3"></a>Etapa 3

Crie uma rede virtual com as sub-redes definidas nas etapas anteriores.

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-4"></a>Etapa 4

Recupere o recurso de rede virtual e recursos de sub-rede a ser usado nas etapas a seguir:

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso IP público a ser usado para o gateway de aplicativo. Este endereço IP público é usado uma etapa seguinte.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Gateway de aplicativo não suporta o uso de um endereço IP público criado com um rótulo de domínio definido. Há suporte para apenas um endereço IP público com um rótulo de domínio dinamicamente criado. Se você solicitar um nome amigável de dns para o gateway de aplicativo, é recomendável usar um registro cname como um alias.

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway de aplicativo

Você deve configurar todos os itens de configuração antes de criar o gateway do aplicativo. As etapas a seguir criam os itens de configuração que são necessários para um recurso de gateway do aplicativo.

### <a name="step-1"></a>Etapa 1

Criar uma configuração de IP de gateway do aplicativo, essa configuração configura quais sub-rede usa o gateway do aplicativo. Quando o gateway do aplicativo for iniciado, ele seleciona um endereço IP da sub-rede configurado e rotear o tráfego de rede para os endereços IP no pool de IP de back-end. Tenha em mente que cada instância tem um endereço IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-2"></a>Etapa 2

Criar uma configuração de IP front-end, essa configuração mapeia um endereço ip particular ou pública para o front-end do gateway aplicativo. A seguinte etapa associa o endereço IP público na etapa anterior a configuração de IP front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-3"></a>Etapa 3

Configure o pool de endereço IP de back-end com os endereços IP dos servidores web back-end. Esses endereços IP são os endereços IP que recebem o tráfego de rede que vem do ponto de extremidade IP front-end. Substitua os seguintes endereços IP para adicionar seus próprios pontos de extremidade do endereço IP do aplicativo.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

> [AZURE.NOTE] Um nome de domínio totalmente qualificado (FQDN) também é um valor válido no lugar de um endereço ip para os servidores de back-end usando a opção - BackendFqdns.

### <a name="step-4"></a>Etapa 4

Configure a porta IP front-end para o ponto de extremidade IP público. Esta porta é a porta que os usuários finais se conectam ao.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-5"></a>Etapa 5

Configure o certificado para o gateway de aplicativo. Este certificado é usado para descriptografar e criptografar novamente o tráfego no gateway do aplicativo.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

> [AZURE.NOTE] Este exemplo configura o certificado usado para conexão SSL. O certificado precisa estar no formato. pfx e a senha deve estar entre caracteres de 4 a 12.

### <a name="step-6"></a>Etapa 6

Crie o ouvinte HTTP para o gateway de aplicativo. Atribua o certificado de configuração, porta e ssl de ip front-end para usar.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-7"></a>Etapa 7

Carregar o certificado a ser usada nos recursos do pool de back-end habilitado ssl.

> [AZURE.NOTE] O teste de padrão obtém a chave pública da vinculação de SSL **padrão** no endereço IP do back-end e compara o valor da chave público que ele recebe o valor da chave público fornecidas aqui. A chave pública recuperada pode não ser necessariamente o local desejado para o qual o tráfego fluirá **se** você estiver usando cabeçalhos de host e SNI no back-end. Se estiver em dúvida, visite https://127.0.0.1/ sobre os back-ends para confirmar qual certificado é usado para a vinculação de SSL **padrão** . Use a chave pública do que a solicitação nesta seção. Se você estiver usando cabeçalhos de host e SNI nas ligações HTTPS e você não recebe uma resposta e o certificado de uma solicitação de navegador manual para https://127.0.0.1/ em back-ends, você deve configurar uma associação de SSL padrão em back-ends. Se você não fizer isso, testes falhará e back-end não estará na lista branca.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer

> [AZURE.NOTE] O certificado fornecido nesta etapa deve ser a chave pública do certificado de pfx presente no back-end. Exporte o certificado (não o certificado raiz) instalado no servidor back-end. CER Formatar e usá-lo nesta etapa. Esta etapa brancas back-end com o gateway do aplicativo.

### <a name="step-8"></a>Etapa 8

Defina as configurações de http de back-end de gateway do aplicativo. Atribua o certificado carregado na etapa anterior para as configurações de http.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-9"></a>Etapa 9

Crie uma regra de roteamento de Balanceador de carga que configura o comportamento de Balanceador de carga. Neste exemplo, uma regra básica round robin é criada.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-10"></a>Etapa 10

Configure o tamanho de instância do gateway aplicativo.  Os tamanhos disponíveis são **padrão\_pequenas**, **padrão\_médio**, e **padrão\_grande**.  De capacidade e os valores disponíveis são 1 a 10.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE] Uma contagem de instância de 1 pode ser escolhida para fins de teste. É importante saber que qualquer contagem de instância em duas instâncias não é coberto pelo SLA e, portanto, não recomendável. Gateways Small devem ser usadas para teste de desenvolvimento e não para fins de produção.

### <a name="step-11"></a>Etapa 11

Configure a política SSL a ser usado no aplicativo Gateway. Gateway de aplicativo é compatível com a capacidade de desabilitar determinadas versões de protocolo SSL.

Os valores a seguir são uma lista de versões de protocolo que pode ser desabilitado.

- **TLSv1_0**
- **TLSv1_1**
- **TLSv1_2**

O exemplo a seguir desativa TLSv1\_0.

    $sslPolicy = New-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0

## <a name="create-the-application-gateway"></a>Criar o Gateway de aplicativo

Usando as etapas anteriores, crie o Gateway do aplicativo. A criação do gateway é um processo longo em execução.

    $appgw = New-AzureRmApplicationGateway -Name appgateway -SslCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicy -AuthenticationCertificates $authcert -Verbose

## <a name="disable-ssl-protocol-versions-on-an-existing-application-gateway"></a>Desativar versões de protocolo SSL em um Gateway de aplicativo existente

As etapas anteriores levam pela criação de um aplicativo com ssl de ponta a ponta e desabilitando certas versões de protocolo SSL. O exemplo a seguir desativa determinadas políticas SSL em um gateway de aplicativo existente.

### <a name="step-1"></a>Etapa 1

Recupere o gateway de aplicativo para atualizar.

    $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG

### <a name="step-2"></a>Etapa 2

Defina uma política de SSL. No exemplo a seguir, TLSv1.0 e TLSv1.1 estão desabilitados.

    Set-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0, TLSv1_1 -ApplicationGateway $gw

### <a name="step-3"></a>Etapa 3

Finalmente, atualize o gateway. É importante observar que a última etapa é uma tarefa demorada. Quando estiver pronto, ssl de ponta a ponta está configurado no gateway do aplicativo.

    $gw | Set-AzureRmApplicationGateway

## <a name="get-application-gateway-dns-name"></a>Obter o nome DNS de gateway do aplicativo

Depois que o gateway é criado, a próxima etapa é configurar o front-end para comunicação. Ao usar um IP público, gateway aplicativo requer um nome DNS atribuído dinamicamente, que não é compatível com. Pode ser usado para garantir que os usuários finais possam atingir o gateway de aplicativo um registro CNAME para apontar para o ponto de extremidade público do gateway aplicativo. [Configurando um nome de domínio personalizado para no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para fazer isso, recupere detalhes do gateway aplicativo e seu nome IP/DNS associado usando o elemento de PublicIPAddress anexado para o gateway de aplicativo. Nome DNS do gateway aplicativo deve ser usado para criar um registro CNAME, que aponta os aplicativos dois web para esse nome DNS. O uso de registros de não é recomendável porque o VIP pode ser alterados na reinicialização do gateway de aplicativo.
    
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

Saiba mais sobre otimizar a segurança dos seus aplicativos da web com o Firewall do aplicativo Web por meio do Gateway de aplicativo visitando [Visão geral sobre o Firewall de aplicativo Web](application-gateway-webapplicationfirewall-overview.md)

[scenario]: ./media/application-gateway-end-to-end-ssl-powershell/scenario.png
