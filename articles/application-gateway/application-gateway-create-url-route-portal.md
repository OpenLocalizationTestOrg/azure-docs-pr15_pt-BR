<properties
   pageTitle="Criar uma regra baseada no caminho para um gateway de aplicativo usando o portal | Microsoft Azure"
   description="Saiba como criar uma regra baseada no caminho para um gateway de aplicativo usando o portal"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-portal"></a>Criar uma regra baseada no caminho para um gateway de aplicativo usando o portal

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-url-route-portal.md)
- [Azure PowerShell do Gerenciador de recursos](application-gateway-create-url-route-arm-ps.md)

Roteamento de baseado em caminho de URL permite que você associe rotas com base no caminho de URL da solicitação de Http. Verifica se houver uma rota para um pool de back-end configurado para nas listas de URL do Gateway de aplicativo e enviar o tráfego de rede para o pool de back-end definido. Um uso comum para roteamento baseado em URL é carregar solicitações de saldo para diferentes tipos de conteúdo para pools de servidor de back-end diferente.

Roteamento baseado em URL apresenta um novo tipo de regra ao gateway do aplicativo. Gateway de aplicativo tem dois tipos de regra: regras básicas e baseado em caminho. Tipo de regra básica fornece serviço alternada para pools de back-end enquanto regras baseadas em caminho além de distribuição de repetição alternada, também leva padrão de caminho de URL da solicitação em consideração ao escolher o pool de back-end.

## <a name="scenario"></a>Cenário

O cenário a seguir percorre criando uma regra baseada em caminho em um gateway de aplicativo existente.
O cenário supõe que você já tiver seguido as etapas para [criar um Gateway de aplicativos](application-gateway-create-gateway-portal.md).

![rota de URL][scenario]

## <a name="createrule"></a>Crie a regra baseada em caminho

Uma regra baseada em caminho requer seu próprio ouvinte, antes de criar a regra ser Verifique se você tem um ouvinte disponível para usar.

### <a name="step-1"></a>Etapa 1

Navegue até http://portal.azure.com e selecione um gateway de aplicativo existente. Clique em **regras**

![Visão geral de Gateway do aplicativo][1]

### <a name="step-2"></a>Etapa 2

Clique em botão **baseado em caminho** para adicionar uma nova regra baseada em caminho.

### <a name="step-3"></a>Etapa 3

A lâmina **Adicionar regra baseada em caminho** tem duas seções. A primeira seção é onde você definiu o ouvinte, o nome da regra e as configurações de caminho de padrão. As configurações de caminho padrão são para rotas que não pertencem a rota de baseada em caminho personalizada. A segunda seção da lâmina **Adicionar regra baseada em caminho** é onde você define as regras baseadas em caminho próprios.

**Configurações básicas**

- **Nome** - este é um nome amigável para a regra que é acessível no portal.
- **Ouvinte** - este é o ouvinte que é usado para a regra.
- **Pool de back-end padrão** - esta configuração é a configuração que define o back-end para ser usado para a regra padrão
- **As configurações padrão HTTP** - esta configuração é a configuração que define as configurações de HTTP a ser usado para a regra padrão.

**Regras baseadas em caminho**

- **Nome** - este é um nome amigável para a regra baseada em caminho.
- **Caminhos** - esta configuração define o caminho que na regra procurará quando o tráfego de encaminhamento
- **Pool de back-end** - esta configuração é a configuração que define o back-end para ser usado para a regra
- **Configuração de HTTP** - esta configuração é o que define as configurações de HTTP a ser usado para a regra.

>[AZURE.IMPORTANT] Caminhos: A lista de padrões de caminho para fazer a correspondência. Cada deve começar com / e o único lugar uma "\*" é permitido está no final. Exemplos válidos são /xyz, /xyz* ou /xyz/*.  

![Adicionar blade regra baseada em caminho com informações preenchidas][2]

Adicionar uma regra baseada no caminho para um gateway de aplicativo existente é um processo fácil através do portal. Depois que uma regra baseada em caminho tiver sido criada, ele pode ser editado para adicionar regras adicionais facilmente. 

![Adicionando regras baseadas em caminhos adicionais][3]

## <a name="next-steps"></a>Próximas etapas

Para saber como configurar SSL transferindo com o Azure Application Gateway consulte [Configurar SSL descarregar](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png