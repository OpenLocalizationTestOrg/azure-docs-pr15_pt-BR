<properties
    pageTitle="O que é Gerenciador de tráfego | Microsoft Azure"
    description="Este artigo ajudará você a entender o que é Gerenciador de tráfego e seja a opção de roteamento de tráfego certo para seu aplicativo"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="overview-of-traffic-manager"></a>Visão geral do Gerenciador de tráfego

Gerenciador de tráfego do Microsoft Azure permite controlar a distribuição de tráfego de usuário para pontos de extremidade do serviço em centros de dados diferentes. Pontos de extremidade do serviço suportados pelo Gerenciador de tráfego incluem Azure VMs, aplicativos Web e os serviços de nuvem. Você também pode usar o Gerenciador de tráfego com pontos de extremidade externos, não Azure.

Gerenciador de tráfego usa o sistema de nome de domínio (DNS) para direcionar as solicitações para o ponto de extremidade mais apropriado com base em um [método de roteamento de tráfego](traffic-manager-routing-methods.md) e a integridade dos pontos de extremidade. Gerenciador de tráfego fornece uma variedade de métodos de roteamento de tráfego para atender às necessidades de aplicativo diferente, ponto de extremidade integridade de [monitoramento](traffic-manager-monitoring.md)e failover automático. Gerenciador de tráfego é flexível a falha, incluindo a falha de uma região Azure inteira.

## <a name="traffic-manager-benefits"></a>Benefícios do Gerenciador de tráfego

Gerenciador de tráfego pode ajudá-lo:

- **Melhorar a disponibilidade de aplicativos essenciais**

    Gerenciador de tráfego oferece alta disponibilidade para seus aplicativos seus pontos de extremidade de monitoramento e fornecendo failover automático quando um ponto de extremidade fica inativa.

- **Melhorar a capacidade de resposta para aplicativos de alto desempenho**

    Azure permite executar serviços de nuvem ou sites dos data centers localizados em todo o mundo. Gerenciador de tráfego melhora a capacidade de resposta do aplicativo direcionando o tráfego para o ponto de extremidade com a menor latência de rede para o cliente.

- **Manutenção de serviço sem tempo de inatividade**

    Você pode executar operações de manutenção planejada em seus aplicativos sem tempo de inatividade. Gerenciador de tráfego direciona o tráfego para pontos de extremidade alternativos enquanto a manutenção está em andamento.

- **Combinar locais e aplicativos baseados em nuvem**

    Gerenciador de tráfego oferece suporte externo, pontos de extremidade não Azure ativá-lo ser usado com híbrido nuvem e local implantações, incluindo a "intermitente-para-nuvem," "migrar para a nuvem," e "failover à nuvem" cenários.

- **Distribuir o tráfego para implantações complexas, grandes**

    Usando [perfis do Gerenciador de tráfego aninhadas](traffic-manager-nested-profiles.md), métodos de roteamento de tráfego podem ser combinados para criar regras sofisticadas e flexíveis para atender às necessidades de implantações maiores e mais complexas.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [funcionamento do Gerenciador de tráfego](traffic-manager-how-traffic-manager-works.md).

- Aprenda a desenvolver aplicativos de alta disponibilidade usando [monitoramento de ponto de extremidade do Gerenciador de tráfego](traffic-manager-monitoring.md).

- Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md) suportados pelo Gerenciador de tráfego.

- [Criar um perfil do Gerenciador de tráfego](traffic-manager-manage-profiles.md).

