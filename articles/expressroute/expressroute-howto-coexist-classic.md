<properties
   pageTitle="Configurar conexões rota expressa e to-Site VPN que podem coexistência | Microsoft Azure"
   description="Este artigo percorre a configuração rota expressa e uma conexão de VPN to-Site pode coexistência para o modelo de implantação clássico."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="charwen"/>

# <a name="configure-expressroute-and-site-to-site-coexisting-connections-for-the-classic-deployment-model"></a>Configurar conexões passa rota expressa e to-Site para o modelo clássico de implantação


> [AZURE.SELECTOR]
- [PowerShell - Gerenciador de recursos](expressroute-howto-coexist-resource-manager.md)
- [PowerShell - clássico](expressroute-howto-coexist-classic.md)

A capacidade de configurar-to-Site VPN e rota expressa tem várias vantagens. Você pode configurar VPN to-Site como um caminho de failover seguro para ExressRoute ou usar VPNs Site-to-Site para se conectar aos sites que não estão conectadas por meio de rota expressa. Abordaremos as etapas para configurar os dois cenários neste artigo. Este artigo se aplica ao modelo clássico de implantação. Essa configuração não está disponível no portal.

**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

>[AZURE.IMPORTANT] Rota expressa circuitos devem ser configurados previamente antes de seguir as instruções abaixo. Certifique-se de que você tiver seguido as guias para [criar um circuito de rota expressa](expressroute-howto-circuit-classic.md) e [Configurar o roteamento](expressroute-howto-routing-classic.md) antes de seguir as etapas abaixo.

## <a name="limits-and-limitations"></a>Limitações e limites

- **Não há suporte para o roteamento de trânsito.** Você não pode rotear (via Azure) entre sua rede local conectada por meio de-to-Site VPN e sua rede local conectada por meio de rota expressa.
- **Não há suporte para o ponto-a-site.** Você não pode habilitar conexões VPN de ponto-a-site para o mesmo VNet que está conectada a rota expressa. Ponto-a-site VPN e rota expressa não coexistam para o mesmo VNet.
- **Túnel forçada não pode ser ativado no gateway-to-Site VPN.** Você pode apenas "forçar" todo o tráfego de Internet ligadas novamente à sua rede local por meio de rota expressa.
- **Não há suporte para o gateway SKU básico.** Você deve usar um gateway SKU não básicas para o [gateway de rota expressa](expressroute-about-virtual-network-gateways.md) e o [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Baseada em rota somente gateway VPN é suportada.** Você deve usar um baseado em rota [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Rota estática deve ser configurada para o gateway VPN.** Se sua rede local está conectada a rota expressa e uma VPN to-Site, você deve ter uma rota estática configurada em sua rede local para rotear a conexão-to-Site VPN à Internet pública.
- **Rota expressa gateway deve ser configurado primeiro.** Você deverá criar o gateway rota expressa primeiro antes de adicionar o gateway-to-Site VPN.

## <a name="configuration-designs"></a>Designs de configuração

### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Configurar uma VPN to-Site como um caminho de failover para rota expressa

Você pode configurar uma conexão de VPN to-Site como um backup para rota expressa. Isso se aplica somente a redes virtuais vinculadas ao caminho aos particular do Azure. Não há uma solução com base em VPN para serviços acessíveis por meio do Azure público e Microsoft peerings. O circuito rota expressa é sempre o link principal. Dados fluirá caminho-to-Site VPN somente se o circuito de rota expressa falhar. 

![Coexistência](media/expressroute-howto-coexist-classic/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Configurar uma VPN to-Site para se conectar aos sites não conectados por meio de rota expressa

Você pode configurar sua rede onde alguns sites conectem diretamente ao Azure através de-to-Site VPN e alguns sites se conectar por meio de rota expressa. 

![Coexistência](media/expressroute-howto-coexist-classic/scenario2.jpg)

>[AZURE.NOTE] Você não pode definir uma rede virtual como um roteador de trânsito.

## <a name="selecting-the-steps-to-use"></a>Selecionando as etapas para usar

Há dois conjuntos diferentes de procedimentos para escolher para configurar conexões que podem coexistência. O procedimento de configuração que você selecionar dependerá se você tem uma rede virtual existente que você deseja se conectar, ou que você deseja criar uma nova rede virtual.


- Eu não tiver um VNet e precisa criar um.
    
    Se você ainda não tiver uma rede virtual, este procedimento apresentará criando uma nova rede virtual usando o modelo clássico de implantação e criação de novas conexões de VPN rota expressa e to-Site. Para configurar, siga as etapas na seção artigo [para criar uma nova rede virtual e passa conexões](#new).

- Eu já tenho um modelo de implantação clássico VNet.

    Você já pode ter uma rede virtual in-loco com uma conexão de VPN to-Site existente ou conexão de rota expressa. A seção de artigo [para configurar conexões de coexsiting para um já existente VNet](#add) o orientará pela excluindo o gateway e, em seguida, criando novas conexões de VPN rota expressa e to-Site. Observe que, ao criar novas conexões, as etapas devem ser concluídas em uma ordem específica muito. Não use as instruções em outros artigos para criar suas conexões e gateways.

    Neste procedimento, criação de conexões que podem coexistência será exigem que você exclua seu gateway e configure novos gateways. Isso significa que você terá tempo de inatividade para suas conexões entre locais enquanto você exclua e recrie seu gateway e conexões, mas você não precisará migrar qualquer um dos seus VMs ou serviços para uma nova rede virtual. Seus serviços e VMs ainda poderão se comunicar através de Balanceador de carga enquanto você configure seu gateway se eles estiverem configurados para fazer isso.


## <a name="new"></a>Para criar uma nova rede virtual e passa conexões

Este procedimento irá orientá-lo a criar um VNet e crie-to-Site e conexões de rota expressa que coexistam.

1. Você precisará instalar a versão mais recente do cmdlets do PowerShell do Azure. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell. Observe que os cmdlets que você usará para esta configuração pode ser um pouco diferentes do que você talvez esteja familiarizado. Certifique-se de usar os cmdlets especificados nestas instruções. 

2. Crie um esquema para sua rede virtual. Para obter mais informações sobre o esquema de configuração, consulte [esquema de configuração de rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

    Quando você cria seu esquema, certifique-se de que você use os seguintes valores:

    - A sub-rede de gateway para a rede virtual deve ser /27 ou um prefixo mais curto (como /26 ou /25).
    - O tipo de conexão de gateway é "dedicado".

              <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
                <AddressSpace>
                  <AddressPrefix>10.17.159.192/26</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Subnet-1">
                    <AddressPrefix>10.17.159.192/27</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.17.159.224/27</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="MyLocalNetwork">
                      <Connection type="Dedicated" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>

3. Após criar e configurar o seu arquivo de esquema xml, carregue o arquivo. Isso criará sua rede virtual.

    Use o seguinte cmdlet para carregar seu arquivo, substituindo o valor pelo seu próprio.

        Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'

4. <a name="gw"></a>Crie um gateway rota expressa. Certifique-se de especificar o GatewaySKU como *padrão*, *HighPerformance*, ou *UltraPerformance* e o GatewayType como *DynamicRouting*.

    Use o exemplo a seguir, substituindo os valores para seu próprio.

        New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance

5. Vincule o gateway rota expressa para o circuito rota expressa. Após essa etapa foi concluída, a conexão entre sua rede local e o Azure, por meio de rota expressa, é estabelecida.

        New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET

6. <a name="vpngw"></a>Em seguida, crie o gateway-to-Site VPN. O GatewaySKU deve ser *padrão*, *HighPerformance*, ou *UltraPerformance* e o GatewayType devem ser *DynamicRouting*.

        New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance

    Para recuperar as configurações de gateway de rede virtual, incluindo a identificação de gateway e o IP público, use o `Get-AzureVirtualNetworkGateway` cmdlet.

        Get-AzureVirtualNetworkGateway

        GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
        GatewayName          : S2SVPN
        LastEventData        :
        GatewayType          : DynamicRouting
        LastEventTimeStamp   : 5/29/2015 4:41:41 PM
        LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
        LastEventID          : 23002
        State                : Provisioned
        VIPAddress           : 104.43.x.y
        DefaultSite          :
        GatewaySKU           : HighPerformance
        Location             :
        VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
        SubnetId             :
        EnableBgp            : False
        OperationDescription : Get-AzureVirtualNetworkGateway
        OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
        OperationStatus      : Succeeded

7. Crie um site local entidade de gateway VPN. Este comando não configurar o gateway VPN local. Em vez disso, ele permite que você forneça as configurações de gateway local, como o IP público e o local de endereço espaço, para que o gateway VPN Azure pode conectá-lo.

    >[AZURE.IMPORTANT] O site local para o VPN to-Site não está definido no netcfg. Em vez disso, você deve usar esse cmdlet para especificar os parâmetros de site local. Você não pode defini-lo usando o portal, ou o arquivo netcfg.

    Use o exemplo a seguir, substituindo os valores com seus próprios.

        New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>

    > [AZURE.NOTE] Se sua rede local tiver várias rotas, você pode passá-los em como uma matriz.  $MyLocalNetworkAddress =@("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")  


    Para recuperar as configurações de gateway de rede virtual, incluindo a identificação de gateway e o IP público, use o `Get-AzureVirtualNetworkGateway` cmdlet. Consulte o exemplo a seguir.

        Get-AzureLocalNetworkGateway

        GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
        GatewayName          : MyLocalNetwork
        IpAddress            : 23.39.x.y
        AddressSpace         : {10.1.2.0/24}
        OperationDescription : Get-AzureLocalNetworkGateway
        OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
        OperationStatus      : Succeeded


8. Configure seu dispositivo VPN local para se conectar ao novo gateway. Use as informações que você recuperou na etapa 6 ao configurar seu dispositivo VPN. Para obter mais informações sobre a configuração de dispositivo VPN, consulte [Configuração de dispositivo de VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

9. Link gateway-to-Site VPN no Azure para o gateway local.

    Neste exemplo, connectedEntityId é a identificação de gateway local, que você pode encontrar executando `Get-AzureLocalNetworkGateway`. Você pode encontrar virtualNetworkGatewayId usando o `Get-AzureVirtualNetworkGateway` cmdlet. Após essa etapa, a conexão entre sua rede local e o Azure via a conexão-to-Site VPN é estabelecida.


        New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>

## <a name="add"></a>Para configurar conexões de coexsiting para um VNet já existente

Se você tiver uma rede virtual existente, verifique o tamanho de sub-rede do gateway. Se a sub-rede de gateway é /28 ou /29, você deve primeiro excluir o gateway de rede virtual e aumentar o tamanho de sub-rede do gateway. As etapas desta seção mostrará como fazer isso.

Se a sub-rede de gateway for /27 ou maior e a rede virtual está conectada por meio de rota expressa, você pode ignorar as etapas abaixo e vá para ["Etapa 6 - criar um gateway - to-Site VPN"](#vpngw) na seção anterior.

>[AZURE.NOTE] Ao excluir o gateway existente, suas instalações locais perderá a conexão à sua rede virtual enquanto você estiver trabalhando nessa configuração.

1. Você precisará instalar a versão mais recente do cmdlets do PowerShell do Gerenciador de recursos do Azure. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell. Observe que os cmdlets que você usará para esta configuração pode ser um pouco diferentes do que você talvez esteja familiarizado. Certifique-se de usar os cmdlets especificados nestas instruções. 

2. Exclua o gateway rota expressa ou -to-Site VPN existente. Use o cmdlet a seguir, substituindo os valores com seus próprios.

        Remove-AzureVNetGateway –VnetName MyAzureVNET

3. Exporte o esquema de rede virtual. Use o seguinte cmdlet do PowerShell, substituindo os valores com seus próprios.

        Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”

4. Edite o esquema de arquivo de configuração de rede para que a sub-rede de gateway é /27 ou um prefixo mais curto (como /26 ou /25). Consulte o exemplo a seguir. 
>[AZURE.NOTE] Se você não tiver endereços IP suficientes em sua rede virtual para aumentar o tamanho de sub-rede gateway, você precisa adicionar mais espaço de endereço IP. Para obter mais informações sobre o esquema de configuração, consulte [esquema de configuração de rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>

5. Se seu gateway anterior era uma VPN to-Site, você também deve alterar o tipo de conexão para **dedicada**.

                 <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="MyLocalNetwork">
                      <Connection type="Dedicated" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>

6. Neste ponto, você terá uma VNet com nenhum gateways. Para criar novos gateways e concluir suas conexões, você pode prosseguir com a [etapa 4 - criar um gateway rota expressa](#gw), encontrado no conjunto anterior de etapas.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a rota expressa, consulte a [Rota expressa FAQ](expressroute-faqs.md)
