<properties
   pageTitle="Criar VNet correspondência usando o portal de Azure | Microsoft Azure"
   description="Aprenda a criar uma rede virtual usando o portal de Azure no Gerenciador de recursos."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai;annahar"/>

# <a name="create-a-virtual-network-peering-using-the-azure-portal"></a>Criar uma rede virtual correspondência usando o portal do Azure

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para criar um VNet correspondência com base no cenário acima usando o portal do Azure, siga as etapas abaixo.

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Para estabelecer VNET correspondência, você precisa criar dois links, uma para cada direção, entre duas VNets. Você pode criar link de correspondência de VNET para VNET1 para VNET2 primeiro. No portal, clique em **Procurar** > **Escolha redes virtuais**

    ![Criar VNet correspondência no portal do Azure](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)

3. Na lâmina de redes virtuais, escolha VNET1, clique em Peerings, clique em Adicionar

    ![Escolha a correspondência](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)

4. Na lâmina adicionar correspondência, dê um nome de link aos LinkToVnet2, escolha a assinatura e o ponto VNET2 de rede Virtual, clique em Okey.

    ![Link para VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)

5. Depois que esse link aos VNET é criado. Você pode ver o estado de link seguinte:

    ![Estado do link](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)

6. Em seguida, crie o vínculo de correspondência de VNET para VNET2 para VNET1. Na lâmina de redes virtuais, escolha VNET2, clique em Peerings, clique em Adicionar

    ![Ponto de outro VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)

7. Na lâmina adicionar correspondência, dê um nome de link aos LinkToVnet1, escolha a assinatura e o ponto de rede Virtual, clique em Okey.

    ![Criar bloco de rede virtual](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)

8. Depois que esse link aos VNET é criado. Você pode ver o estado de link seguinte:

    ![Estado do link final](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)

9. Verificar o estado de LinkToVnet2 e agora muda para conectado também.  

    ![Estado do link final 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)

    > [AZURE.NOTE] VNET correspondência é estabelecida somente se ambos os links estiverem conectados.

Há algumas propriedades configuráveis para cada link:

|Opção|Descrição|Padrão|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|Se o espaço de VNet de ponto a serem incluídos como parte da marca Virtual_network de endereço|Sim|
|AllowForwardedTraffic|Se o tráfego não provenientes um VNet peered for aceito ou descartado|Não|
|AllowGatewayTransit|Permite que o ponto VNet para usar seu gateway VNet|Não|
|UseRemoteGateways|Use o gateway de VNet do seu correspondente. O ponto VNet deve ter um gateway configurado e AllowGatewayTransit está selecionada. Você não pode usar esta opção se você tiver um gateway configurado|Não|

Cada link na correspondência de VNet tem um conjunto de acima de propriedades. No portal do, você pode clicar no Link de correspondência VNet e alterar as opções disponíveis, clique em Salvar para tornar o efeito de alteração.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Neste exemplo usaremos duas assinaturas A e B e dois usuários UsuárioA e UserB com privilégios das assinaturas respectivamente
3. No portal, clique em Procurar, escolha redes virtuais. Clique na VNET e clique em Adicionar.

    ![Cenário 2 procurar](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)

4. Na lâmina acesso adicionar, clique em selecionar uma função e escolha Colaborador de rede, clique em Adicionar usuários, digite o sinal de UserB em nome e clique em Okey.

    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)

    Isso não é um requisito, correspondência pode ser estabelecida mesmo se usuários individualmente geram solicitações de correspondência para seus respectivos Vnets desde que as solicitações correspondem. Adicionando usuários privilegiados da outro VNet como usuários no VNet local facilita a configuração no portal.

5. Em seguida, fazer o login no portal Azure com UserB quem é o usuário de privilégio para SubscriptionB. Siga acima etapas para adicionar UserA como Colaborador de rede.

    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)

    > [AZURE.NOTE] Você pode fazer logoff e logon ambas as sessões de usuário no navegador para garantir que a autorização está habilitada com êxito.

6. Logon no portal como UsuárioA, navegue até a lâmina VNET3, clique em Peering, marque ' sei minha ID de recurso "caixa de seleção e digite o recurso ID para VNET5 no abaixo de formato.

    / subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.Network/VirtualNetwork/{VNETname}

    ![Identificação do recurso](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)

7. Faça logon no portal como UserB e siga acima etapa para criar o link de correspondência de VNET5 para VNet3.

    ![Identificação do recurso 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)

8. Correspondência será estabelecida e qualquer máquina Virtual em VNet3 deve ser capaz de se comunicar com qualquer máquina virtual em VNet5

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. A primeira etapa, links aos VNET de HubVnet para VNET1. Observe que a opção de permitir o tráfego encaminhado não estiver selecionada para o link.

    ![Correspondência básica](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)

2. Próxima etapa aos links de VNET1 para HubVnet pode ser criado. Observe que a opção de tráfego de permitir encaminhada está selecionada.

    ![Correspondência básica](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)

3. Depois correspondência for estabelecida, você pode se referir a este [artigo](virtual-network-create-udr-arm-ps.md) e definir Route(UDR) de definidas pelo usuário para redirecionar o tráfego de VNet1 por meio de um dispositivo virtual para usar seus recursos. Quando você especifica o endereço de próximo salto no roteiro, você pode configurá-a para o endereço IP do dispositivo virtual em ponto VNet HubVNet


[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]



1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.

2. Para estabelecer VNET correspondência nesse cenário, você precisa criar apenas um link, da rede virtual no Gerenciador de recursos Azure para o clássico. Isto é, de **VNET1** para **VNET2**. No portal, clique em **Procurar** > escolha **Redes virtuais**

3. Na lâmina redes virtuais, escolha **VNET1**. Clique em **Peerings**, clique em **Adicionar**.

4. Na lâmina adicionar correspondência, nomeie seu link. Aqui é chamado **LinkToVNet2**. Em detalhes de ponto, selecione **clássico**.

5. Escolha a assinatura e o ponto de rede Virtual **VNET2**. Clique em Okey.

    ![Vinculando Vnet1 a Vnet 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)

6. Depois que esse link aos VNet é criado, as duas redes virtuais são peered e você poderá ver o seguinte:

    ![Verificação de conexão de correspondência](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)


## <a name="remove-vnet-peering"></a>Remover a correspondência de VNet

1.  Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2.  Vá para blade de rede virtual, clique em Peerings o Link que você deseja remover, clique em botão Excluir.

    ![Delete1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)

3. Depois que você remove um link em VNET correspondência, o estado de link ponto irão para desconectado.

    ![Delete2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)

4. Nesse estado, você não pode recriar o link até que o estado de link ponto muda para iniciada. Recomendamos que você remova os dois links antes de recriar a correspondência de VNET.
