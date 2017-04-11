<properties
   pageTitle="Implementar uma arquitetura de rede híbrido altamente disponível | Microsoft Azure"
   description="Como implementar uma arquitetura de rede to-site seguro que abrange uma rede virtual Azure e uma rede local conectado usando rota expressa com failover de gateway VPN."
   services="guidance,virtual-network,vpn-gateway,expressroute"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-highly-available-hybrid-network-architecture"></a>Implementar uma arquitetura de rede híbrido altamente disponível

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve as práticas recomendadas para conexão a uma rede local com redes virtuais no Azure usando rota expressa, com uma-to-site rede virtual privada (VPN) como uma conexão de failover. O tráfego flui entre a rede local e uma rede virtual Azure (VNet) por meio de uma conexão de rota expressa.  Se houver uma perda de conectividade no circuito a rota expressa, o tráfego será roteado através de um túnel VPN IPSec.

> [AZURE.NOTE] Azure tem dois diferentes modelos de implantação: [Gerenciador de recursos] [ resource-manager-overview] e clássico. Este diagrama usa o Gerenciador de recursos, a Microsoft recomenda para novas implantações.

Casos de uso típico para essa arquitetura incluem:

- Aplicativos híbridos onde cargas de trabalho são distribuídas entre uma rede local e o Azure.

- Aplicativos em execução em grande escala essenciais cargas de trabalho que exigem um alto grau de escalabilidade.

- Instalações em grande escala de backup e restauração para dados que devem ser salvo offline.

- Manipulação de cargas de trabalho de grande volume.

- Usando o Azure como um site de recuperação.

Observe que, se o circuito de rota expressa não estiver disponível, a rota VPN somente tratará conexões aos particulares. Correspondência públicas e Microsoft correspondência conexões passará pela Internet.

## <a name="architecture-diagram"></a>Diagrama de arquitetura

>[AZURE.NOTE] O [serviço de Gateway do Azure VPN] [ azure-vpn-gateway] implementa dois tipos de gateways de rede virtual; Os gateways de rede virtual VPN e gateways de rede virtual rota expressa. Neste documento, o termo *Gateway VPN* se refere ao serviço Azure, enquanto as frases *gateway de rede virtual VPN* e *gateway de rede virtual rota expressa* são usadas para consultar as implementações VPN e rota expressa do gateway respectivamente.

O diagrama a seguir destaca os componentes importantes nesta arquitetura:

> Um documento do Visio que inclui este diagrama de arquitetura está disponível para download no [Centro de download da Microsoft][visio-download]. Este diagrama é na "rede híbrido - ER VPN" página.

![[0]][0]

- **Redes virtuais Azure (VNets).** Cada VNet reside em uma única região Azure e pode hospedar vários níveis do aplicativo. Níveis de aplicativos podem ser segmentadas usando sub-redes em cada VNet e/ou grupos de segurança (NSGs) de rede.

- **Rede corporativa no local.** Esta é uma rede de computadores e dispositivos, conectados por meio de uma rede local particular executando dentro de uma organização.

- **Dispositivo de VPN.** Este é um dispositivo ou serviço que fornece conectividade externa à rede local. O dispositivo de VPN pode ser um dispositivo de hardware ou pode ser uma solução de software como o roteamento e acesso remoto (RRAS) no Windows Server 2012.

> [AZURE.NOTE] Para obter uma lista de dispositivos de VPN com suporte e informações sobre como configurar dispositivos VPN selecionados para se conectar a um Gateway de VPN do Azure, consulte as instruções para o dispositivo apropriado na [lista de dispositivos VPN suportados pelo Azure][vpn-appliance].

- **Gateway de rede virtual VPN.** O gateway de rede virtual VPN permite que os VNet para se conectar ao dispositivo VPN na rede local. O gateway de rede virtual VPN está configurado para aceitar solicitações da rede local apenas através do aparelho de VPN. Para obter mais informações, consulte [conectar uma rede local para uma rede virtual do Microsoft Azure][connect-to-an-Azure-vnet].

- **Rota expressa gateway de rede virtual.** O gateway de rede virtual rota expressa habilita o VNet para se conectar ao circuito rota expressa usado para conectividade com a rede local.

- **Sub-rede do gateway.** Os gateways de rede virtual são mantidos na mesma sub-rede.

- **Conexão de VPN.** A conexão tem propriedades que especificam o tipo de conexão (IPSec) e a chave compartilhada com o aparelho de VPN local para criptografar o tráfego.

- **Rota expressa circuito.** Isso é uma camada 2 ou circuito de camada 3 fornecidos pelo provedor de conectividade rede junções o local com o Azure por meio de roteadores de borda. O circuito usa a infraestrutura de hardware gerenciada pelo provedor de conectividade.

- **Aplicativo de nuvem fileiras.** Este é o aplicativo hospedado no Azure. Ela pode incluir vários níveis, com várias sub-redes conectadas por meio de balanceadores de carga Azure. O tráfego em cada sub-rede sujeitas regras definidas usando [Grupos de segurança de rede do Azure][azure-network-security-group](NSGs). Para obter mais informações, consulte [Introdução ao Microsoft Azure segurança][getting-started-with-azure-security].

## <a name="recommendations"></a>Recomendações

Azure oferece muitos recursos diferentes e tipos de recurso, portanto, essa arquitetura de referência pode ser provisionado muitas maneiras diferentes. Fornecemos um modelo do Gerenciador de recursos do Azure para instalar a arquitetura de referência que segue essas recomendações. Se você optar por criar sua própria arquitetura de referência que você deve seguir essas recomendações, a menos que tenha um requisito específico que não correspondam a uma recomendação.

### <a name="vnet-and-gatewaysubnet"></a>VNet e GatewaySubnet

Crie o gateway de rede virtual rota expressa e o gateway de rede virtual VPN no mesmo VNet. Isso significa que eles devem compartilhar a mesma sub-rede denominada **GatewaySubnet**

Se o VNet já inclui uma sub-rede denominada **GatewaySubnet**, certifique-se de que ele tenha um /27 ou maior espaço de endereço. Se a sub-rede existente for muito pequena, removê-lo da seguinte maneira e crie um novo, como mostra o próximo marcador:

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
```

Se o VNet não contiver uma sub-rede denominada **GatewaySubnet**, crie um novo da seguinte maneira:

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.224/27"
$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="vpn-and-expressroute-gateways"></a>Gateways VPN e rota expressa

Verifique se sua organização atende os [requisitos de pré-requisito rota expressa] [ expressroute-prereq] para conexão com o Azure.

Se você já tiver um gateway de rede virtual VPN no seu VNet Azure, removê-lo, conforme mostrado abaixo.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
```

Siga as instruções em [implementar uma arquitetura de rede híbrida com rota expressa do Azure] [ implementing-expressroute] estabelecer sua conexão de rota expressa.

Siga as instruções em [implementar uma arquitetura de rede híbrida com o Azure e local VPN] [ implementing-vpn] estabelecer sua conexão de gateway VPN rede virtual.

Depois de ter estabelecido as conexões de gateway de rede virtual, teste o ambiente do seguinte:

1. Certificar-se de que você possa se conectar sua rede local para sua VNet do Azure.

2. Contate o provedor para parar de conectividade de rota expressa para teste.

3. Verifique se que você ainda pode conectar da sua rede local para sua VNet Azure usando a conexão de gateway VPN rede virtual.

4. Contate o provedor para reestabilish rota expressa conectividade.

## <a name="considerations"></a>Considerações

Para considerações de rota expressa, consulte a [implementar uma arquitetura de rede híbrida com rota expressa do Azure] [ guidance-expressroute] orientação.

Para considerações de-to-Site VPN, consulte a [implementar uma arquitetura de rede híbrida com o Azure e local VPN] [ guidance-vpn] orientação.

Para considerações de segurança do Azure gerais, consulte [segurança de rede e serviços de nuvem da Microsoft][best-practices-security].

## <a name="solution-deployment"></a>Implantação da solução

Se você tiver uma infraestrutura local existente já configurada com um dispositivo de rede adequados, você pode implantar a arquitetura de referência seguindo estas etapas:

1. Clique com botão direito no botão abaixo e selecione um dos "Abrir link em uma nova guia" ou "Abrir link em nova janela":  
[![Implantar para o Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn-er%2Fazuredeploy.json)

2. Aguarde o link para abrir no portal do Azure e, em seguida, siga estas etapas: 
  - O nome do **grupo de recursos** já está definido no arquivo de parâmetros, portanto selecione **Criar novo** e digite `ra-hybrid-vpn-er-rg` na caixa de texto.
  - Selecione a região na caixa suspensa **local** .
  - Não edite o **Modelo raiz Uri** ou as caixas de texto de **Uri de raiz de parâmetro** .
  - Examine os termos e condições, clique na caixa de seleção **para concordar com os termos e condições mencionadas acima** .
  - Clique no botão de **compra** .

3. Aguarde a implantação ser concluída.

4. Clique com botão direito no botão abaixo e selecione um dos "Abrir link em uma nova guia" ou "Abrir link em nova janela":  
[![Implantar para o Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn-er%2Fazuredeploy-expressRouteCircuit.json)

5. Aguarde até que o link para abrir no portal do Azure, e em seguida, digite e siga estas etapas: 
  - Selecione **Usar existente** na seção de **grupo de recursos** e insira `ra-hybrid-vpn-er-rg` na caixa de texto.
  - Selecione a região na caixa suspensa **local** .
  - Não edite o **Modelo raiz Uri** ou as caixas de texto de **Uri de raiz de parâmetro** .
  - Examine os termos e condições, clique na caixa de seleção **para concordar com os termos e condições mencionadas acima** .
  - Clique no botão de **compra** .

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[vpn-appliance]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[azure-vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[azure-network-security-group]: ../virtual-network/virtual-networks-nsg.md
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[expressroute-prereq]: ../expressroute/expressroute-prerequisites.md
[implementing-expressroute]: ./guidance-hybrid-network-expressroute.md#implementing-this-architecture
[implementing-vpn]: ./guidance-hybrid-network-vpn.md#implementing-this-architecture
[guidance-expressroute]: ./guidance-hybrid-network-expressroute.md
[guidance-vpn]: ./guidance-hybrid-network-vpn.md
[best-practices-security]: ../best-practices-network-security.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetwork.parameters.json
[virtualnetworkgateway-vpn-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetworkGateway-vpn.parameters.json
[virtualnetworkgateway-expressroute-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetworkGateway-expressRoute.parameters.json
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[naming conventions]: ./guidance-naming-conventions.md
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[0]: ./media/blueprints/hybrid-network-expressroute-vpn-failover.png "Arquitetura de uma arquitetura de rede híbrido altamente disponível usando gateway VPN e rota expressa"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
