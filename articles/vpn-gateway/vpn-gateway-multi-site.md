<properties 
   pageTitle="Conectar uma rede virtual a vários sites usando Gateway VPN e PowerShell | Microsoft Azure"
   description="Este artigo apresentará conectando vários sites local locais a uma rede virtual usando um Gateway VPN para o modelo de implantação clássico."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="yushwang" />

# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Adicionar uma conexão de-to-Site a uma VNet com uma conexão de gateway VPN existente

> [AZURE.SELECTOR]
- [Gerenciador de recursos - Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
- [Clássico - PowerShell](vpn-gateway-multi-site.md)

Este artigo o orienta usando o PowerShell para adicionar conexões de-to-Site (S2S) para um gateway VPN que tem uma conexão existente. Esse tipo de conexão é conhecido como uma configuração de "vários locais". 

Este artigo se aplica ao redes virtuais criadas usando o modelo de implantação clássico (também conhecido como gerenciamento de serviço). Essas etapas não aplicar configurações de conexão passa rota expressa /-to-Site. Consulte [conexões passa rota expressa/S2S](../expressroute/expressroute-howto-coexist-classic.md) para obter informações sobre conexões passa.

### <a name="deployment-models-and-methods"></a>Métodos e modelos de implantação

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Podemos atualizar esta tabela como novos artigos e ferramentas adicionais se tornam disponíveis para esta configuração. Quando um artigo estiver disponível, podemos vincular diretamente a ele desta tabela.

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)] 



## <a name="about-connecting"></a>Sobre a conexão

Você pode conectar vários sites locais a uma única rede virtual. Isto é especialmente atraente para a criação de soluções de nuvem híbrida. Criar uma conexão de vários local para o gateway de rede virtual Azure é muito semelhante à criação de outras conexões to-Site. Na verdade, você pode usar um gateway VPN Azure existente, desde que o gateway está dinâmico (baseado em rota).

Se você já tiver um gateway estático conectado à sua rede virtual, você pode alterar o tipo de gateway para dinâmico sem precisar recriar a rede virtual para acomodar vários locais. Antes de alterar o tipo de roteamento, certifique-se de que o gateway VPN local oferece suporte a configurações de VPN baseadas em rota. 

![diagrama de vários local] (./media/vpn-gateway-multi-site/multisite.png "vários locais")

## <a name="points-to-consider"></a>Pontos a serem considerados

**Você não poderá usar o Portal de clássico do Azure para fazer alterações a esta rede virtual.** Nesta versão, você precisará fazer alterações no arquivo de configuração de rede em vez de usar o Portal de clássico do Azure. Se você fizer alterações no Portal de clássico do Azure, eles vai substituir as configurações de referência de vários locais para esta rede virtual. 

Você deve se sentir muito confortável usando o arquivo de configuração de rede no momento em que você concluiu o procedimento de vários local. No entanto, se você tiver várias pessoas trabalhando em sua configuração de rede, você precisará certificar-se de que todos sabem sobre essa limitação. Isso não significa que você não pode usar o Portal de clássico do Azure em todos os. Você pode usá-lo para tudo, exceto fazer alterações de configuração nesta rede virtual específica.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar a configuração, verifique se você tem o seguinte:

- Uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, você pode ativar seus [benefícios de assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou sinal para cima, para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Compatibilidade de hardware VPN para cada local local. Verificar [Sobre dispositivos de VPN para conectividade de rede Virtual](vpn-gateway-about-vpn-devices.md) para verificar se o dispositivo que você deseja usar é algo que é conhecido por ser compatível.

- Um externamente oposto público IPv4 endereço IP para cada dispositivo VPN. O endereço IP não pode ser localizado atrás de um NAT. Este é requisito.

- Você precisará instalar a versão mais recente do cmdlets do PowerShell do Azure. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.

- Alguém que esteja eficientes em Configurando o hardware VPN. Você não poderá usar os scripts VPN gerado automaticamente a partir do Portal de clássico do Azure para configurar seus dispositivos VPN. Isso significa que você precisa ter uma profunda compreensão de como configurar seu dispositivo VPN ou se trabalhar com alguém que faz.

- Os intervalos de endereços IP que você deseja usar para sua rede virtual (se você ainda não tiver criado um). 

- Os intervalos de endereços IP para cada um dos sites rede local que você vai ser se conectando. Você precisará certificar-se de que o endereço IP intervalos para cada um dos sites rede local que você deseja se conectar para não se sobreponham. Caso contrário, o Portal de clássico do Azure ou a API REST irá rejeitar a configuração está sendo carregada. 

    Por exemplo, se você tiver dois sites de rede local que contêm o IP endereço intervalo 10.2.3.0/24 e você tiver um pacote com um endereço de destino 10.2.3.3, Azure não sabe qual site que você deseja enviar o pacote porque os intervalos de endereços são sobrepostos. Para evitar problemas de roteamento, Azure não permite que você carregar um arquivo de configuração com intervalos sobrepostos.



## <a name="1-create-a-site-to-site-vpn"></a>1. criar uma VPN to-Site

Se você já tiver uma VPN to-Site com um gateway de roteamento dinâmico, excelente! Você pode continuar a [Exportar as definições de configuração de rede virtual](#export). Caso contrário, faça o seguinte:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Se você já tiver uma rede virtual-to-Site, mas ela tem um gateway roteamento estático (baseado em política):

1. Altere o tipo de gateway para roteamento dinâmico. Uma VPN site vários requer um gateway roteamento dinâmico (também conhecido como rota baseado). Para alterar o tipo de gateway, você precisará primeiro excluir o gateway existente, em seguida, crie um novo. Para obter instruções, consulte [como alterar o tipo de roteamento de VPN para seu gateway](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md#how-to-change-the-vpn-routing-type-for-your-gateway).  

2. Configurar seu novo gateway e crie sua túnel VPN. Para obter instruções, consulte [Configurar um Gateway VPN no Portal de clássico do Azure](vpn-gateway-configure-vpn-gateway-mp.md). Primeiro, altere o tipo de gateway para roteamento dinâmico. 

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Se você não tiver uma rede virtual-to-Site:

1. Criar sua rede virtual-to-Site seguindo estas instruções: [criar uma rede Virtual com uma Conexão de VPN - to-Site no Portal de clássico do Azure](vpn-gateway-site-to-site-create.md).  

2. Configurar um gateway de roteamento dinâmico usando estas instruções: [Configure um Gateway VPN](vpn-gateway-configure-vpn-gateway-mp.md). Certifique-se de selecionar o **roteamento dinâmico** do seu tipo de gateway.

## <a name="export"></a>2. exportar o arquivo de configuração de rede 

Exporte seu arquivo de configuração de rede. O arquivo exportado será usado para configurar as novas configurações de vários locais. Se precisar de instruções sobre como exportar um arquivo, consulte a seção no artigo: [como criar um VNet usando um arquivo de configuração de rede no Portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="3-open-the-network-configuration-file"></a>3. abrir o arquivo de configuração de rede

Abra o arquivo de configuração de rede que você baixou na última etapa. Use qualquer editor de xml que você goste. O arquivo deve ser semelhante ao seguinte:

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. adicionar várias referências de site

Quando você adiciona ou remove informações de referência do site, você vai faça alterações na configuração o ConnectionsToLocalNetwork/LocalNetworkSiteRef. Adicionando um novo gatilhos de referência de site local do Azure para criar um novo túnel. No exemplo abaixo, a configuração de rede é para uma conexão de site único. Salve o arquivo depois de terminar de fazer suas alterações.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

    To add additional site references (create a multi-site configuration), simply add additional "LocalNetworkSiteRef" lines, as shown in the example below: 

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

## <a name="5-import-the-network-configuration-file"></a>5. importar o arquivo de configuração de rede

Importe o arquivo de configuração de rede. Quando você importa esse arquivo com as alterações, os novos encapsulamentos serão adicionados. Os encapsulamentos usará o gateway dinâmico que você criou anteriormente. Se precisar de instruções sobre como importar o arquivo, consulte a seção no artigo: [como criar um VNet usando um arquivo de configuração de rede no Portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="6-download-keys"></a>6. baixar teclas

Depois de seu novo túneis tiverem sido adicionadas, use o cmdlet do PowerShell `Get-AzureVNetGatewayKey` para obter as chaves pré compartilhadas IKE/IPsec para cada túnel.

Por exemplo:

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

Se preferir, você também pode usar a API REST *Obter Virtual rede Gateway chave compartilhada* para obter as chaves pré compartilhadas.

## <a name="7-verify-your-connections"></a>7. Verifique as conexões

Verificar o status de túnel de vários locais. Depois de baixar as chaves para cada túnel, você desejará verificar as conexões. Use `Get-AzureVnetConnection` para obter uma lista dos encapsulamentos de rede virtual, conforme mostrado no exemplo abaixo. VNet1 é o nome da VNet.

    Get-AzureVnetConnection -VNetName VNET1
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Gateways VPN, consulte [Sobre Gateways de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

