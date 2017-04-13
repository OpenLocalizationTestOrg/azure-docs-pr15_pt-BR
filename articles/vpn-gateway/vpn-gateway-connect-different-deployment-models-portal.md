<properties 
   pageTitle="Como conectar redes virtuais clássicas ao Gerenciador de recursos de redes virtuais no portal do | Microsoft Azure"
   description="Saiba como criar uma conexão VPN entre clássico VNets e VNets Gerenciador de recursos usando o Gateway VPN e o portal"
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
   ms.date="10/03/2016"
   ms.author="cherylmc" />

# <a name="connect-virtual-networks-from-different-deployment-models-in-the-portal"></a>Conectar-se redes virtuais de diferentes modelos de implantação no portal

> [AZURE.SELECTOR]
- [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)


Azure atualmente tem dois modelos de gerenciamento: clássico e Gerenciador de recursos (RM). Se você tem usado o Azure por algum tempo, você provavelmente têm VMs do Azure e funções de instância em execução em um VNet clássico. Seu VMs mais recentes e instâncias de função podem estar executando em um VNet criado no Gerenciador de recursos. Este artigo explica a conexão clássico VNets VNets Gerenciador de recursos para permitir que os recursos localizados nos modelos de implantação separada para se comunicar com os outros através de uma conexão de gateway. 

Você pode criar uma conexão entre VNets que estão em diferentes assinaturas e em diferentes regiões. Você também pode conectar VNets que já têm conexões com redes de locais, desde que o gateway que eles foram configurados com é dinâmico ou baseado em rota. Para obter mais informações sobre conexões de VNet para VNet, consulte as [perguntas Frequentes de VNet para VNet](#faq) no final deste artigo.

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implantação e métodos para conexões de VNet para VNet

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Podemos atualizar a tabela a seguir como novos artigos e ferramentas adicionais se tornam disponíveis para esta configuração. Quando um artigo estiver disponível, podemos vincular diretamente a ele da tabela.<br><br>

**VNet para VNet**

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>Correspondência de VNet

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="before-beginning"></a>Antes de começar

As etapas a seguir mostrarão as configurações necessárias para configurar um gateway dinâmico ou baseada em rota para cada VNet e criar uma conexão VPN entre os gateways. Essa configuração não oferece suporte a gateways estáticos ou baseado em políticas. 

Neste artigo, usamos o portal clássico, o portal do Azure e PowerShell. Atualmente, não é possível criar esta configuração usando somente o portal do Azure.

### <a name="prerequisites"></a>Pré-requisitos

 - Os dois VNets já foram criadas.
 - Os intervalos de endereços para o VNets não se sobrepõem uns com os outros, ou se sobrepõem com qualquer um dos intervalos para outras conexões que os gateways podem estar conectados ao.
 - Você instalou as últimas cmdlets do PowerShell (1.0.2 ou posterior). Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações. Certifique-se de que instalar o gerenciamento de serviço (Antoni) e os cmdlets do Gerenciador de recursos (RM). 

### <a name="values"></a>Configurações de exemplo

Você pode usar as configurações de exemplo como referência.

**Configurações de VNet clássicas**

Nome de VNet = ClassicVNet <br>
Local = Oeste EUA <br>
Espaços de endereço de rede virtual = 10.0.0.0/24 <br>
Sub-rede-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nome da rede local = RMVNetLocal <br>

**Configurações do Gerenciador de recursos VNet**

Nome de VNet = RMVNet <br>
Grupo de recursos = RG1 <br>
Espaços de endereço IP de rede virtual = 192.168.0.0/16 <br>
Sub-rede-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Local = US Leste <br>
Nome do gateway de rede virtual = RMGateway <br>
Nome do gateway público IP = gwpip <br>
Tipo de gateway = VPN <br>
Tipo de VPN = baseado em roteiro <br>
Gateway de rede local = ClassicVNetLocal <br>

## <a name="createsmgw"></a>Seção 1: Configurar definições de VNet clássicas

Nesta seção, podemos criar rede local e o gateway para sua VNet clássico. As instruções nesta seção usam o portal clássico. Atualmente, o portal do Azure não oferece todas as configurações que pertencem a um VNet clássico.

### <a name="part-1---create-a-new-local-network"></a>Parte 1 - criar uma nova rede local

Abra o [portal clássico](https://manage.windowsazure.com) e entre com sua conta do Azure.

1. No canto inferior esquerdo da tela, clique em **novo** > **Serviços de rede** > **Rede Virtual** > **rede local de adicionar**.

2. Na janela de **especificar os detalhes da rede local** , digite um nome para o VNet RM que você deseja se conectar. Na caixa **endereço IP de dispositivo VPN (opcional)** , digite qualquer endereço IP público válido. Isso é apenas um espaço reservado temporário. Você pode alterar esse endereço IP posteriormente. No canto inferior direito da janela, clique no botão de seta.
 
3. Na página **especificar o espaço de endereço** , na caixa de texto **IP inicial** , digite o prefixo de rede e bloco CIDR para o VNet Gerenciador de recursos que você deseja se conectar. Esta configuração é usada para especificar o espaço de endereço para rotear para o VNet RM.

### <a name="part-2---associate-the-local-network-to-your-vnet"></a>Parte 2 - associar da rede local para seu VNet

1. Clique em **Redes virtuais** na parte superior da página para alternar para a tela de redes virtuais, clique para selecionar seu VNet clássico. Na página de sua VNet, clique em **Configurar** para navegar até a página de configuração.

2. Na seção de conexão **- to-site conectividade** , selecione a caixa de seleção **conectar à rede local** . Selecione a rede local que você criou. Se você tiver várias redes locais que você criou, certifique-se de selecionar o que você criou para representar VNet seu gerente de recursos na lista suspensa.

3. Clique em **Salvar** na parte inferior da página.

### <a name="part-3---create-the-gateway"></a>Parte 3 - criar o gateway

1. Depois de salvar as configurações, clique em **Painel de controle** na parte superior da página para alterar para a página de painel. Na parte inferior da página de painel, clique em **Criar Gateway**, clique em **Roteamento dinâmico**. Clique em **Sim** para começar a criar seu gateway. Um gateway roteamento dinâmico é necessário para essa configuração.

2. Aguarde até que o gateway a ser criado. Às vezes, isso pode levar 45 minutos ou mais para concluir.

### <a name="ip"></a>Parte 4 - exibir o endereço IP público do gateway

Depois que o gateway foi criado, você pode exibir o endereço IP do gateway na página do **painel** . Este é o endereço IP público do seu gateway. Anote ou copie o endereço IP público. Você usá-lo em etapas posteriores ao criar a rede local para a sua configuração de VNet Gerenciador de recursos.


## <a name="creatermgw"></a>Seção 2: Configurar definições de VNet Gerenciador de recursos

Nesta seção, podemos criar o gateway de rede virtual e da rede local para sua VNet Gerenciador de recursos. Não inicie as etapas a seguir até após você ter recuperou o endereço IP público para o gateway do VNet clássico.

As capturas de tela são fornecidas como exemplos. Certifique-se de que substitua os valores por suas próprias. Se você estiver criando esta configuração como um exercício, consulte estes [valores](#values).


### <a name="part-1---create-a-gateway-subnet"></a>Parte 1 - criar uma sub-rede de gateway

Antes de conectar sua rede virtual para um gateway, primeiro é necessário criar a sub-rede de gateway para a rede virtual à qual você deseja se conectar. Criar uma sub-rede de gateway com contagem CIDR de /28 ou maior (/ 27, / 26, etc.)

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

Em um navegador, navegue até o [portal do Azure](http://portal.azure.com) e entre com sua conta do Azure.

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>Parte 2 - criar um gateway de rede virtual


[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


### <a name="part-3---create-a-local-network-gateway"></a>Parte 3 - criar um gateway de rede local

O gateway de rede local' ' geralmente se refere ao seu local de local. Ele informa Azure qual endereço IP varia a rota para o local e o endereço IP público do dispositivo para esse local. No entanto, nesse caso, ele se refere ao intervalo de endereços e endereço IP público associado ao seu VNet clássico e o gateway de rede virtual.

Nomeie o gateway de rede local pelo qual o Azure pode se referir a ela. Você pode criar seu gateway de rede local enquanto seu gateway de rede virtual está sendo criada. Para essa configuração, você deve usar o endereço IP público que foi atribuído ao seu gateway VNet clássico na [seção anterior](#ip).

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


### <a name="part-4---copy-the-public-ip-address"></a>Parte 4 - copie o endereço IP público

Depois que o gateway de rede virtual terminar a criação, copie o endereço IP público que está associado com o gateway. Você usá-lo quando você configura as configurações de rede local para sua VNet clássico. 


## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>Seção 3: Modificar a rede local para o VNet clássico

Abra o [portal clássico](https://manage.windowsazure.com).

2. No portal do clássico, role para baixo no lado esquerdo e clique em **redes**. Na página de **redes** , clique em **Redes locais** na parte superior da página. 

3. Clique para selecionar a rede local que você configurou na parte 1. Na parte inferior da página, clique em **Editar**.

4. Na página **especificar os detalhes do seu local de rede** , substitua o endereço IP do espaço reservado para o endereço IP público para o gateway do Gerenciador de recursos que você criou na seção anterior. Clique na seta para mover para a próxima seção. Verifique se o **Espaço de endereço** estão corretas e clique na marca de seleção para aceitar as alterações.

## <a name="connect"></a>Seção 4: Criar a conexão

Nesta seção, podemos criar a conexão entre o VNets. As etapas para isso exigem PowerShell. Você não pode criar esta conexão em ambos os portais. Verifique se você baixou e instalou clássico (Antoni) tanto o cmdlets do PowerShell do Gerenciador de recursos (RM).


1. Faça logon em sua conta do Azure no console do PowerShell. O seguinte cmdlet solicita as credenciais de logon para sua conta do Azure. Após fazer o login, suas configurações de conta são baixadas para que fiquem disponíveis para o PowerShell do Azure.

        Login-AzureRmAccount 

    Obter uma lista das suas assinaturas Azure se você tiver mais de uma assinatura.

        Get-AzureRmSubscription

    Especifica a assinatura que você deseja usar. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2. Adicione sua conta do Azure para usar cmdlets do PowerShell clássico. Para fazer isso, você pode usar o seguinte comando:

        Add-AzureAccount

3. Defina a chave compartilhada executando o exemplo a seguir. Neste exemplo, `-VNetName` é o nome da VNet clássico e `-LocalNetworkSiteName` é o nome que você especificou para a rede local quando você configurou-o no portal do clássico. O `-SharedKey` é um valor que você pode gerar e especificar. O valor especificado aqui deve ser o mesmo valor que você especificar na próxima etapa, quando você cria sua conexão.

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

4. Crie a conexão VPN executando os seguintes comandos:
    
    **Defina as variáveis**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **Criar a conexão**<br> Observe que o `-ConnectionType` é 'IPsec', não 'Vnet2Vnet'. Neste exemplo, `-Name` é o nome que você deseja chamar sua conexão. O exemplo a seguir cria uma conexão denominada '*rm para clássico conexão*'.
        
        New-AzureRmVirtualNetworkGatewayConnection -Name rm-to-classic-connection -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="verify-your-connection"></a>Verifique sua conexão

Você pode verificar sua conexão usando o portal de clássico, o portal do Azure ou PowerShell. Você pode usar as seguintes etapas para verificar sua conexão. Substitua os valores por suas próprias.

[AZURE.INCLUDE [vpn-gateway-verify connection](../../includes/vpn-gateway-verify-connection-rm-include.md)] 

## <a name="faq"></a>Perguntas Frequentes de VNet para VNet

Exiba os detalhes de perguntas Frequentes para obter informações adicionais sobre conexões VNet para VNet.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


