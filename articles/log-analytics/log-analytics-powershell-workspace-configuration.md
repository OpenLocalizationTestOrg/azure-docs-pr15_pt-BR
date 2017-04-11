<properties
    pageTitle="Usar o PowerShell para criar e configurar um espaço de trabalho de análise de Log | Microsoft Azure"
    description="Log de dados de usos de análise em servidores em seu local ou infraestrutura de nuvem. Você pode coletar dados de máquina de armazenamento do Azure quando gerados pelo diagnóstico do Azure."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="powershell"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="richrund"/>

# <a name="manage-log-analytics-using-powershell"></a>Gerenciar a análise de Log usando o PowerShell

Você pode usar [Log Analytics cmdlets do PowerShell] (https://msdn.microsoft.com/library/mt188224(v=azure.300\).aspx) para executar várias funções em análise de Log de uma linha de comando ou como parte de um script.  Exemplos de tarefas que você pode executar com o PowerShell:

+ Criar um espaço de trabalho
+ Adicionar ou remover uma solução
+ Importar e exportar pesquisas salvas
+ Criar um grupo de computador
+ Habilitar a coleção de logs do IIS de computadores com o Windows agent instalado
+ Coletar contadores de desempenho de computadores Linux e Windows
+ Coletar eventos de syslog em computadores Linux 
+ Coletar eventos de logs de eventos do Windows
+ Coletar logs de eventos personalizados
+ Adicionar o agente de análise de log para uma máquina virtual Azure
+ Configurar a análise de log para dados de índice coletados usando o diagnóstico do Azure


Este artigo fornece dois exemplos de código que ilustram algumas das funções que você pode executar a partir do PowerShell.  Você pode se referir a [referência de cmdlet do PowerShell de análise de Log] (https://msdn.microsoft.com/library/mt188224(v=azure.300\).aspx) para outras funções.

> [AZURE.NOTE] Análise de log era anteriormente chamado de ideias operacionais, o motivo pelo qual é o nome usado nos cmdlets.

## <a name="prerequisites"></a>Pré-requisitos

Para usar o PowerShell com o seu espaço de trabalho de análise de Log, você deve ter:

+ Uma assinatura do Azure, e 
+ Seu espaço de trabalho de análise de Log do Azure vinculado à sua assinatura do Azure.

Se você tiver criado um espaço de trabalho do OMS, mas ele ainda não está vinculado-uma assinatura do Azure, você pode criar o link:

+ No portal do Azure
+ No portal do OMS ou 
+ Usando os cmdlets Get-AzureRmOperationalInsightsLinkTargets e AzureRmOperationalInsightsWorkspace de novo.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Criar e configurar um espaço de trabalho de análise de Log

O exemplo de script a seguir ilustra como:

1.  Criar um espaço de trabalho
2.  Listar as soluções disponíveis
3.  Adicionar soluções no espaço de trabalho
4.  Pesquisas de importação salvada
5.  Pesquisas de exportação salvada
6.  Criar um grupo de computador
7.  Habilitar a coleção de logs do IIS de computadores com o Windows agent instalado
8.  Coletar contadores de desempenho de disco lógico de computadores Linux (% Inodes usados; Megabytes livres; % Usado espaço; Transferências de disco/segundo; Leituras de disco/s; Gravações de disco/s)
9.  Coletar eventos de syslog de computadores Linux
10. Coletar eventos de erro e aviso do Log de eventos do aplicativo de computadores com Windows
11. Coletar contador de desempenho de memória disponível Mbytes de computadores com Windows
12. Coletar um log personalizado 


```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Configurando a análise de Log indexar diagnóstico do Azure 

Para monitoramento sem agente de recursos Azure, os recursos precisam ter o diagnóstico do Azure habilitado e configurado para gravar uma conta de armazenamento. Análise de log, em seguida, pode ser configurado para coletar os logs da conta de armazenamento. Inclui recursos que você precisa fazer a configuração anterior para:

+ Serviços de nuvem clássico (funções da web e trabalhador)
+ Clusters tecidos serviço
+ Grupos de segurança de rede
+ Chave compartimentos e 
+ Gateways de aplicativos

Você também pode usar o PowerShell para configurar um espaço de trabalho de análise de Log em uma assinatura do Azure para coletar logs de diferentes assinaturas do Azure.

A exemplo a seguir mostra como:

1.  Contas de armazenamento existentes e locais de que a análise de Log será indexar dados a partir de lista
2.  Criar uma configuração para ler a partir de uma conta de armazenamento
3.  Atualizar a configuração recém-criado indexar dados de locais adicionais
4.  Excluir a configuração recém-criado

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles", "insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

## <a name="next-steps"></a>Próximas etapas

- [Cmdlets do PowerShell de análise de Log de revisão](http://msdn.microsoft.com/library/mt188224.aspx) para obter informações adicionais sobre como usar o PowerShell para a configuração de análise de Log.

