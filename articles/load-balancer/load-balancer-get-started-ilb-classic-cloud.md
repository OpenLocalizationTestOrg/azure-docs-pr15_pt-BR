<properties
   pageTitle="Criar um balanceador de carga interno para serviços de nuvem no modelo clássico de implantação | Microsoft Azure"
   description="Aprenda a criar um balanceador de carga interno usando o PowerShell no modelo clássico de implantação"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>Começar a criar um balanceador de carga interno (clássico) para serviços de nuvem

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

<BR>

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Saiba como [executar essas etapas usando o modelo do Gerenciador de recursos](load-balancer-get-started-ilb-arm-ps.md).


## <a name="configure-internal-load-balancer-for-cloud-services"></a>Configurar balanceador de carga interno para serviços de nuvem

Balanceador de carga interna tem suporte para máquinas virtuais e serviços de nuvem. Um ponto de extremidade de Balanceador de carga interno criado em um serviço de nuvem que está fora de uma rede virtual regional estarão acessível apenas dentro do serviço de nuvem.

A configuração de Balanceador de carga interno deve ser definida durante a criação da implantação do primeira no serviço de nuvem, conforme mostrado no exemplo abaixo.

>[AZURE.IMPORTANT] Um pré-requisito para executar as etapas a seguir é ter uma rede virtual já criada para a implantação de nuvem. Você precisará o nome de rede virtual e sub-rede para criar o balanceamento de carga interno.

### <a name="step-1"></a>Etapa 1

Abra o arquivo de configuração de serviço (.cscfg) para a sua implantação de nuvem no Visual Studio e adicione a seção a seguir para criar o balanceamento de carga de interno sob a última "`</Role>`" item para a configuração de rede.




    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="name of the load balancer">
          <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>


Vamos adicionar os valores para o arquivo de configuração de rede mostrar a aparência. No exemplo, suponha que você criou uma sub-rede chamada "test_vnet" com um 10.0.0.0/24 sub-rede chamado test_subnet e um IP estático 10.0.0.4. O balanceador de carga será denominado testLB.

    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="testLB">
          <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>

Para obter mais informações sobre o esquema de Balanceador de carga, consulte [Adicionar balanceador de carga](https://msdn.microsoft.com/library/azure/dn722411.aspx).

### <a name="step-2"></a>Etapa 2


Altere o arquivo de definição (.csdef) de serviço para adicionar pontos de extremidade para o balanceamento de carga interno. No momento em que uma instância de função é criada, o arquivo de definição de serviço adicionará instâncias de função para o balanceamento de carga interno.


    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
      </Endpoints>
    </WorkerRole>

Acompanhar os mesmos valores do exemplo acima, vamos adicionar os valores para o arquivo de definição de serviço.

    <WorkerRole name=WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
      </Endpoints>
    </WorkerRole>

O tráfego de rede será carga equilibrada usando o balanceador de carga de testLB usando a porta 80 para solicitações de entrada, enviando para instâncias de função de trabalho também na porta 80.


## <a name="next-steps"></a>Próximas etapas

[Configurar um modo de distribuição de Balanceador de carga usando afinidade IP de origem](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite TCP ociosas para seu balanceador de carga](load-balancer-tcp-idle-timeout.md)