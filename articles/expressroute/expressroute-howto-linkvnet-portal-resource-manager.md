<properties
   pageTitle="Vincular uma rede virtual a um circuito de rota expressa usando o modelo de implantação do Gerenciador de recursos e o portal do Azure | Microsoft Azure"
   description="Este documento fornece uma visão geral de como vincular redes virtuais (VNets) a rota expressa circuitos."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc" />

# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Vincular uma rede virtual a um circuito de rota expressa

> [AZURE.SELECTOR]
- [Portal Azure - Gerenciador de recursos](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Gerenciador de recursos](expressroute-howto-linkvnet-arm.md)
- [PowerShell - clássico](expressroute-howto-linkvnet-classic.md)



Este artigo ajudará você a vincular redes virtuais (VNets) a rota expressa do Azure circuitos usando o modelo de implantação do Gerenciador de recursos e o portal do Azure. Redes virtuais podem ser na mesma assinatura ou podem fazer parte da outra assinatura.


**Sobre os modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração

- Certifique-se de que revisou os [pré-requisitos](expressroute-prerequisites.md), [Roteamento requisitos](expressroute-routing.md)e [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
- Você deve ter um circuito rota expressa ativo.
    - Siga as instruções para [criar um circuito de rota expressa](expressroute-howto-circuit-arm.md) e ter o circuito habilitado pelo seu provedor de conectividade.

    - Certifique-se de que você tenha correspondência particular Azure configurado para seu circuito. Consulte o artigo [Configurar o roteamento](expressroute-howto-routing-portal-resource-manager.md) para instruções de circulação.

    - Certifique-se de que correspondência particular Azure está configurado e a correspondência de BGP entre sua rede e da Microsoft para cima para que você pode habilitar a conectividade de ponta a ponta.

    - Certifique-se de que você tem uma rede virtual e um gateway de rede virtual criado e totalmente provisionado. Siga as instruções para criar um [gateway VPN](../articles/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) (siga somente as etapas 1 a 5).

Você pode vincular até 10 redes virtuais a um circuito de rota expressa padrão. Todas as redes virtuais devem ser na mesma região geopolíticas quando usando um circuito rota expressa padrão. Você pode vincular um redes virtuais fora da região geopolíticas do circuito rota expressa ou conectar um número maior de redes virtuais ao seu circuito rota expressa se você ativou o complemento premium rota expressa. Verifique as [perguntas Frequentes](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Conectar uma rede virtual na mesma assinatura a um circuito


### <a name="to-create-a-connection"></a>Para criar uma conexão

1. Certifique-se de que seu circuito de rota expressa e correspondência particular Azure foram configurados com êxito. Siga as instruções em [criar um circuito de rota expressa](expressroute-howto-circuit-arm.md) e [Configurar o roteamento](expressroute-howto-routing-arm.md). Seu circuito rota expressa deve parecer com a imagem a seguir.

    ![Captura de tela de circuito de rota expressa](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)

    >[AZURE.NOTE] Informações de configuração de BGP não aparecerá se o provedor de camada 3 configurado seu peerings. Se seu circuito estiver em um estado provisionado, você poderá criar conexões.

2. Agora você pode iniciar uma conexão para vincular seu gateway de rede virtual para seu circuito rota expressa de provisionamento. Clique em **Conexão** > **Adicionar** para abrir a lâmina **Adicionar conexão** e, em seguida, configure os valores. Consulte o seguinte exemplo de referência.


    ![Adicionar a captura de tela de conexão](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  


3. Depois que sua conexão tiver sido configurada com êxito, o seu objeto de conexão mostrará as informações para a conexão.

    ![Captura de tela de objeto de Conexão](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


### <a name="to-delete-a-connection"></a>Para excluir uma conexão

Você pode excluir uma conexão selecionando o ícone **Excluir** na lâmina para sua conexão.

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Conectar uma rede virtual em uma assinatura diferente a um circuito

No momento, você não consegue se conectar redes virtuais em assinaturas usando o portal do Azure. No entanto, você pode usar o PowerShell para fazer isso. Consulte o artigo do [PowerShell](expressroute-howto-linkvnet-arm.md) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a rota expressa, consulte as [Perguntas Frequentes de rota expressa](expressroute-faqs.md).
