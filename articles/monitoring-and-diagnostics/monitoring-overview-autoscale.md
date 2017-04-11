<properties
    pageTitle="Visão geral de escala automática em máquinas virtuais do Microsoft Azure, serviços de nuvem e aplicativos Web | Microsoft Azure"
    description="Visão geral de escala automática no Microsoft Azure. Aplica-se ao máquinas virtuais, serviços de nuvem e aplicativos Web."
    authors="rboucher"
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
    ms.date="09/06/2016"
    ms.author="robb"/>

# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Visão geral de escala automática em máquinas virtuais do Microsoft Azure, serviços de nuvem e aplicativos Web

Este artigo descreve quais escala de automática do Microsoft Azure é, seus benefícios e como começar a usá-lo.  

Escala de Monitor Azure automática se aplica somente a [Conjuntos de escala de máquina Virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Serviços de nuvem](https://azure.microsoft.com/services/cloud-services/)e [Serviço de aplicativo - Web Apps](https://azure.microsoft.com/services/app-service/web/).

>[AZURE.NOTE] Azure tem dois métodos de escala automática. Uma versão antiga do escala automática se aplica a máquinas virtuais (conjuntos de disponibilidade). Este recurso tem suporte limitado e recomendamos a migração para conjuntos de escala de máquina virtual para o suporte de escala automática mais rápido e confiável. Um link de como usar a tecnologia mais antiga será incluído neste artigo.  


## <a name="what-is-autoscale"></a>O que é escala automática?

Escala automática permite que você tenha a quantidade certa de recursos em execução para lidar com a carga no seu aplicativo. Ele permite adicionar recursos para tratar aumentos de carga e também economizar dinheiro removendo recursos que estão situados ocioso. Especificar um número mínimo e máximo de instâncias para executar e adicionar ou remover VMs automaticamente com base em um conjunto de regras. Ter um mínimo torna-se de que seu aplicativo é sempre a execução mesmo sob nenhuma carga. Está ter um máximo limita seu possível por hora custo total. Você dimensiona automaticamente entre esses dois extremos usando regras que você criar.

 ![Escala automática explicadas. Adicionar e remover VMs](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

Quando a regra condições forem atendidas, uma ou mais ações de escala automática é disparada. Você pode adicionar e remover VMs ou realizar outras ações. O diagrama conceitual a seguir mostra esse processo.  

 ![Diagrama de fluxo de escala automática conceitual](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)


## <a name="autoscale-process-explained"></a>Processo de escala automática explicadas
A explicação a seguir se aplicam às partes do diagrama anterior.   

### <a name="resource-metrics"></a>Métricas de recurso
Recursos emitem métricas, que posteriormente são processadas por regras. Métricas vêm via diferentes métodos.
Conjuntos de escala de máquina virtual usa dados de telemetria de agentes de diagnóstico do Azure enquanto telemetria para aplicativos Web e serviços de nuvem vem diretamente da infraestrutura do Azure. Algumas estatísticas comumente usadas incluem o uso da CPU, o uso da memória, contagens de segmento, comprimento da fila e uso de disco. Para uma lista de quais dados de telemetria que você pode usar, consulte [Métricas comuns de escala automática](insights-autoscale-common-metrics.md).

### <a name="time"></a>Tempo
Regras baseadas em cronograma são baseadas em UTC. Você deve definir seu fuso horário corretamente ao configurar suas regras.  

### <a name="rules"></a>Regras
O diagrama mostra apenas uma regra de escala automática, mas você pode ter muitas delas. Você pode criar regras de sobreposição complexas conforme necessário para sua situação.  Tipos de regra incluem  

 - **Baseada em métrica** - por exemplo, realize esta ação quando o uso de CPU for acima de 50%.
 - **Baseada em tempo** - por exemplo, acionar um webhook cada 8 am no sábado em um determinado fuso horário.

Regras baseadas em métrica medem a carga do aplicativo e adicionar ou remover VMs com base em carga. Regras baseadas em cronograma permitem que você dimensionar quando você ver padrões de tempo na sua carga e deseja dimensionar antes de um aumento de carga possíveis ou diminuir ocorre.  


### <a name="actions-and-automation"></a>Automação e ações

As regras podem acionar um ou mais tipos de ações.

- **Escala** - VMs de escala ou reduzir
- **Email** - enviar um email para administradores de assinatura, co-administradores e/ou endereço de email adicionais especificado por você
- **Automatizar via webhooks** - webhooks de chamada, que pode disparar várias ações complexas dentro ou fora do Azure. Dentro do Azure, você pode iniciar uma runbook Azure automação, função do Azure ou Azure lógica de aplicativo. Exemplo de URL de festa 3º fora do Azure incluir serviços como o atraso e Twilio.


## <a name="autoscale-settings"></a>Configurações de escala automática
Escala automática use a seguinte terminologia e estrutura.

- Uma **configuração de escala automática** é lido pelo mecanismo de escala automática para determinar se dimensionar para cima ou para baixo. Ele contém um ou mais perfis, informações sobre o recurso de destino e configurações de notificação.
    - Um **perfil de escala automática** é uma combinação de uma capacidade definindo, um conjunto de regras que regem o disparadores e ações de escala para o perfil e uma recorrência. Você pode ter vários perfis, que permitem que você cuidam de diferentes requisitos sobrepostos.
        - Uma **configuração de capacidade** indica o mínimo, máximo e valores padrão para o número de instâncias. [local apropriado usar Fig. 1]
        - Uma **regra** inclui um disparador — um disparador métrico ou um gatilho de tempo — e uma ação de escala, que indica se a escala automática deve dimensionar para cima ou para baixo quando essa regra é satisfeita.
        - Uma **Recorrência** indica quando escala automática deve colocar este perfil em vigor. Você pode ter perfis de escala automática diferente para horários diferentes do dia ou dias da semana, por exemplo.
- Uma **configuração de notificação** define que notificações devem ocorrer quando ocorre um evento de escala automática com base em satisfaz o critério de um dos perfis de escala automática da configuração. Escala automática pode notificar um ou mais endereços de email ou fazer chamadas para um ou mais webhooks.

![Configuração de escala automática Azure, perfil e estrutura de regra](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure3.png)

A lista completa de campos configuráveis e as descrições está disponível na [API REST de escala automática](https://msdn.microsoft.com/library/dn931928.aspx).

Para obter exemplos de código, consulte

* [Configuração avançada de escala automática usando modelos do Gerenciador de recursos para conjuntos de escala de máquina virtual](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [API REST de escala automática](https://msdn.microsoft.com/library/dn931953.aspx)



## <a name="horizontal-vs-vertical-scaling"></a>Dimensionamento vertical vs horizontais

Escala automática aumenta recursos em escalas somente horizontalmente, que é um aumento ("out") ou diminuir ("em") o número de instâncias de máquina virtual.  Horizontal dimensionamento, que é mais flexível em uma situação de nuvem como ele permite que você execute potencialmente milhares de VMs para lidar com a carga. Dimensionamento vertical é diferente. Ele mantém o mesmo número de VMs, mas torna a máquina virtual mais ("para cima") ou menos ("para baixo") poderosas. Energia é medida em memória, velocidade da CPU, espaço em disco, etc.  Dimensionamento vertical tem limitações mais. Ele depende a disponibilidade de hardware maior, o que pode variar por região e visitas rapidamente e limite superior. Dimensionamento vertical também geralmente requer um parar de máquina virtual e iniciar. Para obter mais informações, consulte [dimensionar verticalmente Azure máquina virtual com a automação do Azure](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md).


## <a name="methods-of-access"></a>Métodos de acesso
Você pode configurar escala automática via

- [Portal do Azure](insights-how-to-scale.md)
- [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
- [Entre plataformas comando Interface de linha)](insights-cli-samples.md#autoscale )
- [API REST de Monitor Azure](https://msdn.microsoft.com/library/azure/dn931953.aspx )

## <a name="supported-services-for-autoscale"></a>Serviços com suporte para escala automática


| Serviço                              | Esquema & documentos                                       |
|--------------------------------------|-----------------------------------------------------|
| Aplicativos Web                             | [Dimensionamento de aplicativos Web](insights-how-to-scale.md)              |
| Serviços de nuvem                       | [Escala automática de um serviço de nuvem](../cloud-services/cloud-services-how-to-scale.md) |
| Máquinas virtuais: clássico           | [Conjuntos de disponibilidade do dimensionamento clássico Máquina Virtual](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Máquinas virtuais: Conjuntos de escala de Windows| [Dimensionamento de escala de máquina virtual define no Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)  |
| Máquinas virtuais: Conjuntos de escala de Linux  | [Dimensionamento de escala de máquina virtual define no Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) |
| Máquinas virtuais: Exemplo de Windows   | [Configuração avançada de escala automática usando modelos do Gerenciador de recursos para conjuntos de escala de máquina virtual](insights-advanced-autoscale-virtual-machine-scale-sets.md) |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre escala automática, use o Autoscale Walkthroughs listados anteriormente ou consulte os seguintes recursos:

- [Métricas comuns do Azure Monitor escala automática](insights-autoscale-common-metrics.md)
- [Práticas recomendadas para escala automática do Monitor do Azure](insights-autoscale-best-practices.md)
- [Usar ações de escala automática para enviar email e webhook notificações de alerta](insights-autoscale-to-webhook-email.md)
- [API REST de escala automática](https://msdn.microsoft.com/library/dn931953.aspx)
- [Escala automática solução conjuntos de escala de máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
