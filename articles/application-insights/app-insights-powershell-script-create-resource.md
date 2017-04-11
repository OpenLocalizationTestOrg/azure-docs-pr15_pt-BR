<properties 
    pageTitle="Script do PowerShell para criar um recurso de obtenção de informações de aplicativo" 
    description="Automatize a criação de recursos de obtenção de informações do aplicativo." 
    services="application-insights" 
    documentationCenter="windows"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/19/2016" 
    ms.author="awills"/>

#  <a name="powershell-script-to-create-an-application-insights-resource"></a>Script do PowerShell para criar um recurso de obtenção de informações de aplicativo

*Obtenção de informações de aplicativo está no modo de visualização.*

Quando você quiser monitorar um novo aplicativo - ou uma nova versão de um aplicativo - com [Ideias de aplicativo do Visual Studio](https://azure.microsoft.com/services/application-insights/), você configurar um novo recurso no Microsoft Azure. Esse recurso é onde os dados de telemetria do seu aplicativo são analisados e exibidos. 

Você pode automatizar a criação de um novo recurso usando o PowerShell.

Por exemplo, se você está desenvolvendo um aplicativo de dispositivo móvel, é provável que, a qualquer momento, haverá várias versões publicadas do aplicativo em uso por seus clientes. Você não quiser receber os resultados de telemetria de diferentes versões misturadas. Assim, você obtém seu processo de construção para criar um novo recurso para cada compilação.

## <a name="script-to-create-an-application-insights-resource"></a>Script para criar um recurso de obtenção de informações de aplicativo

Consulte as especificações de cmdlet relevantes:

* [Novo AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [Novo AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)


*Script do PowerShell*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Add-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 
# - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource

$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} `
  -ResourceType "Microsoft.Insights/Components" `
  -Location "Central US" `
  -PropertyObject @{"Type"="ASP.NET"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


#Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>O que fazer com o iKey

Cada recurso é identificado por sua chave de instrumentação (iKey). O iKey é uma saída do script de criação do recurso. Script de construção deve fornecer que a iKey o SDK de ideias de aplicativo incorporado em seu aplicativo.

Há duas maneiras de disponibilizar o iKey o SDK:
  
* Em [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
 * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Ou no [código de inicialização](app-insights-api-custom-events-metrics.md): 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`



## <a name="see-also"></a>Consulte também

* [Criar ideias de aplicativo e os recursos de teste da web a partir de modelos](app-insights-powershell.md)
* [Configurar o monitoramento do diagnóstico do Azure com o PowerShell](app-insights-powershell-azure-diagnostics.md) 
* [Definir alertas usando o PowerShell](app-insights-powershell-alerts.md)

 