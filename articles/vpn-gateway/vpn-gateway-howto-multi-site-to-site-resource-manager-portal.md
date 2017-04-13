<properties
   pageTitle="Como adicionar várias conexões de gateway to-Site VPN a uma rede virtual para o modelo de implantação do Gerenciador de recursos usando o portal de Azure | Microsoft Azure"
   description="Adicionar conexões de S2S de vários locais para um gateway VPN que tem uma conexão existente"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>



# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Adicionar uma conexão de-to-Site a uma VNet com uma conexão de gateway VPN existente

> [AZURE.SELECTOR]
- [Gerenciador de recursos - Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
- [Clássico - PowerShell](vpn-gateway-multi-site.md)

Este artigo o orienta usando o portal do Azure para adicionar conexões to-Site (S2S) para um gateway VPN que tem uma conexão existente. Esse tipo de conexão é conhecido como uma configuração de "vários locais". 

Você pode usar este artigo para adicionar uma conexão de S2S a uma VNet que já tenha uma conexão de S2S, conexão ponto-a-Site ou conexão de VNet para VNet. Existem algumas limitações ao adicionar conexões. Verifique a seção [antes de começar](#before) neste artigo para verificar antes de iniciar a configuração. 

Este artigo se aplica ao VNets criados usando o modelo de implantação do Gerenciador de recursos que têm um gateway RouteBased VPN. Essas etapas não aplicar configurações de conexão passa rota expressa /-to-Site. Consulte [conexões passa rota expressa/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) para obter informações sobre conexões passa.

### <a name="deployment-models-and-methods"></a>Métodos e modelos de implantação

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Podemos atualizar esta tabela como novos artigos e ferramentas adicionais se tornam disponíveis para esta configuração. Quando um artigo estiver disponível, podemos vincular diretamente a ele desta tabela.

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)] 


## <a name="before"></a>Antes de começar

Verifique os seguintes itens:

- Você não está criando uma conexão passa rota expressa/S2S.
- Você tem uma rede virtual que foi criada usando o modelo de implantação do Gerenciador de recursos com uma conexão existente.
- O gateway de rede virtual para sua VNet é RouteBased. Se você tiver um gateway PolicyBased VPN, você deve excluir o gateway de rede virtual e criar um novo gateway VPN como RoutBased.
- Nenhum dos intervalos de endereços sobrepor qualquer uma da VNets que este VNet está se conectando.
- Você tem dispositivo VPN compatível e alguém que esteja configurá-la. Consulte [sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se você não estiver familiarizado com a configuração de seu dispositivo VPN ou não estiver familiarizado com o endereço IP intervalos localizados na configuração de rede do seu local, você precisa coordenar com alguém que pode fornecer esses detalhes para você.
- Você tem um endereço IP público externamente oposto para seu dispositivo VPN. Este endereço IP não pode ser localizado atrás de um NAT.


## <a name="part1"></a>Parte 1 - configurar uma conexão

1. Em um navegador, navegue até o [portal do Azure](http://portal.azure.com) e, se necessário, entre com sua conta do Azure.
2. Clique em **todos os recursos** e localize seu **gateway de rede virtual** na lista de recursos e clique nele.
3. Na lâmina **gateway de rede Virtual** , clique em **conexões**.

    ![Blade de conexões](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Connections blade")<br>

4. Na lâmina **conexões** , clique em **+ Add**.

    ![Adicionar botão de conexão](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Add connection button")<br>

5. Na lâmina **Adicionar conexão** , preencha os seguintes campos:
    - **Nome:** O nome que você deseja dar para o site que você está criando a conexão para.
    - **Tipo de Conexão:** Selecione **to-site (IPsec)**.

    ![Adicionar blade de conexão](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Add connection blade")<br>

## <a name="part2"></a>Parte 2: adicionar um gateway de rede local

1. Clique em **gateway de rede Local** ***Escolha um gateway de rede local***. Isso abrirá a lâmina **Escolher gateway de rede local** .

    ![Escolha o gateway de rede local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Choose local network gateway")<br>
2. Clique em **Criar novo** para abrir a lâmina de **gateway de rede local de criar** .

    ![Criar blade de gateway de rede local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Create local network gateway")<br>

3. Na lâmina **gateway de rede local de criar** , preencha os seguintes campos:
    - **Nome:** O nome que você deseja dar ao recurso de gateway de rede local.
    - **Endereço IP:** O endereço IP público do dispositivo VPN no site que você deseja se conectar.
    - **Espaço de endereço:** O espaço de endereço que você deseja ser roteados para o novo site de rede local.
4. Clique em **Okey** na lâmina **criar gateway de rede local** para salvar as alterações.

## <a name="part3"></a>Parte 3 - adicionar a chave compartilhada e criar a conexão

1. Na lâmina **Adicionar conexão** , adicione a chave compartilhada que você deseja usar para criar sua conexão. Você pode obter a chave compartilhada do seu dispositivo VPN, ou fazer uma aqui e configure seu dispositivo VPN para usar a mesma chave compartilhada. O importante é que as chaves são exatamente o mesmo.

    ![Chave compartilhada](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Shared key")<br>
2. Na parte inferior da lâmina, clique em **Okey** para criar a conexão.

## <a name="part4"></a>Parte 4 - verificar a conexão de VPN

Você pode verificar sua conexão de VPN no portal, ou usando o PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]


## <a name="next-steps"></a>Próximas etapas

- Quando sua conexão for concluída, você pode adicionar máquinas virtuais às suas redes virtuais. Consulte as máquinas virtuais [caminho de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) para obter mais informações.
