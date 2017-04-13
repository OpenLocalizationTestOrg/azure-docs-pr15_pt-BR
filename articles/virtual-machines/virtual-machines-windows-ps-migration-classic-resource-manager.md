<properties
    pageTitle="Migrar para o gerente de recursos com o PowerShell | Microsoft Azure"
    description="Este artigo explica a migração com suporte plataforma de recursos de IaaS do clássico ao Gerenciador de recursos do Azure usando os comandos do PowerShell do Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="cynthn"/>

# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migrar os recursos de IaaS do clássico ao Gerenciador de recursos do Azure usando o PowerShell do Azure

Estas etapas mostram como usar comandos do PowerShell do Azure para migrar infraestrutura como um serviço (IaaS) recursos do modelo clássico de implantação para o modelo de implantação do Gerenciador de recursos do Azure. 

Se desejar, você também pode migrar recursos usando a [Interface de linha de comando do Azure (Azure CLI)](virtual-machines-linux-cli-migration-classic-resource-manager.md).

- Para o plano de fundo em cenários de migração compatíveis, consulte [plataforma suportada migração de recursos de IaaS do clássico ao Gerenciador de recursos do Azure](virtual-machines-windows-migration-classic-resource-manager.md). 
- Para obter orientações detalhadas e instruções passo a passo de migração, consulte [técnico detalhamento da plataforma suportada migração do clássico ao Gerenciador de recursos do Azure](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md).

## <a name="step-1-plan-for-migration"></a>Etapa 1: Planejar para migração

Aqui estão algumas práticas recomendadas que recomendamos como você avaliar migrando recursos de IaaS do clássico para o Gerenciador de recursos:

- Leia as [configurações e recursos compatíveis com e sem suporte](virtual-machines-windows-migration-classic-resource-manager.md). Se você tiver máquinas virtuais que usam configurações sem suporte ou recursos, recomendamos que você aguarde o suporte de configuração/recurso a ser anunciado. Como alternativa, se ele atende às suas necessidades, remova esse recurso ou tira essa configuração para habilitar a migração.
-   Se você tiver automatizado scripts que implantar sua infraestrutura e aplicativos hoje, tente criar uma configuração de teste semelhante usando esses scripts para a migração. Como alternativa, você pode configurar os ambientes de amostra usando o portal do Azure.

>[AZURE.IMPORTANT]Gateways de rede virtual (-to-site, rota expressa do Azure, gateway de aplicativos, ponto-a-site) não são suportados no momento para a migração do clássico ao Gerenciador de recursos. Para migrar uma rede virtual clássica com um gateway, remova o gateway antes de executar uma operação de confirmação para mover a rede (você pode executar a etapa preparar sem excluir o gateway). Depois de concluir a migração, reconecte o gateway no Gerenciador de recursos do Azure.

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Etapa 2: Instalar a versão mais recente do PowerShell do Azure

Há duas opções principais para instalar o Azure PowerShell: [Galeria do PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) ou [Web Platform Installer (WebPI)](http://aka.ms/webpi-azps). WebPI recebe atualizações mensais. Galeria de PowerShell recebe atualizações em uma base contínua. Este artigo baseia-se no Azure PowerShell versão 2.1.0.

Para obter instruções de instalação, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

<br>
## <a name="step-3-set-your-subscription-and-sign-up-for-migration"></a>Etapa 3: Configurar sua assinatura e inscrever-se para a migração

Primeiro, inicie um prompt de PowerShell. Para a migração, você precisa configurar o ambiente para ambas as clássico e Gerenciador de recursos.

Entre em sua conta para o modelo do Gerenciador de recursos.

```powershell
    Login-AzureRmAccount
```

Obtenha as assinaturas disponíveis usando o seguinte comando:

```powershell
    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName
```

Defina sua assinatura do Azure para a sessão atual. Este exemplo define o nome da assinatura padrão para **Minha assinatura do Azure**. Substitua o nome da assinatura exemplo seu próprio. 

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

>[AZURE.NOTE] Registro é uma etapa única, mas você deve fazer isso vez antes de tentar migração. Sem registrar, você veja a seguinte mensagem de erro: 

>   *BadRequest: A assinatura não está registrada para a migração.* 

Registrar-se com o provedor de recursos de migração, usando o seguinte comando:

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Aguarde cinco minutos para o registro concluir. Você pode verificar o status da aprovação usando o comando a seguir:

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Verifique se RegistrationState está `Registered` antes de continuar. 

Agora entrar em sua conta para o modelo clássico.

```powershell
    Add-AzureAccount
```

Obtenha as assinaturas disponíveis usando o seguinte comando:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Defina sua assinatura do Azure para a sessão atual. Este exemplo define a assinatura padrão para **Minha assinatura do Azure**. Substitua o nome da assinatura exemplo seu próprio. 

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-4-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Etapa 4: Verifique se que você tem bastante cores de máquina de Virtual do Azure Gerenciador de recursos na região Azure da sua implantação atual ou VNET

Você pode usar o seguinte comando do PowerShell para verificar o número atual de cores que você tem no Gerenciador de recursos do Azure. Para saber mais sobre cotas de núcleo, consulte [limites e o Gerenciador de recursos do Azure](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager). 

Este exemplo verifica a disponibilidade na região **Oeste EUA** . Substitua o nome de região de exemplo pelo seu próprio. 

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Etapa 5: Executar comandos para migrar seus recursos de IaaS

>[AZURE.NOTE] Todas as operações descritas aqui são idempotente. Se você tiver um problema diferente de um recurso sem suporte ou um erro de configuração, recomendamos que você repita a preparar, anular ou confirmar a operação. A plataforma tentará a ação novamente.

### <a name="migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Migrar máquinas virtuais em um serviço de nuvem (não em uma rede virtual)

Obtenha a lista de serviços de nuvem usando o seguinte comando e, em seguida, escolha o serviço de nuvem que você deseja migrar. Se as VMs no serviço de nuvem estão em uma rede virtual ou se eles têm funções da web ou de trabalho, o comando retorna uma mensagem de erro.

```powershell
    Get-AzureService | ft Servicename
```

Obter o nome de implantação para o serviço de nuvem. Neste exemplo, o nome do serviço é **Meu serviço**. Substitua o nome do serviço de exemplo pelo seu próprio nome de serviço. 

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Prepare as máquinas virtuais no serviço de nuvem para a migração. Você tem duas opções à sua escolha.

* **Opção 1. Migrar as VMs para uma rede virtual criado de plataforma**

    Primeiro, valide se você pode migrar o serviço de nuvem usando os seguintes comandos:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    O comando anterior exibe avisos e erros que bloqueiam a migração. Se a validação for bem-sucedida, em seguida, você pode mover na etapa de **preparação** :

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```

* **Opção 2. Migrar para uma rede virtual existente no modelo de implantação do Gerenciador de recursos**

    Este exemplo define o nome do grupo de recursos **myResourceGroup**, o nome de rede virtual para **myVirtualNetwork** e o nome de sub-rede para **mySubNet**. Substitua os nomes no exemplo com os nomes dos seus próprios recursos.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Primeiro, valide se você pode migrar o serviço de nuvem usando o seguinte comando:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    O comando anterior exibe avisos e erros que bloqueiam a migração. Se validação não for bem-sucedido, você pode continuar com a seguinte etapa de preparação:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```
    

Após a operação de preparação êxito com qualquer uma das opções anteriores, consulte o estado de migração das VMs. Garantir que eles estejam no `Prepared` estado.

Este exemplo define o nome de máquina virtual para **myVM**. Substitua o nome de exemplo pelo seu próprio nome de máquina virtual.

    ```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
    ```

Verifique a configuração para os recursos preparados usando o PowerShell ou o portal do Azure. Se você não estiver pronto para a migração e quiser voltar para o estado antigo, use o seguinte comando:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Se a configuração preparada gostar, você pode Avançar e confirme os recursos usando o seguinte comando:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="migrate-virtual-machines-in-a-virtual-network"></a>Migrar máquinas virtuais em uma rede virtual

Para migrar máquinas virtuais em uma rede virtual, migrar a rede. As máquinas virtuais migrar automaticamente com a rede. Escolha a rede virtual que você deseja migrar. 

Este exemplo define o nome de rede virtual para **myVnet**. Substitua o nome de rede virtual de exemplo pelo seu próprio. 

```powershell
    $vnetName = "myVnet"
```

>[AZURE.NOTE] Se a rede virtual contiver web ou funções de trabalho ou VMs com configurações sem suporte, receberá uma mensagem de erro de validação.

Primeiro, valide se você pode migrar a rede virtual usando o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

O comando anterior exibe avisos e erros que bloqueiam a migração. Se validação não for bem-sucedido, você pode continuar com a seguinte etapa de preparação:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Verifique a configuração para as máquinas virtuais preparadas usando o PowerShell do Azure ou o portal do Azure. Se você não estiver pronto para a migração e quiser voltar para o estado antigo, use o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Se a configuração preparada gostar, você pode Avançar e confirme os recursos usando o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="migrate-a-storage-account"></a>Migrar uma conta de armazenamento

Quando terminar de migrar as máquinas virtuais, recomendamos que você migra as contas de armazenamento.

Prepare cada conta de armazenamento para a migração usando o comando a seguir. Neste exemplo, o nome da conta de armazenamento é **myStorageAccount**. Substitua o nome de exemplo com o nome de sua própria conta de armazenamento. 

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

Verifique a configuração da conta de armazenamento preparado usando o PowerShell do Azure ou o portal do Azure. Se você não estiver pronto para a migração e quiser voltar para o estado antigo, use o seguinte comando:

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

Se a configuração preparada gostar, você pode Avançar e confirme os recursos usando o seguinte comando:

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre migração, consulte [plataforma suportada migração de recursos de IaaS do clássico ao Gerenciador de recursos do Azure](virtual-machines-windows-migration-classic-resource-manager.md).
- Para migrar recursos de rede adicionais ao Gerenciador de recursos usando o PowerShell do Azure, use etapas semelhantes com [AzureNetworkSecurityGroup mover](https://msdn.microsoft.com/library/mt786729.aspx), [Mover AzureReservedIP](https://msdn.microsoft.com/library/mt786752.aspx)e [Move-AzureRouteTable](https://msdn.microsoft.com/library/mt786718.aspx).
- Para scripts de código-fonte aberto que você pode usar para migrar Azure recursos do clássico ao Gerenciador de recursos, consulte [Ferramentas de comunidade para a migração para a migração do Gerenciador de recursos do Azure](virtual-machines-windows-migration-scripts.md)
