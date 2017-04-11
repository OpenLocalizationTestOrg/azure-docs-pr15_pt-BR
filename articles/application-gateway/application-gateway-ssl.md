<properties
   pageTitle="Configurar um gateway de aplicativo para descarregamento SSL usando implantação clássica | Microsoft Azure"
   description="Este artigo fornece instruções para criar um gateway de aplicativo com SSL descarregar usando o modelo de implantação clássico Azure."
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

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Configurar um gateway de aplicativo para descarregamento SSL usando o modelo clássico de implantação

> [AZURE.SELECTOR]
-[Portal do Azure](application-gateway-ssl-portal.md)
-[PowerShell do Gerenciador de recursos do Azure](application-gateway-ssl-arm.md)
-[PowerShell clássico do Azure](application-gateway-ssl.md)

Azure Application Gateway pode ser configurado para finalizar a sessão de Secure Sockets Layer (SSL) no gateway para evitar dispendiosas tarefas de descriptografia SSL para que ocorram em web farm. Descarregamento SSL também simplifica a configuração de servidor front-end e o gerenciamento do aplicativo da web.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente de cmdlets do PowerShell do Azure usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente da seção **Do Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Verifique se você tem uma rede virtual de trabalho com uma sub-rede válida. Certifique-se de que nenhuma máquinas virtuais ou implantações de nuvem estão usando a sub-rede. O gateway de aplicativo deve estar sozinha em uma sub-rede de rede virtual.
3. Os servidores que você configura para usar o gateway de aplicativo devem existir ou atribuiu seus pontos de extremidade criados na rede virtual ou com um IP/VIP público.

Para configurar descarregamento SSL em um gateway de aplicativo, siga as etapas a seguir na ordem listada:

1. [Criar um gateway de aplicativo](#create-an-application-gateway)
2. [Carregar certificados SSL](#upload-ssl-certificates)
3. [Configurar o gateway](#configure-the-gateway)
4. [Defina a configuração do gateway](#set-the-gateway-configuration)
5. [Iniciar o gateway](#start-the-gateway)
6. [Verificar o status do gateway](#verify-the-gateway-status)


## <a name="create-an-application-gateway"></a>Criar um gateway de aplicativo

Para criar o gateway, use o cmdlet **New-AzureApplicationGateway** , substituindo os valores com seus próprios. Cobrança para o gateway não iniciar neste momento. Cobrança começa em uma etapa posterior, quando o gateway é iniciado com êxito.

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Para validar que o gateway foi criado, você pode usar o cmdlet **Get-AzureApplicationGateway** .

O exemplo, *Descrição*, *InstanceCount*e *GatewaySize* são parâmetros opcionais. O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é médio. Pequenas e grandes são outros valores disponíveis. *VirtualIPs* e *DnsName* são mostrados em branco porque o gateway ainda não foi iniciado. Esses valores são criados depois que o gateway está no estado de execução.

    Get-AzureApplicationGateway AppGwTest

## <a name="upload-ssl-certificates"></a>Carregar certificados SSL

Use **Add-AzureApplicationGatewaySslCertificate** para carregar o certificado de servidor no formato *pfx* para o gateway de aplicativo. O nome de certificado é um nome de usuário escolhido e deve ser exclusivo dentro do gateway de aplicativo. Este certificado é conhecido por esse nome em todas as operações de gerenciamento de certificado no gateway do aplicativo.

Este exemplo a seguir mostra o cmdlet, substitua os valores na amostra seu próprio.

    Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>

Em seguida, valide o upload de certificado. Use o cmdlet **Get-AzureApplicationGatewayCertificate** .

Este exemplo mostra o cmdlet na primeira linha, seguido de saída.

    Get-AzureApplicationGatewaySslCertificate AppGwTest

    VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
    VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
    Name           : SslCert
    SubjectName    : CN=gwcert.app.test.contoso.com
    Thumbprint     : AF5ADD77E160A01A6......EE48D1A
    ThumbprintAlgo : sha1RSA
    State..........: Provisioned

>[AZURE.NOTE] A senha do certificado deve estar entre caracteres, letras ou números de 4 a 12. Caracteres especiais não são aceitas.

## <a name="configure-the-gateway"></a>Configurar o gateway

Uma configuração de gateway do aplicativo consiste em vários valores. Os valores podem ser vinculados juntos para construir a configuração.

Os valores são:

- **Pool de servidor back-end:** A lista de endereços IP de servidores back-end. Os endereços IP listados ou devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público.
- **Configurações de pool do servidor de back-end:** Cada pool tem as configurações como afinidade baseada em cookies, protocolo e porta. Essas configurações estão vinculadas a um pool e são aplicadas a todos os servidores dentro do pool.
- **Porta front-end:** Esta porta é a porta pública que é aberta no gateway do aplicativo. Tráfego visitas esta porta e, em seguida, é redirecionado para um dos servidores back-end.
- **Ouvinte:** O ouvinte tem uma porta de front-end, um protocolo (Http ou Https, esses valores diferenciam maiusculas de minúsculas) e o nome do certificado SSL (se configurar SSL descarregar).
- **Regra:** A regra associará o ouvinte e o pool de servidor back-end e define qual pool de servidor back-end o tráfego deve ser direcionado para quando ele atinge um determinado ouvinte. Atualmente, somente a regra *básica* é suportada. A regra *básica* é a distribuição de carga round-robin.

**Notas de configuração adicionais**

Para configuração de certificados SSL, o protocolo no **HttpListener** deve mudar para *Https* (diferencia maiusculas de minúsculas). O elemento **SslCert** é adicionado à **HttpListener** com o valor definido para o mesmo nome usado em upload de anterior seção de certificados SSL. A porta front-end deve ser atualizada para 443.

**Para habilitar afinidade baseada em cookie**: um gateway de aplicativo pode ser configurado para garantir que uma solicitação de uma sessão de cliente sempre será direcionada para a máquina virtual mesma na web farm. Este cenário é feito pela inclusão de um cookie de sessão que permite que o gateway para direcionar o tráfego adequadamente. Para habilitar afinidade baseada em cookies, defina **CookieBasedAffinity** como *ativado* no elemento **BackendHttpSettings** .



Você pode construir sua configuração criando um objeto de configuração ou usando um arquivo XML de configuração.
Para construir sua configuração usando um arquivo XML de configuração, use o exemplo a seguir:

**Exemplo XML de configuração**

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendIPConfigurations />
        <FrontendPorts>
            <FrontendPort>
                <Name>FrontendPort1</Name>
                <Port>443</Port>
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
                <Protocol>Https</Protocol>
                <SslCert>GWCert</SslCert>
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

Em seguida, defina o gateway do aplicativo. Você pode usar o cmdlet **Set-AzureApplicationGatewayConfig** com um objeto configuração ou com um arquivo XML de configuração.

    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

## <a name="start-the-gateway"></a>Iniciar o gateway

Quando o gateway tiver sido configurado, use o cmdlet **Start-AzureApplicationGateway** para iniciar o gateway. Cobrança para um gateway aplicativo começa depois que o gateway foi iniciado com êxito.


**Observação:** O cmdlet **Start-AzureApplicationGateway** pode demorar até 15-20 minutos para concluir.

    Start-AzureApplicationGateway AppGwTest

## <a name="verify-the-gateway-status"></a>Verificar o status do gateway

Use o cmdlet **Get-AzureApplicationGateway** para verificar o status do gateway. Se **Iniciar-AzureApplicationGateway** foi bem-sucedida na etapa anterior, deve estar executando o *estado* e *VirtualIPs* e *DnsName* devem ter entradas válidas.

Este exemplo mostra um gateway de aplicativos, executando, e estiver pronto para fazer o tráfego.

    Get-AzureApplicationGateway AppGwTest

    Name          : AppGwTest2
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    VirtualIPs    : {23.96.22.241}
    DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net


## <a name="next-steps"></a>Próximas etapas


Se você desejar obter mais informações sobre opções de balanceamento de carregar em geral, consulte:

- [Balanceador de carga Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gerenciador de tráfego Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)