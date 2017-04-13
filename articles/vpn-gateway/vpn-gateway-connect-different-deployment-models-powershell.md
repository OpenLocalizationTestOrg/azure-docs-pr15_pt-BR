<properties 
   pageTitle="Como conectar redes virtuais clássicas ao gerente de recursos redes virtuais usando o PowerShell | Microsoft Azure"
   description="Saiba como criar uma conexão VPN entre VNets e VNets Gerenciador de recursos usando o Gateway VPN e PowerShell clássico"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc" />

# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Conectar-se redes virtuais de diferentes modelos de implantação usando o PowerShell

> [AZURE.SELECTOR]
- [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

Azure atualmente tem dois modelos de gerenciamento: clássico e Gerenciador de recursos (RM). Se você tem usado o Azure por algum tempo, você provavelmente têm VMs do Azure e funções de instância em execução em um VNet clássico. Seu VMs mais recentes e instâncias de função podem estar executando em um VNet criado no Gerenciador de recursos. Este artigo explica a conexão clássico VNets VNets Gerenciador de recursos para permitir que os recursos localizados nos modelos de implantação separada para se comunicar com os outros através de uma conexão de gateway.

Você pode criar uma conexão entre VNets que estão em diferentes assinaturas e em diferentes regiões. Você também pode conectar VNets que já têm conexões com redes de locais, desde que o gateway que eles foram configurados com é dinâmico ou baseado em rota. Para obter mais informações sobre conexões de VNet para VNet, consulte as [perguntas Frequentes de VNet para VNet](#faq) no final deste artigo.

### <a name="deployment-models-and-methods-for-connecting-vnets-in-different-deployment-models"></a>Modelos de implantação e métodos de conexão VNets em diferentes modelos de implantação

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Podemos atualizar a tabela a seguir como novos artigos e ferramentas adicionais se tornam disponíveis para esta configuração. Quando um artigo estiver disponível, podemos vincular diretamente a ele da tabela.<br><br>

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>Correspondência de VNet

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]


## <a name="before-beginning"></a>Antes de começar

As etapas a seguir mostrarão as configurações necessárias para configurar um gateway dinâmico ou baseada em rota para cada VNet e criar uma conexão VPN entre os gateways. Essa configuração não oferece suporte a gateways estáticos ou baseado em políticas.

### <a name="prerequisites"></a>Pré-requisitos

 - Os dois VNets já foram criadas.
 - Os intervalos de endereços para o VNets não se sobrepõem uns com os outros, ou se sobrepõem com qualquer um dos intervalos para outras conexões que os gateways podem estar conectados ao.
 - Você instalou as últimas cmdlets do PowerShell (1.0.2 ou posterior). Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações. Certifique-se de que instalar o gerenciamento de serviço (Antoni) e os cmdlets do Gerenciador de recursos (RM). 

### <a name="exampleref"></a>Configurações de exemplo

Você pode usar as configurações de exemplo como uma referência.

**Configurações de VNet clássicas**

Nome de VNet = ClassicVNet <br>
Local = Oeste EUA <br>
Espaços de endereço de rede virtual = 10.0.0.0/24 <br>
Sub-rede-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nome da rede local = RMVNetLocal <br>
GatewayType = DynamicRouting

**Configurações do Gerenciador de recursos VNet**

Nome de VNet = RMVNet <br>
Grupo de recursos = RG1 <br>
Espaços de endereço IP de rede virtual = 192.168.0.0/16 <br>
Sub-rede-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Local = US Leste <br>
Nome do gateway público IP = gwpip <br>
Gateway de rede local = ClassicVNetLocal <br>
Nome do Gateway de rede virtual = RMGateway <br>
Configuração de endereçamento de IP do gateway = gwipconfig


## <a name="createsmgw"></a>Seção 1 - configurar o VNet clássico

### <a name="part-1---download-your-network-configuration-file"></a>Parte 1 - Download do arquivo de configuração de rede

1. Faça logon em sua conta do Azure no console do PowerShell com direitos elevados. O seguinte cmdlet solicita as credenciais de logon para sua conta do Azure. Após fazer o login, ele baixa suas configurações de conta para que fiquem disponíveis para o PowerShell do Azure. Você usará os cmdlets do PowerShell Antoni para concluir essa parte da configuração.

        Add-AzureAccount

2. Exporte seu arquivo de configuração de rede Azure executando o comando a seguir. Você pode alterar o local do arquivo para exportar para um local diferente, se necessário. Você editar o arquivo e, em seguida, importe-o para o Azure.

        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml

3. Abra o arquivo. XML que você baixou para editá-lo. Para obter um exemplo do arquivo de configuração de rede, consulte o [Esquema de configuração de rede](https://msdn.microsoft.com/library/jj157100.aspx).
 

### <a name="part-2--verify-the-gateway-subnet"></a>Parte 2 - Verifique a sub-rede de gateway

No elemento **VirtualNetworkSites** , adicione uma sub-rede de gateway para sua VNet se um já não foi criado. Ao trabalhar com o arquivo de configuração de rede, a sub-rede de gateway deve ser nomeada "GatewaySubnet" ou Azure não reconhece e usá-la como uma sub-rede de gateway.

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Exemplo:**
    
    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
       
### <a name="part-3---add-the-local-network-site"></a>Parte 3 - adicionar o site de rede local

O site de rede local que você adicionar representa o VNet RM ao qual você deseja se conectar. Você talvez precise adicionar um elemento de **LocalNetworkSites** para o arquivo se ainda não exista. Neste ponto na configuração, o VPNGatewayAddress pode ser qualquer endereço IP público válido, porque nós ainda não tiver criado gateway para VNet o Gerenciador de recursos. Assim que podemos criar o gateway, podemos substitua este endereço IP do espaço reservado para o endereço IP público correto que foi atribuído ao gateway RM.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="part-4---associate-the-vnet-with-the-local-network-site"></a>Parte 4 - associar o VNet com o site de rede local

Nesta seção, podemos especificar o site de rede local que você deseja conectar o VNet para. Nesse caso, é VNet o Gerenciador de recursos que você mencionado anteriormente. Verifique se os nomes correspondem. Esta etapa não criar um gateway. Especifica a rede local que o gateway será conectado ao.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="part-5---save-the-file-and-upload"></a>Parte 5 - salvar o arquivo e carregue

Salve o arquivo e, em seguida, importá-lo para o Azure executando o comando a seguir. Verifique se que você alterar o caminho do arquivo conforme necessário para seu ambiente.

        Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml

Você verá algo semelhante a esse resultado mostrando que a importação bem-sucedido.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="part-6---create-the-gateway"></a>Parte 6 - criar o gateway 

Você pode criar o gateway VNet usando o portal clássico ou usando o PowerShell.

Use o exemplo a seguir para criar um gateway de roteamento dinâmico:

    New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting

Você pode verificar o status do gateway usando o `Get-AzureVNetGateway` cmdlet.


## <a name="creatermgw"></a>Seção 2: Configurar o gateway RM VNet

Para criar um gateway VPN para o VNet RM, siga as instruções a seguir. Não inicie as etapas até após você ter recuperou o endereço IP público para o gateway do VNet clássico. 

1. **Faça logon em sua conta do Azure** no console do PowerShell. O seguinte cmdlet solicita as credenciais de logon para sua conta do Azure. Após fazer o login, suas configurações de conta são baixadas para que fiquem disponíveis para o PowerShell do Azure.

        Login-AzureRmAccount 

    Obter uma lista das suas assinaturas Azure se você tiver mais de uma assinatura.

        Get-AzureRmSubscription

    Especifica a assinatura que você deseja usar. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2.  **Criar um gateway de rede local**. Em uma rede virtual, o gateway de rede local geralmente se refere ao seu local de local. Nesse caso, o gateway de rede local se refere ao seu VNet clássico. Dê um nome pelo qual Azure pode se referir a ela e também especificar o prefixo de espaço de endereço. Azure usa o prefixo de endereço IP que você especificar para identificar o tráfego para enviar para seu local na. Se você precisar ajustar as informações aqui mais tarde, antes de criar o gateway, você pode modificar os valores e executar o exemplo novamente.<br><br>
    - `-Name`é o nome que você deseja atribuir consultem o gateway de rede local.<br>
    - `-AddressPrefix`é o espaço de endereço do seu VNet clássico.<br>
    - `-GatewayIpAddress`é o endereço IP público do gateway do VNet clássico. Certifique-se de alterar o exemplo a seguir para refletir o endereço IP correto.
    
            New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
            -Location "West US" -AddressPrefix "10.0.0.0/24" `
            -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1

3. **Solicitar um endereço IP público** a ser alocado para o gateway de rede virtual para VNet o Gerenciador de recursos. Você não pode especificar o endereço IP que você deseja usar. O endereço IP dinamicamente está alocado para o gateway de rede virtual. No entanto, isso não significa que o endereço IP será alterado. A única vez as alterações de endereço IP de gateway de rede virtual é quando o gateway é excluído e recriado. Ele não mudará em redimensionamento, redefinir ou outras manutenção/atualizações internas do gateway.<br>Nesta etapa, podemos também definir uma variável que é usada em uma etapa posterior.


        $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
        -ResourceGroupName RG1 -Location 'EastUS' `
        -AllocationMethod Dynamic

4. **Verificar se sua rede virtual tem uma sub-rede de gateway**. Se nenhuma sub-rede gateway existir, adicione uma. Certificar-se de que a sub-rede de gateway é chamada *GatewaySubnet*.

5. **Recuperar a sub-rede** usada para o gateway, execute o comando a seguir. Nesta etapa, podemos também definir uma variável a ser usado na próxima etapa.
    - `-Name`é o nome do seu VNet Gerenciador de recursos.
    - `-ResourceGroupName`é o grupo de recursos que o VNet está associado. A sub-rede de gateway já deve existir para esta VNet e deve ser nomeada *GatewaySubnet* funcione corretamente.


            $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
            -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1) 

6. **Criar a configuração de endereçamento de IP do gateway**. A configuração do gateway define a sub-rede e o endereço IP público para usar. Use o exemplo a seguir para criar a configuração do gateway.<br>Nesta etapa, o `-SubnetId` e `-PublicIpAddressId` parâmetros devem ser passados a propriedade id da sub-rede e objetos de endereço IP, respectivamente. Você não pode usar uma cadeia de caracteres simple. Essas variáveis são definidas na etapa para solicitar um IP público e a etapa para recuperar a sub-rede.

        $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
        -Name gwipconfig -SubnetId $subnet.id `
        -PublicIpAddressId $ipaddress.id
    
7. **Criar o gateway de rede virtual do Gerenciador de recursos** , execute o comando a seguir. O `-VpnType` deve ser *RouteBased*. Pode levar 45 minutos ou mais para ser concluída.

        New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
        -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
        -IpConfigurations $gwipconfig `
        -EnableBgp $false -VpnType RouteBased

8. **Copie o endereço IP público** uma vez o gateway VPN foi criado. Você usá-lo quando você configura as configurações de rede local para sua VNet clássico. Você pode usar o cmdlet a seguir para recuperar o endereço IP público. O endereço IP público está listado no retorno como *endereço IP*.

        Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1

## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>Seção 3: Modificar a rede local para o VNet clássico

Você pode executar esta etapa por exportar o arquivo de configuração de rede, editá-lo, salvar e, em seguida, importando o arquivo de volta ao Azure. Ou, você pode modificar essa configuração no portal do clássico. 

### <a name="to-modify-in-the-portal"></a>Modificar no portal

1. Abra o [portal clássico](https://manage.windowsazure.com).

2. No portal do clássico, role para baixo no lado esquerdo e clique em **redes**. Na página de **redes** , clique em **Redes locais** na parte superior da página. 

3. Na página **Redes locais** , clique para selecionar a rede local que você configurou na parte 1, vai para o fim da página e clique em **Editar**.

4. Na página **especificar os detalhes do seu local de rede** , substitua o endereço IP do espaço reservado para o endereço IP público para o gateway do Gerenciador de recursos que você criou na seção anterior. Clique na seta para mover para a próxima seção. Verifique se o **Espaço de endereço** estão corretas e clique na marca de seleção para aceitar as alterações.

### <a name="to-modify-using-the-network-configuration-file"></a>Para modificar usando o arquivo de configuração de rede

1. Exporte o arquivo de configuração de rede.
2. Em um editor de texto, modifique o endereço IP de gateway VPN.

        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>

3. Salvar suas alterações e importar o arquivo editado voltar ao Azure.


## <a name="connect"></a>Seção 4: Criar uma conexão entre os gateways

Criar uma conexão entre os gateways requer PowerShell. Talvez você precise adicionar sua conta do Azure para usar cmdlets do PowerShell clássico. Para fazer isso, você pode usar o seguinte cmdlet: 
        
    Add-AzureAccount

1. **Definir sua chave compartilhada** executando o comando a seguir. Neste exemplo, `-VNetName` é o nome da VNet clássico e `-LocalNetworkSiteName` é o nome que você especificou para a rede local quando você configurou-o no portal do clássico. O `-SharedKey` é um valor que você pode gerar e especificar. O valor especificado aqui deve ser o mesmo valor que você especificar na próxima etapa, quando você cria sua conexão. O retorno para este exemplo deve mostrar **Status: bem-sucedida**.

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

2. Crie a conexão VPN executando os seguintes comandos.

    **Defina as variáveis**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **Criar a conexão**<br> Observe que o `-ConnectionType` é IPsec, não Vnet2Vnet.
        
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

3. Você pode exibir sua conexão no portal, ou usando o `Get-AzureRmVirtualNetworkGatewayConnection` cmdlet.

        Get-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1

## <a name="faq"></a>Perguntas Frequentes de VNet para VNet

Exiba os detalhes de perguntas Frequentes para obter informações adicionais sobre conexões VNet para VNet.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


