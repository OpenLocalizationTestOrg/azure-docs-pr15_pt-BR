<properties
   pageTitle="Conectar VNets usando o modelo de implantação do Gerenciador de recursos e o portal do Azure | Microsoft Azure"
   description="Crie uma conexão de gateway VPN entre VNets usando o Gerenciador de recursos e o portal do Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="cherylmc" />

# <a name="configure-a-vnet-to-vnet-connection-using-the-azure-portal"></a>Configurar uma conexão de VNet para VNet usando o portal do Azure

> [AZURE.SELECTOR]
- [Gerenciador de recursos - Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Gerenciador de recursos - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Clássico - Portal clássico](virtual-networks-configure-vnet-to-vnet-connection.md)


Este artigo conduz você pelas etapas para criar uma conexão entre VNets no modelo de implantação do Gerenciador de recursos usando o Gateway VPN e o portal do Azure.

Quando você usa o portal do Azure para conectar redes virtuais, o VNets devem estar na mesma assinatura. Se suas redes virtuais estiverem em diferentes assinaturas, você ainda pode conectá-los usando as etapas do [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) .

![diagrama de V2V](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implantação e métodos para conexões de VNet para VNet

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela a seguir mostra os métodos para VNet para VNet configurações e modelos de implantação disponível no momento. Quando um artigo com etapas de configuração estiver disponível, podemos vincular diretamente a ele desta tabela.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

#### <a name="vnet-peering"></a>Correspondência de VNet

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Sobre conexões de VNet para VNet

Conectando a uma rede virtual para outra rede virtual (VNet-para-VNet) é semelhante à conectando um VNet para um local de site local. Os dois tipos de conectividade usam um gateway VPN do Azure para fornecer um túnel seguro usando IPsec/IKE. O VNets que você se conecta pode ser em regiões diferentes, ou em diferentes assinaturas.

Você mesmo pode combinar comunicação de VNet para VNet com configurações de vários locais. Permite que você estabelece topologias de rede que combinam entre locais conectividade com conectividade de rede entre virtual, conforme mostrado no diagrama a seguir:

![Sobre conexões] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Sobre conexões")

### <a name="why-connect-virtual-networks"></a>Por que conectar redes virtuais?

Talvez você queira conectar redes virtuais pelos seguintes motivos:

- **Cross redundância região geográfica e localização geográfica-presença**
    - Você pode configurar sua própria replicação geográfica ou a sincronização com conectividade segura sem estourando pontos de extremidade de voltado para a Internet.
    - Com o Gerenciador de tráfego do Azure e balanceador de carga, você pode configurar a carga de trabalho altamente disponível com redundância geográfica em várias regiões Azure. Um exemplo importante é configurar um SQL sempre com grupos de disponibilidade ampliando entre várias regiões Azure.

- **Aplicativos de várias camadas regionais com isolamento ou limite administrativo**
    - Dentro da mesma região, você pode configurar aplicativos de vários níveis com várias redes virtuais conectadas devido a isolamento ou requisitos administrativos.

Para obter mais informações sobre conexões de VNet para VNet, consulte as [perguntas Frequentes de VNet para VNet](#faq) no final deste artigo.

### <a name="values"></a>Configurações de exemplo

Ao usar estas etapas como um exercício, você pode usar os valores de configuração de amostra. Para fins de exemplo, usamos vários espaços de endereço de cada VNet. No entanto, as configurações de VNet para VNet não exigem vários espaços de endereço.

**Valores para TestVNet1:**

- Nome de VNet: TestVNet1
- Espaço de endereço: 10.11.0.0/16
    - Nome de sub-rede: FrontEnd
    - Intervalo de endereços de sub-rede: 10.11.0.0/24
- Grupo de recursos: TestRG1
- Local: Leste EUA
- Espaço de endereço: 10.12.0.0/16
    - Nome de sub-rede: back-end
    - Intervalo de endereços de sub-rede: 10.12.0.0/24
- Nome do gateway sub-rede: GatewaySubnet (Isso irá preencher automaticamente no portal do)
    - Intervalo de endereços de sub-rede de gateway: 10.11.255.0/27
- Servidor DNS: Use o endereço IP do seu servidor DNS
- Nome do Gateway de rede virtual: TestVNet1GW
- Tipo de gateway: VPN
- Tipo de VPN: baseada em roteiro
- SKU: Selecione o SKU de Gateway que você deseja usar
- Nome de endereço IP público: TestVNet1GWIP
- Valores de Conexão:
    - Nome: TestVNet1toTestVNet4
    - Chave compartilhada: você pode criar a chave compartilhada. Neste exemplo, usaremos abc123. O importante é que, quando você cria a conexão entre o VNets, o valor deve corresponder.

**Valores para TestVNet4:**

- Nome de VNet: TestVNet4
- Espaço de endereço: 10.41.0.0/16
    - Nome de sub-rede: FrontEnd
    - Intervalo de endereços de sub-rede: 10.41.0.0/24
- Grupo de recursos: TestRG1
- Local: Oeste EUA
- Espaço de endereço: 10.42.0.0/16
    - Nome de sub-rede: back-end
    - Intervalo de endereços de sub-rede: 10.42.0.0/24
- Nome de GatewaySubnet: GatewaySubnet (Isso irá preencher automaticamente no portal do)
    - Intervalo de endereços GatewaySubnet: 10.41.255.0/27
- Servidor DNS: Use o endereço IP do seu servidor DNS
- Nome do Gateway de rede virtual: TestVNet4GW
- Tipo de gateway: VPN
- Tipo de VPN: baseada em roteiro
- SKU: Selecione o SKU de Gateway que você deseja usar
- Nome de endereço IP público: TestVNet4GWIP
- Valores de Conexão:
    - Nome: TestVNet4toTestVNet1
    - Chave compartilhada: você pode criar a chave compartilhada. Neste exemplo, usaremos abc123. O importante é que, quando você cria a conexão entre o VNets, o valor deve corresponder.


## <a name="CreatVNet"></a>1. criar e configurar TestVNet1

Se você já tiver um VNet, verifique se as configurações são compatíveis com o seu design de gateway VPN. Atenção especial para quaisquer sub-redes que podem se sobrepor com outras redes. Se você tiver sobrepostos sub-redes, sua conexão não funcionarão corretamente. Se seu VNet estiver configurado com as configurações corretas, você pode começar as etapas na seção [especificar um servidor DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## <a name="subnets"></a>2. adicionar espaço de endereço adicional e criar sub-redes

Você pode adicionar espaço de endereço adicional e criar sub-redes quando seu VNet tiver sido criado.
[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. criar uma sub-rede de gateway

Antes de conectar sua rede virtual para um gateway, primeiro é necessário criar a sub-rede de gateway para a rede virtual à qual você deseja se conectar. Se possível, é melhor criar uma sub-rede de gateway usando um bloco CIDR de /28 ou /27 para fornecer endereços IP suficientes para acomodar requisitos adicionais de configuração de futuras.

Se você estiver criando esta configuração como um exercício, consulte estas [configurações de exemplo](#values) ao criar sua sub-rede de gateway.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Para criar uma sub-rede de gateway

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="DNSServer"></a>4. especificar um servidor DNS (opcional)

Se você quiser ter resolução de nomes para máquinas virtuais que são implantados em seu VNets, você deve especificar um servidor DNS.

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]


## <a name="VNetGateway"></a>5. criar um gateway de rede virtual

Nesta etapa, você pode criar o gateway de rede virtual para sua VNet. Esta etapa pode levar até 45 minutos para ser concluída. Se você estiver criando esta configuração como um exercício, você pode fazer referência para as [configurações de exemplo](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway de rede virtual

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


## <a name="CreateTestVNet4"></a>6. criar e configurar TestVNet4

Depois de ter configurado TestVNet1, crie TestVNet4 repetindo as etapas anteriores, substituindo os valores com as de TestVNet4. Você não precisa esperar até que o gateway de rede virtual para TestVNet1 terminar a criação do antes de configurar TestVNet4. Se você estiver usando seus próprios valores, certifique-se de que os espaços de endereço não se sobrepõem com qualquer um da VNets que você deseja se conectar.


## <a name="TestVNet1Connection"></a>7. configurar a conexão de TestVNet1

Quando os gateways de rede virtual para TestVNet1 e TestVNet4 tem concluído, você pode criar sua rede virtual conexões de gateway. Nesta seção, você irá criar uma conexão de VNet1 para VNet4.

1. **Todos os recursos**, navegue até o gateway de rede virtual para sua VNet. Por exemplo, **TestVNet1GW**. Clique em **TestVNet1GW** para abrir a lâmina de gateway de rede virtual.

    ![Blade de conexões] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Blade de conexões")

2. Clique em **+ Add** para abrir a lâmina **Adicionar conexão** .

3. Na lâmina **Adicionar conexão** , no campo nome, digite um nome para sua conexão. Por exemplo, **TestVNet1toTestVNet4**.

    ![Nome de Conexão] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Nome de Conexão")

4. Para **tipo de Conexão**. Selecione **VNet para VNet** na lista suspensa.

5. O valor do campo **primeiro gateway de rede virtual** é preenchido automaticamente porque você está criando esta conexão do gateway rede virtual especificado.

6. O campo de **segundo gateway de rede virtual** é o gateway de rede virtual da VNet que você deseja criar uma conexão para. Clique em **escolher outro gateway de rede virtual** para abrir a lâmina **Escolha rede virtual gateway** .

    ![Adicionar conexão] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Adicionar uma conexão")

7. Exiba os gateways de rede virtual listados neste blade. Observe que somente os gateways de rede virtual que estão em sua assinatura estão listados. Se você quiser conectar a um gateway de rede virtual que não esteja na sua assinatura, use o [artigo do PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). 

8. Clique no gateway de rede virtual que você deseja se conectar.
 
9. No campo de **chave compartilhada** , digite uma chave compartilhada para sua conexão. Você pode gerar ou criar esta chave por conta própria. Em uma conexão de-to-site, a chave usada seria exatamente o mesmo para seu dispositivo local e sua conexão de gateway de rede virtual. O conceito é semelhante aqui, exceto que em vez de se conectar a um dispositivo VPN, você está se conectando ao outro gateway de rede virtual.

    ![Chave compartilhada] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Chave compartilhada")

10. Clique em **Okey** na parte inferior da lâmina para salvar suas alterações.

## <a name="TestVNet4Connection"></a>8. configurar a conexão de TestVNet4

Em seguida, crie uma conexão de TestVNet4 para TestVNet1. Use o mesmo método que você usou para criar a conexão de TestVNet1 para TestVNet4. Certifique-se de que você use a mesma chave compartilhada.

## <a name="VerifyConnection"></a>9. Verifique sua conexão

Verifique se a conexão. Para cada gateway de rede virtual, faça o seguinte:

1. Localize a lâmina para o gateway de rede virtual. Por exemplo, **TestVNet4GW**. 
2. Na lâmina gateway rede virtual, clique em **conexões** para exibir a lâmina de conexões para o gateway de rede virtual.

Exibir as conexões e verificar o status. Depois que a conexão for criada, você verá o **êxito** e **conectado** como os valores de Status.

![Bem-sucedida] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Bem-sucedida")

Clique duas vezes em cada conexão separadamente para exibir mais informações sobre a conexão.

![Essentials] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="faq"></a>Perguntas Frequentes de VNet para VNet

Exiba os detalhes de perguntas Frequentes para obter informações adicionais sobre conexões VNet para VNet.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>Próximas etapas

Quando sua conexão for concluída, você pode adicionar máquinas virtuais às suas redes virtuais. Consulte [criar uma máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter as etapas.
