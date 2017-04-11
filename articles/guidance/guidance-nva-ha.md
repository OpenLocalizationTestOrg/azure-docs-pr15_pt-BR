<properties
   pageTitle="Implantando dispositivos virtuais em alta disponibilidade | Microsoft Azure"
   description="Como implantar dispositivos virtuais de rede em alta disponibilidade."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="telmos"/>

# <a name="deploying-virtual-appliances-in-high-availability"></a>Implantando dispositivos virtuais em alta disponibilidade

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve um conjunto de práticas recomendadas para implantar dispositivos virtuais de rede (NVAs) de maneira altamente disponível. Antes de continuar, verifique se você entende como [rotas definidas pelo usuário (UDR)] [ udr-overview] e [balanceador de carga] [ lb-overview] corporativa no Azure.

Você pode usar diferentes NVAs disponíveis no Azure marketplace para estender a funcionalidade do Azure da mesma maneira que você usar eletrodomésticos no seu centro de dados local. A figura a seguir mostra uma implantação de exemplo de um [único NVA] [ nva-scenario] como um dispositivo de firewall. 

![[0]][0]

Embora a implantação anterior funcione, ele apresenta um único ponto de falha. Se o dispositivo virtual falhar, nenhum tráfego flui. Para resolver esse problema, você precisará usar vários NVAs. No entanto, que também requer outros recursos e configurações a serem usados dependendo das suas necessidades.

Você pode usar uma das seguintes soluções para implantar um ambiente NVA altamente disponível.

|Solução|Benefícios|Considerações|
|---|---|---|
|Ingresso com dispositivos virtuais Camada 7|Todos os nós estão ativos|Requer um NVA que pode encerrar conexões e usar SNAT<br/>Requer um conjunto separado de NVAs para tráfego provenientes da Internet e do Azure<br/>Só pode ser usado para tráfego de origem fora do Azure|
|Ingresso-egresso com dispositivos virtuais Camada 7|Todos os nós estão ativos<br/>Capaz de lidar com o tráfego que se originou no Azure |Requer um NVA que pode encerrar conexões e usar SNAT<br/>Requer um conjunto separado de NVAs para tráfego provenientes da Internet e do Azure|
|Mudança de PIP-UDR|Único conjunto de NVAs para todo o tráfego<br/>Pode lidar com todo o tráfego (sem limite nas regras de porta)|Ativo-passivo<br/>Requer um processo de failover|

## <a name="ingress-with-layer-7-virtual-appliances"></a>Ingresso com dispositivos virtuais Camada 7
Você pode usar um conjunto de NVAs atrás de um balanceador de carga Azure para fornecer conectividade com o Azure cargas de trabalho por trás de um conjunto pequeno de portas do servidor (como HTTP e HTTPS). A figura a seguir destaca como fornecer alta disponibilidade nesse cenário no nível NVA.

![[1]][1]

Neste cenário, o aparelho de rede virtual usada deve encerrar todas as conexões e passe-os para a sub-rede de carga de trabalho. As carga de trabalho VMs (máquinas virtuais) responder a NVA receber uma solicitação de e fluxos de tráfego sem problemas. 

## <a name="ingress-egress-with-layer-7-virtual-appliances"></a>Ingresso-egresso com dispositivos virtuais Camada 7
Você pode estender a arquitetura anterior e adicionar outro conjunto de NVAs tratar tráfego provenientes Azure sejam tratadas por NVAs, conforme mostrado na figura a seguir:

![[2]][2]

Neste cenário, todo o tráfego de origem no Azure é roteado para um balanceador de carga interno que distribui carga entre um conjunto diferente de NVAs. Estas NVAs direcionam o tráfego à Internet usando seus endereços IP públicos individuais. 

## <a name="pip-udr-switch"></a>Mudança de PIP-UDR
Você pode evitar a criação de várias pilhas NVA usando dois NVAs no modo de ativo-passivo. Neste cenário, você pode alternar o endereço IP público (PIP) e rotas definidas pelo usuário (UDRs) quando o nó ativo parar.  

![[3]][3]

Esse cenário é semelhante ao cenário NVA único. A única diferença é que o PIP e UDRs devem ser alteradas para alternar o tráfego entre os NVAs. Essas alterações podem ser feitas manualmente, ou você também pode automatizá-las. Para automatizar, você pode implantar um aplicativo para ambas as NVAs que verifica a integridade do nó ativo. Depois que o nó ativo for pressionada, seu aplicativo pode alterar o PIP e UDRs vincular para o nó passivo.

Uma possível implementação desta solução é usar um [ZooKeeper] [ zookeeper] daemon nas NVAs tratar eleição líder (decidir qual nó é o nó ativo). Assim que um líder é selecionado, ele chama à API REST Azure para remover o PIP do nó com falha e anexá-lo para o líder. Ele também deve alterar UDRs para apontar para o novo endereço do líder particular IP.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [implementar um DMZ entre Azure e data center local] [ dmz-on-prem] usando NVAs de camada-7.
- Saiba como [implementar um DMZ entre Azure e a Internet] [ dmz-internet] usando NVAs de camada-7.

<!-- links -->
[udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[lb-overview]: ../load-balancer/load-balancer-overview.md
[zookeeper]: https://zookeeper.apache.org/
[nva-scenario]: ../virtual-network/virtual-network-scenario-udr-gw-nva.md
[dmz-on-prem]: guidance-iaas-ra-secure-vnet-hybrid.md
[dmz-internet]: guidance-iaas-ra-secure-vnet-dmz.md

<!-- images -->
[0]: ./media/guidance-nva-ha/single-nva.png "Arquitetura NVA única"
[1]: ./media/guidance-nva-ha/l7-ingress.png "Ingresso Camada 7"
[2]: ./media/guidance-nva-ha/l7-ingress-egress.png "Camada 7 de entrada e saída"
[3]: ./media/guidance-nva-ha/active-passive.png "Cluster ativo-passivo"