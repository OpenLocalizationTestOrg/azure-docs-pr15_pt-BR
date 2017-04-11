<properties 
   pageTitle="Criar e configurar um Gateway aplicativo com balanceador de carga interno (ILB) em uma rede Virtual | Microsoft Azure"
   description="Esta página fornece instruções para configurar um Gateway de aplicativo do Azure com um ponto de extremidade de balanceamento de carga interno"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/19/2016"
   ms.author="gwallace"/>

# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Criar um Gateway de aplicativo com um balanceador de carga interno (ILB)

> [AZURE.SELECTOR]
- [Azure etapas clássicas](application-gateway-ilb.md)
- [Etapas do Powershell do Gerenciador de recursos](application-gateway-ilb-arm.md)

Gateway de aplicativo pode ser configurado com um opostas IP virtual da internet ou com um ponto de extremidade interno não expostos na internet, também conhecido como ponto de extremidade de Balanceador de carga interno (ILB). Configuração do gateway com um ILB é útil para aplicativos de linha de negócios internos não expostos à internet. Também é útil para serviços/níveis em um aplicativo de vários nível, que reside em um limite de segurança não exposto à internet, mas ainda exija distribuição de carga de round robin, adesão de sessão ou encerramento de SSL. Este artigo conduz você pelas etapas para configurar um gateway de aplicativo com um ILB.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente do cmdlets do PowerShell do Azure usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente da seção **Do Windows PowerShell** da [página de Download](https://azure.microsoft.com/downloads/).
2. Verifique se você tem uma rede virtual de trabalhar com sub-rede válida.
3. Verifique se você tem servidores de back-end na rede virtual ou com um IP/VIP público atribuído.


Para criar um gateway de aplicativo, execute as etapas a seguir na ordem listada. 

1. [Criar um gateway de aplicativo](#create-a-new-application-gateway)
2. [Configurar o gateway](#configure-the-gateway)
3. [Defina a configuração do gateway](#set-the-gateway-configuration)
4. [Iniciar o gateway](#start-the-gateway)
4. [Verificar o gateway](#verify-the-gateway-status)



## <a name="create-an-application-gateway"></a>Crie um gateway de aplicativo:

**Para criar o gateway**, use o `New-AzureApplicationGateway` cmdlet, substituindo os valores com seus próprios. Observe que cobrança para o gateway não inicie neste momento. Cobrança começa em uma etapa posterior, quando o gateway é iniciado com êxito.

    PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**Para validar** que o gateway foi criado, você pode usar o `Get-AzureApplicationGateway` cmdlet. 

O exemplo, *Descrição*, *InstanceCount*e *GatewaySize* são parâmetros opcionais. O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é médio. Pequenas e grandes são outros valores disponíveis. *VIP* e *DnsName* são mostrados em branco porque o gateway ainda não foi iniciado. Estes são criadas depois que o gateway está no estado de execução. 

    PS C:\> Get-AzureApplicationGateway AppGwTest

    VERBOSE: 4:39:39 PM - Begin Operation:
    Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
    Operation: Get-AzureApplicationGateway
    Name: AppGwTest 
    Description: 
    VnetName: testvnet1 
    Subnets: {Subnet-1} 
    InstanceCount: 2 
    GatewaySize: Medium 
    State: Stopped 
    VirtualIPs: 
    DnsName:


## <a name="configure-the-gateway"></a>Configurar o gateway

Uma configuração de gateway do aplicativo consiste em vários valores. Os valores podem ser vinculados juntos para construir a configuração.
 
Os valores são:

- **Pool de back-end do servidor:** A lista de endereços IP de servidores back-end. Os endereços IP listados ou devem pertencer à sub-rede VNet ou devem ser um IP/VIP público. 
- **Configurações de pool do servidor de back-end:** Cada pool tem as configurações como afinidade baseada em cookies, protocolo e porta. Essas configurações estão vinculadas a um pool e são aplicadas a todos os servidores dentro do pool.
- **Porta Frontend:** Esta porta é o público aberto no gateway do aplicativo. Tráfego visitas esta porta e, em seguida, é redirecionado para um dos servidores back-end.
- **Ouvinte:** O ouvinte tem uma porta de front-end, um protocolo (Http ou Https, estes são diferencia maiusculas de minúsculas) e o nome do certificado SSL (se configurar SSL descarregar). 
- **Regra:** A regra associará o ouvinte e o pool de servidor back-end e define qual pool de servidor back-end o tráfego deve ser direcionado para quando ele atinge um determinado ouvinte. Atualmente, somente a regra *básica* é suportada. A regra *básica* é a distribuição de carga round-robin.

Você pode construir sua configuração criando um objeto de configuração, ou usando um arquivo XML de configuração. Para construir sua configuração usando um arquivo XML de configuração, use o exemplo abaixo.



Observe o seguinte:


- O elemento *FrontendIPConfigurations* descreve os detalhes ILB relevantes para configurar o Gateway de aplicativo com um ILB. 

- O *tipo* de Frontend IP deve ser definida como 'Privada'

- O *StaticIPAddress* deve ser definido para o IP interno desejado no qual o gateway recebe tráfego. Observe que o elemento *StaticIPAddress* é opcional. Se não estiver definido, um IP interno disponíveis da sub-rede implantada é escolhido. 

- O valor do *nome do* elemento especificado em *FrontendIPConfiguration* deve ser usado no elemento de *FrontendIP* do HTTPListener consultem o FrontendIPConfiguration.

 **Exemplo XML de configuração**

 

        <?xml version="1.0" encoding="utf-8"?>
        <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
            <FrontendIPConfigurations>
                <FrontendIPConfiguration>
                    <Name>fip1</Name> 
                    <Type>Private</Type> 
                    <StaticIPAddress>10.0.0.10</StaticIPAddress> 
                </FrontendIPConfiguration>
            </FrontendIPConfigurations>
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
                    <FrontendIP>fip1</FrontendIP>
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
    


## <a name="set-the-gateway-configuration"></a>Defina a configuração do gateway

Em seguida, você irá configurar o gateway de aplicativo. Você pode usar o `Set-AzureApplicationGatewayConfig` cmdlet com um objeto de configuração, ou com um arquivo XML de configuração. 

    PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## <a name="start-the-gateway"></a>Iniciar o gateway

Depois que o gateway foi configurado, usar o `Start-AzureApplicationGateway` cmdlet para iniciar o gateway. Cobrança para um gateway aplicativo começa depois que o gateway foi iniciado com êxito. 


> [AZURE.NOTE] O `Start-AzureApplicationGateway` cmdlet pode demorar até 15-20 minutos para ser concluída. 
   
    PS C:\> Start-AzureApplicationGateway AppGwTest 

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## <a name="verify-the-gateway-status"></a>Verificar o status do gateway

Use o `Get-AzureApplicationGateway` cmdlet para verificar o status do gateway. Se *Iniciar-AzureApplicationGateway* foi bem-sucedida na etapa anterior, o estado deve estar *em execução*e o Vip e DnsName devem ter entradas válidas. Este exemplo mostra o cmdlet na primeira linha, seguido de saída. Neste exemplo, o gateway está em execução e estiver pronto para fazer o tráfego. 

> [AZURE.NOTE] O gateway de aplicativo está configurado para aceitar o tráfego no ponto de extremidade ILB configurado de 10.0.0.10 neste exemplo.

    PS C:\> Get-AzureApplicationGateway AppGwTest 

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   : 
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    VirtualIPs    : {10.0.0.10}
    DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net

## <a name="next-steps"></a>Próximas etapas


Se você desejar obter mais informações sobre opções de balanceamento de carregar em geral, consulte:

- [Balanceador de carga Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gerenciador de tráfego Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
