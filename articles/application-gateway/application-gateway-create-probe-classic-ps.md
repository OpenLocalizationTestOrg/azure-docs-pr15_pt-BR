<properties
   pageTitle="Criar um teste personalizado para o Gateway de aplicativo usando o PowerShell no modelo clássico de implantação | Microsoft Azure"
   description="Saiba como criar um teste personalizado para o Gateway de aplicativo usando o PowerShell no modelo clássico de implantação"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Criar um teste personalizado para o Gateway de aplicativo do Azure (clássico) usando o PowerShell

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-probe-portal.md)
- [Azure PowerShell do Gerenciador de recursos](application-gateway-create-probe-ps.md)
- [Azure PowerShell clássico](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Saiba como [executar essas etapas usando o modelo do Gerenciador de recursos](application-gateway-create-probe-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicativo

Para criar um gateway de aplicativo:

1. Crie um recurso de gateway do aplicativo.
2. Crie um arquivo de configuração de XML ou um objeto de configuração.
3. Confirme a configuração para o recurso de gateway do aplicativo recém-criado.

### <a name="create-an-application-gateway-resource"></a>Criar um recurso de gateway do aplicativo

Para criar o gateway, use o cmdlet **New-AzureApplicationGateway** , substituindo os valores com seus próprios. Cobrança para o gateway não iniciar neste momento. Cobrança começa em uma etapa posterior, quando o gateway é iniciado com êxito.

O exemplo a seguir cria um gateway aplicativo usando uma rede virtual chamado "testvnet1" e uma sub-rede chamado "sub-rede-1".

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Para validar que o gateway foi criado, você pode usar o cmdlet **Get-AzureApplicationGateway** .

    Get-AzureApplicationGateway AppGwTest

>[AZURE.NOTE]  O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é médio. Você pode escolher entre pequeno, médio e grande.

 *VirtualIPs* e *DnsName* são mostrados em branco porque o gateway ainda não foi iniciado. Estes são criadas depois que o gateway está no estado de execução.

## <a name="configure-an-application-gateway"></a>Configurar um gateway de aplicativo

Você pode configurar o gateway de aplicativo usando XML ou um objeto de configuração.

## <a name="configure-an-application-gateway-by-using-xml"></a>Configurar um gateway de aplicativo usando o XML

No exemplo a seguir, você pode usar um arquivo XML para definir todas as configurações de gateway do aplicativo e confirmá-las para o recurso de gateway do aplicativo.  

### <a name="step-1"></a>Etapa 1

Copie o seguinte texto para o bloco de notas.

    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name>
            <Type>Private</Type>
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>    
    <FrontendPorts>
        <FrontendPort>
            <Name>port1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
      </Probes>
     <BackendAddressPools>
        <BackendAddressPool>
            <Name>pool1</Name>
            <IPAddresses>
                <IPAddress>1.1.1.1</IPAddress>
                <IPAddress>2.2.2.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>listener1</Name>
            <FrontendIP>fip1</FrontendIP>
        <FrontendPort>port1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>lbrule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>setting1</BackendHttpSettings>
            <Listener>listener1</Listener>
            <BackendAddressPool>pool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


Edite os valores entre os parênteses para os itens de configuração. Salve o arquivo com extensão. XML.

O exemplo a seguir mostra como usar um arquivo de configuração para configurar o gateway de aplicativo, o tráfego HTTP na porta pública 80 de balanceamento de carga e enviar tráfego de rede à porta de back-end 80 entre dois endereços IP usando um teste personalizado.

>[AZURE.IMPORTANT] O item de protocolo Http ou Https diferencia maiusculas de minúsculas.

Um novo item de configuração \<teste\> é adicionada ao configurar testes personalizados.

Os parâmetros de configuração são:

- **Nome** - nome de referência de teste personalizado.
- **Protocolo** - protocolo usado (valores possíveis são HTTP ou HTTPS).
- **Host** e **caminho** - caminho de URL completo que é chamado pelo gateway para determinar a integridade da instância do aplicativo. Por exemplo, se você tiver um site http://contoso.com/, em seguida, o teste personalizado pode ser configurado para "http://contoso.com/path/custompath.htm" para verificações de teste ter uma resposta HTTP bem-sucedida.
- **Intervalo** - configura as verificações de intervalo de teste em segundos.
- **Tempo limite** - define o tempo de limite de teste para uma verificação de resposta HTTP.
- **UnhealthyThreshold** - o número de respostas HTTP falhas necessário para sinalizar a instância de back-end como *não íntegra*.

O nome do teste sejam referenciado na <BackendHttpSettings> configuração para atribuir qual pool de back-end usa configurações de teste personalizado.

## <a name="add-a-custom-probe-configuration-to-an-existing-application-gateway"></a>Adicionar uma configuração de teste personalizado para um gateway de aplicativo existente

Alterando a configuração atual de um gateway de aplicativo requer três etapas: obtenha o arquivo de configuração XML atual, modificar para ter um teste personalizado e configurar o gateway de aplicativo com as novas configurações de XML.

### <a name="step-1"></a>Etapa 1

Obter o arquivo XML, usando get-AzureApplicationGatewayConfig. Isso exporta o XML para ser modificada para adicionar uma configuração de teste de configuração.

    Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"


### <a name="step-2"></a>Etapa 2

Abra o arquivo XML em um editor de texto. Adicionar um `<probe>` seção após `<frontendport>`.

    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
    </Probes>

Na seção backendHttpSettings do XML, adicione o nome de teste, conforme mostrado no exemplo a seguir:

        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

Salve o arquivo XML.

### <a name="step-3"></a>Etapa 3

Atualize a configuração do gateway de aplicativo com o novo arquivo XML usando o **Set-AzureApplicationGatewayConfig**. Isso atualiza o gateway de aplicativo com a nova configuração.

    Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"


## <a name="next-steps"></a>Próximas etapas

Se você quiser configurar descarregamento Secure Sockets Layer (SSL), consulte [Configurar um gateway de aplicativo para SSL descarregar](application-gateway-ssl.md).

Se desejar configurar um gateway de aplicativo para usar com um balanceador de carga interno, consulte [criar um gateway de aplicativo com um balanceador de carga interno (ILB)](application-gateway-ilb.md).
