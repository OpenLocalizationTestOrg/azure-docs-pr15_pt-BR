<properties
 pageTitle="Referência de Cmdlets do PowerShell Agendador"
 description="Referência de Cmdlets do PowerShell Agendador"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-powershell-cmdlets-reference"></a>Referência de Cmdlets do PowerShell Agendador

A tabela a seguir descreve e links para a página de referência de cada um dos principais cmdlets no Agendador do Azure.

Para instalar o PowerShell do Azure e associá-lo a sua assinatura do Azure, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). 

Para obter mais informações sobre [cmdlets do Gerenciador de recursos do Azure](https://msdn.microsoft.com/library/mt125356\(v=azure.200\).aspx), consulte [Usando o PowerShell Azure com o Gerenciador de recursos do Azure](../powershell-azure-resource-manager.md).

|Cmdlet|Descrição do cmdlet|
|---|---|
[Desativar AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490133\(v=azure.200\).aspx) |Desativa um conjunto de trabalho. 
[Habilitar AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490135\(v=azure.200\).aspx) |Habilita um conjunto de trabalho.
[Get-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490125\(v=azure.200\).aspx) |Obtém trabalhos do Agendador.
[Get-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490132\(v=azure.200\).aspx) |Obtém conjuntos de trabalho.
[Get-AzureRmSchedulerJobHistory](https://msdn.microsoft.com/library/mt490126\(v=azure.200\).aspx) |Obtém o histórico de trabalho.
[Novo AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490136\(v=azure.200\).aspx) |Cria um trabalho HTTP.
[Novo AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490141\(v=azure.200\).aspx) |Cria um conjunto de trabalho.
[Novo AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490134\(v=azure.200\).aspx) |Cria um trabalho de fila de barramento de serviço.
[Novo AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490142\(v=azure.200\).aspx) |Cria um trabalho de tópico de barramento de serviço.
[Novo AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490127\(v=azure.200\).aspx) |Cria um trabalho de fila do armazenamento. 
[Remover AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490140\(v=azure.200\).aspx) |Remove um trabalho de Agendador.  
[Remover AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490131\(v=azure.200\).aspx) |Remove um conjunto de trabalho. 
[Set-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490130\(v=azure.200\).aspx) |Modifica um trabalho de Agendador HTTP.
[Set-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490129\(v=azure.200\).aspx) |Modifica um conjunto de trabalho. 
[Set-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490143\(v=azure.200\).aspx) |Modifica um trabalho de fila do barramento de serviço.  
[Set-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490137\(v=azure.200\).aspx) |Modifica um trabalho de tópico de barramento de serviço. 
[Set-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490128\(v=azure.200\).aspx) |Modifica um trabalho de fila do armazenamento.   

Para obter informações mais detalhadas, você pode executar qualquer um dos seguintes cmdlets: 

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Consulte também


 [O que é o Agendador?](scheduler-intro.md)

 [Hierarquia de entidades, terminologia e conceitos de Agendador Azure](scheduler-concepts-terms.md)

 [Começar a usar o Agendador no portal do Azure](scheduler-get-started-portal.md)

 [Planos e cobranças no Agendador do Azure](scheduler-plans-billing.md)

 [Referência de API do Agendador REST Azure](https://msdn.microsoft.com/library/mt629143)

 [Azure Agendador alta disponibilidade e confiabilidade](scheduler-high-availability-reliability.md)

 [Limites de Agendador Azure, padrões e códigos de erro](scheduler-limits-defaults-errors.md)

 [Autenticação de saída Agendador Azure](scheduler-outbound-authentication.md)
