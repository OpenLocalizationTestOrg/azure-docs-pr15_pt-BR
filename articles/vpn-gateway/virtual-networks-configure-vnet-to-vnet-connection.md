<properties
   pageTitle="Configurar uma conexão de VNet para VNet para o modelo clássico de implantação | Microsoft Azure"
   description="Como conectar redes virtuais Azure em conjunto usando o PowerShell e o portal de clássico Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>


# <a name="configure-a-vnet-to-vnet-connection-for-the-classic-deployment-model"></a>Configurar uma conexão de VNet para VNet para o modelo clássico de implantação

> [AZURE.SELECTOR]
- [Gerenciador de recursos - Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Gerenciador de recursos - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Clássico - Portal clássico](virtual-networks-configure-vnet-to-vnet-connection.md)



Este artigo conduz você pelas etapas para criar e conectar redes virtuais em conjunto usando o modelo de implantação clássico (também conhecido como gerenciamento de serviço). As etapas a seguir usam portal clássico do Azure para criar a VNets e gateways e PowerShell para configurar a conexão de VNet para VNet. Você não pode configurar a conexão no portal.

![VNet ao diagrama de conectividade de VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/v2vclassic.png)


### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implantação e métodos para conexões de VNet para VNet

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela a seguir mostra os métodos para VNet para VNet configurações e modelos de implantação disponível no momento. Quando um artigo com etapas de configuração estiver disponível, podemos vincular diretamente a ele desta tabela.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Sobre conexões de VNet para VNet

Conectando a uma rede virtual para outra rede virtual (VNet-para-VNet) é semelhante ao se conectar a uma rede virtual a um site local na. Os dois tipos de conectividade usam um gateway VPN para fornecer um túnel seguro usando IPsec/IKE. 

O VNets que você se conecta podem estar em diferentes regiões e assinaturas diferentes. Você pode combinar VNet para VNet a comunicação com configurações de vários locais. Isso permite estabelecer topologias de rede que combinam a conectividade entre locais com conectividade de rede entre virtual.


### <a name="why-connect-virtual-networks"></a>Por que conectar redes virtuais?

Talvez você queira conectar redes virtuais pelos seguintes motivos:

- **Cross redundância região geográfica e localização geográfica-presença**
    - Você pode configurar sua própria replicação geográfica ou a sincronização com conectividade segura sem estourando pontos de extremidade de voltado para a Internet.
    - Com o balanceador de carga do Azure e Microsoft ou tecnologia de cluster de terceiros, você pode configurar a carga de trabalho altamente disponível com redundância geográfica em várias regiões Azure. Um exemplo importante é configurar um SQL sempre com grupos de disponibilidade ampliando entre várias regiões Azure.

- **Aplicativos de várias camadas regionais com o limite de isolamento forte**
    - Dentro da mesma região, você pode configurar aplicativos de vários níveis com vários VNets conectado junto com forte isolamento e comunicação entre camadas segura.

- **Cross assinatura, comunicação entre organizações no Azure**
    - Se você tiver várias assinaturas Azure, você pode conectar cargas de trabalho de assinaturas diferentes juntos com segurança entre redes virtuais.
    - Para empresas ou provedores de serviço, você pode habilitar a comunicação entre organizações com tecnologia VPN segura dentro do Azure.

### <a name="vnet-to-vnet-faq-for-classic-vnets"></a>Perguntas Frequentes de VNet para VNet para VNets clássico

- As redes virtuais podem estar nas assinaturas iguais ou diferentes.

- As redes virtuais podem estar nas regiões Azure iguais ou diferentes (locais).

- Um serviço de nuvem ou um ponto de extremidade de balanceamento de carga não é possível abranger em redes virtuais, mesmo se eles estão conectados.

- Conectar várias redes virtuais juntos não exige qualquer dispositivos VPN.

- VNet-VNet suporta conectar redes Virtual do Azure. Ele não oferece suporte a conexão máquinas virtuais ou serviços que não são implantados em uma rede virtual em nuvem.

- VNet para VNet requer gateways roteamento dinâmicos. Não há suporte para o Azure gateways roteamento estáticos.

- Conectividade de rede virtual pode ser usada simultaneamente com VPNs de vários locais. Há um máximo de 10 túneis VPN para um gateway VPN rede virtual se conectar a outras redes virtuais ou sites de local.

- Os espaços de endereço dos sites de rede local redes e local virtuais não devem ser sobrepostos. Espaços de endereço sobrepostos fará com que a criação de redes virtuais ou carregar arquivos de configuração de netcfg falha.

- Túneis redundantes entre um par de redes virtuais não são suportados.

- Todos os túneis VPN para o VNet, incluindo P2S VPNs, compartilhem a largura de banda disponível para o gateway VPN e a mesma atividade de gateway VPN SLA no Azure.

- O tráfego de VNet para VNet viaja pelo backbone Azure.


## <a name="step1"></a>Etapa 1: planejar seu intervalos de endereços IP

É importante decidir os intervalos que você usará para configurar suas redes virtuais. Para essa configuração, você deve garantir que nenhum dos intervalos de VNet se sobrepõem uns com os outros, ou com qualquer uma das redes locais que se conectarem ao.

A tabela a seguir mostra um exemplo de como definir seu VNets. Use os intervalos apenas como orientação. Anote os intervalos para suas redes virtuais. Você precisará dessas informações para etapas posteriores.

**Configurações de exemplo**

|Rede virtual  |Espaço de endereço               |Região      |Se conecta ao site de rede local|
|:----------------|:---------------------------|:-----------|:-----------------------------|
|VNet1            |VNet1 (10.1.0.0/16)         |Oeste EUA     |VNet2Local (10.2.0.0/16)      |
|VNet2            |VNet2 (10.2.0.0/16)         |Japão Leste  |VNet1Local (10.1.0.0/16)      |
  
## <a name="step-2---create-vnet1"></a>Etapa 2 - criar VNet1

Nesta etapa, podemos criar VNet1. Ao usar qualquer um dos exemplos, certifique-se de substituir seus próprios valores. Se seu VNet já existir, não é necessário concluir esta etapa. Mas, você precisa verificar se os intervalos de endereços IP não se sobreponham com os intervalos para sua segunda VNet ou com qualquer um da outros VNets ao qual você deseja se conectar.

1. Faça logon no [portal de clássico Azure](https://manage.windowsazure.com). Neste artigo, vamos usar o portal de clássico porque algumas das definições de configuração necessárias ainda não estão disponíveis no portal do Azure.

2. No canto inferior esquerdo da tela, clique em **novo** > **Serviços de rede** > **Rede Virtual** > **Criar personalizada** para iniciar o Assistente de configuração. Enquanto você navega através do assistente, adicione os valores especificados para cada página.

### <a name="virtual-network-details"></a>Detalhes de uma rede virtual

Na página Detalhes de rede Virtual, insira as seguintes informações:

  ![Detalhes de uma rede virtual](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **Nome** - nome da sua rede virtual. Por exemplo, VNet1.
  - **Local** – quando você cria uma rede virtual, você associá-lo com um local Azure (região). Por exemplo, se você quiser suas VMs que são implantados à sua rede virtual para física estar localizados em Oeste EUA, selecione esse local. Você não pode alterar o local associado a sua rede virtual depois de criá-lo.

### <a name="dns-servers-and-vpn-connectivity"></a>Servidores DNS e conectividade VPN

Na página DNS Servers e a conectividade VPN, insira as seguintes informações e clique na seta próxima na parte inferior direita.

  ![Servidores DNS e conectividade VPN](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)  

- **Servidores DNS** - insira o nome do servidor DNS e o endereço IP ou selecione um servidor DNS anteriormente registrado na lista suspensa. Essa configuração não cria um servidor DNS. Ele permite especificar os servidores DNS que você deseja usar para a resolução de nome para esta rede virtual. Se você quiser ter resolução de nomes entre suas redes virtuais, você precisa configurar seu próprio servidor DNS, em vez de usar a resolução de nome fornecido pelo Azure.
- Não selecione qualquer uma das caixas de seleção para conectividade P2S ou S2S. Clique na seta na parte inferior direita para mover para a próxima tela.

### <a name="virtual-network-address-spaces"></a>Espaços de endereço de rede virtual

Na página de espaços de endereço de rede Virtual, especifique o intervalo de endereços que você deseja usar para sua rede virtual. Esses são os endereços IP dinâmicos (queda) que serão atribuídos aos VMs e outras ocorrências de função que você implantar a esta rede virtual. 

Se você estiver criando um VNet que também terá uma conexão à sua rede local, é especialmente importante selecionar um intervalo que não sobreponha com qualquer um dos intervalos que são usados para sua rede local. Nesse caso, você precisa coordenar com seu administrador de rede. O administrador da rede pode ser necessário reservar um intervalo de endereços IP do seu espaço de endereço de rede local para ser usado para sua VNet.

  ![Página de espaços de endereço de rede virtual](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  - **Espaço de endereço** , incluindo IP inicial e a contagem de endereço. Verifique se que os espaços de endereço que você especificar não se sobrepõem com qualquer um dos espaços de endereço de que você tem em sua rede local. Neste exemplo, usamos o 10.1.0.0/16 para VNet1.
  - **Adicionar sub-rede** - incluindo IP inicial e a contagem de endereço. Sub-redes adicionais não são necessárias, mas talvez você queira criar uma sub-rede separada para VMs que terão queda estática. Ou talvez você queira ter suas VMs em uma sub-rede que é separada do seu outras instâncias de função.
 
**Clique na marca de seleção** na parte inferior direita da página e sua rede virtual começará a criar. Quando terminar, você verá "Criada" listado em Status na página de redes.

## <a name="step-3---create-vnet2"></a>Etapa 3 - criar VNet2

Em seguida, repita as etapas anteriores para criar outro VNet. Em etapas posteriores, você se conecta as duas VNets. Você pode consultar as [configurações de exemplo](#step1) na etapa 1. Se seu VNet já existir, não é necessário concluir esta etapa. No entanto, você precisa verificar que os intervalos de endereços IP não se sobreponham com qualquer uma das outras VNets ou locais redes que você deseja se conectar.

## <a name="step-4---add-the-local-network-sites"></a>Etapa 4: adicione os sites da rede local

Quando você cria uma configuração de VNet para VNet, você precisa configurar sites de rede local, que são mostradas na página **Redes locais** do portal. Azure usa as configurações especificadas em cada site de rede local para determinar como rotear o tráfego entre os VNets. Determinar o nome que você deseja usar para se referir a cada site de rede local. É melhor usar algo descritivo, enquanto você seleciona o valor em uma lista suspensa em etapas posteriores.

Por exemplo, VNet1 se conecta a um site de rede local que você criar denominado "VNet2Local". As configurações de VNet2Local contêm os prefixos de endereço para VNet2 e um endereço IP público para o gateway VNet2. VNet2 se conecta a um site de rede local que você cria chamado "VNet1Local" que contém os prefixos de endereço para VNet1 e o endereço IP público para o gateway VNet1.

### <a name="localnet"></a>Adicionar o site de rede local VNet1Local

1. No canto inferior esquerdo da tela, clique em **novo** > **Serviços de rede** > **Rede Virtual** > **Adicionar rede Local**.

2. Na página **especificar os detalhes da rede local** , para **nome**, insira um nome que você deseja usar para representar a rede que você deseja se conectar. Neste exemplo, você pode usar "VNet1Local" para se referir aos intervalos de endereços IP e o gateway para VNet1.

3. **Endereço IP do dispositivo de VPN (opcional)**, especifica qualquer endereço IP público válido. Normalmente, você poderia usar o endereço IP externo real para um dispositivo VPN. Configurações de VNet para VNet, você usar o endereço IP público atribuído para o gateway para sua VNet. Mas, considerando que você ainda não tiver criado gateway, você pode especificar qualquer endereço IP público válido como um espaço reservado. Não deixe em branco - não é opcional para esta configuração. Em uma etapa posterior, você voltar para essas configurações e configurá-los com os endereços IP de gateway correspondentes depois Azure gera-lo. Clique na seta para Avançar para a próxima tela.

4. Em **especificar a página de endereço**, insira a contagem de intervalo e endereço da endereço IP para VNet1. Isso deve corresponder exatamente ao intervalo que está configurado para VNet1. Azure usa os intervalos de endereços IP que você especificar para rotear o tráfego destinado VNet1. Clique na marca de seleção para criar a rede local.

### <a name="add-the-local-network-site-vnet2local"></a>Adicionar o site de rede local VNet2Local

Use as etapas acima para criar o site de rede local "VNet2Local". Você pode fazer referência aos valores nas [configurações de exemplo](#step1) na etapa 1, se necessário.

### <a name="configure-each-vnet-to-point-to-a-local-network"></a>Configurar cada VNet para apontar para uma rede local

Cada VNet deve apontar para a rede local respectiva que você deseja rotear o tráfego para. 

#### <a name="for-vnet1"></a>Para VNet1

1. Navegue até a página **Configurar** para rede virtual **VNet1**. 
2. Em conectividade to-site, selecione "Conectar à rede local" e selecione **VNet2Local** como a rede local na lista suspensa. 
3. Salve suas configurações.

#### <a name="for-vnet2"></a>Para VNet2

1. Navegue até a página **Configurar** para rede virtual **VNet2**. 
2. Em conectividade to-site, selecione "Conectar à rede local" e selecione **VNet1Local** na lista suspensa como a rede local. 
3. Salve suas configurações.

## <a name="step-5---configure-a-gateway-for-each-vnet"></a>Etapa 5 - configurar um gateway para cada VNet

Configure um gateway roteamento dinâmico para cada rede virtual. Essa configuração não oferece suporte a gateways roteamento estático. Se você estiver usando VNets que configurados anteriormente e que já têm gateways roteamento dinâmico, você não precisa concluir esta etapa. Se seu gateways roteamento estático, é necessário excluí-las e recrie-as como gateways roteamento dinâmico. Se você excluir um gateway, o endereço IP público atribuído obtém lançado e você precisa voltar e reconfigurar qualquer uma das suas redes locais e dispositivos VPN com o novo endereço IP público para o novo gateway.

1. Na página **redes** , verifique se que a coluna de status para sua rede virtual é **criado**.

2. Na coluna **nome** , clique no nome da sua rede virtual. Neste exemplo, usamos "VNet1".

3. Na página do **Dashboard** , observe que este VNet não tem um gateway configurado ainda. Você verá esse status alterar periodicamente as etapas para configurar seu gateway.

4. Na parte inferior da página, clique em **Criar o Gateway** e **Roteamento dinâmico**. Quando o sistema solicita que você confirme se deseja que o gateway criado, clique em Sim.

    ![Tipo de gateway](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)  

5. Quando seu gateway está criando, observe o gráfico de gateway na página altera para amarelo e diz "Criando Gateway". Normalmente leva cerca de 30 minutos para criar o gateway.

6. Repita as mesmas etapas para VNet2. Você não precisa primeiro gateway VNet para ser concluída antes de começar a criar o gateway para seu outro VNet.

7. Quando o status do gateway muda para "Conectando", o endereço IP público para cada gateway fica visível no painel de controle. Anote o endereço IP que corresponde a cada VNet, tomando cuidado para não confundi-los. Esses são os endereços IP que são usados quando você edita os endereços IP de espaço reservado para o dispositivo de VPN para cada rede local.

## <a name="step-6---edit-the-local-network"></a>Etapa 6 - Editar da rede local

1. Na página **Redes locais** , clique no nome do nome da rede Local que você deseja editar, clique em **Editar** na parte inferior da página. **Endereço IP do dispositivo de VPN**, insira o endereço IP do gateway que corresponde à VNet. Por exemplo, para VNet1Local, coloque o endereço de IP do gateway atribuído a VNet1. Clique na seta na parte inferior da página.

2. Na página **especificar o espaço de endereço** , clique na marca de seleção na parte inferior direita sem fazer alterações.

## <a name="step-7---create-the-vpn-connection"></a>Etapa 7 - criar a conexão VPN

Quando todas as etapas anteriores foram concluídas, defina as chaves pré compartilhadas IPsec/IKE e criar a conexão. Esse conjunto de etapas usa PowerShell e não pode ser configurado no portal. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell do Azure. Certifique-se baixar a versão mais recente dos cmdlets de gerenciamento de serviço (Antoni). 

1. Abrir o Windows PowerShell e faça logon.

        Add-AzureAccount

2. Selecione a assinatura que sua VNets residem em.

        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
        Select-AzureSubscription -SubscriptionName "<Subscription Name>"

3. Cria as conexões. Nos exemplos, observe que a chave compartilhada é exatamente o mesmo. A chave compartilhada sempre deve corresponder.


    VNet1 para conexão de VNet2

        Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2Local -SharedKey A1b2C3D4

    VNet2 para conexão de VNet1

        Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1Local -SharedKey A1b2C3D4

4. Aguarde as conexões ao inicializar. Depois que o gateway tem inicializado, o gateway se parece com o gráfico a seguir.

    ![Status do gateway - conectado](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)  

    [AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="next-steps"></a>Próximas etapas

Você pode adicionar máquinas virtuais às suas redes virtuais. Consulte a [documentação de máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/) para obter mais informações.



[1]: ../hdinsight-hbase-geo-replication-configure-vnets.md
[2]: http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks
 
