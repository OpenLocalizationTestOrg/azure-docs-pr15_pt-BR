<properties
    pageTitle="Escala de máquina Virtual do Windows de escala automática define | Microsoft Azure"
    description="Configurar autoscaling para um Windows Máquina Virtual escala conjunto usando o PowerShell do Azure"
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

# <a name="automatically-scale-machines-in-a-virtual-machine-scale-set"></a>Dimensionar automaticamente máquinas em um conjunto de escala de máquina virtual

Conjuntos de escala de máquina virtual tornam mais fácil para você implantar e gerenciar idênticas máquinas virtuais como um conjunto. Conjuntos de escala fornecem uma camada de computação altamente flexível e personalizável para aplicativos de hyperscale e dão suporte a imagens de plataforma do Windows, imagens da plataforma Linux, imagens personalizadas e extensões. Para obter mais informações sobre conjuntos de escala, consulte [Conjuntos de escala de máquina Virtual](virtual-machine-scale-sets-overview.md).

Este tutorial mostra como criar um conjunto de escala de máquinas virtuais do Windows e dimensionar automaticamente as máquinas no conjunto. Criar a escala de configurar e configurar o dimensionamento criando um modelo do Gerenciador de recursos do Azure e implantá-lo usando o PowerShell do Azure. Para obter mais informações sobre modelos, consulte [modelos de coautoria Gerenciador de recursos do Azure](../resource-group-authoring-templates.md). Para saber mais sobre o dimensionamento automático dos conjuntos de escala, consulte o [dimensionamento automático e conjuntos de escala de máquina Virtual](virtual-machine-scale-sets-autoscale-overview.md).

Neste artigo, você pode implantar as extensões e os recursos a seguir:

- Microsoft.Storage/storageAccounts
- Microsoft.Network/virtualNetworks
- Microsoft.Network/publicIPAddresses
- Microsoft.Network/loadBalancers
- Microsoft.Network/networkInterfaces
- Microsoft.Compute/virtualMachines
- Microsoft.Compute/virtualMachineScaleSets
- Microsoft.Insights.VMDiagnosticsSettings
- Microsoft.Insights/autoscaleSettings

Para obter mais informações sobre recursos do Gerenciador de recursos, consulte [computação Azure, rede, fornecedores de armazenamento e em Gerenciador de recursos do Azure](../virtual-machines/virtual-machines-windows-compare-deployment-models.md).

## <a name="step-1-install-azure-powershell"></a>Etapa 1: Instalar o PowerShell Azure

Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do PowerShell do Azure, selecionando sua assinatura e entrar no Azure.

## <a name="step-2-create-a-resource-group-and-a-storage-account"></a>Etapa 2: Criar um grupo de recursos e uma conta de armazenamento

1. **Criar um grupo de recursos** – todos os recursos devem ser implantados para um grupo de recursos. Use [AzureRmResourceGroup de novo](https://msdn.microsoft.com/library/mt603739.aspx) para criar um grupo de recursos denominado **vmsstestrg1**.

2. **Criar uma conta de armazenamento** – esta conta de armazenamento é onde o modelo é armazenado. Use [AzureRmStorageAccount de novo](https://msdn.microsoft.com/library/mt607148.aspx) para criar uma conta de armazenamento denominada **vmsstestsa**.

## <a name="step-3-create-the-template"></a>Etapa 3: Criar o modelo
Um modelo do Gerenciador de recursos do Azure possibilita para implantar e gerenciar recursos Azure juntos usando uma descrição de JSON dos recursos e parâmetros de implantação associado.

1. Em seu editor de favorito, crie o arquivo C:\VMSSTemplate.json e adicione a estrutura JSON inicial para o modelo de suporte.

        {
          "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
          ]
        }

2. Parâmetros nem sempre são necessários, mas eles oferecem uma maneira de valores de entrada quando o modelo é implantado. Adicione esses parâmetros sob o elemento pai de parâmetros que você adicionou ao modelo.

        "vmName": { "type": "string" },
        "vmSSName": { "type": "string" },
        "instanceCount": { "type": "string" },
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" },
        "resourcePrefix": { "type": "string" }

    - Definir um nome para a máquina virtual separado que é usado para acessar as máquinas na escala.
    - O nome da conta de armazenamento onde o modelo é armazenado.
    - O número de máquinas virtuais para criar inicialmente no conjunto de escala.
    - O nome e a senha da conta de administrador nas máquinas virtuais.
    - Definir um prefixo de nome para os recursos que são criados para oferecer suporte a escala.

3. Variáveis podem ser usadas em um modelo para especificar valores que podem ser alteradas com frequência ou que precisam ser criado a partir de uma combinação de valores de parâmetro. Adicione essas variáveis sob o elemento pai de variáveis que você adicionou ao modelo.

        "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
        "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
        "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
        "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
        "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
        "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
        "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
        "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
        "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
        "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
          "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
        "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

  - Nomes DNS que são usados pelas interfaces de rede.
    - Os nomes de endereço IP e prefixos para a rede virtual e sub-redes.
    - Os nomes e os identificadores de rede virtual, carregar balanceador e interfaces de rede.
    - Nomes de conta de armazenamento para as contas associadas as máquinas na escala de definir.
    - Configurações para a extensão de diagnóstico que está instalado nas máquinas virtuais. Para obter mais informações sobre a extensão de diagnóstico, consulte [criar uma máquina Virtual do Windows com o monitoramento e diagnóstico usando o modelo de Gerenciador de recursos do Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).

4. Adicione o recurso de conta de armazenamento sob o elemento pai de recursos que você adicionou ao modelo. Este modelo usa um loop para criar as contas de armazenamento de cinco recomendado onde são armazenados os discos do sistema operacional e dados de diagnóstico. Este conjunto de contas pode oferecer suporte a até 100 máquinas virtuais em um conjunto de escala, que é o máximo atual. Cada conta de armazenamento denomina-se com um designador de letra que foi definido nas variáveis combinadas com o prefixo que você fornecer nos parâmetros para o modelo.

        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
          "apiVersion": "2015-06-15",
          "copy": {
            "name": "storageLoop",
            "count": 5
          },
          "location": "[resourceGroup().location]",
          "properties": { "accountType": "Standard_LRS" }
        },

5. Adicione o recurso de rede virtual. Para obter mais informações, consulte o [Provedor de recursos de rede](../virtual-network/resource-groups-networking.md).

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[variables('virtualNetworkName')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "subnet1",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },

6. Adicione os recursos de endereço IP públicos que são usados pelo Balanceador de carga e interface de rede.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP1')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName1')]"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP2')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName2')]"
            }
          }
        },

7. Adicione o recurso de Balanceador de carga que é usado pelo conjunto de escala. Para obter mais informações, consulte [Suporte de Gerenciador de recursos do Azure para balanceador de carga](../load-balancer/load-balancer-arm.md).

        {
          "apiVersion": "2015-06-15",
          "name": "[variables('loadBalancerName')]",
          "type": "Microsoft.Network/loadBalancers",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
          ],
          "properties": {
            "frontendIPConfigurations": [
              {
                "name": "loadBalancerFrontEnd",
                "properties": {
                  "publicIPAddress": {
                    "id": "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
                  }
                }
              }
            ],
            "backendAddressPools": [ { "name": "bepool1" } ],
            "inboundNatPools": [
              {
                "name": "natpool1",
                "properties": {
                  "frontendIPConfiguration": {
                    "id": "[variables('frontEndIPConfigID')]"
                  },
                  "protocol": "tcp",
                  "frontendPortRangeStart": 50000,
                  "frontendPortRangeEnd": 50500,
                  "backendPort": 3389
                }
              }
            ]
          }
        },

8. Adicione o recurso de interface de rede que é usado pela máquina virtual separada. Como máquinas em um conjunto de escala não estão acessíveis por meio de um endereço IP público, uma outra máquina virtual é criada na mesma rede virtual acessem remotamente as máquinas.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[variables('nicName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
            "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
                  },
                  "subnet": {
                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                  }
                }
              }
            ]
          }
        },

9. Adicione a máquina virtual separada na mesma rede como o conjunto de escala.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[parameters('vmName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "storageLoop",
            "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_A1" },
            "osProfile": {
              "computername": "[parameters('vmName')]",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "[concat(parameters('resourcePrefix'), 'os1')]",
                "vhd": {
                  "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"        
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                }
              ]
            }
          }
        },

10. Adicione que a escala de máquina virtual definir o recurso e especifique a extensão de diagnóstico que esteja instalada em todas as máquinas virtuais do conjunto de escala. Muitas das configurações para esse recurso são semelhantes com o recurso de máquina virtual. As principais diferenças são o elemento de capacidade que especifica o número de máquinas virtuais no conjunto de escala e upgradePolicy que especifica como as atualizações são feitas em máquinas virtuais. O conjunto de escala não é criado até que todas as contas de armazenamento são criadas conforme especificado com o elemento de dependsOn.

            {
              "type": "Microsoft.Compute/virtualMachineScaleSets",
              "apiVersion": "2016-03-30",
              "name": "[parameters('vmSSName')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "storageLoop",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
              ],
              "sku": {
                "name": "Standard_A1",
                "tier": "Standard",
                "capacity": "[parameters('instanceCount')]"
              },
              "properties": {
                "upgradePolicy": {
                  "mode": "Manual"
                },
                "virtualMachineProfile": {
                  "storageProfile": {
                    "osDisk": {
                      "vhdContainers": [
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4], '.blob.core.windows.net/vhds')]"
                      ],
                      "name": "vmssosdisk",
                      "caching": "ReadOnly",
                      "createOption": "FromImage"
                    },
                    "imageReference": {
                      "publisher": "MicrosoftWindowsServer",
                      "offer": "WindowsServer",
                      "sku": "2012-R2-Datacenter",
                      "version": "latest"
                    }
                  },
                  "osProfile": {
                    "computerNamePrefix": "[parameters('vmSSName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                  },
                  "networkProfile": {
                    "networkInterfaceConfigurations": [
                      {
                        "name": "networkconfig1",
                        "properties": {
                          "primary": "true",
                          "ipConfigurations": [
                            {
                              "name": "ip1",
                              "properties": {
                                "subnet": {
                                  "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                                },
                                "loadBalancerBackendAddressPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                                  }
                                ],
                                "loadBalancerInboundNatPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                                  }
                                ]
                              }
                            }
                          ]
                        }
                      }
                    ]
                  },
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
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-06-15').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                          }
                        }
                      }
                    ]
                  }
                }
              }
            },

11. Adicione o recurso de autoscaleSettings que define como o conjunto de escala ajusta com base no uso do processador nas máquinas o conjunto de escala.

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
                          "metricName": "\\Processor(_Total)\\% Processor Time",
                          "metricNamespace": "",
                          "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                          "timeGrain": "PT1M",
                          "statistic": "Average",
                          "timeWindow": "PT5M",
                          "timeAggregation": "Average",
                          "operator": "GreaterThan",
                          "threshold": 50.0
                        },
                        "scaleAction": {
                          "direction": "Increase",
                          "type": "ChangeCount",
                          "value": "1",
                          "cooldown": "PT5M"
                        }
                      }
                    ]
                  }
                ],
                "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              }
            }

    Para este tutorial, esses valores são importantes:

    - **metricName** - esse valor é a mesma que o contador de desempenho que definimos na variável wadperfcounter. Usando essa variável, a extensão de diagnóstico coleta a **( total)\% tempo de processador** contador.
    - **metricResourceUri** - esse valor é o identificador de recursos do conjunto de escala de máquina virtual.
    - **timeGrain** – esse valor é o detalhamento de métricas são coletados. Neste modelo, ela é definida como um minuto.
    - **estatística** – esse valor determina como as métricas são combinadas para acomodar a ação de dimensionamento automática. Os valores possíveis são: média, Min, Max. Neste modelo, o uso da CPU total médio das máquinas virtuais é coletado.
    - **timeWindow** – esse valor é o intervalo de tempo em que os dados de instância são coletados. Ele deve estar entre 5 minutos e 12 horas.
    - **timeAggregation** – este valor determina como os dados coletados devem ser combinados ao longo do tempo. O valor padrão é média. Os valores possíveis são: média, mínimo, máximo, última, Total, contagem.
    - **operador** – esse valor é o operador que é usado para comparar os dados de métrica e o limite. Os valores possíveis são: for igual a, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
    - **limite** – este é o valor que aciona a ação de escala. Neste modelo, máquinas são adicionadas à escala definida quando o uso da CPU médio entre máquinas no conjunto está mais de 50%.
    - **direção** – esse valor determina a ação que é executada quando o valor limite é alcançado. Os valores possíveis são aumentar ou reduzir. Neste modelo, o número de máquinas virtuais do conjunto de escala é aumentado se o limite for mais de 50% na janela de tempo definido.
    - **tipo** – esse valor é o tipo de ação que deve ocorrer e deve ser definida como ChangeCount.
    - **valor** – esse valor é o número de máquinas virtuais que são adicionados ou removidos do conjunto de escala. Este valor deve ser 1 ou maior. O valor padrão é 1. Neste modelo, o número de máquinas na escala de definir aumenta por 1 quando o limite é atendido.
    - **cooldown** – esse valor é a quantidade de tempo de espera desde a última ação de dimensionamento antes que a próxima ação ocorra. Este valor deve estar entre um minuto e uma semana.

12. Salve o arquivo de modelo.    

## <a name="step-4-upload-the-template-to-storage"></a>Etapa 4: Carregar o modelo de armazenamento

O modelo pode ser carregado desde que você souber o nome e a chave primária da conta de armazenamento que você criou na etapa 1.

1.  Na janela do Microsoft Azure PowerShell, defina uma variável que especifica o nome da conta de armazenamento que você criou na etapa 1.

            $storageAccountName = "vmstestsa"

2.  Defina uma variável que especifica a chave primária da conta de armazenamento.

            $storageAccountKey = "<primary-account-key>"

    Você pode obter essa chave clicando no ícone de chave ao exibir o recurso de conta de armazenamento no portal do Azure.

3.  Crie o objeto de contexto de conta de armazenamento que é usado para validar operações com a conta de armazenamento.

            $ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

4.  Crie o contêiner para armazenar o modelo.

            $containerName = "templates"
            New-AzureStorageContainer -Name $containerName -Context $ctx  -Permission Blob

5.  Carregar o arquivo de modelo para o novo contêiner.

            $blobName = "VMSSTemplate.json"
            $fileName = "C:\" + $BlobName
            Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob  $blobName -Context $ctx

## <a name="step-5-deploy-the-template"></a>Etapa 5: Implantar o modelo

Agora que você criou o modelo, você pode começar implantando os recursos. Use este comando para iniciar o processo:

    New-AzureRmResourceGroupDeployment -Name "vmsstestdp1" -ResourceGroupName "vmsstestrg1" -TemplateUri "https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json"

Quando você pressiona insira, você é solicitado a fornecer valores para as variáveis atribuída a você. Forneça estes valores:

    vmName: vmsstestvm1
      vmSSName: vmsstest1
      instanceCount: 5
      adminUserName: vmadmin1
      adminPassword: VMpass1
      resourcePrefix: vmsstest

Ele deve levar cerca de 15 minutos para todos os recursos para ser implantado com êxito.

>[AZURE.NOTE] Você também pode usar a capacidade do portal para implantar os recursos. Use este link: "https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>"

## <a name="step-6-monitor-resources"></a>Etapa 6: Recursos de Monitor

Você pode obter algumas informações sobre conjuntos de escala de máquina virtual usando esses métodos:

 - Portal do Azure - no momento você pode obter uma quantidade limitada de informações usando o portal.
 - O [Gerenciador de recursos do Azure](https://resources.azure.com/) - esta ferramenta é o melhor para explorar o estado atual do seu conjunto de escala. Siga este caminho e você verá a exibição da instância do conjunto de escala que você criou:

        subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - PowerShell Azure - Use este comando para obter algumas informações:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

        Or

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView

 - Conectar-se à máquina virtual separada exatamente como faria com qualquer outro computador e, em seguida, você pode acessar remotamente máquinas virtuais a escala conjunto para monitorar processos individuais.

>[AZURE.NOTE] Uma API REST concluída para obter informações sobre conjuntos de escala podem ser encontrada na [Máquina Virtual define de escala](https://msdn.microsoft.com/library/mt589023.aspx)

## <a name="step-7-remove-the-resources"></a>Etapa 7: Remover os recursos

Porque cobrado para recursos usados no Azure, sempre é uma boa prática excluir recursos que não são mais necessárias. Você não precisa excluir cada recurso separadamente de um grupo de recursos. Você pode excluir o grupo de recursos e todos os seus recursos são excluídos automaticamente.

    Remove-AzureRmResourceGroup -Name vmsstestrg1

Se você quiser manter seu grupo de recursos, você pode excluir a escala Defina somente.

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name"

## <a name="next-steps"></a>Próximas etapas

- Gerencie o conjunto de escala que você acabou de criar usando as informações em [gerenciar máquinas virtuais em um conjunto de escala de máquina Virtual](virtual-machine-scale-sets-windows-manage.md).
- Saiba mais sobre o dimensionamento vertical revisando [escala Vertical automática com conjuntos de escala de máquina Virtual](virtual-machine-scale-sets-vertical-scale-reprovision.md)
- Encontrar exemplos do Monitor do Azure recursos no [Azure Monitor PowerShell rápido iniciar amostras](../monitoring-and-diagnostics/insights-powershell-samples.md) de monitoramento
- Aprender sobre recursos de notificação em [usar ações de escala automática para enviar email e webhook notificações de alerta no Monitor do Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)
- Saiba como [logs de auditoria de uso para enviar email e webhook notificações de alerta no Monitor do Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
