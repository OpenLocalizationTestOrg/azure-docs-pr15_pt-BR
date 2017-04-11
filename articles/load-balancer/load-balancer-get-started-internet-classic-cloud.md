<properties
   pageTitle="Começar a criar um Internet opostas balanceador de carga no uso de modelo de implantação clássica para serviços de nuvem | Microsoft Azure"
   description="Aprenda a criar um opostas balanceador de carga no modelo de implantação clássico para serviços de nuvem da Internet"
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
   ms.date="03/17/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Começar a criar um opostas balanceador de carga para serviços de nuvem da Internet

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo discute o modelo de implantação clássico. Você também pode [aprender a criar uma Internet opostas balanceador de carga usando o Gerenciador de recursos do Azure](load-balancer-get-started-internet-arm-cli.md).

Serviços de nuvem são configurados automaticamente com um balanceador de carga e podem ser personalizados por meio do modelo de serviço.

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Criar um balanceador de carga usando o arquivo de definição de serviço

Você pode aproveitar o SDK do Azure para .NET 2.5 atualizar seu serviço de nuvem. Configurações de ponto de extremidade para serviços de nuvem são feitas no arquivo de .csdef de [definição de serviço](https://msdn.microsoft.com/library/azure/gg557553.aspx).

O exemplo a seguir mostra como um arquivo de servicedefinition.csdef para uma implantação de nuvem é configurado:

Verificando a snipet para o arquivo de .csdef gerado por uma implantação de nuvem, você pode ver o ponto de extremidade externo configurado para usar portas HTTP na porta 10000, 10001 e 10002.


    <ServiceDefinition name=“Tenant“>
    <WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
    </WorkerRole>
    </ServiceDefinition>




## <a name="check-load-balancer-health-status-for-cloud-services"></a>Verificar o status de integridade de Balanceador de carga para serviços de nuvem


A seguir é um exemplo de um teste de integridade:

        <LoadBalancerProbes>
        <LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
        </LoadBalancerProbes>

O balanceador de carga combina as informações do ponto de extremidade e as informações do teste para criar uma URL na forma de http://{DIP de VM}:80/Probe.aspx que podem ser usados para consultar a integridade do serviço.

O serviço detecta testes periódicos do mesmo endereço IP. Esta é a solicitação de teste de integridade proveniente do host do nó onde a máquina virtual está sendo executado.
O serviço tem a responder com um código de status HTTP 200 para o balanceador de carga assuma que o serviço está íntegro. Qualquer outro código de status HTTP (por exemplo 503) leva diretamente na máquina virtual fora rotação.

A definição de teste também controla a frequência do teste. Em nosso caso acima, o balanceador de carga é sondagem o ponto de extremidade cada 5 segundos. Se nenhuma resposta positiva for recebida para 10 segundos (dois intervalos de teste), o teste será considerado para baixo e a máquina virtual é tirada de rotação. Da mesma forma, se o serviço estiver fora do rotação e uma resposta positiva for recebida, o serviço é colocado de volta para rotação imediatamente. Se o serviço é flutuante entre com e sem integridade, pode decidir balanceador de carga atrasar a introdução re do serviço volta a rotação até que ele foi eficaz, de um número de testes.

Verifique o esquema de definição de serviço para o [teste de integridade](https://msdn.microsoft.com/library/azure/jj151530.aspx) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas

[Começar a configurar um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição de Balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite TCP ociosas para seu balanceador de carga](load-balancer-tcp-idle-timeout.md)

