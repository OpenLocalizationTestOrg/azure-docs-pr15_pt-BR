<properties
   pageTitle="Configurar uma rede Virtual e um Gateway para rota expressa no portal do clássico | Microsoft Azure"
   description="Este artigo explica a configuração de uma rede virtual para rota expressa usando o modelo de implantação clássico e o portal clássico."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/20/2016"
   ms.author="cherylmc"/>

# <a name="create-a-virtual-network-for-expressroute-in-the-classic-portal"></a>Criar uma rede Virtual para rota expressa no portal do clássico

As etapas neste artigo apresentará Configurando uma rede virtual e um gateway de rede virtual para uso com rota expressa usando o modelo de implantação clássico e o portal clássico.

Se você estiver procurando instruções para o modelo de implantação do Gerenciador de recursos, você pode usar os seguintes artigos: [criar uma rede virtual usando o PowerShell](../virtual-network/virtual-networks-create-vnet-arm-ps.md) e [Adicionar um Gateway VPN para VNet um gerente de recursos para rota expressa](expressroute-howto-add-gateway-resource-manager.md).

**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="create-a-classic-vnet-and-gateway"></a>Criar um VNet e um gateway clássico

As etapas a seguir criam um VNet clássico e um gateway de rede virtual. Se você já tiver uma VNet clássica, consulte a seção de [Configurar um VNet clássico existente](#config) neste artigo.

1. Faça logon no [portal de clássico Azure](http://manage.windowsazure.com).

2. No canto inferior esquerdo da tela, clique em **novo**. No painel de navegação, clique em **Serviços de rede**e clique em **Rede Virtual**. Clique em **Criar personalizada** para iniciar o Assistente de configuração.

3. Na página **Detalhes de rede Virtual** , insira o seguinte:

    - **Nome** – nome da sua rede virtual. Você usará esse nome de rede virtual quando você implanta suas instâncias VMs e PaaS, para que você não deseja tornar o nome muito complicado.
    - **Local** – o local está relacionado diretamente para o local físico (região) onde deseja que seus recursos (VMs) para residem. Por exemplo, se desejar que as VMs que você implantar a esta rede virtual a ser localizado física em Leste EUA, selecione esse local. Você não pode alterar a região associada a sua rede virtual depois de criá-lo.

4. Na página **DNS Servers e a conectividade VPN** , insira as seguintes informações e clique na seta próxima na parte inferior direita. 

    - **Servidores DNS** - insira o nome do servidor DNS e o endereço IP ou selecione um servidor DNS anteriormente registrado no menu de atalho. Essa configuração não cria um servidor DNS. Ele permite especificar os servidores DNS que você deseja usar para a resolução de nome para esta rede virtual.
    - **Conectividade de-to-Site** - selecionar a caixa de seleção para **Configurar uma VPN to-site**.
    - **Rota expressa** – marque a caixa de seleção **Rota expressa de uso**. Essa opção só aparece se você selecionou **Configurar uma VPN to-Site**.
    - **Rede local** - é necessário ter um site de rede local para rota expressa. No entanto, no caso de uma conexão de rota expressa, os prefixos de endereço especificados para o site de rede local serão ignorados. Em vez disso, os prefixos de endereço anunciados à Microsoft por meio de circuito a rota expressa serão usados para fins de roteamento.<BR>Se você já tiver uma rede local criada para sua conexão de rota expressa, você pode selecioná-la na lista suspensa. Caso contrário, selecione **especificar uma nova rede Local**.

5. A página de **conectividade to-Site** aparece se você selecionou para especificar uma nova rede local na etapa anterior. Para configurar sua rede local, insira as seguintes informações e, em seguida, clique na seta próxima. 

    - Site de rede de **nome** - o nome que você deseja chamar local (local).
    - **Espaço de endereço** , incluindo IP inicial e CIDR (contagem de endereço). Você pode especificar qualquer intervalo de endereços, desde que ele não sobreponha com o intervalo de endereços para sua rede virtual. Normalmente, isso seria especificar os intervalos de endereços para suas redes locais, mas no caso de rota expressa, essas configurações não são usadas. No entanto, essa configuração é necessária para criar a rede local quando você estiver usando o portal de clássico.
    - **Adicionar espaço de endereço** - essa configuração não é relevante para rota expressa.


6. Na página de **Espaços de endereço de rede Virtual** , insira as seguintes informações e clique na marca de seleção na parte inferior direita para configurar sua rede. 

    - **Espaço de endereço** , incluindo a contagem IP e o endereço inicial. Verifique se que os espaços de endereço que você especificar não se sobreponham qualquer um dos espaços de endereço de que você tem em sua rede local.
    - **Adicionar sub-rede** - incluindo IP inicial e a contagem de endereço. Sub-redes adicionais não são necessárias.
    - **Adicionar sub-rede de gateway** - clique para adicionar a sub-rede de gateway. A sub-rede de gateway é usada somente para o gateway de rede virtual e é necessária para essa configuração.<BR>A sub-rede de gateway CIDR (contagem de endereço) para rota expressa deve ser /28 ou maior (/ 27, / 26 etc.). Isso permite endereços IP suficientes nessa sub-rede para permitir a configuração trabalhar. No portal do clássico, se você tiver selecionado a caixa de seleção Usar rota expressa, o portal Especifica uma sub-rede de gateway com /28.  Você não pode ajustar a contagem de endereço CIDR no portal do clássico. A sub-rede de gateway aparecerá como **Gateway** no portal do clássico, embora o nome real da sub-rede gateway criado é realmente **GatewaySubnet**. Você pode exibir esse nome usando o PowerShell ou no portal do Azure.

7. Clique na marca de seleção na parte inferior da página e sua rede virtual começará a criar. Quando terminar, você verá **que criado** listado em **Status** na página **redes** no portal do clássico.

## <a name="gw"></a>Criar o gateway

1. Na página de **redes** , clique na rede virtual que você acabou de criar, clique em **Painel de controle** na parte superior da página.

2. Na parte inferior da página de **painel** , clique em **Criar Gateway** e selecione **Roteamento dinâmico**. Clique em **Sim** para confirmar que você deseja criar um gateway.

3. Quando o gateway inicia a criação, você verá uma mensagem opção que permite que você sabe que o gateway foi iniciado. Ele pode levar até 45 minutos para o gateway criar.

4. Vincule sua rede para um circuito. Siga as instruções no artigo [como vincular VNets a rota expressa circuitos](expressroute-howto-linkvnet-classic.md).

## <a name="config"></a>Configurar um VNet clássico existente para rota expressa

Se você já tiver um VNet clássico, você pode configurá-lo para conectar-se a rota expressa no portal do clássico. As configurações são a mesma que as seções acima, portanto leia essas seções para se familiarizar com as configurações necessárias. Se você quiser criar uma conexão passa rota expressa /-to-Site, consulte [Este artigo](expressroute-howto-coexist-classic.md) para ver as etapas. Eles são diferentes as etapas neste artigo.
 
1. Você precisa criar rede local antes de atualizar o restante das suas configurações de VNet. Para criar uma nova rede local, que é necessária durante a configuração rota expressa por meio do portal clássico, clique em **novo** **>** **Serviços de rede** **>** **Rede Virtual** **>** **rede local de adicionar**. Siga as etapas do Assistente para criar a rede local.

2. Use **Configurar** página para atualizar o restante das configurações de sua VNet e associar o VNet à rede local.

3. Depois de configurar as configurações, vá para a seção de [criar o gateway](#gw) deste artigo para criar o gateway.


## <a name="next-steps"></a>Próximas etapas

- Se você quiser adicionar máquinas virtuais à sua rede virtual, consulte [caminhos de aprendizado de máquinas virtuais](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/).
- Se você quiser saber mais sobre rota expressa, consulte [Visão geral de rota expressa](expressroute-introduction.md).


 
