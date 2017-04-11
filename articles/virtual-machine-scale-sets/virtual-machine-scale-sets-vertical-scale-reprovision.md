<properties
    pageTitle="Dimensionar verticalmente conjuntos de escala do Azure máquina virtual | Microsoft Azure"
    description="Como dimensionar verticalmente uma máquina Virtual em resposta ao monitoramento alertas com a automação do Azure"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="madhana"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="guybo"/>

# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Escala vertical automática com conjuntos de escala de máquina Virtual

Este artigo descreve como escalar verticalmente Azure [Conjuntos de escala de máquina Virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/) com ou sem reprovisionamento. Para dimensionamento vertical de VMs que não estão em conjuntos de escala, consulte [dimensionar verticalmente Azure máquina virtual com a automação do Azure](../virtual-machines/virtual-machines-windows-vertical-scaling-automation.md).

Dimensionamento vertical, também conhecido como _Dimensionar_ e _Dimensionar para baixo_, significa crescentes ou decrescentes tamanhos de máquina virtual (VM) em resposta a uma carga de trabalho. Compare isso com [dimensionamento horizontal](./virtual-machine-scale-sets-autoscale-overview.md), também conhecido como _escala check-out_ e _escala em_, onde o número de VMs é alterado dependendo da carga de trabalho.

Reprovisionamento significa remover uma máquina virtual existente e substituí-lo por um novo. Quando você aumenta ou diminuir o tamanho de VMs em um conjunto de escala de máquina virtual, em alguns casos você deseja redimensionar VMs existentes e manter seus dados, em outros casos você precisa implantar novas VMs do novo tamanho. Este documento aborda ambos os casos.

Dimensionamento vertical pode ser útil quando:

- Um serviço baseado em máquinas virtuais é utilizada em (por exemplo, em fins de semana). Reduzir o tamanho de máquina virtual pode reduzir custos mensais.
- Aumentar o tamanho de máquina virtual para lidar com maior demanda sem criar VMs adicionais.

Você pode configurar o dimensionamento vertical para ser disparadas com base no métricas alertas com base do seu conjunto de escala de máquina virtual. Quando o alerta é ativado-aciona um webhook que aciona um runbook que pode chegar a sua escala definido para cima ou para baixo. Dimensionamento vertical pode ser configurada seguindo estas etapas:

1. Crie uma conta de automação do Azure com o recurso de executar como.
2. Importe escala Vertical do Azure automação runbooks para conjuntos de escala de máquina virtual para sua assinatura.
3. Adicione uma webhook ao seu runbook.
4. Adicione um alerta para seu conjunto de escala de máquina virtual usando uma notificação de webhook.

> [AZURE.NOTE] Autoscaling vertical só podem ocorrer em determinados intervalos de tamanhos de máquina virtual. Compare as especificações de cada tamanho antes de decidir expandir de um para outro (números maiores não sempre indicar maiores de máquina virtual). Você pode optar por dimensionar entre os seguintes pares de tamanhos:

>| Tamanhos de máquina virtual dimensionamento par |   |
|---|---|
|  Standard_A0 | Standard_A11 |
|  Standard_D1 |  Standard_D14 |
|  Standard_DS1 |  Standard_DS14 |
|  Standard_D1v2 |  Standard_D15v2 |
|  Standard_G1 |  Standard_G5 |
|  Standard_GS1 |  Standard_GS5 |

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Criar uma conta de automação do Azure com o recurso de executar como

A primeira coisa que você precisa fazer é criar uma conta de automação do Azure que hospedará o runbooks usado para dimensionar as instâncias de máquina virtual escala definida. Recentemente [Azure automação](https://azure.microsoft.com/services/automation/) introduziu o recurso de "Executar como conta" que torna a configuração de capital de serviço para execução automática a runbooks em nome de um usuário muito fácil. Você pode ler mais sobre isso no artigo abaixo:

* [Autenticar Runbooks com conta executar como do Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar escala Vertical do Azure automação runbooks para sua assinatura

Os runbooks necessários para dimensionar verticalmente seus conjuntos de escala de máquina virtual já publicados na Galeria de Runbook de automação do Azure. Para importá-los em sua assinatura siga as etapas neste artigo:

* [Galerias de runbook e módulo de automação do Azure](../automation/automation-runbook-gallery.md)

Escolha a opção Procurar galeria no menu Runbooks:

![Runbooks a ser importado][runbooks]

Os runbooks que precisam ser importados são mostradas. Selecione runbook com base em se deseja vertical de dimensionamento com ou sem reprovisionamento:

![Galeria de runbooks][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Adicionar uma webhook ao seu runbook

Assim que você importou os runbooks que você precisará adicionar um webhook runbook para que ele pode ser acionado por um alerta de um conjunto de escala de máquina virtual. Os detalhes da criação de um webhook para seu Runbook são descritos neste artigo:

* [Azure webhooks de automação](../automation/automation-webhooks.md)

> [AZURE.NOTE] Verifique se que você copiar o URI webhook antes de fechar a caixa de diálogo webhook, pois você precisará isso na próxima seção.

## <a name="add-an-alert-to-your-vm-scale-set"></a>Adicionar um alerta para seu conjunto de escala de máquina virtual

Abaixo está um script do PowerShell, que mostra como adicionar um alerta para um conjunto de escala de máquina virtual. Consulte o artigo a seguir para obter o nome da métrica para acionar o alerta em: [métricas comuns do Azure Monitor autoscaling](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [AZURE.NOTE] É recomendável para configurar uma janela de tempo razoável para o alerta para evitar disparar dimensionamento vertical e qualquer interrupção do serviço associado, muitas vezes. Considere a possibilidade de uma janela de menos 20 a 30 minutos ou mais. Considere se você precisar evitar qualquer interrupção de dimensionamento horizontal.

Para obter mais informações sobre como criar alertas, consulte os seguintes artigos:

* [Exemplos de início rápido do Azure PowerShell do Monitor](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Exemplos de início rápido do Azure Monitor entre plataformas CLI](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Resumo

Este artigo mostrava exemplos de dimensionamento verticais simples. Com esses blocos de construção - conta de automação, runbooks, webhooks, alertas - você pode conectar uma grande variedade de eventos com um conjunto personalizado de ações.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
