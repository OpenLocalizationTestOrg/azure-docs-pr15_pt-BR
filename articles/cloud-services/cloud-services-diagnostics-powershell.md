<properties
    pageTitle="Habilitar o diagnóstico nos serviços de nuvem do Azure usando o PowerShell | Microsoft Azure"
    description="Saiba como habilitar o diagnóstico para serviços de nuvem usando o PowerShell"
    services="cloud-services"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>


# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Habilitar o diagnóstico nos serviços de nuvem do Azure usando o PowerShell

Você pode coletar dados de diagnóstico como logs de aplicativos, etc. de um serviço de nuvem usando a extensão de diagnóstico do Azure contador de desempenho. Este artigo descreve como habilitar a extensão de diagnóstico do Azure para um serviço de nuvem usando o PowerShell.  Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para os pré-requisitos necessários para este artigo.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Habilitar a extensão de diagnóstico como parte da implantação de um serviço de nuvem

Essa abordagem de boa para tipo de integração contínua de cenários em que a extensão de diagnóstico pode ser habilitada como parte da implantação do serviço de nuvem. Ao criar uma nova implantação de serviço de nuvem, você pode habilitar a extensão de diagnóstico passando o parâmetro *ExtensionConfiguration* para o cmdlet [New-AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx) . O parâmetro *ExtensionConfiguration* aceita uma matriz de configurações de diagnóstico que podem ser criados usando o cmdlet [New-AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx) .

O exemplo a seguir mostra como você pode habilitar diagnóstico para um serviço de nuvem com um WebRole e WorkerRole cada um com uma configuração de diagnósticos diferentes.

    $service_name = "MyService"
    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

Se o arquivo de configuração de diagnóstico Especifica um elemento StorageAccount com um nome de conta de armazenamento, o cmdlet New-AzureServiceDiagnosticsExtensionConfig usará automaticamente dessa conta de armazenamento. Para este trabalho, a conta de armazenamento precisa estar na mesma assinatura como o serviço de nuvem sendo implantado.

Do Azure SDK 2.6 em diante publicar os arquivos de configuração de extensão gerados pelo MSBuild destino incluirá o nome da conta de armazenamento com base em sequência de configuração de diagnóstico especificada no arquivo de configuração do serviço (.cscfg). O script a seguir mostra como analisar os arquivos de configuração de extensão da saída do destino publicar e configurar a extensão de diagnóstico para cada função quando implantar o serviço de nuvem.

    $service_name = "MyService"
    $service_package = "C:\build\output\CloudService.cspkg"
    $service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

    #Find the Extensions path based on service configuration file
    $extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

    $diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
    $diagnosticsConfigurations = @()
    foreach ($extPath in $diagnosticsExtensions)
    {
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
        {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
        }
    }
    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations

Visual Studio Online usa uma abordagem semelhante para deploymnts automatizado de serviços de nuvem com a extensão de diagnóstico. Consulte [AzureCloudDeployment.ps1 publicar](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) para um exemplo completo.

Se nenhuma StorageAccount foi especificada na configuração de diagnóstico, você precisa passar no parâmetro StorageAccountName para o cmdlet. Se o parâmetro StorageAccountName for especificado, o cmdlet sempre usará a conta de armazenamento especificado no parâmetro e não o que é especificado no arquivo de configuração de diagnóstico.

Se a conta de armazenamento de diagnóstico estiver em uma assinatura diferente do serviço de nuvem, você precisa passar explicitamente nos parâmetros StorageAccountName e StorageAccountKey para o cmdlet. O parâmetro StorageAccountKey não é necessário quando a conta de armazenamento de diagnóstico está na mesma assinatura, como o cmdlet pode automaticamente da consulta e defina o valor de chave ao ativar a extensão de diagnóstico. Entretanto, se a conta de armazenamento de diagnóstico estiver em uma assinatura diferente, o cmdlet talvez não seja possível obter a chave automaticamente, e você precisa especificar explicitamente a chave por meio do parâmetro StorageAccountKey.

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key


## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Habilitar extensão de diagnóstico em um serviço de nuvem existente

Você pode usar o cmdlet [Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) para habilitar ou atualizar a configuração de diagnóstico em um serviço de nuvem que já está executando.


    $service_name = "MyService"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name


## <a name="get-current-diagnostics-extension-configuration"></a>Obter a configuração atual de extensão de diagnóstico
Use o cmdlet [Get-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) para obter a configuração de diagnóstico atual de um serviço na nuvem.

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## <a name="remove-diagnostics-extension"></a>Remover extensão de diagnóstico
Para desativar o diagnóstico em um serviço na nuvem, você pode usar o cmdlet [Remove-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx) .

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

Se você ativou a extensão de diagnóstico usando *Set-AzureServiceDiagnosticsExtension* ou o *Novo AzureServiceDiagnosticsExtensionConfig* sem o parâmetro de *função* , você poderá remover a extensão usando *AzureServiceDiagnosticsExtension remover* sem o parâmetro de *função* . Se o parâmetro de *função* foi usado ao ativar a extensão, em seguida, ele também deverá ser usado quando removendo a extensão.

Para remover a extensão de diagnóstico de cada função individual:

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## <a name="next-steps"></a>Próximas etapas

- Para obter diretrizes adicionais sobre como usar o diagnóstico do Azure e outras técnicas para solucionar problemas, consulte [Habilitando diagnóstico nos serviços de nuvem do Azure e máquinas virtuais](cloud-services-dotnet-diagnostics.md).
- O [Esquema de configuração de diagnóstico](https://msdn.microsoft.com/library/azure/dn782207.aspx) explica as diversas opções de configurações de xml para a extensão de diagnóstico.
- Para saber como habilitar a extensão de diagnóstico para máquinas virtuais, consulte [criar uma máquina Virtual do Windows com o monitoramento e diagnóstico usando o modelo de Gerenciador de recursos do Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)  
