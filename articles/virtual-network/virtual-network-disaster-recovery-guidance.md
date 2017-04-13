<properties
    pageTitle="O que fazer em caso de uma interrupção de serviço Azure afetar redes virtuais Azure | Microsoft Azure"
    description="Saiba o que fazer em caso de uma interrupção de serviço Azure afetar redes virtuais do Azure."
    services="virtual-network"
    documentationCenter=""
    authors="NarayanAnnamalai"
    manager="jefco"
    editor=""/>

<tags
    ms.service="virtual-network"
    ms.workload="virtual-network"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="narayan;aglick"/>

#<a name="virtual-network--business-continuity"></a>Rede virtual – continuidade de negócios

##<a name="overview"></a>Visão geral

Uma rede Virtual (VNet) é uma representação lógica de sua rede na nuvem. Ele permite que você defina seu próprio espaço de endereço IP particular e segmento da rede em sub-redes. VNets serve como um limite de confiança para hospedar seus recursos de computação como máquinas virtuais do Azure e serviços de nuvem (funções da web/trabalho). Um VNet permite que a comunicação de IP particular direta entre os recursos hospedados nele. Uma rede Virtual também podem ser vinculada a uma rede local por meio de uma das opções híbrido como um Gateway VPN ou rota expressa.
 
Um VNet é criado dentro do escopo de uma região. Você pode criar VNets com o mesmo espaço de endereço em duas regiões diferentes (ou seja conosco Leste e Oeste EUA, mas não pode conectá-las entre si diretamente). 

##<a name="business-continuity"></a>Continuidade de negócios

Pode haver várias maneiras que seu aplicativo poderia ser interrompido. Uma determinada região poderia ser cortada completamente devido a um desastre natural ou um desastre parcial devido a uma falha de vários dispositivos/serviços. O impacto sobre o serviço de VNet é diferente em cada uma dessas situações.

**P: o que fazer em caso de uma interrupção para toda a região? Por exemplo, se uma região é completamente corte devido a um desastre natural? O que acontece com as redes virtuais hospedado na região?**

R: a rede Virtual e os recursos na região afetado permanece inacessíveis durante o tempo da interrupção de serviço.

![Diagrama de rede Virtual simples](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**P: o que é possível criar novamente a mesma rede Virtual em uma região diferente?**

R: VPN (VNet) é relativamente leve recurso. Você pode chamar APIs do Azure para criar um VNet com o mesmo espaço de endereço em uma região diferente. Para recriar o mesmo ambiente que estava presente na região afetado, você precisará fazer chamadas de API para implantar novamente seus serviços de nuvem (funções da web/trabalho) e máquinas virtuais que você tinha. Você também terá um Gateway VPN de rotação e se conectar à sua rede local se você tivesse conectividade de local (como em uma implantação híbrida).

As instruções para a criação de um VNet forem encontradas [aqui](./virtual-networks-create-vnet-arm-pportal.md). 

**P: uma réplica de uma VNet em uma determinada região ser recriada em outra região de antecedência?**

R: Sim, você pode criar dois VNets usando o mesmo espaço de endereço IP particular e recursos em duas regiões diferentes com antecedência. Se um cliente foi hospedando internet opostas serviços na VNet, eles poderiam definiu o Gerenciador de tráfego para localização geográfica-rotear o tráfego para a região que está ativa. No entanto, um cliente não pode conectar dois VNets com o mesmo espaço de endereço à sua rede local como ela poderia causar problemas de roteamento. No momento de um desastre e perda de uma VNet em uma região, um cliente pode se conectar a outro VNet na região disponível com correspondência de espaço de endereço para sua rede local.

As instruções para a criação de um VNet forem encontradas [aqui](./virtual-networks-create-vnet-arm-pportal.md).
