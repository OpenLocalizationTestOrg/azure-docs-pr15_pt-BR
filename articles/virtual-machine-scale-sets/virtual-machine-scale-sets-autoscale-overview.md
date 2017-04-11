<properties
    pageTitle="Dimensionamento automático e máquina virtual dimensionam conjuntos | Microsoft Azure"
    description="Saiba como usar o diagnóstico e recursos de escala automática dimensionar automaticamente máquinas virtuais em um conjunto de escala."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="davidmu"/>

# <a name="automatic-scaling-and-virtual-machine-scale-sets"></a>Dimensionamento automático e máquina virtual dimensionam conjuntos

Dimensionamento automático de máquinas virtuais em um conjunto de escala é a criação ou exclusão de máquinas no conjunto conforme necessário para atender às necessidades de desempenho. Conforme o volume de trabalho aumenta, um aplicativo pode exigir recursos adicionais para habilitá-la para a realização de tarefas.

Dimensionamento automático é um processo automatizado que ajuda a facilitar a sobrecarga de gerenciamento. Reduzindo a sobrecarga, você não precisa continuamente monitorar o desempenho do sistema ou decidir como gerenciar recursos. Dimensionamento é um processo Elástico. Mais recursos podem ser adicionados como a carga aumenta, mas como demanda diminui recursos podem ser removidos para minimizar os custos e manter os níveis de desempenho.

Configure o dimensionamento automático em uma escala definir usando um modelo do Gerenciador de recursos do Azure, Azure PowerShell, Azure CLI ou o portal do Azure.

## <a name="set-up-scaling-by-using-resource-manager-templates"></a>Configurar o dimensionamento usando modelos do Gerenciador de recursos

Em vez de implantação e gerenciamento de cada recurso do seu aplicativo separadamente, usar um modelo que implanta todos os recursos em uma operação única e coordenado. No modelo, recursos de aplicativo são definidos e implantação são especificados para ambientes diferentes. O modelo consiste em JSON e expressões que você pode usar para construir valores para a sua implantação. Para saber mais, examine o [Gerenciador de recursos do Azure criação de modelos](../resource-group-authoring-templates.md).

No modelo, especifique o elemento de capacidade:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

Capacidade identifica o número de máquinas virtuais do conjunto. Você pode alterar manualmente a capacidade Implantando um modelo com um valor diferente. Se você estiver implantando um modelo para alterar somente a capacidade, você pode incluir apenas o elemento SKU com a capacidade de atualização.

Altere automaticamente a capacidade de sua escala definir usando a combinação do recurso autoscaleSettings e a extensão de diagnóstico.

### <a name="configure-the-azure-diagnostics-extension"></a>Configurar a extensão do diagnóstico do Azure

Dimensionamento automático só pode ser feito se o conjunto de métricas for bem-sucedida em cada máquina virtual do conjunto de escala. A extensão de diagnóstico do Azure fornece os recursos de monitoramento e diagnóstico que atende às necessidades de coleta de métricas do recurso escala automática. Você pode instalar a extensão como parte do modelo de Gerenciador de recursos.

Este exemplo mostra as variáveis que são usadas no modelo para configurar a extensão de diagnósticos:

    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
    "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Thread Count\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

Parâmetros são fornecidos quando o modelo é implantado. Neste exemplo, o nome da conta de armazenamento onde os dados são armazenados e o nome do conjunto de escala do qual os dados são coletados são fornecidos. Também neste exemplo do Windows Server, somente o contador de desempenho contagem de segmentos é coletado. Todos os contadores de desempenho disponíveis no Windows ou no Linux podem ser usados para coletar informações de diagnóstico e podem ser incluídos na configuração da extensão.

Este exemplo mostra a definição da extensão no modelo:

    "extensionProfile": {
      "extensions": [
        {
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
              "storageAccount": "[variables('diagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
              "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
              "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
              "storageAccountEndPoint": "https://core.windows.net"
            }
          }
        }
      ]
    }

Quando a extensão de diagnóstico é executada, os dados são coletados em uma tabela que está localizada na conta de armazenamento que você especificar. Na tabela WADPerformanceCounters, você pode encontrar os dados coletados:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### <a name="configure-the-autoscalesettings-resource"></a>Configurar o recurso de autoScaleSettings

O recurso de autoscaleSettings usa as informações da extensão de diagnóstico para decidir se deseja aumentar ou diminuir o número de máquinas virtuais do conjunto de escala.

Este exemplo mostra a configuração do recurso no modelo:

    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 650
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 550
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
      }
    }

No exemplo acima, são criadas duas regras para definir as ações de dimensionamento automáticas. A primeira regra define a ação de escala-out e a segunda regra define a ação de escala. Esses valores são fornecidos nas regras:

- **metricName** - esse valor é a mesma que o contador de desempenho que você definiu na variável wadperfcounter para a extensão de diagnóstico. No exemplo acima, o contador de contagem de segmentos é usado.  
- **metricResourceUri** - esse valor é o identificador de recursos do conjunto de escala de máquina virtual. Este identificador contém o nome do grupo de recursos, o nome do provedor de recursos e o nome da escala definido para uma escala.
- **timeGrain** – esse valor é o detalhamento de métricas são coletados. No exemplo anterior, os dados são coletados em um intervalo de um minuto. Esse valor é usado com timeWindow.
- **estatística** – esse valor determina como as métricas são combinadas para acomodar a ação de dimensionamento automática. Os valores possíveis são: média, Min, Max.
- **timeWindow** – esse valor é o intervalo de tempo em que os dados de instância são coletados. Ele deve estar entre 5 minutos e 12 horas.
- **timeAggregation** – este valor determina como os dados coletados devem ser combinados ao longo do tempo. O valor padrão é média. Os valores possíveis são: média, mínimo, máximo, última, Total, contagem.
- **operador** – esse valor é o operador que é usado para comparar os dados de métrica e o limite. Os valores possíveis são: for igual a, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
- **limite** – este é o valor que aciona a ação de escala. Certifique-se de fornecer uma suficiente diferença entre o limite para a ação de escala-out e o limite para a ação de escala. Se você definir os valores para ser o mesmo, o sistema prevê alteração constante, o que impede a implementação de uma ação de dimensionamento. Por exemplo, a configuração ambos a 600 threads no exemplo anterior não funciona.
- **direção** – esse valor determina a ação que é executada quando o valor limite é alcançado. Os valores possíveis são aumentar ou reduzir.
- **tipo** – esse valor é o tipo de ação que deve ocorrer e deve ser definida como ChangeCount.
- **valor** – esse valor é o número de máquinas virtuais que são adicionados ou removidos do conjunto de escala. Este valor deve ser 1 ou maior.
- **cooldown** – esse valor é a quantidade de tempo de espera desde a última ação de dimensionamento antes que a próxima ação ocorra. Este valor deve estar entre um minuto e uma semana.

Dependendo do contador de desempenho que você está usando, alguns dos elementos na configuração do modelo são usados de forma diferente. No exemplo anterior, o contador de desempenho é o número de segmentos, o valor limite é 650 para uma ação de escala-out e o valor de limite é 550 para a ação de escala. Se você usar um contador como % tempo do processador, o valor de limite está definido para a porcentagem de uso da CPU que determina uma ação de dimensionamento.

Quando um carregamento é criado nas máquinas virtuais que aciona uma ação de escala, a capacidade do conjunto é aumentada com base no valor no modelo. Por exemplo, em uma escala conjunto onde a capacidade está definida como 3 e o valor de ação de escala é definido como 1:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Quando a carga é criada que faz com que a contagem de segmento média ir acima do limite de 650:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

Uma ação de dimensionamento é disparada que aciona a capacidade do conjunto de aumento por um:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

E uma máquina virtual é adicionada ao conjunto de escala:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Após um período de cooldown de cinco minutos, se o número médio de threads nas máquinas permanece 600 acima, outra máquina será adicionada ao conjunto. Se a contagem de segmento média permanecer abaixo 550, a capacidade do conjunto de escala é reduzida por uma e uma máquina é removida do conjunto.

## <a name="set-up-scaling-using-azure-powershell"></a>Configurar o dimensionamento usando o PowerShell do Azure

Para ver exemplos de como usar o PowerShell para configurar auto-dimensionamento, examine o [Azure Monitor PowerShell rápido iniciar amostras](../monitoring-and-diagnostics/insights-powershell-samples.md).

## <a name="set-up-scaling-using-azure-cli"></a>Configurar o dimensionamento usando CLI do Azure

Para ver exemplos do uso do Azure CLI configurar auto-dimensionamento, examine o [Azure Monitor entre plataformas CLI rápido iniciar amostras](../monitoring-and-diagnostics/insights-cli-samples.md).

## <a name="set-up-scaling-using-the-azure-portal"></a>Configurar o dimensionamento usando o portal do Azure

Para ver um exemplo de como usar o portal do Azure para configurar auto-dimensionamento, examine a [criar um conjunto de escala de máquina Virtual usando o portal do Azure](virtual-machine-scale-sets-portal-create.md).

## <a name="investigate-scaling-actions"></a>Investigar ações de escala

- [Portal do azure]() - no momento que você pode obter uma quantidade limitada de informações usando o portal.
- [Azure Resource Explorer]() - esta ferramenta é o melhor para explorar o estado atual do seu conjunto de escala. Siga este caminho e você verá a exibição da instância do conjunto de escala que você criou: assinaturas > {sua assinatura} > resourceGroups > {seu grupo de recursos} > provedores > Microsoft.Compute > virtualMachineScaleSets > {seu conjunto de escala} > virtualMachines
- PowerShell Azure - Use este comando para obter algumas informações:

        Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
        Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput

- Conectar-se à máquina virtual jumpbox exatamente como faria com qualquer outro computador e, em seguida, você pode acessar remotamente máquinas virtuais a escala conjunto para monitorar processos individuais.

## <a name="next-steps"></a>Próximas etapas

- Examine a [Dimensionar automaticamente máquinas em um conjunto de escala de máquina Virtual](virtual-machine-scale-sets-windows-autoscale.md) para ver um exemplo de como criar uma escala em conjunto com o dimensionamento automático configurado.
- Encontrar exemplos do Monitor do Azure recursos no [Azure Monitor PowerShell rápido iniciar amostras](../monitoring-and-diagnostics/insights-powershell-samples.md) de monitoramento
- Saiba mais sobre os recursos de notificação no [usar ações de escala automática para enviar email e webhook notificações de alerta no Monitor do Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md).
- Saiba mais sobre como [logs de auditoria de uso para enviar email e webhook notificações de alerta no Monitor do Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
- Saiba mais sobre [cenários de escala automática avançadas](./virtual-machine-scale-sets-advanced-autoscale.md).
