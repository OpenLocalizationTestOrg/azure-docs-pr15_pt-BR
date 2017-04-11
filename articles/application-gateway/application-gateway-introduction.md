<properties
   pageTitle="Introdução ao Gateway de aplicativo | Microsoft Azure"
   description="Esta página fornece uma visão geral do serviço de aplicativo Gateway para balanceamento de carga de camada 7, incluindo tamanhos de gateway, afinidade de sessão baseados em cookies, balanceamento de carga de HTTP e SSL descarregar."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>

# <a name="application-gateway-overview"></a>Visão geral de Gateway do aplicativo

## <a name="what-is-application-gateway"></a>O que é o Gateway de aplicativo

Gateway de aplicativo do Microsoft Azure fornece controlador de entrega de aplicativo (ADC) como um serviço, oferecendo vários recursos para seu aplicativo de balanceamento de carga de camada 7. Ele permite aos clientes otimizar a produtividade de farm da web, transferindo a carga de CPU intenso SSL taxas de término para o Gateway de aplicativo. Ele fornece também outros recursos de roteamento de camada 7 incluindo round robin distribuição de tráfego de entrada, cookie baseado afinidade de sessão, o roteamento de caminho com base de URL e a capacidade de hospedar vários sites atrás de um único aplicativo Gateway. Gateway do aplicativo também possui um firewall de aplicativo da web (WAF) que protege seu aplicativo contra a maioria das OWASP superiores 10 comuns da web vulnerabilidades. Gateway do aplicativo pode ser configurado como gateway oposta na internet, gateway somente interno ou uma combinação de ambos. Gateway de aplicativo é totalmente Azure gerenciado, flexível e altamente disponível. Ele fornece conjunto sofisticado de diagnósticos e os recursos de registro em log para melhor capacidade de gerenciamento. Gateway do aplicativo funciona com máquinas virtuais, serviços de nuvem e internos ou externos aplicativos web opostas.

Gateway de aplicativo é um dispositivo virtual dedicado para seu aplicativo e consiste de várias instâncias de trabalho para alta disponibilidade e escalabilidade. Quando você cria um gateway de aplicativos, um ponto de extremidade (VIP público ou interno ILB IP) é usado para o tráfego de rede de ingresso e associado. Esta VIP ou ILB IP é fornecido pelo Balanceador de carga do Azure trabalhando no nível de transporte (TCP/UDP) e ter todo o tráfego rede entrada sendo carga equilibrada para as instâncias de trabalho Application Gateway. O Gateway de aplicativo e em seguida, rotas o tráfego HTTP/HTTPS com base em sua configuração seja uma máquina virtual, serviço, interno ou um endereço IP externo em nuvem. Para o SLA e preços, consulte as páginas de [preços](https://azure.microsoft.com/pricing/details/application-gateway/) e [SLA](https://azure.microsoft.com/support/legal/sla/) .

## <a name="features"></a>Recursos

Gateway de aplicativo atualmente oferece suporte a entrega de aplicativos 7 de camada com os seguintes recursos:

- **[Firewall de aplicativo da web (visualização)](application-gateway-webapplicationfirewall-overview.md)** - o firewall do aplicativo da web (WAF) no Azure Application Gateway protege aplicativos web contra ataques comuns baseado na web, como a inclusão de SQL, ataques de script entre sites e sequestros de sessão.
- **Balanceamento de carga HTTP** - Application Gateway fornece balanceamento de carga de repetição alternada. Balanceamento de carga é feito na camada 7 e é usado para tráfego de HTTP (S) somente.
- **Afinidade de sessão baseada em cookies** - esse recurso é útil quando você deseja manter uma sessão de usuário no mesmo back-end. Usando o gateway gerenciado cookies, o Gateway de aplicativo é capaz de direcionar o tráfego subsequente de uma sessão de usuário para o mesmo back-end para processamento. Esse recurso é importante em casos em que o estado da sessão é salvo localmente no servidor back-end para uma sessão de usuário.
- **[Descarregar secure Sockets Layer (SSL)](application-gateway-ssl-arm.md)** - este recurso leva a tarefa cara de descriptografar o tráfego HTTPS desativar os servidores web. Encerrando a conexão SSL no aplicativo Gateway e encaminhar a solicitação para o servidor não criptografada, o servidor web é unburdened pela descriptografia.  Gateway de aplicativo criptografa novamente a resposta antes de enviá-la ao cliente. Esse recurso é útil em cenários onde o back-end está localizado na mesma rede virtual protegida como o Gateway de aplicativo no Azure.
- **[SSL de ponta a ponta](application-gateway-backend-ssl.md)** - Application Gateway dá suporte à criptografia de ponta a ponta de tráfego. Gateway de aplicativo faz isso terminando a conexão SSL no gateway de aplicativo. O gateway, em seguida, aplica as regras de roteamento para o tráfego, criptografa novamente o pacote e encaminha o pacote para o back-end apropriado com base nas regras de roteamento definidas. Qualquer resposta do servidor web percorre o mesmo processo de volta para o usuário final.
- **[Encaminhamento de conteúdo baseado em URL](application-gateway-url-route-overview.md)** - esse recurso oferece a capacidade de usar os servidores de back-end diferentes para o tráfego de diferentes. O tráfego para uma pasta no servidor web ou para uma CDN poderia ser roteado para um back-end diferente, reduzindo a carga desnecessário nos back-ends que não servem conteúdo específico.
- **[Roteamento de vários locais](application-gateway-multi-site-overview.md)** - aplicativo gateway permite que você consolidar até 20 sites em um gateway único aplicativo.
- **[Suporte de Websocket](application-gateway-websocket.md)** - outro grande recurso do Gateway de aplicativo é o suporte nativo para Websocket.
- **[Monitoramento de integridade](application-gateway-probe-overview.md)** - gateway aplicativo fornece testes de integridade de padrão monitoramento dos recursos de back-end e personalizada para monitorar a cenários mais específicos.

## <a name="benefits"></a>Benefícios

Gateway de aplicativo é útil para:

- Aplicativos que exigem solicitações da sessão de usuário/cliente mesmo alcançar a mesma máquina virtual back-end. Exemplos desses aplicativos deve ser compras aplicativos do carrinho e servidores de email da web.
- Aplicativos que deseja liberar server farms da web de sobrecarga de encerramento SSL.
- Aplicativos, como uma rede de distribuição de conteúdo, que requer várias solicitações HTTP sobre a conexão de TCP demorada mesmo ser circulados ou carregar equilibrada para diferentes servidores de back-end.
- Aplicativos que suportam tráfego websocket
- Protegendo aplicativos web ataques comuns baseado na web como inclusão SQL, ataques de script entre sites e sequestros de sessão.

Gateway de aplicativo balanceamento de carga como um serviço gerenciado Azure permite o provisionamento de um balanceador de carga de camada 7 atrás do balanceador de carga de software Azure. Gerenciador de tráfego pode ser usado para concluir o cenário, conforme mostrado na imagem a seguir. Onde Gerenciador de tráfego oferece redirecionamento e disponibilidade, balanceador de carga fornece disponibilidade e escalabilidade de região e gateway aplicativo fornece balanceamento de carga de camada 7 de região cruzada.

![asdasd](./media/application-gateway-introduction/tm-lb-ag-scenario.png)

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="gateway-sizes-and-instances"></a>Instâncias e tamanhos de gateway

Gateway de aplicativo é atualmente oferecida em três tamanhos: pequeno, médio e grande. Tamanhos de instância pequena destinam-se a cenários de testes e desenvolvimento.

Atualmente, existem duas skus para Application Gateway: WAF e padrão.

Você pode criar até 50 gateways de aplicativo por assinatura e gateway cada aplicativo pode ter até 10 instâncias. Gateway cada aplicativo pode consistir em 20 ouvintes de http. Para obter uma lista completa dos limites de gateway do aplicativo, visite a página de [Limites de serviço](../azure-subscription-service-limits.md#application-gateway) .

A tabela a seguir mostra uma taxa de transferência média de desempenho para cada instância de gateway do aplicativo:

| Resposta de página de back-end | Pequeno | Média | Grande|
|---|---|---|---|
| 6K | 7.5 Mbps | 13 Mbps | 50 Mbps |
|100K | 35 Mbps | 100 Mbps| 200 Mbps |

>[AZURE.NOTE] Esses valores são valores aproximados para uma taxa de transferência de gateway do aplicativo. A taxa de transferência real depende de diversos detalhes de ambiente, como o tamanho da página média, localização do instâncias de back-end e tempo de processamento para atender a uma página. Para números de desempenho exatos, você deve executar seus próprios testes, esses valores são fornecidos apenas para a orientação de planejamento da capacidade.

## <a name="health-monitoring"></a>Monitoramento de integridade

Azure Application Gateway automaticamente monitora a integridade das instâncias de back-end através de basic ou testes de integridade personalizado. Usando testes de integridade, isso garante que hospeda apenas eficaz, responde ao tráfego. Para obter mais informações, consulte [Visão geral sobre monitoramento de integridade de Gateway do aplicativo](application-gateway-probe-overview.md).

## <a name="configuring-and-managing"></a>Configurar e gerenciar

Para seu ponto de extremidade, gateway aplicativo pode ter um IP público, IP privado ou ambos quando está configurado. Gateway de aplicativo é configurado dentro de uma rede virtual em sua própria sub-rede. A sub-rede criado ou usado para o gateway de aplicativo não pode conter quaisquer outros tipos de recursos, apenas os recursos que são permitidos na sub-rede são os gateways de outros aplicativos. Para proteger seus recursos de back-end back-end servidores podem estar contidos em uma sub-rede diferente na mesma rede virtual como o gateway do aplicativo. Esta sub-rede adicional que ele não é necessário para os aplicativos de back-end, desde que o gateway de aplicativo pode acessar o endereço ip, o gateway de aplicativo é capaz de fornecer ADC recursos para os servidores de back-end.

Você pode criar e gerenciar um gateway aplicativo usando APIs REST, cmdlets do PowerShell, Azure CLI ou [portal Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Próximas etapas

Depois de conhecer o gateway do aplicativo, você pode [criar um gateway de aplicativo](application-gateway-create-gateway-portal.md) ou você pode [criar um gateway de aplicativo SSL descarregar](application-gateway-ssl-arm.md) a conexões HTTPS de balanceamento de carga.

Para saber como criar um gateway de aplicativo usando o encaminhamento de conteúdo baseado em URL, vá para [criar um gateway de aplicativo usando o roteamento baseado em URL](application-gateway-create-url-route-arm-ps.md) para obter mais informações.

