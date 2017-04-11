<properties
   pageTitle="Configurar o modo de distribuição de Balanceador de carga | Microsoft Azure"
   description="Como configurar o modo de distribuição de Balanceador de carga Azure para oferecer suporte a afinidade IP de origem"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="configure-the-distribution-mode-for-load-balancer"></a>Configurar o modo de distribuição para balanceador de carga

## <a name="hash-based-distribution-mode"></a>Modo de distribuição baseado em hash

O algoritmo de distribuição padrão é uma tupla de 5 (fonte IP, porta de origem, tipo de protocolo de IP de destino, porta de destino,) hash para mapear o tráfego para servidores disponíveis. Ele fornece adesão apenas dentro de uma sessão de transporte. Pacotes na mesma sessão serão direcionados para a mesma instância de IP (DIP) do data center atrás do ponto de extremidade de balanceamento de carga. Quando o cliente inicia uma nova sessão do mesmo IP de origem, a porta de origem é alterado e faz com que o tráfego ir até um ponto de extremidade DIP diferentes.

![Balanceador de carga de hash com base](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

Figura 1-5-tupla distribuição

## <a name="source-ip-affinity-mode"></a>Modo de afinidade IP de origem

Temos outro modo de distribuição chamado afinidade de IP de origem (também conhecido como afinidade de sessão ou afinidade IP do cliente). Azure balanceador de carga pode ser configurado para usar uma tupla 2 (IP de origem, destino IP) ou 3-tupla (protocolo de IP de origem, IP de destino,) para mapear o tráfego para os servidores disponíveis. Usando afinidade de IP de origem, conexões iniciadas do mesmo computador cliente vai para o mesmo ponto de extremidade DIP.

O diagrama a seguir ilustra uma configuração de 2-tupla. Observe como 2-tupla percorre balanceador de carga para máquina virtual 1 (VM1) qual é feito backup da VM2 e VM3.

![afinidade de sessão](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Figura 2-2-tupla distribuição

Afinidade IP de origem resolve uma incompatibilidade entre o balanceador de carga do Azure e o Gateway de área de trabalho remota (RD). Agora você pode criar um farm de gateway RD em um serviço de nuvem único.

Outro cenário de caso de uso é carregar mídia onde o upload de dados acontece por meio de UDP, mas o plano de controle é obtido por meio de TCP:

- Um cliente primeiro inicia uma sessão TCP para o endereço público de balanceamento de carga, obtém direcionado para um DIP específico, esse canal permanece ativo para monitorar a integridade de conexão
- Uma nova sessão UDP no mesmo computador cliente é iniciada à mesma empresa pública de balanceamento de carga, as expectativas aqui é que esta conexão também será direcionado para o mesmo ponto de extremidade DIP conforme a conexão TCP anterior para que o upload de mídia pode ser executada a transferência alta, mantendo também um canal de controle por meio de TCP.

>[AZURE.NOTE] Quando um conjunto de balanceamento de carga é alterado (removendo ou adicionando uma máquina virtual), a distribuição de solicitações de cliente é recalculada. Você não pode depender novas conexões dos clientes existentes terminando no mesmo servidor. Além disso, usando o IP de origem afinidade distribuição modo pode causar uma distribuição desigual de tráfego. Os clientes que executam proxies anteriores podem ser vistos como um aplicativo de cliente exclusivo.

## <a name="configuring-source-ip-affinity-settings-for-load-balancer"></a>Configurar definições de afinidade de IP de origem para balanceador de carga

Para máquinas virtuais, você pode usar o PowerShell para alterar as configurações de tempo limite:

Adicionar um ponto de extremidade Azure a uma máquina Virtual e configurar o modo de distribuição de Balanceador de carga

    Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM

LoadBalancerDistribution pode ser definida para sourceIP 2-tupla (IP de origem, destino IP) balanceamento de carga, sourceIPProtocol de balanceamento de carga de 3-tupla (protocolo de IP de origem, IP de destino,) ou nenhum se quiser que o comportamento padrão do balanceamento de carga de 5 tuplas.

Use este procedimento para recuperar uma configuração de modo de distribuição do balanceador de carga de ponto de extremidade:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

Se não houver elemento LoadBalancerDistribution balanceador de carga do Azure usa o algoritmo de 5 tuplas padrão.

### <a name="set-the-distribution-mode-on-a-load-balanced-endpoint-set"></a>Definir o modo de distribuição em um conjunto de ponto de extremidade de balanceamento de carga

Se pontos de extremidade fazem parte de um conjunto de ponto de extremidade de balanceamento de carga, o modo de distribuição deve ser definido no conjunto de ponto de extremidade de balanceamento de carga:

    Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP

### <a name="cloud-service-configuration-to-change-distribution-mode"></a>Configuração de serviço de nuvem para alterar o modo de distribuição

Você pode aproveitar o SDK do Azure para .NET 2,5 (a ser lançado em novembro) para atualizar seu serviço de nuvem. Configurações de ponto de extremidade para serviços de nuvem são feitas na .csdef. Para atualizar o modo de distribuição de Balanceador de carga, para uma implantação de serviços de nuvem, uma atualização de implantação é necessária.
Aqui está um exemplo de alterações de .csdef para configurações de ponto de extremidade:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
      </Endpoints>
    </WorkerRole>
    <NetworkConfiguration>
      <VirtualNetworkSite name="VNet"/>
      <AddressAssignments>
    <InstanceAddress roleName="VMRolePersisted">
      <PublicIPs>
        <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
      </PublicIPs>
    </InstanceAddress>
      </AddressAssignments>
    </NetworkConfiguration>

## <a name="api-example"></a>Exemplo de API

Você pode configurar a distribuição de Balanceador de carga usando a API de gerenciamento do serviço. Certifique-se de adicionar o `x-ms-version` cabeçalho é definido para a versão `2014-09-01` ou superior.

### <a name="update-the-configuration-of-the-specified-load-balanced-set-in-a-deployment"></a>Atualizar a configuração do conjunto de balanceamento de carga especificada em uma implantação

#### <a name="request-example"></a>Exemplo de solicitação

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet    x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

O valor de LoadBalancerDistribution pode ser sourceIP para afinidade tupla de 2, sourceIPProtocol para afinidade de 3-tupla ou nenhum (para sem afinidade. Isto é de 5 tuplas)

#### <a name="response"></a>Resposta

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Próximas etapas

[Visão geral de Balanceador de carga interno](load-balancer-internal-overview.md)

[Começar a configurar um opostas balanceador de carga de Internet](load-balancer-get-started-internet-arm-ps.md)

[Definir configurações de tempo limite TCP ociosas para seu balanceador de carga](load-balancer-tcp-idle-timeout.md)
