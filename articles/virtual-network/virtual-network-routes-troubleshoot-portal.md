<properties 
   pageTitle="Solucionar problemas de rotas - Portal | Microsoft Azure"
   description="Saiba como solucionar problemas de rotas no modelo de implantação do Gerenciador de recursos do Azure usando o Portal do Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# <a name="troubleshoot-routes-using-the-azure-portal"></a>Solucionar problemas de rotas usando o Portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](virtual-network-routes-troubleshoot-portal.md)
- [PowerShell](virtual-network-routes-troubleshoot-powershell.md)

Se você estiver enfrentando problemas de conectividade de rede para ou do seu Azure Máquina Virtual (máquina virtual), rotas podem causar impacto seus fluxos de tráfego de máquina virtual. Este artigo fornece uma visão geral dos recursos de diagnóstico para rotas ajudar a solucionar ainda mais.

Tabelas de rota são associadas a sub-redes e eficazes em todas as interfaces de rede (NIC) nessa sub-rede. Os seguintes tipos de rotas podem ser aplicados a cada interface de rede:

- **Rotas do sistema:** Por padrão, cada sub-rede criada em uma rede Virtual do Azure (VNet) tem tabelas de rota de sistema que permita o tráfego de VNet local, o tráfego de local por meio de gateways VPN e o tráfego de Internet. Também existem rotas de sistema para VNets peered.
- **Rotas BGP:** Propagado para interfaces de rede por meio de rota expressa ou conexões de VPN-to-site. Saiba mais sobre o roteamento de BGP lendo os artigos [BGP com gateways VPN](../vpn-gateway/vpn-gateway-bgp-overview.md) e a [Visão geral de rota expressa](../expressroute/expressroute-introduction.md) .
- **Rotas definidas pelo usuário (UDR):** Se você estiver usando dispositivos virtuais de rede ou estão túnel forçado tráfego para uma rede local por meio de uma VPN to-site, você pode ter definido pelo usuário rotas (UDRs) associadas a sua tabela de rota de sub-rede. Se você não estiver familiarizado com UDRs, leia o artigo de [rotas definidas pelo usuário](virtual-networks-udr-overview.md#user-defined-routes) .

Com várias rotas que podem ser aplicadas a uma interface de rede, pode ser difícil determinar quais rotas agregadas são eficientes. Para ajudar a solucionar problemas de conectividade de rede de máquina virtual, você pode exibir todas as rotas eficazes para uma interface de rede no modelo de implantação do Gerenciador de recursos do Azure.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>Usando rotas eficaz para solucionar problemas de fluxo de tráfego de máquina virtual

Este artigo usa o cenário a seguir como exemplo para ilustrar como solucionar problemas com as rotas eficazes para uma interface de rede:

Uma máquina virtual (*VM1*) conectado ao VNet (*VNet1*, prefixo: 10.9.0.0/16) tenta se conectar a um VM(VM3) em um VNet recentemente peered (*VNet3*, prefixo 10.10.0.0/16). Há nenhuma UDRs ou BGP roteia aplicado a interface de rede de NIC1 VM1 conectada para a máquina virtual, apenas rotas de sistema são aplicadas.

Este artigo explica como determinar a causa da falha de conexão, usando o recurso de rotas eficaz no modelo de implantação do gerenciamento de recursos do Azure.
Enquanto o exemplo usa apenas rotas de sistema, as mesmas etapas podem ser usadas para determinar falhas de conexão de entrada e saída sobre qualquer tipo de rota.

>[AZURE.NOTE] Se sua máquina virtual tem mais de uma NIC anexada, marque rotas eficazes para cada uma das NICs para diagnosticar problemas de conectividade de rede de e para uma máquina virtual.

### <a name="view-effective-routes-for-a-virtual-machine"></a>Exibir rotas eficazes para uma máquina virtual

Para ver as rotas agregadas que são aplicadas a uma máquina virtual, conclua as seguintes etapas:

1. Fazer login no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**, clique em **máquinas virtuais** na lista que aparece.
3. Selecione uma máquina virtual para solucionar problemas na lista exibida e uma lâmina de máquina virtual com opções aparece.
4. Clique em **diagnosticar & resolver problemas** e selecione um problema comum. Neste exemplo, **não é possível conectar-se para minha máquina virtual Windows** está selecionada. 

    ![](./media/virtual-network-routes-troubleshoot-portal/image1.png)

5. Etapas aparecem sob o problema, conforme mostrado na figura a seguir: 

    ![](./media/virtual-network-routes-troubleshoot-portal/image2.png)

    Clique em *rotas eficazes* na lista de etapas recomendadas.

6. A lâmina de **rotas efetivas** é exibida, conforme mostrado na figura a seguir:

    ![](./media/virtual-network-routes-troubleshoot-portal/image3.png)

    Se sua máquina virtual tiver apenas uma NIC, ele é selecionado por padrão. Se você tiver mais de uma NIC, selecione o NIC para o qual você deseja exibir as rotas eficazes.

    >[AZURE.NOTE] Se a máquina virtual associada a NIC não estiver em um estado em execução, rotas eficazes não serão exibidas. Apenas as primeiras 200 rotas eficientes são mostradas no portal. Para a lista inteira, clique em **Download**. Você pode filtrar ainda mais os resultados do arquivo. csv baixado.

    Observe o seguinte na saída:
    - **Fonte**: indica o tipo de rota. Rotas de sistema são mostradas como *padrão*, UDRs são mostrados como rotas de *usuário* e o gateway (estática ou BGP) são mostrados como *VPNGateway*.
    - **Estado**: indica o estado da rota eficaz. Valores possíveis são *ativo* ou *inválidos*.
    - **AddressPrefixes**: Especifica o prefixo de endereço da rota eficaz na notação CIDR. 
    - **nextHopType**: indica o próximo salto para a rota determinado. Valores possíveis são *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*ou *Nulo*. Um valor *Nulo* para **nextHopType** em um UDR pode indicar uma rota inválida. Por exemplo, se **nextHopType** for *VirtualAppliance* e a rede virtual aparelho máquina virtual não está em um estado provisionado/execução. Se **nextHopType** for *VPNGateway* e não há nenhum gateway provisionado/em execução no VNet determinado, a rota pode se tornar inválida.
    
7. Não há nenhuma rota listada para o VNet *WestUS VNET3* (prefixo 10.10.0.0/16) da *WestUS-VNet1* (prefixo 10.9.0.0/16) na imagem na etapa anterior. Na figura a seguir, o link de correspondência está no estado *desconectado* :
    
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    O link bidirecional para a correspondência é interrompida, que explica por que VM1 não pôde conectar VM3 no VNet *WestUS VNet3* .

8. A figura a seguir mostra as rotas após estabelecer o link de correspondência bidirecional:

    ![](./media/virtual-network-routes-troubleshoot-portal/image5.png)

Cenários de solução de problemas mais para túnel forçado e avaliação de rota, leia a seção [Considerações](virtual-network-routes-troubleshoot-portal.md#Considerations) deste artigo.

### <a name="view-effective-routes-for-a-network-interface"></a>Exibir rotas eficazes para uma interface de rede

Se o fluxo de tráfego de rede é afetado para uma interface de rede específico (NIC), você pode exibir uma lista completa de rotas eficazes uma NIC diretamente. Para ver as rotas agregadas que são aplicadas a uma NIC, conclua as seguintes etapas:

1. Fazer login no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**, clique em **interfaces de rede**
3. Pesquisar a lista para o nome de uma NIC ou selecione-o na lista que aparece. Neste exemplo, **NIC1 VM1** está selecionada.
4. Selecione **rotas eficazes** na lâmina **interface de rede** , conforme mostrado na figura a seguir:
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image6.png)

    O **escopo** padrão é a interface de rede selecionada.

    ![](./media/virtual-network-routes-troubleshoot-portal/image7.png)


### <a name="view-effective-routes-for-a-route-table"></a>Exibir rotas eficazes para uma tabela de roteiro

Quando estiver modificando rotas definidas pelo usuário (UDRs) em uma tabela de rota, talvez você queira examinar o impacto das rotas sendo adicionado em uma determinada VM. Uma tabela de rota pode ser associada a qualquer número de sub-redes. Agora você pode exibir todas as rotas eficazes para todas as NICs que uma tabela de determinada rota é aplicada, sem precisar alternar o contexto da lâmina determinada rota tabela.

Neste exemplo, um UDR (*UDRoute*) é especificado em uma tabela de rota (*UDRouteTable*). Essa rota envia todo o tráfego de Internet do *Subnet1* no VNet *WestUS-VNet1* , por meio de um dispositivo virtual de rede (NVA), em *Subnet2* de VNet o mesmo. A rota é mostrada na figura a seguir:

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)

Para ver as rotas agregação para uma tabela de rota, conclua as seguintes etapas:

1. Fazer login no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**, clique em **tabelas de rota**
3. Pesquise a lista para a tabela de rota que você deseja ver agregadas rotas para e selecione-o. Neste exemplo, **UDRouteTable** é selecionado. Um blade para a tabela de rota selecionada aparece, como mostrado na figura a seguir:

    ![](./media/virtual-network-routes-troubleshoot-portal/image9.png)

4. Selecione **Eficaz rotas** na **tabela de rotas** lâmina. O **escopo** está definido para a tabela de rota selecionada.
5. Uma tabela de rota pode ser aplicada a várias sub-redes. Selecione a **sub-rede** que você queira examinar na lista. Neste exemplo, **Subnet1** é selecionado.
6. Selecione uma **Interface de rede**. Todas as NICs conectadas à sub-rede selecionada são listadas. Neste exemplo, **NIC1 VM1** está selecionada.

    ![](./media/virtual-network-routes-troubleshoot-portal/image10.png)

    >[AZURE.NOTE] Se a NIC não está associada uma máquina virtual em execução, nenhum rotas eficientes são mostradas.

## <a name="considerations"></a>Considerações

Alguns aspectos a ter em mente ao revisar a lista de rotas retornado:

- O roteamento é baseado em maior prefixo correspondência LPM () entre UDRs, rotas BGP e sistema. Se houver mais de um roteiro com a mesma correspondência LPM, uma rota é selecionada com base na sua origem na seguinte ordem:
    - Rota definidos pelo usuário
    - Rota BGP
    - Rota de sistema (padrão)

    Com rotas eficazes, você só poderá ver rotas eficientes que são LPM correspondência com base em todas as rotas de disponível. Mostrando como as rotas realmente são avaliadas para uma determinada NIC, isso facilita muito solucionar rotas específicas que podem causar impacto conectividade de/para sua máquina virtual.

- Se você tiver UDRs e estiver enviando o tráfego para um dispositivo virtual de rede (NVA), com *VirtualAppliance* como **nextHopType**, verifique se o encaminhamento de IP é ativado o NVA recebendo o tráfego ou pacotes são descartados. 
- Se forçada túnel estiver habilitada, todo o tráfego de saída do Internet será roteado para o local. RDP/SSH da Internet para sua máquina virtual pode não funcionar com essa configuração, dependendo de como o local lida com esse tráfego. 
  Forçado de túnel pode ser habilitado:
    - Se usando-to-site VPN, definindo uma rota definidos pelo usuário (UDR) com nextHopType como Gateway VPN
    - Se uma rota padrão é anunciada sobre BGP
- VNet tráfego aos funcione corretamente, uma rota de sistema com **nextHopType** *VNetPeering* deve existir para o intervalo de prefixo do VNet peered. Se tal uma rota não existe e o link de correspondência VNet parece Okey:
    - Aguarde alguns segundos e repita se ele for um link de correspondência recentemente estabelecido. Ocasionalmente demora mais tempo para propagar rotas a todas as interfaces de rede em uma sub-rede.
    - Regras de grupo de segurança de rede (NSG) podem causar impacto os fluxos de tráfego. Para obter mais informações, consulte o artigo de [Solucionar problemas de grupos de segurança de rede](virtual-network-nsg-troubleshoot-portal.md) .
