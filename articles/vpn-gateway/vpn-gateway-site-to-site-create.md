<properties
   pageTitle="Criar uma rede virtual com uma conexão de Gateway VPN-to-site usando o portal de clássico Azure | Microsoft Azure"
   description="Criar um VNet com uma conexão de Gateway VPN de S2S para entre locais e híbridos configurações usando o modelo clássico de implantação."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-classic-portal"></a>Criar um VNet com uma conexão de-to-Site usando o portal de clássico Azure

> [AZURE.SELECTOR]
- [Gerenciador de recursos - Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Gerenciador de recursos - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Clássico - Portal clássico](vpn-gateway-site-to-site-create.md)

Este artigo explica a criação de uma rede virtual e uma conexão de gateway-to-site VPN à sua rede local usando o modelo de implantação clássico e o portal clássico. Conexões de-to-Site podem ser usadas para entre locais e híbridos configurações.

![Diagrama de-to-Site] (./media/vpn-gateway-site-to-site-create/site2site.png "to-site")


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos de implantação e métodos para conexões to-Site

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

A tabela a seguir mostra os métodos para configurações de Site para Site e modelos de implantação disponível no momento. Quando um artigo com etapas de configuração estiver disponível, podemos vincular diretamente a ele desta tabela.

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurações adicionais 

Se você quiser conectar VNets juntos, consulte [Configurar uma conexão de VNet para VNet para o modelo de implantação clássico](virtual-networks-configure-vnet-to-vnet-connection.md). Se você quiser adicionar uma conexão de-to-Site a uma VNet que já tenha uma conexão, consulte [Adicionar uma conexão de S2S para um VNet com uma conexão de gateway VPN existente](vpn-gateway-multi-site.md).
 
## <a name="before-you-begin"></a>Antes de começar

Verifique se você tem os seguintes itens antes de iniciar a configuração.

- Um dispositivo VPN compatível e alguém que esteja configurá-la. Consulte [sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se você não estiver familiarizado com a configuração de seu dispositivo VPN ou não estiver familiarizado com o endereço IP intervalos localizados na configuração de rede do seu local, você precisa coordenar com alguém que pode fornecer esses detalhes para você.

- Um externamente opostas público endereço IP para o seu dispositivo VPN. Este endereço IP não pode ser localizado atrás de um NAT.

- Uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, você pode ativar seus [benefícios de assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou sinal para cima, para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).


## <a name="CreateVNet"></a>Criar sua rede virtual

1. Faça logon no [portal de clássico Azure](https://manage.windowsazure.com/).

2. No canto inferior esquerdo da tela, clique em **novo**. No painel de navegação, clique em **Serviços de rede**e clique em **Rede Virtual**. Clique em **Criar personalizada** para iniciar o Assistente de configuração.

3. Para criar seu VNet, insira as configurações nas seguintes páginas:

## <a name="Details"></a>Página de detalhes de uma rede virtual

Insira as seguintes informações:

- **Nome**: o nome de sua rede virtual. Por exemplo, *EastUSVNet*. Você usará esse nome de rede virtual quando você implanta suas instâncias VMs e PaaS, para que você não deseja tornar o nome muito complicado.
- **Local**: O local está relacionado diretamente para o local físico (região) onde deseja que seus recursos (VMs) para residem. Por exemplo, se desejar que as VMs que você implantar a esta rede virtual a ser localizado física em *Leste EUA*, selecione esse local. Você não pode alterar a região associada a sua rede virtual depois de criá-lo.

## <a name="DNS"></a>Página de conectividade VPN e servidores DNS

Insira as seguintes informações e clique na seta próxima na parte inferior direita.

- **Servidores DNS**: digite o nome do servidor DNS e o endereço IP ou selecione um servidor DNS anteriormente registrado no menu de atalho. Essa configuração não cria um servidor DNS. Ele permite especificar os servidores DNS que você deseja usar para a resolução de nome para esta rede virtual.
- **Configurar-To-Site VPN**: marque a caixa de seleção para **Configurar uma VPN to-site**.
- **Rede local**: uma rede local representa sua localização física no local. Você pode selecionar uma rede local que você criou anteriormente, ou você pode criar uma nova rede local. Entretanto, se você selecionar usar uma rede local que você criou anteriormente, vá para a página de configuração de **Redes locais** e verificar se o endereço de IP de dispositivo de VPN (endereço público de IPv4 oposto) para o dispositivo VPN é preciso.

## <a name="Connectivity"></a>Página de conectividade to-site

Se você estiver criando uma nova rede local, você verá a página de **Conectividade To-Site** . Se você quiser usar uma rede local que você criou anteriormente, esta página não aparecerão no assistente e você pode mover em para a próxima seção.

Insira as seguintes informações e, em seguida, clique na seta próxima.

-   **Nome**: O nome que você deseja chamar local (local) site de rede.
-   **Endereço de IP do dispositivo de VPN**: opostas IPv4 no endereço público de seu dispositivo VPN no local que você usa para se conectar ao Azure. O dispositivo VPN não pode ser localizado atrás de um NAT.
-   **Espaço de endereço**: incluir iniciando IP e CIDR (contagem de endereço). Especifique o endereço range(s) que você deseja ser enviado por meio do gateway de rede virtual para seu local no local. Se um endereço IP de destino ficar dentro dos intervalos que você especificar aqui, ele é roteado através do gateway de rede virtual.
-   **Adicionar espaço de endereço**: se você tiver vários intervalos de endereços que você deseja ser enviado por meio do gateway de rede virtual, especifique cada intervalo de endereço adicional. Você pode adicionar ou remover intervalos mais tarde na página **Rede Local** .

## <a name="Address"></a>Página de espaços de endereço de rede virtual

Especifique o intervalo de endereços que você deseja usar para sua rede virtual. Esses são os endereços IP dinâmicos (queda) que serão atribuídos aos VMs e outras ocorrências de função que você implantar a esta rede virtual.

É especialmente importante selecionar um intervalo que não sobreponha com qualquer um dos intervalos que são usados para sua rede local. É necessário coordenar com seu administrador de rede. O administrador da rede pode ser necessário reservar um intervalo de endereços IP do seu espaço de endereço de rede local para ser usado para sua rede virtual.

Insira as seguintes informações e, em seguida, clique na marca de seleção na parte inferior direita para configurar sua rede.

- **Espaço de endereço**: incluir o início de IP e a contagem de endereço. Verifique se que os espaços de endereço que você especificar não se sobreponham qualquer um dos espaços de endereço de que você tem em sua rede local.
- **Adicionar sub-rede**: incluir IP inicial e a contagem de endereço. Sub-redes adicionais não são necessárias, mas talvez você queira criar uma sub-rede separada para VMs que terão queda estática. Ou talvez você queira ter suas VMs em uma sub-rede que é separada do seu outras instâncias de função.
- **Adicionar sub-rede de gateway**: clique para adicionar a sub-rede de gateway. A sub-rede de gateway é usada somente para o gateway de rede virtual e é necessária para essa configuração.

Clique na marca de seleção na parte inferior da página e sua rede virtual começará a criar. Quando terminar, você verá **que criado** listado em **Status** na página **redes** no Portal de clássico do Azure. Após a VNet tiver sido criado, você pode configurar o gateway de rede virtual.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="VNetGateway"></a>Configurar o gateway de rede virtual

Configure o gateway de rede virtual para criar uma conexão de-to-site seguro. Consulte [Configurar um gateway de rede virtual no portal de clássico do Azure](vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="next-steps"></a>Próximas etapas

Quando sua conexão for concluída, você pode adicionar máquinas virtuais às suas redes virtuais. Consulte a documentação de [máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/) para obter mais informações.
