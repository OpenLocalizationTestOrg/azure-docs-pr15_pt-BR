<properties 
   pageTitle="Solucionar problemas de rotas - PowerShell | Microsoft Azure"
   description="Saiba como solucionar problemas de rotas no modelo de implantação do Gerenciador de recursos do Azure usando o PowerShell do Azure."
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

# <a name="troubleshoot-routes-using-azure-powershell"></a>Solucionar problemas de rotas usando o PowerShell do Azure

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

### <a name="view-effective-routes-for-a-network-interface"></a>Exibir rotas eficazes para uma interface de rede

Para ver as rotas agregadas que são aplicadas a uma interface de rede, conclua as seguintes etapas:

1.  Inicie uma sessão do PowerShell do Azure e faça logon no Azure. Se não estiver familiarizado com o Azure PowerShell, leia o artigo [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md) .

2.  O comando a seguir retorna todas as rotas aplicadas a uma interface de rede chamada *NIC1 VM1* no grupo de recursos *RG1*.

        Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

    >[AZURE.TIP] Se você não souber o nome de uma interface de rede, digite o seguinte comando para recuperar os nomes de todas as interfaces de rede em um group.* do recurso

        Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name

    A seguinte saída é semelhante a saída para cada rota aplicada à sub-rede que a NIC é conectada ao:

        Name :
        State : Active
        AddressPrefix : {10.9.0.0/16}
        NextHopType : VNetLocal
        NextHopIpAddress : {}

        Name :
        State : Active
        AddressPrefix : {0.0.0.0/16}
        NextHopType : Internet
        NextHopIpAddress : {}

    Observe o seguinte na saída:
    - **Nome**: nome do roteiro eficaz pode estar vazia, a menos que seja explicitamente especificada, para rotas definidas pelo usuário. 
    - **Estado**: indica o estado da rota eficaz. Valores possíveis são "Ativo" ou "Inválida"
    - **AddressPrefixes**: Especifica o prefixo de endereço da rota eficaz na notação CIDR. 
    - **nextHopType**: indica o próximo salto para a rota determinado. Valores possíveis são *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*ou *Nulo*. Um valor *Nulo* para **nextHopType** em um UDR pode indicar uma rota inválida. Por exemplo, se **nextHopType** for *VirtualAppliance* e a rede virtual aparelho máquina virtual não está em um estado provisionado/execução. Se **nextHopType** for *VPNGateway* e não há nenhum gateway provisionado/em execução no VNet determinado, a rota pode se tornar inválida.
    - **NextHopIpAddress**: Especifica o endereço IP do próximo salto da rota eficaz.
    
    O comando a seguir retorna as rotas em fácil de exibir tabela:

        Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table

    A saída a seguir é parte da saída recebida para o cenário descrito anteriormente:

        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {0.0.0.0/0} Internet {}
    

3. Não há nenhuma rota listada para *WestUS-VNet3* VNet (prefixo 10.10.0.0/16)* * do *WestUS-VNet1* (prefixo 10.9.0.0/16) na saída da etapa anterior. Como mostrado na figura a seguir, o link de correspondência VNet com a VNet *WestUS VNet3* está no estado *desconectado* .
    
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    O link bidirecional para a correspondência é interrompida, que explica por que VM1 não pôde conectar VM3 no VNet *WestUS VNet3* . Configurar um link de correspondência de VNet bidirecional novamente para *WestUS-VNet1* e *WestUS VNet3* VNets. A saída retornada depois que o link de correspondência VNet corretamente é estabelecido segue:

        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
        
    Depois de determinar o problema, você pode adicionar, remover, ou alterar as rotas e rotear tabelas. Digite o seguinte comando para ver uma lista dos comandos usados para fazê-lo:

        Get-Help *-AzureRmRouteConfig

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
    - Regras de grupo de segurança de rede (NSG) podem causar impacto os fluxos de tráfego. Para obter mais informações, consulte o artigo de [Solucionar problemas de grupos de segurança de rede](virtual-network-nsg-troubleshoot-powershell.md) .
