<properties
   pageTitle="Implementar uma arquitetura de rede híbrida com rota expressa do Azure | Arquitetura de referência | Microsoft Azure"
   description="Como implementar uma arquitetura de rede to-site seguro que abrange uma rede virtual Azure e uma rede local conectado usando rota expressa do Azure."
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
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-hybrid-network-architecture-with-azure-expressroute"></a>Implementar uma arquitetura de rede híbrida com rota expressa do Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve as práticas recomendadas para conexão a uma rede local com redes virtuais no Azure usando rota expressa. Rota expressa conexões são feitas usando uma conexão dedicada particular através de um provedor de conectividade de terceiros. A conexão privada estende sua rede local para o Azure, fornecendo acesso a sua própria infraestrutura IaaS no Azure, públicos pontos de extremidade usados nos serviços de PaaS e serviços SaaS do Office 365. Este documento se concentra nos usando rota expressa para se conectar a uma única Azure rede virtual (VNet) usando o que é chamado de correspondência particular.

> [AZURE.NOTE] Azure tem dois diferentes modelos de implantação: [Gerenciador de recursos] [ resource-manager-overview] e clássico. Este diagrama usa o Gerenciador de recursos, a Microsoft recomenda para novas implantações.

Casos de uso típico para essa arquitetura incluem:

- Aplicativos híbridos onde cargas de trabalho são distribuídas entre uma rede local e o Azure.

- Aplicativos em execução em grande escala essenciais cargas de trabalho que exigem um alto grau de escalabilidade.

- Instalações em grande escala de backup e restauração para dados que devem ser salvo offline.

- Manipulação de cargas de trabalho de grande volume.

- Usando o Azure como um site de recuperação.

> [AZURE.NOTE] A [Visão geral técnica de rota expressa] [ expressroute-technical-overview] fornece uma introdução a rota expressa.

## <a name="architecture-diagram"></a>Diagrama de arquitetura

O diagrama a seguir destaca os componentes importantes nesta arquitetura:

> Um documento do Visio que inclui este diagrama de arquitetura está disponível para download no [Centro de download da Microsoft][visio-download]. Este diagrama é na página "Híbrido network - ER".

![[0]][0]

- **Redes virtuais Azure (VNets).** Cada VNet reside em uma única região Azure e pode hospedar vários níveis do aplicativo. Níveis de aplicativos podem ser segmentadas usando sub-redes em cada VNet e/ou grupos de segurança (NSGs) de rede. 

- **Serviços públicos Azure.** Esses são os serviços do Azure que podem ser utilizados em um aplicativo híbrido. Esses serviços também estão disponíveis na Internet pública, mas acessá-los por meio de um circuito de rota expressa oferece baixa latência e desempenho mais previsível, desde que o tráfego não vai através da Internet. Conexões são executadas usando **correspondência público**, com endereços que são pertencentes a sua organização ou fornecidos por seu provedor de conectividade. 

- **Serviços do Office 365.** Estes são os aplicativos do Office 365 disponíveis publicamente e serviços fornecidos pela Microsoft. Conexões são executadas usando o **Microsoft correspondência**, com endereços que são pertencentes a sua organização ou fornecidos por seu provedor de conectividade.

>[AZURE.NOTE] Você também pode conectar diretamente ao Microsoft CRM Online por meio de uma correspondência da Microsoft.

- **Rede corporativa no local.** Esta é uma rede de computadores e dispositivos, conectados por meio de uma rede local particular executando dentro de uma organização.

- **Roteadores de borda local.** Estas são roteadores que conectam da rede local para o circuito gerenciado pelo provedor. Dependendo de como sua conexão está provisionado, você precisa fornecer os endereços IP públicos usados pelos roteadores. 

- **Roteadores de borda da Microsoft.** Estes são os dois roteadores em uma configuração altamente disponível ativa. Esses roteadores habilitar um provedor de conectividade conectar seus circuitos diretamente ao data center. Dependendo de como sua conexão está provisionado, você precisa fornecer os endereços IP públicos usados pelos roteadores.

- **Rota expressa circuito.** Isso é uma camada 2 ou circuito de camada 3 fornecidos pelo provedor de conectividade rede junções o local com o Azure por meio de roteadores de borda. O circuito usa a infraestrutura de hardware gerenciada pelo provedor de conectividade.

- **Provedores de conectividade.** Estas são empresas que fornecem uma conexão usando camada 2 ou conectividade de camada 3 entre seu data center e um data center Azure.

## <a name="recommendations"></a>Recomendações

Azure oferece muitos recursos diferentes e tipos de recurso, portanto, essa arquitetura de referência pode ser provisionado muitas maneiras diferentes. Fornecemos um modelo do Gerenciador de recursos do Azure para instalar a arquitetura de referência que segue essas recomendações. Se você optar por criar sua própria arquitetura de referência que você deve seguir essas recomendações, a menos que tenha um requisito específico que não correspondam a uma recomendação.

### <a name="connectivity-providers"></a>Provedores de conectividade

Selecione um provedor de conectividade rota expressa adequado para seu local. Para obter uma lista de provedores de conectividade disponíveis em seu local, use o seguinte comando do PowerShell do Azure:

```powershell
Get-AzureRmExpressRouteServiceProvider
```

Provedores de conectividade de rota expressa conectam seu data center à Microsoft das seguintes maneiras:

- **Localizada no exchange nuvem.** Se você estiver localizada em um recurso com uma troca de nuvem, você pode solicitar conexões entre virtuais para a nuvem da Microsoft por meio do exchange de Ethernet do provedor localização conjunta. Provedores de localização conjunta podem oferecer entre-conexões de camada 2 ou gerenciado Layer 3 entre conexões entre sua infraestrutura no recurso localização conjunta e nuvem da Microsoft..

- **Conexões de Ethernet de ponto a ponto.** Você pode conectar sua data centers/escritórios locais na nuvem da Microsoft por meio de links de Ethernet de ponto a ponto. Provedores de Ethernet de ponto a ponto podem oferecer conexões de camada 2 ou gerenciado conexões de camada 3 entre seu site e de nuvem da Microsoft.

- **Para qualquer redes de (IPVPN).** Você pode integrar seu WAN com a nuvem da Microsoft. Provedores de IPVPN (normalmente MPLS VPN) oferecem a qualquer conectividade entre seus escritórios de ramificação e dos data centers. Nuvem da Microsoft pode ser interconectada para sua WAN para torná-lo como qualquer outra filial. Provedores de WAN normalmente oferecem conectividade de camada 3 gerenciada.

Para obter mais informações sobre os provedores de conectividade, consulte [rota expressa circuitos e domínios de roteamento][connectivity-providers].

### <a name="expressroute-circuit"></a>Rota expressa circuito

Certifique-se de que sua organização atendeu os [requisitos de pré-requisito rota expressa] [ expressroute-prereqs] para conexão com o Azure.

Se você ainda não fez isso, adicione uma sub-rede denominada `GatewaySubnet` à sua VNet do Azure e criar um gateway de rede virtual rota expressa usando o serviço de Gateway de VPN do Azure. Para obter mais informações sobre esse processo, consulte [fluxos de trabalho de rota expressa para provisionamento de circuito e estados de circuito][ExpressRoute-provisioning].

Crie um circuito de rota expressa da seguinte maneira:

1. Execute o seguinte comando do PowerShell:

    ```powershell
    New-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>> -Location <<location>> -SkuTier <<sku-tier>> -SkuFamily <<sku-family>> -ServiceProviderName <<service-provider-name>> -PeeringLocation <<peering-location>> -BandwidthInMbps <<bandwidth-in-mbps>>
    ```

2. Enviar o `ServiceKey` para o novo circuito para o provedor de serviço.

3. Aguarde o provedor provisionar o circuito. Você pode verificar o estado de provisionamento de um circuito usando o seguinte comando do PowerShell:

    ```powershell
    Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    ```

O `Provisioning state` campo o `Service Provider` seção da saída deixará de ser `NotProvisioned` para `Provisioned` quando o circuito está pronto.

>[AZURE.NOTE]Se você estiver usando uma conexão de camada 3, o provedor deve configurar e gerenciar o roteamento para você; Forneça as informações necessárias para ativar o provedor implementar as rotas apropriadas.

Se você estiver usando uma conexão de camada 2, siga as etapas abaixo:

1. Reservar dois/30 sub-redes composto de endereços IP públicos válidos para cada tipo de correspondência para o qual você deseja implementar. Esses/30 sub-redes serão usadas para fornecer endereços IP para roteadores usados para o circuito. Se você estiver implementando privado, público e Microsoft correspondência, você precisará sub-redes de 6 /30 com endereços IP públicos válidos.     

2. Configure o roteamento para o circuito rota expressa. Você precisa executar o comando abaixo para cada tipo de correspondência para o qual você deseja configurar (privado, público e Microsoft).

    >[AZURE.NOTE]Consulte [criar e modificar o roteamento para um circuito de rota expressa] [ configure-expressroute-routing] para obter detalhes. Use os seguintes comandos do PowerShell para adicionar uma correspondência para rotear o tráfego de rede:

    ```powershell
    Set-AzureRmExpressRouteCircuitPeeringConfig -Name <<peering-name>> -Circuit <<circuit-name>> -PeeringType <<peering-type>> -PeerASN <<peer-asn>> -PrimaryPeerAddressPrefix <<primary-peer-address-prefix>> -SecondaryPeerAddressPrefix <<secondary-peer-address-prefix>> -VlanId <<vlan-id>>

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit <<circuit-name>>
    ```

3. Reserve outro pool de endereços IP públicos válidos para uso para NAT para público e Microsoft correspondência. É recomendável ter um pool diferente para cada correspondência. Especifique o pool de seu provedor de conectividade, para que eles podem configurar anúncios de BGP para esses intervalos.

[Vincular seu VNet(s) particular na nuvem para o circuito rota expressa][link-vnet-to-expressroute]. Use os seguintes comandos do PowerShell:

```
$circuit = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
$gw = Get-AzureRmVirtualNetworkGateway -Name <<gateway-name>> -ResourceGroupName <<resource-group>>
New-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> -ResourceGroupName <<resource-group>> -Location <<location> -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

Observe os seguintes pontos:

- Rota expressa usa o protocolo de Gateway de borda (BGP) para trocar informações de roteamento entre a rede e o Azure.

- Você pode conectar vários VNets localizado em diferentes regiões para o mesmo circuito de rota expressa desde que todos os VNets e o circuito de rota expressa estão localizados dentro da mesma região geopolíticas.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

Rota expressa circuitos fornecem um caminho de largura de banda alta entre redes. Em geral, quanto maior a largura de banda maior custo. Embora alguns provedores permitem que você altere a largura de banda, verifique se que você escolher uma largura de banda inicial que ultrapassa às suas necessidades e fornece espaço para crescimento. No caso de precisar aumentar a largura de banda no futuro, são deixados com duas opções.

Aumente a largura de banda. Lembre-se de que nem todos os provedores permitem que você faça isso dinamicamente. E você deve evitar fazer isso tanto quanto possível. Mas, se necessário, depois de verificar com seu provedor, executar comandos a seguir.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
$ckt.ServiceProviderProperties.BandwidthInMbps = <<bandwidth-in-mbps>>
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Você pode aumentar a largura de banda sem perda de conectividade. Fazer downgrade a largura de banda resultará em interrupções na conectividade. Você precisa excluir o circuito e recriá-lo com a nova configuração.

Se você estiver usando o SKU padrão para rota expressa e precisa atualizar para o Premium ou alterar você está seus preços planejar (medida ou ilimitado), execute os comandos abaixo. O `Sku.Tier` propriedade pode ser `Standard` ou `Premium`; o `Sku.Name` propriedade pode ser `MeteredData` ou `UnlimitedData`.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>

$ckt.Sku.Tier = "Premium"
$ckt.Sku.Family = "MeteredData"
$ckt.Sku.Name = "Premium_MeteredData"

 Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

>[AZURE.IMPORTANT] Verifique se o `Sku.Name` correspondências de propriedade a `Sku.Tier` e `Sku.Family`. Se você alterar a família e camadas, mas não o nome, sua conexão será desabilitado.

Você pode atualizar o SKU sem interrupções, mas você não pode mudar o plano de preços ilimitado para limitadas. Quando fazer downgrade a SKU, seu consumo de largura de banda deve permanecer dentro do limite de padrão da SKU padrão.

> [AZURE.NOTE] Rota expressa oferece dois planos de preços para clientes, com base em dados de medição ou ilimitados. Consulte a [rota expressa preços] [ expressroute-pricing] para obter detalhes. Encargos variam de acordo com a largura de banda de circuito. Largura de banda disponível provavelmente variam de provedor para o provedor. Use o `Get-AzureRmExpressRouteServiceProvider` cmdlet para ver os provedores disponíveis na sua região e as larguras de banda que eles oferecem.

Um único circuito de rota expressa pode oferecer suporte a um número de peerings e links de VNet. Consulte [limites de rota expressa] [ expressroute-limits] para obter mais informações.

Para um custo adicional, rota expressa Premium complemento fornece:

- Até 10.000 rotas por circuito. Sem rota expressa Premium complemento, o limite é de 4.000 rotas por circuito.

- Conectividade global, permitindo que um circuito de rota expressa localizado em qualquer lugar no mundo para acessar recursos em qualquer região em vez de apenas regiões no mesmo continente.

- Um aumento no número de links de VNet por circuito de 10 para um limite maior, dependendo da largura de banda do circuito.

Rota expressa circuitos foram projetados para permitir picos de rede temporária até duas vezes o largura de banda limite que você obteve por nenhum custo adicional. Isso é feito usando links redundantes. No entanto, nem todos os provedores de conectividade suportam a esse recurso; Verifique se que o seu provedor de conectividade permite que esse recurso antes dependendo-lo.

## <a name="availability-considerations"></a>Considerações de disponibilidade

Você pode configurar alta disponibilidade para sua conexão Azure de maneiras diferentes, dependendo do tipo de provedor que você usa e o número de rota expressa circuitos e conexões de gateway de rede virtual, que você está disposto a configurar. Os pontos abaixo resumem as opções de disponibilidade:

- Rota expressa não suporta protocolos de redundância de roteador como HSRP e VRRP implementar alta disponibilidade. Em vez disso, ele usa um par redundante de sessões BGP por correspondência. Para facilitar conexões altamente disponível para sua rede, o Microsoft Azure provisiona duas portas redundantes em dois roteadores (parte da borda Microsoft) em uma configuração ativa.

- Se você estiver usando uma conexão de camada 2, implante roteadores redundantes na sua rede local em uma configuração ativa. Conecte o circuito principal para um roteador e o circuito secundário para a outra. Isso lhe dará uma conexão altamente disponível em ambas as extremidades da conexão. Isso é necessário se você solicitar o SLA rota expressa. Consulte [SLA rota expressa do Azure] [ sla-for-expressroute] para obter detalhes.

O diagrama a seguir mostra uma configuração com roteadores redundantes local conectada a circuitos primárias e secundários. Cada circuito manipula o tráfego para um público correspondência e uma correspondência particular (cada correspondência é designado um par de /30 endereço espaços, conforme descrito na seção anterior).

![[1]][1]

Se você estiver usando uma conexão de camada 3, verifique se ele oferece BGP redundantes sessões que lidar com disponibilidade para você.

Redes virtuais podem ser conectadas a vários rota expressa circuitos e cada circuitos podem ser fornecido por provedores de serviços diferente. Essa estratégia fornece alta disponibilidade adicional e desastres recursos de recuperação.

Configure uma VPN to-Site como um caminho de failover para rota expressa. Isso só é aplicável a correspondência particular. Para os serviços do Azure e o Office 365, a Internet é o caminho de failover somente.

Por padrão, sessões de BGP usam um valor de tempo limite ocioso de 60 segundos. Depois de uma sessão excedido 3 vezes, a rota está marcada como indisponível e todo o tráfego será redirecionado para o roteador restante. Esse tempo limite 180 segundo pode ser muito tempo para aplicativos críticos. Nesse caso, você pode alterar suas configurações de tempo limite BGP no roteador local para um valor menor.

## <a name="manageability-considerations"></a>Considerações de capacidade de gerenciamento

Você pode usar o [Kit de ferramentas do Azure conectividade (AzureCT)] [ azurect] para monitorar a conectividade entre o seu centro de dados local e o Azure.

## <a name="security-considerations"></a>Considerações de segurança

Você pode configurar opções de segurança para sua conexão Azure de maneiras diferentes, dependendo das suas necessidades de conformidade e preocupações com segurança. Os pontos abaixo resumem as opções de segurança.

Rota expressa opera na camada 3. Ameaças na camada de aplicativo podem ser impedidas usando um dispositivo de segurança de rede que restringe o tráfego recursos legítimos. Além disso, as conexões de rota expressa usando correspondência pública só poderá ser iniciadas do local. Isso impede que um serviço autorizados acessem e comprometam dados de locais da Internet pública.

Para maximizar a segurança, adicione os dispositivos de segurança de rede entre a rede local e os roteadores de borda de provedor. Isso ajudará a impedir que o fluxo de tráfego não autorizado a VNet:

![[2]][2]

Para fins de auditoria ou conformidade, talvez seja necessário proibir acesso direto de componentes em execução na VNet com a Internet e implementar [forçado túnel][forced-tuneling]. Nessa situação, o tráfego de Internet deve ser redirecionado volta através de um proxy executando o local onde ele pode ser auditado. O proxy pode ser configurado para bloquear o tráfego não autorizado fluindo check-out e filtrar mal-intencionados tráfego de entrada.

![[3]][3]

Por padrão, o Azure VMs expor pontos de extremidade usados para fornecer acesso de logon para fins de gerenciamento - RDP e Powershell remoto para Windows VMs e SSH para VMs baseadas em Linux quando implantado por meio do portal Azure. Para maximizar a segurança, não habilitar um endereço IP público para suas VMs e usar NSGs para garantir que essas VMs não estão acessíveis publicamente. VMs só devem estar disponíveis usando o endereço IP interno. Esses endereços podem ser feitos acessíveis através da rede de rota expressa, permitindo que a equipe de DevOps local executar qualquer configuração necessárias ou manutenção.

Se você deve expor pontos de extremidade de gerenciamento para VMs a uma rede externa, use NSGs e/ou listas de controle para restringir a visibilidade dessas portas a uma lista branca de endereços IP ou redes de acesso.

## <a name="troubleshooting-considerations"></a>Considerações de solução de problemas

Se um circuito rota expressa anteriormente funcionando agora falhar ao conectar-se de que, na ausência de qualquer configuração alterações locais ou dentro de sua VNet particular, talvez você precise contate o provedor de conectividade e trabalhar com eles para corrigir o problema. Você também pode usar os seguintes comandos do PowerShell do Azure para realizar alguma verificação limitada e ajudar a determinar onde os problemas podem estar:

- Verifique se que o circuito foi provisionado:

```powershell
Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

A saída desse comando mostra várias propriedades para sua circuito, incluindo `ProvisioningState`, `CircuitProvisioningState`, e `ServiceProviderProvisioningState` conforme mostrado abaixo.

```
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
```

Se o `ProvisioningState` não estiver definida para `Succeeded` depois que você tentou criar um novo circuito, remova o circuito usando o comando a seguir e tente criá-lo novamente.

```powershell
Remove-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

Se o seu provedor já tinha provisionado circuito e o `ProvisioningState` está definida como `Failed`, ou o `CircuitProvisioningState` não é `Enabled`, entre em contato com seu provedor para obter assistência.

## <a name="solution-deployment"></a>Implantação da solução

Se você tiver uma infraestrutura local existente já configurada com um dispositivo de rede adequados, você pode implantar a arquitetura de referência seguindo estas etapas:

Se você tiver uma infraestrutura local existente já configurada com um dispositivo de VPN, você pode implantar a arquitetura de referência seguindo estas etapas:

1. Clique com botão direito no botão abaixo e selecione um dos "Abrir link em uma nova guia" ou "Abrir link em nova janela":  
[![Implantar para o Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-er%2Fazuredeploy.json)

2. Aguarde o link para abrir no portal do Azure e, em seguida, siga estas etapas: 
  - O nome do **grupo de recursos** já está definido no arquivo de parâmetros, portanto selecione **Criar novo** e digite `ra-hybrid-er-rg` na caixa de texto.
  - Selecione a região na caixa suspensa **local** .
  - Não edite o **Modelo raiz Uri** ou as caixas de texto de **Uri de raiz de parâmetro** .
  - Examine os termos e condições, clique na caixa de seleção **para concordar com os termos e condições mencionadas acima** .
  - Clique no botão de **compra** .

3. Aguarde a implantação ser concluída.

4. Clique com botão direito no botão abaixo e selecione um dos "Abrir link em uma nova guia" ou "Abrir link em nova janela":  
[![Implantar para o Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-er%2Fazuredeploy-expressRouteCircuit.json)

5. Aguarde o link para abrir no portal do Azure e, em seguida, siga estas etapas: 
  - Selecione **Usar existente** na seção de **grupo de recursos** e insira `ra-hybrid-er-rg` na caixa de texto.
  - Selecione a região na caixa suspensa **local** .
  - Não edite o **Modelo raiz Uri** ou as caixas de texto de **Uri de raiz de parâmetro** .
  - Examine os termos e condições, clique na caixa de seleção **para concordar com os termos e condições mencionadas acima** .
  - Clique no botão de **compra** .

6. Aguarde a implantação ser concluída.

## <a name="next-steps"></a>Próximas etapas

- Consulte [Implementando uma arquitetura de rede altamente disponível híbrido] [ highly-available-network-architecture] para obter informações sobre como aumentar a disponibilidade de uma rede híbrida com base em rota expressa por deixar ao longo de uma conexão VPN.

<!-- links -->
[expressroute-technical-overview]: ../expressroute/expressroute-introduction.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[expressroute-prereqs]: ../expressroute/expressroute-prerequisites.md
[configure-expressroute-routing]: ../expressroute/expressroute-howto-routing-arm.md
[sla-for-expressroute]: https://azure.microsoft.com/support/legal/sla/expressroute/v1_0/
[link-vnet-to-expressroute]: ../expressroute/expressroute-howto-linkvnet-arm.md
[ExpressRoute-provisioning]: ../expressroute/expressroute-workflows.md
[expressroute-pricing]: https://azure.microsoft.com/pricing/details/expressroute/
[expressroute-limits]: ../azure-subscription-service-limits.md#networking-limits
[sample-script]: #sample-solution-script
[connectivity-providers]: ../expressroute/expressroute-introduction.md#how-can-i-connect-my-network-to-microsoft-using-expressroute
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[forced-tuneling]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[highly-available-network-architecture]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[arm-templates]: ../resource-group-authoring-templates.md
[naming-conventions]: ./guidance-naming-conventions.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/virtualNetwork.parameters.json
[virtualnetworkgateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/virtualNetworkGateway.parameters.json
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./media/guidance-hybrid-network-expressroute/figure1.png "Arquitetura de rede de híbrido usando rota expressa do Azure"
[1]: ./media/guidance-hybrid-network-expressroute/figure2.png "Usando roteadores redundantes com circuitos de rota expressa primárias e secundários"
[2]: ./media/guidance-hybrid-network-expressroute/figure3.png "Adicionando dispositivos de segurança à rede local"
[3]: ./media/guidance-hybrid-network-expressroute/figure4.png "Usando forçado túnel tráfego de Internet ligadas de auditoria"
[4]: ./media/guidance-hybrid-network-expressroute/figure5.png "Localizando o ServiceKey de circuito uma rota expressa"
