<properties
    pageTitle="Azure Monitor autoscaling comuns métricas. | Microsoft Azure"
    description="Saiba quais medidas são comumente usadas para autoscaling seus serviços de nuvem, máquinas virtuais e aplicativos Web."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/02/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor-autoscaling-common-metrics"></a>Métricas comuns do Azure Monitor autoscaling

Azure autoscaling Monitor permite que você dimensionar o número de instâncias em execução para cima ou para baixo, com base em dados de telemetria (métricas). Este documento descreve métricas comuns que talvez você queira usar. No Portal do Azure para serviços de nuvem e Server Farms, você pode escolher a métrica do recurso para escala por. Entretanto, você também pode escolher qualquer métrica de um recurso diferente a escala por.

Aqui estão os detalhes sobre como localizar e listar as métricas que deseja dimensionar por. Aplica o seguinte para dimensionamento conjuntos de escala de máquina Virtual também.

## <a name="compute-metrics"></a>Calcular métricas
Por padrão, máquina virtual do Azure v2 vem com extensão de diagnóstico configurado e têm as seguintes métricas ativadas.

- [Métricas de convidado para v2 de máquina virtual do Windows](#compute-metrics-for-windows-vm-v2-as-a-guest-os)
- [Métricas de convidado para Linux VM v2](#compute-metrics-for-linux-vm-v2-as-a-guest-os)

Você pode usar o `Get MetricDefinitions` PoSH/API/CLI para exibir as métricas disponíveis para o recurso VMSS. 

Se você estiver usando conjuntos de escala de máquina virtual e você não vir uma determinada métrica listada, é provável *desativado* em sua extensão de diagnóstico.

Se uma determinada métrica não está sendo amostras ou transferidas na frequência desejada, você pode atualizar a configuração de diagnóstico.

Se dois casos acima for verdadeiro, revise [Usar o PowerShell para habilitar o diagnóstico do Azure em uma máquina virtual executando o Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) sobre o PowerShell para configurar e atualizar sua extensão de diagnóstico de máquina virtual do Azure para habilitar a métrica. Esse artigo também inclui um arquivo de configuração de diagnóstico de amostra.

### <a name="compute-metrics-for-windows-vm-v2-as-a-guest-os"></a>Calcular métricas para v2 de máquina virtual do Windows como um sistema operacional convidado

Quando você cria uma nova VM (v2) no Azure, diagnóstico está habilitado usando a extensão de diagnóstico.

Você pode gerar uma lista das métricas usando o seguinte comando no PowerShell.


```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Você pode criar um alerta para as métricas a seguir.

|Nome de métrica|   Unidade|
|---|---|
|\Processor(_Total)\% tempo de processador    |Porcentagem|
|\Processor(_Total)\% tempo privilegiado   |Porcentagem|
|\Processor(_Total)\% tempo de usuário |Porcentagem|
|Frequência de \Processor \Processor informações ( total) |Contagem|
|\System\Processes| Contagem|
|Contagem de \Thread \processo ( total)| Contagem|
|Contagem de \Handle \processo ( total)  |Contagem|
|\Memory.\% confirmada Bytes em uso   |Porcentagem|
|\Memory\Available bytes|   Bytes|
|\Memory\Committed bytes    |Bytes|
|Limite de \Memory\Commit|  Bytes|
|\Memory\Pool Bytes de paginação|  Bytes|
|\Memory\Pool Bytes não-|   Bytes|
|\PhysicalDisk(_Total)\% tempo de disco| Porcentagem|
|\PhysicalDisk(_Total)\% tempo de leitura de disco|    Porcentagem|
|\PhysicalDisk(_Total)\% tempo de gravação de disco|   Porcentagem|
|\PhysicalDisk ( total) \Disk transferências/seg   |CountPerSecond|
|\PhysicalDisk ( total) \Disk leituras/s   |CountPerSecond|
|\PhysicalDisk ( total) \Disk gravações/s  |CountPerSecond|
|\PhysicalDisk ( total) \Disk bytes/seg   |BytesPerSecond|
|\PhysicalDisk ( total) \Disk Bytes lidos/s| BytesPerSecond|
|Bytes de gravação de \PhysicalDisk ( total) \Disk/s |BytesPerSecond|
|\Avg \PhysicalDisk ( total). Comprimento da fila de disco|  Contagem|
|\Avg \PhysicalDisk ( total). Comprimento de fila de leitura de disco| Contagem|
|\Avg \PhysicalDisk ( total). Comprimento de fila de gravação de disco |Contagem|
|\LogicalDisk(_Total)\% espaço livre| Porcentagem|
|\LogicalDisk ( total) \Free megabytes|   Contagem|



### <a name="compute-metrics-for-linux-vm-v2-as-a-guest-os"></a>Calcular métricas para Linux VM v2 como um sistema operacional convidado

Quando você cria uma nova VM (v2) no Azure, diagnóstico está habilitado por padrão, usando a extensão de diagnóstico.

Você pode gerar uma lista das métricas usando o seguinte comando no PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Você pode criar um alerta para as métricas a seguir.

|Nome de métrica                            |Unidade|
|---|---|
|\Memory\AvailableMemory                |Bytes|
|\Memory\PercentAvailableMemory         |Porcentagem|
|\Memory\UsedMemory                     |Bytes|
|\Memory\PercentUsedMemory              |Porcentagem|
|\Memory\PercentUsedByCache             |Porcentagem|
|\Memory\PagesPerSec                    |CountPerSecond|
|\Memory\PagesReadPerSec                |CountPerSecond|
|\Memory\PagesWrittenPerSec             |CountPerSecond|
|\Memory\AvailableSwap                  |Bytes|
|\Memory\PercentAvailableSwap           |Porcentagem|
|\Memory\UsedSwap                       |Bytes|
|\Memory\PercentUsedSwap                |Porcentagem|
|\Processor\PercentIdleTime             |Porcentagem|
|\Processor\PercentUserTime             |Porcentagem|
|\Processor\PercentNiceTime             |Porcentagem|
|\Processor\PercentPrivilegedTime       |Porcentagem|
|\Processor\PercentInterruptTime        |Porcentagem|
|\Processor\PercentDPCTime              |Porcentagem|
|\Processor\PercentProcessorTime        |Porcentagem|
|\Processor\PercentIOWaitTime           |Porcentagem|
|\PhysicalDisk\BytesPerSecond           |BytesPerSecond|
|\PhysicalDisk\ReadBytesPerSecond       |BytesPerSecond|
|\PhysicalDisk\WriteBytesPerSecond      |BytesPerSecond|
|\PhysicalDisk\TransfersPerSecond       |CountPerSecond|
|\PhysicalDisk\ReadsPerSecond           |CountPerSecond|
|\PhysicalDisk\WritesPerSecond          |CountPerSecond|
|\PhysicalDisk\AverageReadTime          |Segundos|
|\PhysicalDisk\AverageWriteTime         |Segundos|
|\PhysicalDisk\AverageTransferTime      |Segundos|
|\PhysicalDisk\AverageDiskQueueLength   |Contagem|
|\NetworkInterface\BytesTransmitted     |Bytes|
|\NetworkInterface\BytesReceived        |Bytes|
|\NetworkInterface\PacketsTransmitted   |Contagem|
|\NetworkInterface\PacketsReceived      |Contagem|
|\NetworkInterface\BytesTotal           |Bytes|
|\NetworkInterface\TotalRxErrors        |Contagem|
|\NetworkInterface\TotalTxErrors        |Contagem|
|\NetworkInterface\TotalCollisions      |Contagem|




## <a name="commonly-used-web-server-farm-metrics"></a>Comumente usadas métricas de Web (Farm de servidor)

Você também pode executar escala automática com base em comum métricas de servidor web como o comprimento da fila Http. Métrica nome é **HttpQueueLength**.  A seção a seguir lista métricas de farm (Web Apps) do servidor disponível.

### <a name="web-apps-metrics"></a>Métricas de aplicativos Web

Você pode gerar uma lista das métricas aplicativos Web usando o seguinte comando no PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Você pode alertar ou dimensionar por essas métricas.

|Nome de métrica        |Unidade|
|---                |---|
|CpuPercentage      |Porcentagem|
|MemoryPercentage   |Porcentagem|
|DiskQueueLength    |Contagem|
|HttpQueueLength    |Contagem|
|BytesReceived      |Bytes|
|BytesSent          |Bytes|


## <a name="commonly-used-storage-metrics"></a>Métricas de armazenamento comumente usadas
Você pode dimensionar pelo comprimento de fila do armazenamento, que é o número de mensagens na fila do armazenamento. Comprimento de fila do armazenamento é uma métrica especial e o limite aplicado será o número de mensagens por instância. Isso significa que se existem duas instâncias e se o limite é definido como 100, ele será dimensionada quando o número total de mensagens na fila é 200. Por exemplo, 100 mensagens por instância.

Você pode configurar isso é no Portal do Azure na lâmina **configurações** . Para conjuntos de escala de máquina virtual, você pode atualizar a configuração de escala automática no modelo ARM para usar *metricName* como *ApproximateMessageCount* e passar a ID da fila armazenamento como *metricResourceUri*.

Por exemplo, com uma conta de armazenamento clássico a metricTrigger de configuração de escala automática incluiria:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
 ```

Para uma conta de armazenamento (não-clássico), o metricTrigger incluiria:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Storage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

## <a name="commonly-used-service-bus-metrics"></a>Comumente usadas métricas de barramento de serviço

Você pode dimensionar pelo comprimento de fila de barramento de serviço, que é o número de mensagens na fila do barramento de serviço. Comprimento de fila de barramento de serviço é uma métrica especial e o limite especificado aplicado será o número de mensagens por instância. Isso significa que se existem duas instâncias e se o limite é definido como 100, ele será dimensionada quando o número total de mensagens na fila é 200. Por exemplo, 100 mensagens por instância.

Para conjuntos de escala de máquina virtual, você pode atualizar a configuração de escala automática no modelo ARM para usar *metricName* como *ApproximateMessageCount* e passar a ID da fila armazenamento como *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

>[AZURE.NOTE] Barramento de serviço, o conceito de grupo de recursos não existir mas Gerenciador de recursos do Azure cria um grupo de recursos padrão por região. O grupo de recursos é geralmente no formato 'Default - ServiceBus-[Região]'. Por exemplo, 'Padrão-ServiceBus-EastUS', 'Padrão-ServiceBus-WestUS', 'Padrão-ServiceBus-AustraliaEast' etc.
