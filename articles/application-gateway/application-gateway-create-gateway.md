<properties
   pageTitle="Criar, iniciar ou excluir um gateway aplicativo | Microsoft Azure"
   description="Esta página fornece instruções para criar, configurar, iniciar e excluir um gateway de aplicativo do Azure"
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

# <a name="create-start-or-delete-an-application-gateway"></a>Criar, iniciar ou excluir um gateway de aplicativo

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell do Gerenciador de recursos](application-gateway-create-gateway-arm.md)
- [Azure PowerShell clássico](application-gateway-create-gateway.md)
- [Modelo do Gerenciador de recursos Azure](application-gateway-create-gateway-arm-template.md)
- [CLI Azure](application-gateway-create-gateway-cli.md)

Azure Application Gateway é um balanceador de carga de camada-7. Ele oferece failover, roteamento de desempenho solicitações HTTP entre diferentes servidores, independentemente de estarem na nuvem ou local. Gateway do aplicativo fornece vários recursos de controlador de entrega de aplicativo (ADC), incluindo balanceamento de carga de HTTP afinidade de sessão baseada em cookies, Secure Sockets Layer (SSL) descarregar, sondagens de integridade personalizados, suporte para vários locais e várias outras. Para localizar uma lista completa de recursos com suporte, visite [Visão geral de Gateway do aplicativo](application-gateway-introduction.md)

Este artigo explica as etapas para criar, configurar, iniciar e excluir um gateway de aplicativos.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente de cmdlets do PowerShell do Azure usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente da seção **Do Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Se você tiver uma rede virtual existente, selecione uma sub-rede vazia existente ou criar uma nova sub-rede na rede virtual existente unicamente para uso pelo gateway de aplicativo. Você não pode implantar o gateway de aplicativo para uma rede virtual diferente os recursos que você pretende implantar atrás do gateway de aplicativo, a menos que vnet correspondência é usado. Para saber mais, visite [Vnet correspondência](../virtual-network/virtual-network-peering-overview.md)
3. Verifique se você tem uma rede virtual de trabalho com uma sub-rede válida. Certifique-se de que nenhuma máquinas virtuais ou implantações de nuvem estão usando a sub-rede. O gateway de aplicativo deve estar sozinha em uma sub-rede de rede virtual.
3. Os servidores que você configura para usar o gateway de aplicativo devem existir ou atribuiu seus pontos de extremidade criados na rede virtual ou com um IP/VIP público.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway de aplicativo?

Quando você usa o comando **New-AzureApplicationGateway** para criar o gateway do aplicativo, nenhuma configuração está definida neste ponto, e o recurso recém-criado são configurados usando XML ou um objeto de configuração.

Os valores são:

- **Pool de servidor back-end:** A lista de endereços IP de servidores back-end. Os endereços IP listados ou devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público.
- **Configurações de pool do servidor de back-end:** Cada pool tem as configurações como afinidade baseada em cookies, protocolo e porta. Essas configurações estão vinculadas a um pool e são aplicadas a todos os servidores dentro do pool.
- **Porta front-end:** Esta porta é a porta pública que é aberta no gateway do aplicativo. Tráfego visitas esta porta e, em seguida, é redirecionado para um dos servidores back-end.
- **Ouvinte:** O ouvinte tem uma porta de front-end, um protocolo (Http ou Https, esses valores diferenciam maiusculas de minúsculas) e o nome do certificado SSL (se configurar SSL descarregar).
- **Regra:** A regra associará o ouvinte e o pool de servidor back-end e define qual pool de servidor back-end o tráfego deve ser direcionado para quando ele atinge um determinado ouvinte.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicativo

Para criar um gateway de aplicativo:

1. Crie um recurso de gateway do aplicativo.
2. Crie um arquivo de configuração de XML ou um objeto de configuração.
3. Confirme a configuração para o recurso de gateway do aplicativo recém-criado.

>[AZURE.NOTE] Se você precisar configurar um teste personalizado para seu gateway de aplicativo, consulte [criar um gateway de aplicativo com testes personalizados usando o PowerShell](application-gateway-create-probe-classic-ps.md). Confira [testes personalizados e monitoramento de integridade](application-gateway-probe-overview.md) para obter mais informações.

![Exemplo de cenário][scenario]

### <a name="create-an-application-gateway-resource"></a>Criar um recurso de gateway do aplicativo

Para criar o gateway, use o cmdlet **New-AzureApplicationGateway** , substituindo os valores com seus próprios. Cobrança para o gateway não iniciar neste momento. Cobrança começa em uma etapa posterior, quando o gateway é iniciado com êxito.

O exemplo a seguir cria um gateway aplicativo usando uma rede virtual chamado "testvnet1" e uma sub-rede chamado "sub-rede-1".


    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Descrição*, *InstanceCount*e *GatewaySize* são parâmetros opcionais.

Para validar que o gateway foi criado, você pode usar o cmdlet **Get-AzureApplicationGateway** .

    Get-AzureApplicationGateway AppGwTest
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Stopped
    VirtualIPs    : {}
    DnsName       :

>[AZURE.NOTE]  O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é médio. Você pode escolher entre pequeno, médio e grande.

*VirtualIPs* e *DnsName* são mostrados em branco porque o gateway ainda não foi iniciado. Estes são criadas depois que o gateway está no estado de execução.

## <a name="configure-the-application-gateway"></a>Configurar o gateway de aplicativo

Você pode configurar o gateway de aplicativo usando XML ou um objeto de configuração.

## <a name="configure-the-application-gateway-by-using-xml"></a>Configurar o gateway de aplicativo usando o XML

No exemplo a seguir, você pode usar um arquivo XML para definir todas as configurações de gateway do aplicativo e confirmá-las para o recurso de gateway do aplicativo.  

### <a name="step-1"></a>Etapa 1  

Copie o seguinte texto para o bloco de notas.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>(name-of-your-frontend-port)</Name>
                <Port>(port number)</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>(name-of-your-backend-pool)</Name>
                <IPAddresses>
                    <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
                    <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>(backend-setting-name-to-configure-rule)</Name>
                <Port>80</Port>
                <Protocol>[Http|Https]</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>(name-of-the-listener)</Name>
                <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
                <Protocol>[Http|Https]</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>(name-of-load-balancing-rule)</Name>
                <Type>basic</Type>
                <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
                <Listener>(name-of-the-listener)</Listener>
                <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>

Edite os valores entre os parênteses para os itens de configuração. Salve o arquivo com extensão. XML.

>[AZURE.IMPORTANT] O item de protocolo Http ou Https diferencia maiusculas de minúsculas.

O exemplo a seguir mostra como usar um arquivo de configuração para configurar o gateway de aplicativo. A carga de exemplo equilibra o tráfego HTTP na porta pública 80 e envia o tráfego de rede à porta de back-end 80 entre dois endereços IP.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>FrontendPort1</Name>
                <Port>80</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>BackendPool1</Name>
                <IPAddresses>
                    <IPAddress>10.0.0.1</IPAddress>
                    <IPAddress>10.0.0.2</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>BackendSetting1</Name>
                <Port>80</Port>
                <Protocol>Http</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>HTTPListener1</Name>
                <FrontendPort>FrontendPort1</FrontendPort>
                <Protocol>Http</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>HttpLBRule1</Name>
                <Type>basic</Type>
                <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
                <Listener>HTTPListener1</Listener>
                <BackendAddressPool>BackendPool1</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


### <a name="step-2"></a>Etapa 2

Em seguida, defina o gateway do aplicativo. Use o cmdlet **Set-AzureApplicationGatewayConfig** com um arquivo XML de configuração.


    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## <a name="configure-the-application-gateway-by-using-a-configuration-object"></a>Configurar o gateway de aplicativo usando um objeto de configuração

O exemplo a seguir mostra como configurar o gateway de aplicativo usando objetos de configuração. Todos os itens de configuração devem ser configurados individualmente e, em seguida, adicionados a um objeto de configuração do gateway de aplicativo. Depois de criar o objeto de configuração, você pode usar o comando de **Definir AzureApplicationGateway** para confirmar a configuração para o recurso de gateway do aplicativo criado anteriormente.

>[AZURE.NOTE] Antes de atribuir um valor a cada objeto de configuração, você precisa declarar que tipo de objeto PowerShell usa para armazenamento. A primeira linha para criar os itens individuais define quais Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model (nome de objeto) são usadas.

### <a name="step-1"></a>Etapa 1

Crie todos os itens de configuração individual.

Crie o IP front-end, conforme mostrado no exemplo a seguir.

    $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
    $fip.Name = "fip1"
    $fip.Type = "Private"
    $fip.StaticIPAddress = "10.0.0.5"

Crie a porta front-end, conforme mostrado no exemplo a seguir.

    $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
    $fep.Name = "fep1"
    $fep.Port = 80

Crie o pool de servidor back-end.

 Defina os endereços IP que são adicionados ao pool de servidor back-end, conforme mostrado no exemplo a seguir.


    $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
    $servers.Add("10.0.0.1")
    $servers.Add("10.0.0.2")

 Use o objeto $server para adicionar os valores para o objeto de pool de back-end ($pool).

    $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
    $pool.BackendServers = $servers
    $pool.Name = "pool1"

Crie a configuração de pool de servidor back-end.

    $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
    $setting.Name = "setting1"
    $setting.CookieBasedAffinity = "enabled"
    $setting.Port = 80
    $setting.Protocol = "http"

Crie o ouvinte.

    $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
    $listener.Name = "listener1"
    $listener.FrontendPort = "fep1"
    $listener.FrontendIP = "fip1"
    $listener.Protocol = "http"
    $listener.SslCert = ""

Crie a regra.

    $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
    $rule.Name = "rule1"
    $rule.Type = "basic"
    $rule.BackendHttpSettings = "setting1"
    $rule.Listener = "listener1"
    $rule.BackendAddressPool = "pool1"

### <a name="step-2"></a>Etapa 2

Atribua todos os itens de configuração individuais a um objeto de configuração do gateway de aplicativo ($appgwconfig).

Adicione o IP front-end na configuração.

    $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
    $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
    $appgwconfig.FrontendIPConfigurations.Add($fip)

Adicione a porta front-end na configuração.

    $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
    $appgwconfig.FrontendPorts.Add($fep)

Adicione o pool de servidor back-end na configuração.

    $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
    $appgwconfig.BackendAddressPools.Add($pool)

Adicione a configuração de pool de back-end na configuração.

    $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
    $appgwconfig.BackendHttpSettingsList.Add($setting)

Adicione o ouvinte para a configuração.

    $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
    $appgwconfig.HttpListeners.Add($listener)

Adicione a regra para a configuração.

    $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
    $appgwconfig.HttpLoadBalancingRules.Add($rule)

### <a name="step-3"></a>Etapa 3

Confirme o objeto de configuração para o recurso de gateway do aplicativo usando **Set-AzureApplicationGatewayConfig**.

    Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## <a name="start-the-gateway"></a>Iniciar o gateway

Quando o gateway tiver sido configurado, use o cmdlet **Start-AzureApplicationGateway** para iniciar o gateway. Cobrança para um gateway aplicativo começa depois que o gateway foi iniciado com êxito.

> [AZURE.NOTE] O cmdlet **Start-AzureApplicationGateway** pode demorar até 15-20 minutos para concluir.

    Start-AzureApplicationGateway AppGwTest

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## <a name="verify-the-gateway-status"></a>Verificar o status do gateway

Use o cmdlet **Get-AzureApplicationGateway** para verificar o status do gateway. Se **Iniciar-AzureApplicationGateway** foi bem-sucedida na etapa anterior, deve estar executando o *estado* e *Vip* e *DnsName* devem ter entradas válidas.

O exemplo a seguir mostra um gateway de aplicativo que está para cima, executando, e pronto para assumir o tráfego destinado `http://<generated-dns-name>.cloudapp.net`.

    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    Vip           : 138.91.170.26
    DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## <a name="delete-an-application-gateway"></a>Excluir um gateway de aplicativo

Para excluir um gateway de aplicativo:

1. Use o cmdlet **AzureApplicationGateway de parar** para interromper o gateway.
2. Use o cmdlet **AzureApplicationGateway remover** para remover o gateway.
3. Verifique se que o gateway foi removido usando o cmdlet **Get-AzureApplicationGateway** .

O exemplo a seguir mostra o cmdlet **AzureApplicationGateway parar** na primeira linha, seguida de saída.

    Stop-AzureApplicationGateway AppGwTest

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Depois que o gateway de aplicativo estiver em um estado parado, use o cmdlet **AzureApplicationGateway remover** para remover o serviço.


    Remove-AzureApplicationGateway AppGwTest

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Para verificar que o serviço foi removido, você pode usar o cmdlet **Get-AzureApplicationGateway** . Esta etapa não é necessária.


    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>Próximas etapas

Se você quiser configurar descarregamento SSL, consulte [Configurar um gateway de aplicativo para SSL descarregar](application-gateway-ssl.md).

Se desejar configurar um gateway de aplicativo para usar com um balanceador de carga interno, consulte [criar um gateway de aplicativo com um balanceador de carga interno (ILB)](application-gateway-ilb.md).

Se você desejar obter mais informações sobre opções de balanceamento de carregar em geral, consulte:

- [Balanceador de carga Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gerenciador de tráfego Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

[scenario]: ./media/application-gateway-create-gateway/scenario.png