<properties 
   pageTitle="O que são rotas de definidas pelo usuário e encaminhamento de IP?"
   description="Saiba como usar rotas de definidas pelo usuário (UDR) e o encaminhamento de IP para encaminhar o tráfego para dispositivos virtuais no Azure de rede."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="what-are-user-defined-routes-and-ip-forwarding"></a>O que são rotas de definidas pelo usuário e encaminhamento de IP?
Quando você adiciona máquinas virtuais (VMs) a uma rede virtual (VNet) no Azure, você observará que as VMs são capazes de se comunicar entre si pela rede, automaticamente. Você não precisa especificar um gateway, embora as VMs estiverem em sub-redes diferentes. O mesmo é verdadeiro para comunicação do VMs à Internet pública e até mesmo para seu local de rede quando uma conexão de híbrido do Azure para seu próprio data center está presente.

Este fluxo de comunicação é possível porque Azure usa uma série de rotas de sistema para definir como o tráfego IP flui. Rotas de sistema controlam o fluxo de comunicação nas seguintes situações:

- De dentro da mesma sub-rede.
- De uma sub-rede para outro dentro de um VNet.
- De VMs com a Internet.
- De um VNet para outra VNet por meio de um gateway VPN.
- De um VNet à sua rede local por meio de um gateway VPN.

A figura a seguir mostra uma configuração simples com um VNet, duas sub-redes e alguns VMs, juntamente com as rotas de sistema que permitem o tráfego IP fluxo.

![Rotas de sistema no Azure](./media/virtual-networks-udr-overview/Figure1.png)

Embora o uso de rotas de sistema facilita o tráfego automaticamente para a sua implantação, há casos em que você deseja controlar o roteamento de pacotes por meio de um dispositivo virtual. Você pode então criando rotas definidas pelo usuário que especificar o próximo salto para pacotes fluindo para uma sub-rede específica para ir para o seu dispositivo virtual em vez disso e ativar o encaminhamento de IP para a máquina virtual executando como o dispositivo virtual.

A figura a seguir mostra um exemplo de rotas definidas pelo usuário e encaminhamento de IP para forçar pacotes enviados para uma sub-rede do outro para dar uma olhada em um dispositivo virtual em uma terceira sub-rede.

![Rotas de sistema no Azure](./media/virtual-networks-udr-overview/Figure2.png)

>[AZURE.IMPORTANT] Rotas definidas pelo usuário são aplicadas apenas ao tráfego deixando uma sub-rede. não é possível criar rotas para especificar como o tráfego entra em uma sub-rede da Internet, por exemplo. Além disso, o dispositivo que você está encaminhando tráfego não pode ser na mesma sub-rede qual se origina o tráfego. Sempre crie uma sub-rede separada para seus dispositivos. 

## <a name="route-resource"></a>Recurso de roteiro
Pacotes são roteados através de uma rede de TCP/IP com base em uma tabela de rota definida em cada nó na rede física. Uma tabela de rota é um conjunto de rotas individuais usado para decidir onde encaminhar pacotes com base no endereço IP de destino. Uma rota consiste no seguinte:

|Propriedade|Descrição|Restrições|Considerações|
|---|---|---|---|
| Prefixo de endereço | O CIDR de destino para o qual a rota se aplica, como 10.1.0.0/16.|Deve ser um intervalo CIDR válido que representam endereços na Internet pública, Azure rede virtual ou data center local.|Verifique se que o **prefixo de endereço** não contém o endereço para o **endereço do próximo salto**, caso contrário, seus pacotes entrará em um loop partindo de origem para o próximo salto sem atinja o destino. |
| Próximo tipo de salto | O tipo de salto Azure o pacote deve ser enviado para. | Deve ser um dos seguintes valores: <br/> **Rede virtual**. Representa a rede virtual local. Por exemplo, se você tiver duas sub-redes, 10.1.0.0/16 e 10.2.0.0/16 na mesma rede virtual, a rota de cada sub-rede na tabela de rotas terá um próximo valor de salto de *Rede Virtual*. <br/> **Gateway de rede virtual**. Representa um Gateway VPN S2S Azure. <br/> **Internet**. Representa o gateway de Internet padrão fornecido pelo infraestrutura do Azure. <br/> **Dispositivo virtual**. Representa um dispositivo virtual que você adicionou à sua rede virtual Azure. <br/> **Nenhum**. Representa uma falha de preto. Pacotes encaminhados para uma falha de preto não serão encaminhadas todo.| Considere usar um tipo de **Nenhum** para parar de pacotes de fluindo para um determinado destino. | 
| Endereço do próximo salto | O endereço do próximo salto contém o endereço IP pacotes devem ser encaminhadas. Valores de salto próxima só são permitidas em rotas onde o próximo tipo de salto é *Dispositivo Virtual*.| Deve ser um endereço IP que é alcançado dentro da rede Virtual onde é aplicada a rota definidas pelo usuário. | Se o endereço IP representa uma máquina virtual, verifique se que você habilitar o [encaminhamento de IP](#IP-forwarding) no Azure para a máquina virtual. |

No Azure PowerShell alguns dos valores "NextHopType" têm nomes diferentes:
- Rede virtual é VnetLocal
- Gateway de rede virtual é VirtualNetworkGateway
- Dispositivo virtual é VirtualAppliance
- Internet é Internet
- Nenhum é nenhum

### <a name="system-routes"></a>Rotas de sistema
Cada sub-rede criada em uma rede virtual é automaticamente associada uma tabela de rota que contém as seguintes regras de roteiro do sistema:

- **Regra de Vnet local**: esta regra é criada automaticamente para cada sub-rede em uma rede virtual. Especifica que há um link direto entre as VMs na VNet e não há nenhuma próximo salto intermediário.
- **Regra de local**: esta regra se aplica a todo o tráfego destinado para o intervalo de endereço do local e usa o gateway VPN como o destino do próximo salto.
- **Regra de Internet**: alças esta regra todo o tráfego destinado à Internet pública (o prefixo de endereço 0.0.0.0/0) e usa o gateway de internet infraestrutura como o próximo salto para todo o tráfego destinado à Internet.

### <a name="user-defined-routes"></a>Rotas definidas pelo usuário
Para a maioria dos ambientes, precisará somente as rotas de sistema já definidas pelo Azure. No entanto, você talvez precise criar uma tabela de rota e adicione uma ou mais rotas em casos específicos, como:

- Forçar túnel na Internet por meio de sua rede local.
- Uso de dispositivos virtuais no seu ambiente do Azure.

Nos cenários acima, será necessário criar uma tabela de rota e adicionar rotas definidas pelo usuário a ele. Você pode ter várias tabelas de rota e a mesma tabela de rota pode ser associada a uma ou mais sub-redes. E cada sub-rede só pode ser associado a uma tabela única rota. Todas as VMs e serviços de nuvem em uma tabela de rotas do uso de sub-rede associada a sub-rede.

Sub-redes dependem rotas de sistema até que a tabela de rota está associada à sub-rede. Depois que uma associação existe, roteamento é feito com base na correspondência de prefixo mais longa (LPM) entre rotas definidas pelo usuário e rotas de sistema. Se houver mais de uma rota com a correspondência LPM mesma uma rota é selecionada com base na sua origem na seguinte ordem:

1. Rota definida do usuário
1. Rota BGP (quando rota expressa é usada)
1. Rota de sistema

Para saber como criar rotas definidas pelo usuário, consulte [como criar rotas e habilitar o encaminhamento de IP no Azure](virtual-network-create-udr-arm-template.md).

>[AZURE.IMPORTANT] Rotas definidas pelo usuário são aplicadas apenas aos serviços VMs do Azure e a nuvem. Por exemplo, se você quiser adicionar um dispositivo virtual de firewall entre sua rede local e o Azure, você precisará criar uma rota de definidas pelo usuário para as tabelas de rota Azure que encaminha todo o tráfego indo para o espaço de endereço do local para o dispositivo virtual. Você também pode adicionar uma rota definidas pelo usuário (UDR) para o GatewaySubnet para encaminhar todo o tráfego de locais para Azure através do aparelho virtual. Este é um acréscimo recente.

### <a name="bgp-routes"></a>Rotas BGP
Se você tiver uma conexão de rota expressa entre sua rede local e o Azure, você pode habilitar BGP propagar rotas da sua rede local para o Azure. Essas rotas BGP são usadas da mesma forma como rotas de sistema e rotas de definidas pelo usuário em cada sub-rede Azure. Para obter mais informações, consulte [Introdução rota expressa](../expressroute/expressroute-introduction.md).

>[AZURE.IMPORTANT] Você pode configurar seu ambiente Azure para usar forçar túnel por meio de sua rede local, criando uma rota de usuário definido para 0.0.0.0/0 sub-rede que usa o gateway VPN como o próximo salto. No entanto, isso só funciona se você estiver usando um gateway VPN, não rota expressa. Rota expressa, túnel forçada é configurado pelo BGP.

## <a name="ip-forwarding"></a>Encaminhamento de IP
Conforme descrito acima, uma das principais razões para criar uma rota de usuário definido é encaminhar o tráfego para um dispositivo virtual. Um dispositivo virtual é nada mais do que uma máquina virtual que executa um aplicativo usado para lidar com o tráfego de rede de alguma forma, como um firewall ou um dispositivo NAT.

Este dispositivo virtual máquina virtual deve ser capaz de receber tráfego de entrada não endereçada a mesmo. Para permitir que uma máquina virtual para receber tráfego endereçado para outros destinos, você deve habilitar o encaminhamento de IP para a máquina virtual. Este é um Azure definindo, não uma configuração no sistema operacional convidado.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar rotas no modelo de implantação do Gerenciador de recursos](virtual-network-create-udr-arm-template.md) e associá-los a sub-redes. 
- Saiba como [criar rotas no modelo clássico de implantação](virtual-network-create-udr-classic-ps.md) e associá-los a sub-redes.
