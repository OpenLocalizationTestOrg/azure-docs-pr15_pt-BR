<properties
    pageTitle="Como planejar sua rede virtual para um conjunto de RemoteApp Azure | Microsoft Azure"
    description="Aprenda a planejar sua rede virtual para um conjunto de RemoteApp do Azure."
    services="remoteapp"
    documentationCenter="" 
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="how-to-plan-your-virtual-network-for-azure-remoteapp"></a>Como planejar sua rede virtual RemoteApp do Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Este documento descreve como configurar sua rede virtual Azure (VNET) e a sub-rede para RemoteApp do Azure. Se você estiver familiarizado com o Azure redes virtuais, esse é um recurso que ajuda você a virtualização sua infraestrutura de rede na nuvem e para criar soluções de híbrido com o Azure e seus recursos locais. Você pode ler mais sobre ele [aqui](../virtual-network/virtual-networks-overview.md).

Se você quiser definir políticas de segurança para o tráfego (entrada e saída) em sua rede virtual onde você está implantando RemoteApp do Azure, é altamente recomendável criar uma sub-rede separada para o Azure RemoteApp do restante do seu implantações da rede virtual Azure. Para obter mais informações sobre como definir políticas de segurança em sua sub-rede Azure rede virtual, leia [o que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md).

## <a name="types-of-azure-remoteapp-collections-with-azure-virtual-networks"></a>Tipos de conjuntos de RemoteApp Azure com redes virtuais Azure

Os gráficos a seguir mostram as duas opções de outro conjunto quando você quiser usar uma rede virtual.

### <a name="azure-remoteapp-cloud-collection-with-vnet"></a>Conjunto de nuvem de RemoteApp Azure com VNET

 ![Azure RemoteApp - conjunto de nuvem com um VNET](./media/remoteapp-planvpn/ra-cloudvpn.png)

Isso representa uma coleção de RemoteApp Azure onde todos os recursos que a sessão de RemoteApp hosts precisam de acesso são implantados no Azure. Eles podem ser a mesma VNET como o RemoteApp VNET ou um VNET diferente no Azure.

### <a name="azure-remoteapp-hybrid-collection-with-vnet"></a>Conjunto de híbrido de RemoteApp Azure com VNET

![Azure RemoteApp - conjunto híbrido com uma VNET](./media/remoteapp-planvpn/ra-hybridvpn.png)

Isso representa uma coleção de RemoteApp Azure alguns dos recursos que os hosts de sessão de RemoteApp precisam acessar anotados implantado no local. O RemoteApp VNET está vinculada à rede local usando tecnologias híbrido Azure como-to-site VPN ou via expressa.


## <a name="how-the-system-works"></a>Como o sistema funciona

Nos bastidores Azure RemoteApp implanta Azure máquinas virtuais (com sua imagem carregada) à sub-rede da rede virtual que você escolheu durante a configuração. Se você tiver optado por um conjunto de híbrido, podemos tentar resolver o FQDN do controlador de domínio que você inseriu no fluxo de trabalho provisionamento com o servidor DNS fornecido na rede virtual.  
Se você estiver se conectando a uma rede virtual existente, verifique se expor as portas necessárias em seus grupos de segurança de rede na sua sub-rede RemoteApp do Azure. 

Recomendamos que você use uma [sub-rede grande o suficiente para RemoteApp do Azure](remoteapp-vnetsizing.md). O maior suportada por rede Virtual do Azure é /8 (usando definições de sub-rede CIDR). Sua sub-rede deve ser grande o suficiente para acomodar todas as VMs de RemoteApp Azure durante a escala quando mais usuários acessam os aplicativos. 

A seguir estão as coisas que você precisa habilitar em sua sub-rede de rede virtual: 

2.  O tráfego de saída da sub-rede deve ser permitido no intervalo de portas 10101-10175 para se comunicar com um dos serviços do Azure RemoteApp internos.
3.  Tráfego de saída deve ser permitido da sua sub-rede para se conectar ao armazenamento do Azure na porta 443
4.  Se você tiver o Active Directory hospedado no Azure, verifique se que qualquer máquina virtual dentro da sub-rede de rede virtual para Azure RemoteApp é capaz de se conectar ao controlador de domínio. O DNS na rede virtual deve ser capaz de resolver o FQDN deste controlador de domínio.


## <a name="virtual-network-with-forced-tunneling"></a>Rede virtual com túnel forçada

[Forçado túnel](../vpn-gateway/vpn-gateway-about-forced-tunneling.md) agora tem suporte para todos os novos conjuntos de RemoteApp do Azure. Atualmente, não suportamos a migração de um conjunto existente para dar suporte a túnel forçada.  Você terá que excluir todos os seus conjuntos de existentes usando o VNET que você está se vinculando RemoteApp do Azure e crie um novo para obter forçado túnel habilitado em seus conjuntos. 
