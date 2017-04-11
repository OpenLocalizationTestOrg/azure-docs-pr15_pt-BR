<properties
    pageTitle="Visão geral de métricas no Microsoft Azure | Microsoft Azure"
    description="Visão geral de métricas e seus usos no Microsoft Azure"
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="ashwink"/>

# <a name="overview-of-metrics-in-microsoft-azure"></a>Visão geral de métricas no Microsoft Azure 

Este artigo descreve o que são métricas no Microsoft Azure, seus benefícios e como começar a usá-los.  

## <a name="what-are-metrics"></a>O que são métricas?

Monitor Azure permite consumir telemetria para obter visibilidade o desempenho e a integridade de suas cargas de trabalho no Azure. O tipo de dados de telemetria Azure mais importante são as métricas (também chamadas de contador de desempenho) emitidas por recursos mais Azure. Monitor Azure fornece várias maneiras de configurar e consumir essas métricas para monitoramento e solução de problemas.


## <a name="what-can-you-do-with-metrics"></a>O que você pode fazer com métricas?

Métricas são uma fonte valiosa de telemetria e permitem que você faça o seguinte:

- **Controlar o desempenho** do recurso (como uma máquina virtual, site ou aplicativo de lógica) por seu métricas em um gráfico de portal de plotagem e fixando esse gráfico a um painel.
- **Ser notificado sobre um problema** afetar o desempenho do seu recurso quando uma métrica exceder um determinado limite.
- **Configurar ações automatizadas**, como autoscaling um recurso ou demissões um runbook quando uma métrica exceder um determinado limite.
- **Executar análise avançada** ou relatórios sobre tendências de desempenho ou uso de seus recursos.
- **Arquivar** o histórico de desempenho ou integridade de seus objetivos **para/auditoria de conformidade** do recurso.

##  <a name="metric-characteristics"></a>Características métricas
Métricas têm as seguintes características:

- Todas as métricas tem **frequência de 1 minuto**. Você recebe um valor métrico cada minuto de seu recurso, dando a você perto visibilidade em tempo real de estado de integridade do recurso.
- Métricas são **disponível - de-prontos sem precisar optar por usar em** ou configurar diagnósticos adicionais.
- Você pode acessar **30 dias de histórico** de cada métrica. Rapidamente, você pode examinar as tendências recentes e mensais ao desempenho ou à integridade do recurso.

É possível:

- Descobrir facilmente, access e **Exibir todas as métricas** através do portal Azure quando você seleciona um recurso e plotá-las em um gráfico. 
- Configure uma **regra de alerta que envia uma notificação ou leva ação automatizada** de métrica quando a métrica exceder o limite que você definiu. Escala automática é uma ação automatizada especial que permite que você dimensionar seu recurso para atender as solicitações de entrada ou carregar no seu site ou recursos de computação. Você pode configurar uma regra de configuração de escala automática para dimensionar out / com base em uma métrica ultrapassando um limite.
- Métricas de **arquivo morto** por mais tempo ou usá-las para relatórios offline. Você pode rotear suas métricas para armazenamento de blob ao configurar definições de diagnóstico para o recurso.
- Métricas de **fluxo** a um Hub de evento, permitindo que você encaminhe a análise de fluxo de Azure ou aplicativos personalizados para análise de quase em tempo real. Você pode fazer o usando configurações de diagnóstico.
- **Rotear** todas as métricas para análise de Log de OMS desbloquear análise instantânea, pesquisa e alertas personalizados em dados de métricas de seus recursos.
- **Consume** as métricas através de novas APIs do Azure Monitor restante.
- Métricas de **consulta** usando os Cmdlets do PowerShell ou API REST de entre plataformas.

 ![Roteamento de métricas no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview0.png)

## <a name="access-metrics-via-portal"></a>Métricas de acesso por meio de portal
Veja aqui uma breve passo a passo de criação de um gráfico de métrica via portal do Azure

### <a name="view-metrics-after-creating-a-resource"></a>Métricas de modo de exibição após a criação de um recurso
1. Abrir portal do Azure
2. Crie um novo serviço de aplicativo - Site.
3. Depois de criar um site, vá para a lâmina de visão geral do site.
4. Você pode exibir novas métricas como um bloco de 'Monitorando'. Você pode editar o bloco e selecione mais métricas

 ![Métricas em um recurso no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview1.png)    

### <a name="access-all-metrics-in-a-single-place"></a>Acessar todas as métricas em um único lugar
1. Abra o portal do Azure 
2. Navegue até a guia Monitor novo e selecione a opção de métricas sob ele 
3. Você pode selecionar sua assinatura, grupo de recursos e o nome do recurso na lista suspensa. 
4. Agora você pode exibir a lista de métricas disponíveis. 
5. Selecione a métrica que você está interessado e plotá-lo. 
6. Você pode fixá-lo ao painel clicando no pin no canto superior direito.

 ![Acessar todas as métricas em um único lugar no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview2.png) 


>[AZURE.NOTE] Você pode acessar métricas de nível de host de VMs (o Gerenciador de recursos do Azure com base) e conjuntos de escala de máquina virtual sem qualquer configuração de diagnóstico adicionais. Essas novas métricas de nível de host estão disponíveis para instâncias do Windows e Linux. Essas métricas não devem ser confundidas com as métricas de nível de SO convidado que você tem acesso a quando você ativar diagnóstico do Azure no VMs ou VMSS. Para saber mais sobre como configurar o diagnóstico do Azure, consulte [o que é o diagnóstico do Microsoft Azure](../azure-diagnostics.md).

## <a name="access-metrics-via-rest-api"></a>Métricas de acesso via API REST
Métricas Azure podem ser acessadas através do Azure Monitor APIs. Há duas APIs ajudam você a descobrir e acessar métricas. Usar o: 

- [Definições do azure Monitor métrica API REST](https://msdn.microsoft.com/library/mt743621.aspx) para acessar a lista de métricas disponíveis para um serviço.
- [API REST do azure Monitor métricas](https://msdn.microsoft.com/library/mt743622.aspx) para acessar os dados de métrica real

>[AZURE.NOTE] Este artigo aborda as métricas por meio da [nova API para métricas](https://msdn.microsoft.com/library/dn931930.aspx) para recursos Azure. A versão da API para as novas definições de métrica API é 2016-03-01 e a versão de métricas API é 2016-09-01. As definições de métrica herdadas e métricas podem ser acessadas com a versão de api 2014-04-01.

Para obter mais detalhes usando as APIs REST do Azure Monitor, consulte [Azure Monitor REST API passo a passo](monitoring-rest-api-walkthrough.md).

## <a name="export-options-for-metrics"></a>Opções de exportação para métricas
Você pode ir para a lâmina de logs de diagnóstico na guia Monitor e exibir as opções de exportação para métricas. Você pode selecionar métricas (e logs de diagnóstico) para ser roteados para armazenamento de Blob, Hubs de evento ou para a análise de Log OMS para casos de uso mencionados anteriormente neste artigo. 

 ![Opções de exportação para métricas no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview3.png)   

Você pode configurar isso por meio de modelos do Gerenciador de recursos, [PowerShell](insights-powershell-samples.md), [CLI](insights-cli-samples.md) ou [APIs REST](https://msdn.microsoft.com/library/dn931943.aspx). 

## <a name="take-action-on-metrics"></a>Agir em métricas
Você pode receber notificações ou realizar ações automáticas em dados métricos. Você pode configurar regras de alerta ou configurações de escala automática para fazê-lo.

### <a name="alert-rules"></a>Regras de alerta
Você pode configurar regras de alerta em métricas. Estas regras de alerta podem verificar se uma métrica cruzou um determinado limite e notificá-lo por email ou acionar um webhook que pode ser usado para executar qualquer script personalizado. Você também pode usar o webhook para configurar 3º integrações de produtos.

 ![Métricas e regras de alerta no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview4.png)

### <a name="autoscale"></a>Escala automática
Suporte a alguns recursos Azure várias instâncias dimensionar para fora ou em lidar com suas cargas de trabalho. Escala automática se aplica a serviços de aplicativo (Web apps), conjuntos de escala de máquina Virtual (VMSS) e os serviços de nuvem clássico. Você pode configurar regras de escala automática dimensionar para fora ou em quando certa métrica afetando sua carga de trabalho exceder um limite especificado por você. Para obter mais informações, consulte [Visão geral de autoscaling](monitoring-overview-autoscale.md).

 ![Métricas e escala automática no Monitor do Azure](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="supported-services-and-metrics"></a>Serviços com suporte e métricas
Monitor Azure é uma nova infraestrutura de métricas. Ele oferece suporte para os seguintes serviços Azure no portal do Azure e a nova versão da API do Monitor do Azure:

- VMs (o Gerenciador de recursos do Azure com base)
- Conjuntos de escala de máquina virtual
- Em lotes
- Namespace do Hub de evento 
- Namespace do barramento de serviço (premium SKU somente)
- SQL (versão 12)
- Pool SQL elástica
- Sites da Web
- Server Farms da Web
- Aplicativos de lógica
- IoT Hubs
- Relacionada Cache
- Rede: Os Gateways de aplicativos
- Pesquisa

Você pode exibir uma uma lista detalhada de todos os serviços com suporte e suas medidas no [Monitor do Azure métricas - métricas suportados por tipo de recurso](monitoring-supported-metrics.md). 


## <a name="next-steps"></a>Próximas etapas

Consulte os links neste artigo. Além disso, saiba mais:  

- sobre [métricas comuns para autoscaling](insights-autoscale-common-metrics.md)
- como [criar regras de alerta](insights-alerts-portal.md)




