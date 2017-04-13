<properties
    pageTitle="Criar uma máquina Virtual do Windows com o monitoramento e diagnóstico usando o modelo de Gerenciador de recursos do Azure | Microsoft Azure"
    description="Use um modelo de Gerenciador de recurso Azure para criar uma nova máquina virtual Windows com extensão de diagnóstico do Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="sbtron"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>

# <a name="create-a-windows-virtual-machine-with-monitoring-and-diagnostics-using-azure-resource-manager-template"></a>Criar uma máquina Virtual do Windows com o monitoramento e diagnóstico usando o modelo de Gerenciador de recursos do Azure

A extensão de diagnóstico do Azure fornece o monitoramento e os recursos de diagnóstico em um Windows base Azure máquina virtual. Você pode habilitar esses recursos na máquina virtual, incluindo a extensão como parte do modelo de Gerenciador de recurso azure. Para obter mais informações sobre incluindo qualquer extensão como parte de um modelo de máquina virtual, consulte [Criação modelos do Gerenciador de recursos do Azure com as extensões de máquina virtual](virtual-machines-windows-extensions-authoring-templates.md) . Este artigo descreve como você pode adicionar a extensão de diagnóstico do Azure para um modelo de máquina virtual do windows.  
  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Adicionar a extensão de diagnóstico do Azure para a definição de recurso de máquina virtual 

Para habilitar a extensão de diagnóstico em uma máquina Virtual do Windows, você precisa adicionar a extensão como um recurso de máquina virtual no modelo de Gerenciador do recurso.

Para um gerente de recursos simples com base Máquina Virtual adicione a configuração de extensão no conjunto de *recursos* para a máquina Virtual: 

    "resources": [
                {
                    "name": "Microsoft.Insights.VMDiagnosticsSettings",
                    "type": "extensions",
                    "location": "[resourceGroup().location]",
                    "apiVersion": "2015-06-15",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
                    ],
                    "tags": {
                        "displayName": "AzureDiagnostics"
                    },
                    "properties": {
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "type": "IaaSDiagnostics",
                        "typeHandlerVersion": "1.5",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
                        },
                        "protectedSettings": {
                            "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                        }
                    }
                }
            ]


Outra convenção comum é adicionar a configuração de extensão no nó raiz recursos do modelo em vez de defini-las em nó de recursos da máquina virtual. Com essa abordagem, você precisa especificar explicitamente uma relação hierárquica entre a extensão e a máquina virtual com os valores de *nome* e *tipo* . Por exemplo: 
  
    "name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
    "type": "Microsoft.Compute/virtualMachines/extensions",

A extensão está sempre associada a máquina virtual, você pode tanto diretamente definir o nó de recurso da máquina virtual diretamente ou defini-la no nível de base e usar a convenção de nomenclatura hierárquica para associá-la a máquina virtual.

Para conjuntos de escala de máquina Virtual a configuração de extensões é especificada na propriedade *extensionProfile* da *VirtualMachineProfile*.
   
A propriedade *publisher* com o valor de **Microsoft.Azure.Diagnostics** e a propriedade de *tipo* com o valor de **IaaSDiagnostics** identifica a extensão do diagnóstico do Azure.

O valor da propriedade *name* pode ser usado para consultar a extensão no grupo de recursos. Definindo-especificamente para **Microsoft.Insights.VMDiagnosticsSettings** ativará sejam facilmente identificados garantindo o Azure clássico portal portal que os gráficos de monitoramento aparecem corretamente no portal de clássico do Azure.

O *typeHandlerVersion* Especifica a versão da extensão que você gostaria de usar. Definir *autoUpgradeMinorVersion* versão secundária como **true** garante que você obterá a versão secundária mais recente da extensão que está disponível. É altamente recomendável que você defina sempre *autoUpgradeMinorVersion* sempre seja **verdadeira** para que você sempre chegar ao usar a extensão de diagnósticos disponíveis mais recente com todos os novos recursos e correções de bugs. 

O elemento de *configurações* contém propriedades de configurações para a extensão que podem ser definidas e ler novamente a extensão (às vezes chamada de como configuração pública). A propriedade *xmlcfg* contém configuração baseado em xml para os logs de diagnóstico, contadores de desempenho etc que serão coletadas pelo agente de diagnóstico. Consulte o [Esquema de configuração de diagnóstico](https://msdn.microsoft.com/library/azure/dn782207.aspx) para obter mais informações sobre o esquema xml em si. Uma prática comum é armazenar a configuração de xml real como uma variável no modelo do Gerenciador de recursos do Azure e, em seguida, concatenar e na Base 64 codificá-los para definir o valor *xmlcfg*. Consulte a seção sobre [variáveis de configuração de diagnóstico](#diagnostics-configuration-variables) para compreender mais sobre como armazenar xml em variáveis. A propriedade *storageAccount* Especifica o nome da conta de armazenamento para o qual os dados de diagnóstico serão transferidos. 
 
As propriedades em *protectedSettings* (às vezes chamado de configuração privada) podem ser definidas, mas não podem ser lido novamente após sendo definida. A natureza somente gravação do *protectedSettings* torna mais útil para armazenar segredos como a chave da conta de armazenamento onde os dados de diagnóstico serão gravados.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Especificar a conta de armazenamento de diagnóstico como parâmetros 

O trecho de json de extensão de diagnóstico acima assume dois parâmetros *existingdiagnosticsStorageAccountName* e *existingdiagnosticsStorageResourceGroup* para especificar a conta de armazenamento de diagnóstico onde os dados de diagnóstico serão armazenados. Especificar a conta de armazenamento de diagnóstico como um parâmetro torna mais fácil alterar a conta de armazenamento de diagnóstico em diferentes ambientes, por exemplo, talvez você queira usar uma conta de armazenamento de diagnósticos diferentes para testes e um diferente para a sua implantação de produção.  

        "existingdiagnosticsStorageAccountName": {
            "type": "string",
            "metadata": {
        "description": "The name of an existing storage account to which diagnostics data will be transfered."
            }        
        },
        "existingdiagnosticsStorageResourceGroup": {
            "type": "string",
            "metadata": {
        "description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
            }
        }

É uma prática recomendada para especificar uma conta de armazenamento de diagnóstico em um grupo de recurso diferente que o grupo de recursos para a máquina virtual. Um grupo de recursos pode ser considerado uma unidade de implantação com sua própria vida útil, uma máquina virtual pode ser implantada e redistribuída conforme novas atualizações de configurações são feitas a ele, mas talvez você queira continuar a armazenar os dados de diagnóstico na mesma conta de armazenamento nessas implantações de máquina virtual. Ter a conta de armazenamento em um recurso diferente habilita a conta de armazenamento aceitar dados de várias implantações de máquina virtual, tornando mais fácil para solucionar problemas em várias versões.

>[AZURE.NOTE] Se você criar um modelo de máquina virtual do windows do Visual Studio a conta de armazenamento padrão pode ser definida para usar a mesma conta de armazenamento onde a máquina virtual VHD é carregada. Isso é para simplificar a configuração inicial da máquina virtual. Novamente, você deve incluir o modelo para usar uma conta de armazenamento diferentes que pode ser passada como um parâmetro. 

## <a name="diagnostics-configuration-variables"></a>Variáveis de configuração de diagnóstico
 
O trecho de json de extensão de diagnóstico acima define uma variável *accountid* para simplificar Obtendo a chave da conta de armazenamento para o armazenamento de diagnósticos:   
    
    "accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"


A propriedade *xmlcfg* para a extensão de diagnóstico é definida usando diversas variáveis que são concatenados. Os valores dessas variáveis são em xml para que eles precisam ser escape corretamente ao definir as variáveis de json.

A tabela a seguir descreve o xml de configuração de diagnóstico que coleta contadores de desempenho de nível de sistema padrão junto com alguns logs de eventos do windows e logs de infraestrutura de diagnóstico. Ele foi escape e formatado corretamente para que a configuração pode ser colada diretamente na seção variáveis do seu modelo. Consulte o [Esquema de configuração de diagnóstico](https://msdn.microsoft.com/library/azure/dn782207.aspx) para obter um exemplo de legível mais humano do xml configuração.
    
        "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
        "wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
        "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"

O nó de xml de definição de métricas na configuração acima é um elemento de configuração importantes porque ela define como os contadores de desempenho definidos anteriormente no xml no nó *PerformanceCounter* serão agregados e armazenados. 

> [AZURE.IMPORTANT] Os gráficos de monitoramento e alertas de unidade essas métricas no portal do Azure.  O nó de **métricas** com a *identificação de recurso* e **MetricAggregation** deve ser incluído na configuração de diagnóstico para sua máquina virtual se você quiser ver os dados de monitoramento de máquina virtual no portal do Azure. 

Este é um exemplo de xml para definições de métricas: 

        <Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>

O atributo de *identificação de recurso* identifica exclusivamente máquina virtual na sua assinatura. Certifique-se de usar as funções subscription() e resourceGroup() para que o modelo atualiza automaticamente esses valores com base na assinatura e o grupo de recursos que você está implantando.

Se você estiver criando várias máquinas virtuais em um loop você terá que preencher o valor de *identificação de recurso* com uma função de copyIndex() para diferenciar corretamente cada máquina virtual individual. O valor de *xmlCfg* pode ser atualizado para dar suporte a isso da seguinte maneira:  

    "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 

O valor de MetricAggregation de *PT1H* e *PT1M* significam uma agregação em um minuto e uma agregação em uma hora.

## <a name="wadmetrics-tables-in-storage"></a>Tabelas de WADMetrics em armazenamento

A configuração de métricas acima gerará tabelas em sua conta de armazenamento de diagnóstico com as seguintes convenções de nomenclatura:

- **WADMetrics** : prefixo padrão para todas as tabelas de WADMetrics
- **PT1H** ou **PT1M** : significa que a tabela contém agregar dados mais de 1 hora ou 1 minuto
- **P10D** : significa que a tabela conterá dados por 10 dias quando a tabela iniciado coleta de dados
- **V2S** : constante de cadeia de caracteres
- **aaaammdd** : A data em que a tabela iniciada coleta de dados

Exemplo: *WADMetricsPT1HP10DV2S20151108* conterão dados de métricas agregados ao longo de uma hora por 10 dias iniciando em 11 de novembro de 2015    

Cada tabela WADMetrics irá conter as seguintes colunas:

- **PartitionKey**: O partitionkey foi criada com base no valor *resourceID* para identificar exclusivamente o recurso de máquina virtual. para por exemplo: 002Fsubscriptions:<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
- **RowKey** : segue o formato <Descending time tick>:<Performance Counter Name>. O cálculo de escala de tempo decrescente é escalas de tempo máximo menos a hora de início do período de agregação. Por exemplo Se o período de amostra iniciado em 10 de novembro de 2015 e 00:00Hrs UTC e em seguida, o cálculo seria: DateTime.MaxValue.Ticks - (DateTime(2015,11,10,0,0,0,DateTimeKind.Utc) novo. Escalas). Para a memória contador de desempenho de bytes disponíveis a chave de linha será a aparência, como: 2519551871999999999__:005CMemory:005CAvailable:0020 Bytes
- **CounterName** : É o nome do contador de desempenho. Isso corresponde a *counterSpecifier* definidos no config xml.
- **Máximo** : O valor máximo do contador de desempenho durante o período de agregação.
- **Mínimo** : O valor mínimo do contador de desempenho durante o período de agregação.
- **Total** : A soma de todos os valores do contador de desempenho relatados durante o período de agregação.
- **Contagem** : O número total de valores relatados para o contador de desempenho.
- **Média** : O valor de média (total/contagem) do contador de desempenho durante o período de agregação.


## <a name="next-steps"></a>Próximas etapas

- Para um modelo de exemplo completos de um computador de virtual do Windows com extensão de diagnóstico consulte [201 máquina virtual-monitoramento-diagnóstico-extensão](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
- Implantar o modelo de Gerenciador de recurso usando [O PowerShell do Azure](virtual-machines-windows-ps-manage.md) ou [linha de comando do Azure](virtual-machines-linux-cli-deploy-templates.md)
- Saiba mais sobre a [criação de modelos de Gerenciador de recursos do Azure](../resource-group-authoring-templates.md)







