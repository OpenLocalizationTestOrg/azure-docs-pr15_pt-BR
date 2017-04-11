<properties
    pageTitle="Gerenciar VMs em um conjunto de escala de máquina Virtual | Microsoft Azure"
    description="Gerencie máquinas virtuais em uma escala de máquina virtual definiu usando o PowerShell do Azure."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>Gerenciar máquinas virtuais em um conjunto de escala de máquina virtual

Use as tarefas neste artigo para gerenciar máquinas virtuais em seu conjunto de escala de máquina virtual.

A maioria das tarefas que envolvem Gerenciando uma máquina virtual em um conjunto de escala exige que você saiba a ID de instância da máquina que você deseja gerenciar. Você pode usar o [Gerenciador de recursos do Azure](https://resources.azure.com) para localizar a identificação de instância de uma máquina virtual em um conjunto de escala. Você também pode usar Resource Explorer para verificar o status das tarefas que você terminar.

Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do PowerShell do Azure, selecionando sua assinatura e entrar sua conta.

## <a name="display-information-about-a-scale-set"></a>Exibir informações sobre um conjunto de escala

Você pode obter informações gerais sobre um conjunto de escala, que também é conhecido como o modo de exibição de instância. Ou, você pode obter informações mais específicas, como informações sobre os recursos do conjunto de escala.

Substitua os valores entre aspas o nome ou o grupo de recursos e escala definir e, em seguida, execute o comando:

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Ela retorna algo parecido com isto:

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded
    
Substitua os valores entre aspas com o nome do seu conjunto de grupo e a escala do recurso. Substituir *#* com o identificador de instância da máquina virtual que você deseja obter informações sobre e, em seguida, executá-lo:

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
Ela retorna algo parecido com este exemplo:

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded
        
## <a name="start-a-virtual-machine-in-a-scale-set"></a>Iniciar uma máquina virtual em um conjunto de escala

Substitua os valores entre aspas com o nome do seu conjunto de grupo e a escala do recurso. Substituir *#* com o identificador da máquina virtual que você deseja iniciar e, em seguida, executá-lo:

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

No Gerenciador de recursos, podemos ver o status da instância está **em execução**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

Você pode iniciar todas as máquinas virtuais em escala de definir não usando o parâmetro - InstanceId.
    
## <a name="stop-a-virtual-machine-in-a-scale-set"></a>Interromper uma máquina virtual em um conjunto de escala

Substitua os valores entre aspas com o nome do seu conjunto de grupo e a escala do recurso. Substituir *#* com o identificador da máquina virtual que você deseja interromper e, em seguida, executá-lo:

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

No Gerenciador de recursos, podemos ver o status da instância é **desalocada**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]
    
Para interromper uma máquina virtual e não desalocá-lo, use o parâmetro - StayProvisioned. Você pode parar todas as máquinas virtuais do conjunto não usando o parâmetro - InstanceId.
    
## <a name="restart-a-virtual-machine-in-a-scale-set"></a>Reiniciar uma máquina virtual em um conjunto de escala

Substitua os valores entre aspas com o nome do seu grupo de recursos e o conjunto de escala. Substituir *#* com o identificador da máquina virtual que você deseja reiniciar e, em seguida, executá-lo:

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
    
Você pode reiniciar todas as máquinas virtuais do conjunto não usando o parâmetro - InstanceId.

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>Remover uma máquina virtual de um conjunto de escala

Substitua os valores entre aspas com o nome do seu grupo de recursos e o conjunto de escala. Substituir *#* com o identificador da máquina virtual que você deseja remover e, em seguida, executá-lo:  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Você pode remover o conjunto de escala de máquina virtual ao mesmo tempo não usando o parâmetro - InstanceId.

## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de escala

Você pode adicionar ou remover máquinas virtuais alterando a capacidade do conjunto. Obter o conjunto de escala que você deseja alterar, definir a capacidade que você deseja que ele seja e, em seguida, atualize a escala com a nova capacidade. Nesses comandos, substitua os valores entre aspas o nome do seu grupo de recursos e o conjunto de escala.

  $vmss = get-AzureRmVmss - ResourceGroupName "nome do grupo do recurso" - VMScaleSetName "nome do conjunto de escala" $vmss.sku.capacity = 5 Update-AzureRmVmss - ResourceGroupName "grupo nome do recurso"-nome "nome do conjunto de escala" - VirtualMachineScaleSet $vmss 

Se você estiver removendo máquinas virtuais do conjunto de escala, as máquinas virtuais com as identificações altos são removidas primeiro.
