<properties
    pageTitle="Usando o PowerShell para configuração ideias de aplicativo em um Azure | Microsoft Azure"
    description="Automatize Configurando diagnóstico do Azure para a conexão de obtenção de informações do aplicativo."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/17/2015"
    ms.author="awills"/>

# <a name="using-powershell-to-set-up-application-insights-for-an-azure-web-app"></a>Usando o PowerShell para configurar a obtenção de informações de aplicativo para um aplicativo web Azure

[Microsoft Azure](https://azure.com) pode ser [configurado para enviar o diagnóstico do Azure](app-insights-azure-diagnostics.md) de [Obtenção de informações do Visual Studio aplicativo](app-insights-overview.md). O diagnóstico referem-se aos serviços de nuvem do Azure e VMs do Azure. Eles complementam a telemetria enviados a partir do aplicativo usando o SDK de obtenção de informações do aplicativo. Como parte do automatizar o processo de criação de novos recursos no Azure, você pode configurar o diagnóstico usando o PowerShell.

## <a name="azure-template"></a>Modelo do Azure

Se o aplicativo da web está no Azure e criar seus recursos usando um modelo do Gerenciador de recursos do Azure, você pode configurar a obtenção de informações de aplicativo adicionando isso para o nó de recursos:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource`-um nome para o recurso de obtenção de informações de aplicativo
* `myWebAppName`-a id do aplicativo web


## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Habilitar a extensão de diagnóstico como parte da implantação de um serviço de nuvem

O `New-AzureDeployment` cmdlet tem um parâmetro `ExtensionConfiguration`, que usa uma matriz de configurações de diagnóstico. Eles podem ser criados usando o `New-AzureServiceDiagnosticsExtensionConfig` cmdlet. Por exemplo:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Habilitar extensão de diagnóstico em um serviço de nuvem existente

Em um serviço existente, use `Set-AzureServiceDiagnosticsExtension`.

```ps
 
    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>Obter a configuração atual de extensão de diagnóstico

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>Remover extensão de diagnóstico

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Se você habilitou a extensão de diagnóstico usando `Set-AzureServiceDiagnosticsExtension` ou `New-AzureServiceDiagnosticsExtensionConfig` sem o parâmetro de função, em seguida, você pode remover a extensão usando `Remove-AzureServiceDiagnosticsExtension` sem o parâmetro de função. Se o parâmetro de função foi usado ao ativar a extensão, em seguida, ele também deverá ser usado quando removendo a extensão.

Para remover a extensão de diagnóstico de cada função individual:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Consulte também

* [Monitorar o Azure aplicativos com ideias de aplicativo de serviços de nuvem](app-insights-cloudservices.md)
* [Enviar diagnóstico do Azure para obtenção de informações de aplicativo](app-insights-azure-diagnostics.md)
* [Automatizar o processo de configuração de alertas](app-insights-powershell-alerts.md)

