<properties
    pageTitle="Analisar os logs de diagnóstico Azure usando a análise de Log | Microsoft Azure"
    description="Análise de log pode ler os logs de serviços de Azure que escrever Azure logs de diagnóstico para armazenamento no formato JSON de blob."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>Analisar os logs de diagnóstico Azure usando a análise de Log

Análise de log pode coletar os logs para os seguintes serviços Azure que escrever [Azure logs de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) para armazenamento de blob no formato JSON:

+ Automação (prévia)
+ Chave cofre (prévia)
+ Gateway de aplicativo (prévia)
+ Grupo de segurança de rede (prévia)

As seções a seguir orientá-lo usando o PowerShell para:

+ Configurar a análise de Log para coletar os logs de armazenamento para cada recurso  
+ Habilitar a solução de análise de Log para o serviço do Azure

Antes de análise de Log pode coletar dados para esses recursos, diagnóstico do Azure deve ser habilitado. Use o `Set-AzureRmDiagnosticSetting` cmdlet para habilitar o log.

Consulte os artigos a seguir para obter mais informações sobre como habilitar o log de diagnóstico:

+ [Chave cofre](../key-vault/key-vault-logging.md)
+ [Gateway de aplicativo](../application-gateway/application-gateway-diagnostics.md)
+ [Grupo de segurança de rede](../virtual-network/virtual-network-nsg-manage-log.md)

Esta documentação também inclui detalhes sobre:

+ Coleta de dados de solução de problemas
+ Interrompendo a coleta de dados

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configurar a análise de Log para coletar logs de diagnóstico Azure

Coletar logs para esses serviços e habilitando a solução visualizar os logs é executada usando scripts do PowerShell.

O exemplo a seguir permite fazer logon em todos os recursos com suporte

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.Automation/AutomationAccounts", "Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


Para alguns recursos, é possível executar a configuração anterior do portal do Azure usando as etapas descritas em [logs de diagnóstico de visão geral do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configurar a análise de Log para coletar logs de diagnóstico Azure

Fornecemos um módulo de script do PowerShell que exporta dois cmdlets para ajudá-lo com a análise de Log de configuração:

1. `Add-AzureDiagnosticsToLogAnalyticsUI`solicita entrada e é capaz de configurar configurações simples
2. `Add-AzureDiagnosticsToLogAnalytics`leva os recursos para monitorar como entrada e, em seguida, configura a análise de Log  

### <a name="pre-requisites"></a>Pré-requisitos

1. PowerShell Azure com versão 1.0.8 ou mais recente dos cmdlets ideias operacionais.
  - [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md)
  - Verifique se sua versão do cmdlets:`Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `
2. Log de diagnóstico está configurado para o recurso Azure que você deseja monitorar. Use `Set-AzureRmDiagnosticSetting` ou se referir a [Análise de Log de uso para coletar dados de contas de armazenamento do Azure](log-analytics-azure-storage.md) para como habilitar o diagnóstico.
3. Um espaço de trabalho de [Análise de Log](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS)  
4. O módulo do AzureDiagnosticsAndLogAnalytics PowerShell
  - Baixar o módulo [AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/) da Galeria do PowerShell

### <a name="option-1-run-the-interactive-configuration-scripts"></a>Opção 1: Executar os Scripts de configuração interativa

Abra o PowerShell e execute:

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

Você é mostrada uma lista de seleções disponíveis e recebe um prompt de fazer sua seleção.
Você será solicitado a fazer seleções para cada um dos seguintes:

+ Tipos de recursos (Azure Services) para coletar logs de
+ Instâncias de recursos para coletar logs de
+ Espaço de trabalho de análise de log para coletar os dados

Depois de executar este script, você deve ver registros em Log Analytics cerca de 30 minutos depois de novos dados de diagnósticos são gravados ao armazenamento. Se os registros não estão disponíveis depois desta vez consulte a seção solução de problemas abaixo.

### <a name="option-2-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>Opção 2: Criar uma lista de recursos e passe-os para o cmdlet de configuração

Você pode criar uma lista de recursos que têm diagnóstico do Azure ativado e, em seguida, passar os recursos para o cmdlet de configuração.

Você pode ver informações adicionais sobre o cmdlet executando `Get-Help Add-AzureDiagnosticsToLogAnalytics`.


Para encontrar mais detalhes sobre OMS [Cmdlets do PowerShell de análise de Log](https://msdn.microsoft.com/library/mt188224.aspx)

>[AZURE.NOTE] Se o recurso e espaço de trabalho estiverem em diferentes assinaturas do Azure, alternar entre eles, conforme necessário usando`Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>`

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
Depois de executar este script, você deve ver registros em Log Analytics cerca de 30 minutos depois de novos dados de diagnósticos são gravados ao armazenamento. Se os registros não estão disponíveis depois desta vez consulte a seção solução de problemas abaixo.  

>[AZURE.NOTE] A configuração não está visível no portal do Azure. Você pode verificar a configuração usando o `Get-AzureRmOperationalInsightsStorageInsight` cmdlet.  


## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>Impedindo a análise de Log de coletar logs de diagnóstico Azure

Para excluir a configuração de análise de Log para um recurso, use o `Remove-AzureRmOperationalInsightsStorageInsight` cmdlet.

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>Solucionando problemas de configuração do Azure logs de diagnóstico

*Problema*

O seguinte erro é exibido ao configurar um recurso que está fazendo armazenamento clássico:

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*Resolução*

Faça logon na API para o modelo de implantação clássico com`Add-AzureAccount`

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>Solução de problemas de coleta de dados do Azure logs de diagnóstico

Se você não está vendo dados para o recurso Azure de análise de Log, você pode usar as seguintes etapas de solução de problemas:

+ Verificar dados fluindo para a conta de armazenamento
+ Verifique se que a solução de análise de Log para o serviço está habilitada
+ Verificar se a análise de Log está configurada para ler a partir do armazenamento
+ Atualize a chave da conta de armazenamento

### <a name="verify-data-is-flowing-to-the-storage-account"></a>Verificar dados que está fluindo para a conta de armazenamento

Você pode verificar se os dados está fluindo para a conta de armazenamento com uma ferramenta que permite a navegação de armazenamento do Azure (por exemplo, Visual Studio) ou usando o PowerShell.

Para localizar a conta de armazenamento o recurso está configurado para fazer logon para usar o PowerShell a seguir:

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

O contêiner de armazenamento usado pelo diagnóstico do Azure tem um nome com um formato de:  

`insights-logs-<Category>`

Consulte [usar cmdlets de armazenamento para verificar um contêiner de dados recentes](../storage/storage-powershell-guide-full.md) para saber mais sobre como visualizar o conteúdo da conta de armazenamento.

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>Verifique se que a solução de análise de Log para o serviço está habilitada

Se você usar `Add-AzureDiagnosticsToLogAnalyticsUI`, a solução de análise de Log correta é ativada automaticamente para você.

Para verificar se uma solução está habilitada, execute o seguinte PowerShell:

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

Se a solução não está habilitada, pode habilitá-la usando o PowerShell a seguir:

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

Para localizar o nome da solução para habilitar para cada tipo de recurso, use o seguinte PowerShell (essa variável está disponível quando você tiver importado o módulo):

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>Verificar se a análise de Log está configurada para ler a partir do armazenamento

Se você adicionar recursos adicionais do Azure, é necessário habilitar log para eles de diagnósticos e configurar a análise de Log para eles.
Para verificar quais recursos e contas de armazenamento a análise de Log estiver configurada para coletar logs para, use o PowerShell a seguir:

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>Atualize a chave de armazenamento

Se você alterar a chave da conta de armazenamento, a configuração de análise de Log também precisa ser atualizado com a nova chave.
Você pode atualizar a configuração de análise de Log com uma nova chave usando o PowerShell a seguir:

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

Para localizar o nome de uma visão de armazenamento, use o `Get-AzureRmOperationalInsightsStorageInsight` cmdlet, conforme mostrado nos exemplos anteriores.

## <a name="next-steps"></a>Próximas etapas

- [Armazenamento de blob do uso do IIS e armazenamento de tabela para eventos](log-analytics-azure-storage-iis-table.md) para ler os logs do Azure serviços que diagnóstico de gravação para armazenamento de tabela ou logs do IIS gravados armazenamento de blob.
- [Habilitar soluções](log-analytics-add-solutions.md) agregar os dados.
- [Consultas de pesquisa de uso](log-analytics-log-searches.md) para analisar os dados.
