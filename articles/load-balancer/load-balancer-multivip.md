<properties
   pageTitle="Texto com várias VIPs para um serviço de nuvem"
   description="Visão geral de multiVIP e como definir VIPs múltiplos em um serviço na nuvem"
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

# <a name="configure-multiple-vips-for-a-cloud-service"></a>Configurar VIPs múltiplos para um serviço de nuvem

Você pode acessar serviços de nuvem Azure na Internet pública usando um endereço IP fornecido pelo Azure. Este endereço IP público é conhecido como um VIP (virtual IP) desde que ele está vinculado ao balanceador de carga Azure, e não na máquina Virtual (VM) instâncias dentro do serviço de nuvem. Você pode acessar qualquer instância de máquina virtual dentro de um serviço de nuvem usando um único VIP.

No entanto, há situações em que talvez seja necessário mais de um VIP como uma entrada de apontar para o mesmo serviço de nuvem. Por exemplo, seu serviço de nuvem pode hospedar vários sites que exigem conectividade SSL usando a porta padrão de 443, como cada site está hospedado por um cliente diferente ou locatário. Neste cenário, você precisa ter um público oposto endereço IP diferente para cada site. O diagrama a seguir ilustra um típica locatário múltipla hospedagem na web com a necessidade de vários certificados SSL na mesma porta público.

![Cenário de vários VIP SSL](./media/load-balancer-multivip/Figure1.png)

No exemplo acima, todos os VIPs usarem a mesma porta pública (443) e o tráfego será redirecionado para uma ou VMs em uma porta particular exclusiva para o endereço IP interno do serviço de nuvem que todos os sites de hospedagem de balanceamento de carga mais.

>[AZURE.NOTE] Outra situação exigir o uso de vários VIPs está hospedando vários ouvintes do grupo de disponibilidade sempre ativado SQL no mesmo conjunto de máquinas virtuais.

VIPs são dinâmicos por padrão, o que significa que o endereço IP real atribuído ao serviço de nuvem pode mudar ao longo do tempo. Para impedir que isso aconteça, você pode reservar um VIP de seu serviço. Para saber mais sobre VIPs reservadas, consulte [Reservadas IP público](../virtual-network/virtual-networks-reserved-public-ip.md).

>[AZURE.NOTE] Consulte [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses/) para obter informações sobre preços em VIPs e IPs reservadas.

Você pode usar o PowerShell para verificar VIPs usados por seus serviços de nuvem, bem como adicionar e remover VIPs, associar um VIP com um ponto de extremidade e configurar o balanceamento de carga em um VIP específico.

## <a name="limitations"></a>Limitações

No momento, a funcionalidade de Multi VIP está limitada aos seguintes situações:

- **Somente IaaS**. Você só pode habilitar vários VIP para serviços de nuvem que contêm VMs. Você não pode usar vários VIP em cenários de PaaS com instâncias de função.
- **Somente o PowerShell**. Você só pode gerenciar vários VIP usando o PowerShell.

Essas limitações são temporárias e podem alterar a qualquer momento. Verifique se rever esta página para verificar alterações futuras.


## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Como adicionar um VIP a um serviço de nuvem

Para adicionar um VIP ao seu serviço, execute o seguinte comando do PowerShell:

    Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

Este comando exibe um resultado semelhante ao exemplo a seguir:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Como remover um VIP de um serviço de nuvem

Para remover o VIP adicionado ao seu serviço no exemplo acima, execute o seguinte comando do PowerShell:

    Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

>[AZURE.IMPORTANT] Você só pode remover um VIP se ele tiver nenhum ponto de extremidade associado a ele.

## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Como recuperar informações de VIP de um serviço de nuvem

Para recuperar os VIPs associados a um serviço de nuvem, execute o seguinte script do PowerShell:

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

O script exibe um resultado semelhante ao exemplo a seguir:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

Neste exemplo, o serviço de nuvem tem 3 VIPs:

- **Vip1** é o VIP padrão, você sabe que porque o valor de IsDnsProgrammedName é definido como true.
- **Vip2** e **Vip3** não são usadas como não tiverem todos os endereços IP. Eles só serão usados se você associar um ponto de extremidade para o VIP.

>[AZURE.NOTE] Sua assinatura só será cobrada por VIPs extras depois que eles estejam associados um ponto de extremidade. Para obter mais informações sobre preços, consulte [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Como associar um VIP para um ponto extremo

Para associar um VIP em um serviço de nuvem para um ponto de extremidade, execute o seguinte comando do PowerShell:

    Get-AzureVM -ServiceName myService -Name myVM1 `
  	| Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 `
  	| Update-AzureVM

O comando cria um ponto de extremidade vinculado à VIP chamado *Vip2* na porta *80*e links-lo para a máquina virtual chamado *myVM1* em um serviço de nuvem chamado *myService* usando *TCP* na porta *8080*.

Para verificar a configuração, execute o seguinte comando do PowerShell:

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

A saída é semelhante ao seguinte exemplo:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Como habilitar o balanceamento de carga em um VIP específico

Você pode associar um único VIP várias máquinas virtuais para fins de balanceamento de carga. Por exemplo, você tem um serviço de nuvem chamado *myService*e duas máquinas virtuais *myVM1* e *myVM2*. E seu serviço de nuvem tem VIPs múltiplos, um deles denominada *Vip2*. Se você quiser garantir que todo o tráfego para a porta *81* em *Vip2* é equilibrado entre *myVM1* e *myVM2* na porta *8181*, execute o seguinte script do PowerShell:

    Get-AzureVM -ServiceName myService -Name myVM1 `
  	| Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
  	| Update-AzureVM

    Get-AzureVM -ServiceName myService -Name myVM2 `
  	| Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
  	| Update-AzureVM

Você também pode atualizar seu balanceador de carga para usar um VIP diferentes. Por exemplo, se você executar o comando do PowerShell abaixo, você irá alterar a conjunto para usar um VIP denominado Vip1 de balanceamento de carga:

    Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1

## <a name="next-steps"></a>Próximas etapas

[Análise de log de balanceamento de carga do Azure](load-balancer-monitor-log.md)

[Overview de Balanceador de carga oposta Internet](load-balancer-internet-overview.md)

[Começar a Internet opostas balanceador de carga](load-balancer-get-started-internet-arm-ps.md)

[Visão geral de rede virtual](../virtual-network/virtual-networks-overview.md)

[IP reservado restante APIs](https://msdn.microsoft.com/library/azure/dn722420.aspx)