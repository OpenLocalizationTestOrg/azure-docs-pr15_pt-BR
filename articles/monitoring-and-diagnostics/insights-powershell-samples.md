<properties
    pageTitle="Exemplos de início rápido do PowerShell do Monitor Azure. | Microsoft Azure"
    description="Use o PowerShell para acessar os recursos do Monitor do Azure como escala automática, alertas, webhooks e pesquisando logs de atividades."
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
    ms.date="10/26/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor-powershell-quick-start-samples"></a>Exemplos de início rápido do Azure PowerShell do Monitor

Mostra artigo amostra de comandos do PowerShell para ajudá-lo a acessar os recursos de Monitor Azure. Monitor Azure permite para serviços de nuvem de escala automática, máquinas virtuais e aplicativos da Web e para enviar notificações de alerta ou URLs de web com base em valores de dados de telemetria configurado de chamadas.

>[AZURE.NOTE] Monitor Azure é o novo nome para o que era chamado "Azure ideias" até 25 de setembro de 2016. No entanto, os namespaces e, portanto, os comandos abaixo ainda contenham "Percepções".

## <a name="set-up-powershell"></a>Configurar o PowerShell
Se você ainda não começou, configure o PowerShell para executar no seu computador. Para obter mais informações, consulte [como instalar e configurar o PowerShell](../powershell-install-configure.md) .

## <a name="examples-in-this-article"></a>Exemplos deste artigo

Os exemplos no artigo ilustram como você pode usar cmdlets do Monitor do Azure. Você também pode examinar a lista inteira Azure Monitor de cmdlets do PowerShell no [Cmdlets do Azure Monitor (ideias)](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx).


## <a name="sign-in-and-use-subscriptions"></a>Entrar e usar assinaturas

Primeiro, faça logon em sua assinatura do Azure.

```PowerShell
Login-AzureRmAccount
```

Isso requer que você entrar. Depois de fazer, sua conta, TenantId e Id da assinatura padrão são exibidos. Todos os cmdlets do Azure funcionam no contexto da sua assinatura padrão. Para exibir a lista de assinaturas que você tem acesso à, use o comando a seguir.

```PowerShell
Get-AzureRmSubscription
```

Para alterar o contexto de trabalho para uma assinatura diferente, use o comando a seguir.

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-audit-logs-for-a-subscription"></a>Recuperar os logs de auditoria para uma assinatura
Use o `Get-AzureRmLog` cmdlet.  Abaixo estão alguns exemplos comuns.

Obter entradas de log dessa data/hora para apresentar:

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Obter entradas de log entre um intervalo de data/hora:

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter entradas de log de um grupo de recursos específicos:

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Obter entradas de log de um provedor de recursos específicos entre um intervalo de data/hora:

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter todas as entradas de log com um chamador específica:

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

O comando a seguir recupera os últimos 1000 eventos do log de auditoria:

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog`compatível com muitos outros parâmetros. Consulte o `Get-AzureRmLog` referência para obter mais informações.

>[AZURE.NOTE] `Get-AzureRmLog`fornece apenas 15 dias do histórico. Usando o parâmetro **- MaxEvents** permite que você os últimos eventos de N, além de 15 dias da consulta. Eventos de acesso mais de 15 dias, use o API REST ou o SDK (c# exemplo usando o SDK). Se você não incluir a **hora de início**, o valor padrão é **hora de término** menos uma hora. Se você não incluir a **hora de término**, o valor padrão é hora atual. Todos os horários estão no UTC.

## <a name="retrieve-alerts-history"></a>Recuperar histórico de alertas
Para exibir todos os eventos de alerta, você pode consultar os logs do Gerenciador de recursos do Azure usando os exemplos a seguir.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Para exibir o histórico para uma regra de alerta específica, você pode usar o `Get-AzureRmAlertHistory` cmdlet, passando a identificação do recurso da regra de alerta.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

O `Get-AzureRmAlertHistory` cmdlet oferece suporte a vários parâmetros. Obter mais informações, consulte [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).


## <a name="retrieve-information-on-alert-rules"></a>Recuperar informações sobre regras de alerta
Todos os comandos a seguintes agir em um grupo de recursos denominada "montest".

Exiba todas as propriedades da regra de alerta:

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Recupere todos os alertas em um grupo de recursos:

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

Recupere todas as regras de alerta definidas para um recurso de destino. Por exemplo, todas as regras de alerta definir em uma máquina virtual.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule`compatível com outros parâmetros. Consulte [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) para obter mais informações.

## <a name="create-alert-rules"></a>Criar regras de alerta
Você pode usar o `Add-AlertRule` cmdlet para criar, atualizar ou desativar uma regra de alerta.

Você pode criar propriedades de email e webhook usando `New-AzureRmAlertRuleEmail` e `New-AzureRmAlertRuleWebhook`, respectivamente. No cmdlet de regra de alerta, atribua essas como ações para a propriedade de **ações** da regra de alerta.

A próxima seção contém um exemplo que mostra como criar uma regra de alerta com vários parâmetros.

### <a name="alert-rule-on-a-metric"></a>Regra de alerta em uma métrica
A tabela a seguir descreve os parâmetros e valores usados para criar um alerta usando uma métrica.


|parâmetro|valor|
|---|---|
|Nome|  simpletestdiskwrite|
|Local desta regra de alerta|   Leste EUA|
|ResourceGroup| montest|
|TargetResourceId|  /Subscriptions/S1/resourceGroups/montest/Providers/Microsoft.Compute/virtualMachines/testconfig|
|MetricName do alerta que é criado|   \PhysicalDisk ( total) \Disk gravações/s. Consulte o `Get-MetricDefinitions` cmdlet abaixo sobre como recuperar os nomes de métrica exatos|
|operador|  GreaterThan|
|Valor de limite (contagem/sec para essa métrica)|    1|
|Tamanho_da_janela (hh formato)|  05:00:00|
|agregador (estatística da métrica, que usa a contagem de média, nesse caso)|  Média|
|emails personalizados (matriz de cadeia de caracteres)|'foo@example.com','bar@example.com'|
|Enviar email para proprietários, colaboradores e os leitores|    -SendToServiceOwners|

Criar uma ação de Email

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Criar uma ação de Webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Criar a regra de alerta no métrica de % da CPU em uma VM clássica

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Recuperar a regra de alerta

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

O cmdlet de alerta de adicionar também atualiza a regra se já existe uma regra de alerta para as propriedades de determinado. Para desabilitar uma regra de alerta, inclua o parâmetro **- DisableRule**.

### <a name="alert-on-audit-log-event"></a>Alertar sobre o evento de log de auditoria

>[AZURE.NOTE] Este recurso ainda está em Visualizar.

Neste cenário, você vai enviar email quando um site é iniciado com êxito em minha assinatura em grupo de recursos *abhingrgtest123*.

Configurar uma regra de email

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Configurar uma regra de webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Crie a regra no evento

```PowerShell
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

Recuperar a regra de alerta

```PowerShell
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

O `Add-AlertRule` cmdlet permite vários outros parâmetros. Obter mais informações, consulte [Adicionar AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Obter uma lista das métricas disponíveis para alertas
Você pode usar o `Get-AzureRmMetricDefinition` cmdlet para exibir a lista de todas as métricas para um recurso específico.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

O exemplo a seguir gera uma tabela com a métrica nome e a unidade para ele.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Uma lista completa das opções disponíveis para `Get-AzureRmMetricDefinition` está disponível em [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).


## <a name="create-and-manage-autoscale-settings"></a>Criar e gerenciar as configurações de escala automática
Um recurso, como um aplicativo Web, máquina virtual, serviço de nuvem ou conjunto de escala de máquina virtual pode ter apenas uma configuração de escala automática configurada para ele.
No entanto, cada configuração de escala automática pode ter vários perfis. Por exemplo, um para um perfil de escala baseados em desempenho e uma segunda para um cronograma com base perfil. Cada perfil pode ter várias regras configuradas nele. Para obter mais informações sobre escala automática, consulte [como escala automática de um aplicativo](../cloud-services/cloud-services-how-to-scale.md).

Aqui estão as etapas que usaremos:

1. Crie regras.
2. Crie perfis de mapeamento as regras que você criou anteriormente aos perfis.
3. Opcional: Crie notificações para escala automática configurando propriedades webhook e email.
4. Crie uma configuração de escala automática com um nome do recurso de destino mapeando os perfis e notificações que você criou nas etapas anteriores.

Os exemplos a seguir mostram como você pode criar uma configuração de escala automática para uma escala de máquina virtual definido para um sistema operacional do Windows com base usando a métrica de utilização de CPU.

Primeiro, crie uma regra para fora de escala, com um aumento de contagem de instância.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 0.01 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```     

Em seguida, crie uma regra para escala-in, com uma diminuição de contagem de instância.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 2 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```

Em seguida, crie um perfil para as regras.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Crie uma propriedade de webhook.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Crie a propriedade de notificação para a configuração de escala automática, incluindo email e o webhook que você criou anteriormente.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Finalmente, crie a configuração de escala automática para adicionar o perfil que você criou acima.

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Para obter mais informações sobre como gerenciar as configurações de escala automática, consulte [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Histórico de escala automática
O exemplo a seguir mostra como você pode exibir escala automática recente e eventos de alerta. Use a pesquisa de log de auditoria para exibir o histórico de escala automática.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Você pode usar o `Get-AzureRmAutoScaleHistory` cmdlet para recuperar histórico de escala automática.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Para obter mais informações, consulte [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Exibir os detalhes de uma configuração de escala automática
Você pode usar o `Get-Autoscalesetting` cmdlet para recuperar mais informações sobre a configuração de escala automática.

O exemplo a seguir mostra detalhes sobre todas as configurações de escala automática no grupo de recursos 'myrg1'.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

O exemplo a seguir mostra detalhes sobre todas as configurações de escala automática no grupo de recursos 'myrg1' e especificamente a configuração de escala automática chamado 'MyScaleVMSSSetting'.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Remover uma configuração de escala automática
Você pode usar o `Remove-Autoscalesetting` cmdlet para excluir uma configuração de escala automática.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-audit-logs"></a>Gerenciar perfis de log para logs de auditoria

Você pode criar um *perfil de log* e exportar dados do seu logs de auditoria para uma conta de armazenamento e você pode configurar retenção de dados para ele. Opcionalmente, você também pode transmitir os dados para o seu Hub de evento. Observe que este recurso está atualmente em Visualizar e você só pode criar um perfil de log por assinatura. Você pode usar os seguintes cmdlets com sua assinatura atual para criar e gerenciar perfis de log. Você também pode escolher uma assinatura específica. Embora PowerShell padrões à assinatura atual, você sempre pode alterar que usando `Set-AzureRmContext`. Você pode configurar logs de auditoria rotear dados para qualquer conta de armazenamento ou o Hub de evento dentro dessa assinatura. Dados são gravados como arquivos de blob no formato JSON.

### <a name="get-a-log-profile"></a>Obter um perfil de log
Para buscar seus perfis de log existentes, use o `Get-AzureRmLogProfile` cmdlet.

### <a name="add-a-log-profile-without-data-retention"></a>Adicionar um perfil de log sem retenção de dados

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Remover um perfil de log

```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Adicionar um perfil de log com retenção de dados

Você pode especificar a propriedade **- RetentionInDays** com o número de dias, como um inteiro positivo, onde os dados são mantidos.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Adicionar perfil de log com retenção e EventHub
Além de roteamento seus dados a conta de armazenamento, você também pode transmiti-la a um Hub de evento. Observe que esta versão de visualização e do armazenamento configuração de conta é obrigatória mas configuração Hub de evento é opcional.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Configurar logs de diagnóstico
Muitos serviços Azure fornecem logs adicionais e telemetria, incluindo grupos de segurança de rede do Azure, balanceadores de carga de Software, chave cofre, serviços de pesquisa do Azure, e aplicativos de lógica e eles podem ser configurados para salvar os dados em sua conta de armazenamento do Azure. Essa operação só pode ser executada em um nível de recurso e a conta de armazenamento deve estar presente na mesma região como o recurso de destino onde a configuração de diagnóstico está configurada.

### <a name="get-diagnostic-setting"></a>Obter a configuração de diagnóstico

```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Desativar a configuração de diagnóstico

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Habilitar a configuração de diagnóstico sem retenção

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Habilitar configuração de diagnóstico com retenção

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Habilitar configuração de diagnóstico com retenção para uma categoria de log específico

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```
