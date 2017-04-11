<properties
   pageTitle="Tipos de nós de serviço tecidos e conjuntos de escala de máquina virtual | Microsoft Azure"
   description="Descreve como os tipos de nós de serviço tecidos relacionam conjuntos de escala de máquina virtual e como para remoto se conecta a uma instância de máquina virtual escala definida ou de um nó de cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>A relação entre os tipos de nó de estrutura de serviço e conjuntos de escala de máquina Virtual

Os conjuntos de escala de máquina virtual são um recurso de computação Azure que você pode usar para implantar e gerenciar um conjunto de máquinas virtuais como um conjunto. Cada tipo de nó que é definido em um cluster de estrutura de serviço está configurado como um conjunto de escala de máquina virtual separada. Cada tipo de nó, em seguida, pode ser dimensionado para cima ou para baixo independentemente, ter diferentes conjuntos de portas abertas e pode ter métricas de capacidade diferente.

A captura de tela a seguir mostra um cluster que tem dois tipos de nó: front-end e back-end.  Cada tipo de nó tem cinco nós.

![Captura de tela de um cluster que tem dois tipos de nó][NodeTypes]

## <a name="mapping-vm-scale-set-instances-to-nodes"></a>Mapeando instâncias de máquina virtual escala definida para nós

Como você pode ver acima, as instâncias de máquina virtual escala definida inicie da instância 0 e, em seguida, vai para cima. A numeração é refletida nos nomes. Por exemplo, BackEnd_0 é instância 0 o conjunto de escala de back-end máquina virtual. Este determinado conjunto de escala de máquina virtual tem cinco instâncias, chamadas BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando você dimensionar o uma escala de máquina virtual configurar uma nova instância é criada. O novo nome de instância do conjunto de escala de máquina virtual normalmente será o nome do conjunto de escala de máquina virtual + número da próxima instância. Em nosso exemplo, é BackEnd_5.


## <a name="mapping-vm-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>Mapeamento de escala de máquina virtual definir balanceadores de carga para cada tipo de nó/máquina virtual conjunto de escala

Se você tiver implantado o seu cluster no portal do ou tiver usado a amostra de modelo de Gerenciador de recursos que fornecemos, em seguida, quando você recebe uma lista de todos os recursos em um grupo de recursos, em seguida, você verá os balanceadores de carga para cada tipo de máquina virtual escala definida ou nó.

O nome seria algo como: **LB -&lt;NodeType nome&gt;**. Por exemplo, LB-sfcluster4doc-0, conforme mostrado nesta captura de tela:


![Recursos][Resources]


## <a name="remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node"></a>Remoto se conectar a uma instância de máquina virtual escala definida ou de um nó de cluster
Cada tipo de nó que é definido em um cluster está configurado como um conjunto de escala de máquina virtual separada.  Isso significa que os tipos de nós podem ser dimensionados para cima ou para baixo independentemente e podem ser feitas de diferentes SKUs de máquina virtual. Ao contrário de única instância VMs, as instâncias de máquina virtual escala definida não obtém um endereço IP virtual próprias. Portanto ele pode ser um pouco desafiador quando você estiver procurando por um endereço IP e porta que você pode usar para remoto se conectar a uma instância específica.

Aqui estão as etapas que você pode seguir para descobri-las.

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>Etapa 1: Descubra o endereço IP virtual para o tipo de nó e, em seguida, regras de entrada NAT para RDP

Para obter que, você precisa obter os valores de regras NAT entrados que foram definidos como parte da definição de recurso para **Microsoft.Network/loadBalancers**.

No portal do, navegue até o blade de Balanceador de carga e em seguida **configurações**.

![LBBlade][LBBlade]


Em **configurações**, clique em **regras de entrada NAT**. Isso agora mostra o endereço IP e porta que você pode usar para remoto conectar-se para a primeira instância de máquina virtual escala definida. A captura de tela abaixo, é **104.42.106.156** e **3389**

![NATRules][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-vm-scale-set-instancenode"></a>Etapa 2: Descobrir a porta que você pode usar para remoto conectar-se para o conjunto de escala de máquina virtual instância/nó específico

Anteriormente contidas neste documento, posso falar sobre como as instâncias de máquina virtual escala definida mapeiam os nós. Usaremos que descobrir a porta exata.

As portas são alocadas em ordem crescente de instância do conjunto de escala de máquina virtual. portanto no meu exemplo para o tipo de nó FrontEnd, as portas para cada uma das cinco instâncias são as seguintes. Agora, você precisa fazer o mesmo mapeamento para a sua instância de máquina virtual escala definida.

|**Instância de conjunto de escala de máquina virtual**|**Porta**|
|-----------------------|--------------------------|
|FrontEnd_0|3389|
|FrontEnd_1|3390|
|FrontEnd_2|3391|
|FrontEnd_3|3392|
|FrontEnd_4|3393|
|FrontEnd_5|3394|


### <a name="step-3-remote-connect-to-the-specific-vm-scale-set-instance"></a>Etapa 3: Remoto conectar à instância específica de conjunto de escala de máquina virtual

Na captura de tela abaixo para usar Conexão de área de trabalho remota para se conectar à FrontEnd_1:

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>Como alterar os valores de intervalo de porta RDP

### <a name="before-cluster-deployment"></a>Antes da implantação de cluster

Quando você configura o cluster usando um modelo do Gerenciador de recursos, você pode especificar o intervalo na **inboundNatPools**.

Vá para a definição de recurso para **Microsoft.Network/loadBalancers**. Em que você localize a descrição de **inboundNatPools**.  Substitua os valores *frontendPortRangeStart* e *frontendPortRangeEnd* .

![InboundNatPools][InboundNatPools]


### <a name="after-cluster-deployment"></a>Após a implantação de cluster
Este é um pouco mais complexo e pode resultar em VMs obtendo recicladas. Agora, você precisará definir novos valores usando o PowerShell do Azure. Certifique-se de que o Azure PowerShell 1.0 ou posterior está instalado no seu computador. Se você não tiver feito isso antes, eu altamente recomendável que você siga as etapas descritas em [como instalar e configurar o Azure PowerShell.](../powershell-install-configure.md)

Entrar sua conta do Azure. Se este comando do PowerShell falhar por algum motivo, você deve verificar se você tem Azure PowerShell instalado corretamente.

```
Login-AzureRmAccount
```

Execute o seguinte para obter detalhes sobre sua balanceador de carga e você verá os valores para a descrição de **inboundNatPools**:

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

Agora, defina *frontendPortRangeEnd* e *frontendPortRangeStart* para os valores desejados.

```
$PropertiesObject = @{
    #Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```


## <a name="next-steps"></a>Próximas etapas

- [Visão geral do recurso "Implante em qualquer lugar" e uma comparação com clusters gerenciados do Azure](service-fabric-deploy-anywhere.md)
- [Segurança de cluster](service-fabric-cluster-security.md)
- [Serviço tecidos SDK e guia de Introdução](service-fabric-get-started.md)


<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
