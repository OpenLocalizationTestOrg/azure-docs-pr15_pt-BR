<properties
    pageTitle="Usar o PowerShell para habilitar o diagnóstico do Azure em uma máquina virtual executando o Windows | Microsoft Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    description="Aprenda a usar o PowerShell para habilitar o diagnóstico do Azure em uma máquina virtual executando o Windows"
    authors="sbtron"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>


# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Usar o PowerShell para habilitar o diagnóstico do Azure em uma máquina virtual executando o Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Diagnóstico do Azure é o recurso dentro do Azure que permite a coleta de dados de diagnóstico em um aplicativo implantado. Você pode usar a extensão de diagnóstico para coletar dados de diagnóstico como logs de aplicativos ou contadores de desempenho de uma Azure virtuais de máquina que está executando o Windows. Este artigo descreve como usar o Windows PowerShell para habilitar a extensão de diagnóstico para uma máquina virtual. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para os pré-requisitos necessários para este artigo.

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Habilitar a extensão de diagnóstico, se você usar o modelo de implantação do Gerenciador de recursos

Você pode habilitar a extensão de diagnóstico enquanto você cria uma máquina de virtual do Windows através do modelo de implantação do Gerenciador de recursos do Azure, adicionando a configuração de extensão para o modelo do Gerenciador de recursos. Consulte [criar um computador de virtual do Windows com o monitoramento e diagnóstico usando o modelo do Gerenciador de recursos do Azure](virtual-machines-windows-extensions-diagnostics-template.md).

Para habilitar a extensão de diagnóstico em uma máquina virtual existente que foi criado por meio do modelo de implantação do Gerenciador de recursos, você pode usar o cmdlet [Set-AzureRMVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt603499.aspx) PowerShell conforme mostrado abaixo.


    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* é o caminho para o arquivo que contém a configuração de diagnóstico em XML, conforme descrito no [exemplo](#sample-diagnostics-configuration) abaixo.  

Se o arquivo de configuração de diagnóstico Especifica um elemento **StorageAccount** com um nome de conta de armazenamento, o script *Set-AzureRMVMDiagnosticsExtension* definirá automaticamente a extensão de diagnóstico para enviar dados de diagnóstico para essa conta de armazenamento. Para este trabalho, a conta de armazenamento precisa estar na mesma assinatura como a máquina virtual.

Se nenhuma **StorageAccount** foi especificada na configuração de diagnóstico, você precisa passar no parâmetro *StorageAccountName* para o cmdlet. Se o parâmetro *StorageAccountName* for especificado, o cmdlet sempre usará a conta de armazenamento especificado no parâmetro e não o que é especificado no arquivo de configuração de diagnóstico.

Se a conta de armazenamento de diagnóstico estiver em uma assinatura diferente da máquina virtual, você precisa passar explicitamente nos parâmetros *StorageAccountName* e *StorageAccountKey* para o cmdlet. O parâmetro *StorageAccountKey* não é necessário quando a conta de armazenamento de diagnóstico está na mesma assinatura, como o cmdlet pode automaticamente da consulta e defina o valor de chave ao ativar a extensão de diagnóstico. Entretanto, se a conta de armazenamento de diagnóstico estiver em uma assinatura diferente, o cmdlet talvez não seja possível obter a chave automaticamente, e você precisa especificar explicitamente a chave por meio do parâmetro *StorageAccountKey* .  

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Quando a extensão de diagnóstico estiver ativada em uma máquina virtual, você pode obter as configurações atuais usando o cmdlet [Get-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603678.aspx) .

    Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

O cmdlet retorna *PublicSettings*, que contém a configuração do XML em um formato codificado na Base 64. Para ler o XML, você precisa decodificá-lo.

    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

O cmdlet [Remover AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603782.aspx) pode ser usado para remover a extensão de diagnóstico da máquina virtual.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Habilitar a extensão de diagnóstico, se você usar o modelo de implantação clássico

Você pode usar o cmdlet [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) para habilitar uma extensão de diagnóstico em uma máquina virtual que você criar através do modelo de implantação clássico. O exemplo a seguir mostra como criar uma nova VM por meio do modelo de implantação clássico com a extensão de diagnóstico habilitada.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

Para habilitar a extensão de diagnóstico em uma máquina virtual existente que foi criado por meio do modelo clássico de implantação, use o cmdlet [Get-AzureVM](https://msdn.microsoft.com/library/mt589152.aspx) para obter a configuração de máquina virtual. Em seguida, atualize a configuração de máquina virtual para incluir a extensão de diagnóstico usando o cmdlet [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) . Finalmente, aplique a configuração atualizada para a máquina virtual usando [AzureVM de atualização](https://msdn.microsoft.com/library/mt589121.aspx).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Exemplo de configuração de diagnóstico

O seguinte XML pode ser usado para a configuração de público de diagnóstico com os scripts acima. Esta configuração de exemplo será transferido vários contadores de desempenho para a conta de armazenamento de diagnóstico, além de erros do aplicativo, segurança e canais de sistema em logs de eventos do Windows e quaisquer erros dos logs de infraestrutura de diagnóstico.

A configuração precisa ser atualizado para incluir o seguinte:

- O atributo de *identificação de recurso* do elemento **métricas** precisa ser atualizado com a identificação do recurso para a máquina virtual.
    - A identificação do recurso pode ser construída usando o seguinte padrão: "/resourceGroups//assinaturas / {*ID da assinatura para a assinatura com a máquina virtual*} {*o nome de resourcegroup para a máquina virtual*} / providers/Microsoft.Compute/virtualMachines/ {*o nome de máquina virtual*}".
    - Por exemplo, se a ID de assinatura para a assinatura à qual a máquina virtual está sendo executado é **11111111-1111-1111-1111-111111111111**, o nome do grupo de recursos do grupo de recursos é **MyResourceGroup**e o nome de máquina virtual é **MyWindowsVM**, em seguida, o valor de *identificação de recurso* seria:

        ```
        <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
        ```

    - Para obter mais informações sobre como métricas são geradas com base na configuração de contadores e métricas de desempenho, consulte a [tabela de métricas de diagnóstico do Azure no armazenamento](virtual-machines-windows-extensions-diagnostics-template.md#wadmetrics-tables-in-storage).

- O elemento **StorageAccount** precisa ser atualizado com o nome da conta de armazenamento de diagnóstico.

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Próximas etapas
- Para obter diretrizes adicionais sobre como usar o recurso de diagnóstico do Azure e outras técnicas para solucionar problemas, consulte [Habilitando diagnóstico nos serviços de nuvem do Azure e máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md).
- [Esquema de configurações do diagnóstico](https://msdn.microsoft.com/library/azure/mt634524.aspx) explica as diversas opções de configurações de XML para a extensão de diagnóstico.
