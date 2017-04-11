<properties 
    pageTitle="Monitorar e gerenciar trabalhos de análise de fluxo com o PowerShell | Microsoft Azure" 
    description="Aprenda a usar o PowerShell do Azure e cmdlets para monitorar e gerenciar trabalhos de análise de fluxo." 
    keywords="Azure powershell, cmdlets do powershell azure, comando do powershell, scripts do powershell" 
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>


# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Monitorar e gerenciar trabalhos de análise de fluxo com cmdlets do PowerShell do Azure

Aprenda a monitorar e gerenciar recursos de análise de fluxo com cmdlets do PowerShell do Azure e scripts do powershell que executar tarefas básicas de análise de fluxo.

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Pré-requisitos para executar cmdlets do PowerShell do Azure para análise de fluxo

 - Crie um grupo de recursos do Azure em sua assinatura. O que vem a seguir é um exemplo de script do PowerShell do Azure. Para obter informações do Azure PowerShell, consulte [instalar e configurar o Azure PowerShell](../powershell-install-configure.md);  

PowerShell Azure 0.9.8:  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
        Select-AzureSubscription -SubscriptionName <subscription name>
 
        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

Azure PowerShell 1.0:  

        # Log in to your Azure account
        Login-AzureRmAccount

        # Select the Azure subscription you want to use to create the resource group.
        Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
        
        # Create an Azure resource group
        New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
        


> [AZURE.NOTE] Trabalhos de análise de fluxo criados programaticamente não têm monitoramento ativado por padrão.  Você pode ativar manualmente monitoramento no Portal do Azure navegando até a página de Monitor do trabalho e clicando no botão Habilitar ou você pode fazer isso programaticamente seguindo as etapas localizadas no [Azure fluxo Analytics - Monitor fluxo Analytics trabalhos programaticamente](stream-analytics-monitor-jobs.md).

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure cmdlets do PowerShell para análise de fluxo
Os seguintes cmdlets do PowerShell do Azure pode ser usados para monitorar e gerenciar trabalhos de análise de fluxo de Azure. Observe que o Azure PowerShell tem diferentes versões. 
**Nos exemplos listados o primeiro comando é para Azure PowerShell 0.9.8, o segundo comando é para Azure PowerShell 1.0.** Os comandos do Azure PowerShell 1.0 sempre terão "AzureRM" no comando.

### <a name="get-azurestreamanalyticsjob--get-azurermstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Lista todos os trabalhos de análise de fluxo definidos na assinatura do Azure ou grupo de recursos especificado ou obtém trabalho informações sobre um trabalho específico dentro de um grupo de recursos.

**Exemplo 1**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob

Este comando do PowerShell retorna informações sobre todos os trabalhos de análise de fluxo na assinatura do Azure.

**Exemplo 2**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Este comando do PowerShell retorna informações sobre todos os trabalhos de análise de fluxo no grupo de recursos StreamAnalytics-padrão-Central-US.

**Exemplo 3**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Este comando do PowerShell retorna informações sobre a análise de fluxo de trabalho StreamingJob no grupo de recursos StreamAnalytics-padrão-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azurermstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
Lista todas as entradas que são definidas em um trabalho de análise de fluxo especificado ou obtém informações sobre uma entrada específica.

**Exemplo 1**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Este comando do PowerShell retorna informações sobre todas as entradas definidas no trabalho StreamingJob.

**Exemplo 2**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Este comando do PowerShell retorna informações sobre nomeados EntryStream definido no trabalho StreamingJob de entrada.

### <a name="get-azurestreamanalyticsoutput--get-azurermstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
Lista todas as saídas que são definidas em um trabalho de análise de fluxo especificado ou obtém informações sobre uma saída específica.

**Exemplo 1**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Este comando do PowerShell retorna informações sobre as saídas definidos no trabalho StreamingJob.

**Exemplo 2**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Este comando do PowerShell retorna informações sobre a saída chamada Output definido no trabalho StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azurermstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
Obtém informações sobre a cota de fluxo de unidades em uma região especificada.

**Exemplo 1**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsQuota –Location "Central US" 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

Este comando do PowerShell retorna informações sobre a cota e o uso de unidades streaming na região Centro dos EUA.

### <a name="get-azurestreamanalyticstransformation--getazurermstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Obtém informações sobre uma transformação específica definida em um trabalho de análise de fluxo.

**Exemplo 1**

PowerShell Azure 0.9.8:  

    Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Este comando do PowerShell retorna informações sobre a transformação chamada StreamingJob no trabalho StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azurermstreamanalyticsinput"></a>Novo AzureStreamAnalyticsInput | Novo AzureRMStreamAnalyticsInput
Cria uma nova entrada do trabalho de análise de fluxo ou atualiza uma entrada especificada existente.
  
O nome da entrada pode ser especificado no arquivo .json ou na linha de comando. Se ambos são especificados, o nome na linha de comando deve ser o mesmo no arquivo.

Se você especificar uma entrada já existente e não especificar o forçar parâmetro –, o cmdlet perguntará se deseja ou não substituir a entrada existente.

Se você especificar – forçar o parâmetro e especifique uma existente entrada nome, a entrada será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura de arquivo JSON e conteúdo, consulte a [Entrada criar (análise de fluxo de Azure)] [ msdn-rest-api-create-stream-analytics-input] seção da [biblioteca de referência API do resto de gerenciamento de análise de fluxo]de[stream.analytics.rest.api.reference].

**Exemplo 1**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Este comando do PowerShell cria uma nova entrada do arquivo Input.json. Se uma entrada existente com o nome especificado no arquivo de definição de entrada já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 2**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Este comando do PowerShell cria uma nova entrada no trabalho chamado EntryStream. Se uma entrada existente com este nome já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 3**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Este comando do PowerShell substitui a definição da fonte de entrada existente chamado EntryStream com a definição do arquivo.

### <a name="new-azurestreamanalyticsjob--new-azurermstreamanalyticsjob"></a>Novo AzureStreamAnalyticsJob | Novo AzureRMStreamAnalyticsJob
Cria uma nova tarefa de fluxo de análise no Microsoft Azure ou atualiza a definição de um trabalho especificado existente.

O nome do trabalho pode ser especificado no arquivo .json ou na linha de comando. Se ambos são especificados, o nome na linha de comando deve ser o mesmo no arquivo.

Se você especificar um nome de trabalho que já existe e não especificar o forçar parâmetro –, o cmdlet perguntará se deseja ou não substituir o trabalho existente.

Se você especificar – forçar o parâmetro e especifique um nome de trabalho existente, a definição de trabalho será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura de arquivo JSON e conteúdo, consulte ao [Criar trabalho de análise de fluxo de] [ msdn-rest-api-create-stream-analytics-job] seção da [biblioteca de referência API do resto de gerenciamento de análise de fluxo]de[stream.analytics.rest.api.reference].

**Exemplo 1**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Este comando do PowerShell cria uma nova tarefa da definição no JobDefinition.json. Se um trabalho existente com o nome especificado no arquivo de definição de trabalho já estiver definido, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 2**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Este comando do PowerShell substitui a definição de trabalho para StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azurermstreamanalyticsoutput"></a>Novo AzureStreamAnalyticsOutput | Novo AzureRMStreamAnalyticsOutput
Cria uma nova saída do trabalho de análise de fluxo ou atualiza uma saída existente.  

O nome da saída pode ser especificado no arquivo .json ou na linha de comando. Se ambos são especificados, o nome na linha de comando deve ser o mesmo no arquivo.

Se você especificar uma saída que já existe e não especificar o forçar parâmetro –, o cmdlet perguntará se deseja ou não substituir a saída existente.

Se você especificar – forçar o parâmetro e especifique o nome de saída de um existente, o resultado será substituído sem confirmação.

Para obter informações detalhadas sobre a estrutura de arquivo JSON e conteúdo, consulte a [Saída criar (análise de fluxo de Azure)] [ msdn-rest-api-create-stream-analytics-output] seção da [biblioteca de referência API do resto de gerenciamento de análise de fluxo]de[stream.analytics.rest.api.reference].

**Exemplo 1**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Este comando do PowerShell cria uma nova chamada "saída" no trabalho StreamingJob de saída. Se uma saída existente com este nome já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 2**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Este comando do PowerShell substitui a definição para "saída" no trabalho StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azurermstreamanalyticstransformation"></a>Novo AzureStreamAnalyticsTransformation | Novo AzureRMStreamAnalyticsTransformation
Cria uma nova transformação do trabalho de análise de fluxo ou atualiza a transformação existente.
  
O nome da transformação pode ser especificado no arquivo .json ou na linha de comando. Se ambos são especificados, o nome na linha de comando deve ser o mesmo no arquivo.

Se você especificar uma transformação que já existe e não especificar o forçar parâmetro –, o cmdlet perguntará se deseja ou não substituir a transformação existente.

Se você especificar – forçar o parâmetro e especifique um nome de transformação existente, a transformação será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura de arquivo JSON e conteúdo, consulte a [Transformação criar (análise de fluxo de Azure)] [ msdn-rest-api-create-stream-analytics-transformation] seção da [biblioteca de referência API do resto de gerenciamento de análise de fluxo]de[stream.analytics.rest.api.reference].

**Exemplo 1**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Este comando do PowerShell cria uma nova transformação chamada StreamingJobTransform no trabalho StreamingJob. Se uma transformação existente já estiver definida com este nome, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 2**

PowerShell Azure 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 Este comando do PowerShell substitui a definição de StreamingJobTransform no trabalho StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azurermstreamanalyticsinput"></a>Remover AzureStreamAnalyticsInput | Remover AzureRMStreamAnalyticsInput
Assíncrona exclui uma entrada específica de um trabalho de análise de fluxo no Microsoft Azure.  
Se você especificar o forçar parâmetro –, a entrada será excluída sem confirmação.

**Exemplo 1**

PowerShell Azure 0.9.8:  

    Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Este comando do PowerShell remove a entrada EventStream no trabalho StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azurermstreamanalyticsjob"></a>Remover AzureStreamAnalyticsJob | Remover AzureRMStreamAnalyticsJob
Assíncrona exclui um trabalho de análise de fluxo específico no Microsoft Azure.  
Se você especificar o forçar parâmetro –, o trabalho será excluído sem confirmação.

**Exemplo 1**

PowerShell Azure 0.9.8:  

    Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Este comando do PowerShell remove o trabalho StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azurermstreamanalyticsoutput"></a>Remover AzureStreamAnalyticsOutput | Remover AzureRMStreamAnalyticsOutput
Assíncrona exclui uma saída específico de um trabalho de análise de fluxo no Microsoft Azure.  
Se você especificar o forçar parâmetro –, o resultado será excluído sem confirmação.

**Exemplo 1**

PowerShell Azure 0.9.8:  

    Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Isso remove de comando do PowerShell a saída de saída no trabalho StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azurermstreamanalyticsjob"></a>Início-AzureStreamAnalyticsJob | Início-AzureRMStreamAnalyticsJob
Assíncrona implanta e inicia um trabalho de análise de fluxo no Microsoft Azure.

**Exemplo 1**

PowerShell Azure 0.9.8:  

    Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Azure PowerShell 1.0:  

    Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Este comando do PowerShell inicia o trabalho StreamingJob com uma hora de início de saída personalizada definida como 12 de dezembro de 2012, 12:12:12 UTC.


### <a name="stop-azurestreamanalyticsjob--stop-azurermstreamanalyticsjob"></a>Parar-AzureStreamAnalyticsJob | Parar AzureRMStreamAnalyticsJob
Assíncrona interrompe um trabalho de análise de fluxo de executarem no Microsoft Azure e desaloja recursos que estavam que estavam sendo usados. A definição de trabalho e metadados permanecerá disponíveis dentro de sua assinatura através do portal do Azure e o gerenciamento de APIs, por exemplo, que o trabalho pode ser editado e reiniciado. Você não será cobrado para um trabalho no estado interrompido.

**Exemplo 1**

PowerShell Azure 0.9.8:  

    Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Este comando do PowerShell interrompe o trabalho StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azurermstreamanalyticsinput"></a>Teste-AzureStreamAnalyticsInput | Teste-AzureRMStreamAnalyticsInput
Testa a capacidade de análise de fluxo de conectar a uma entrada especificada.

**Exemplo 1**

PowerShell Azure 0.9.8:  

    Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Este comando do PowerShell testa o status de conexão da entrada EntryStream em StreamingJob.  

###<a name="test-azurestreamanalyticsoutput--test-azurermstreamanalyticsoutput"></a>Teste-AzureStreamAnalyticsOutput | Teste-AzureRMStreamAnalyticsOutput
Testa a capacidade de análise de fluxo de conectar a uma saída especificado.

**Exemplo 1**

PowerShell Azure 0.9.8:  

    Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Este testes de comando do PowerShell o status de conexão de saída da saída em StreamingJob.  

## <a name="get-support"></a>Obter suporte
Para obter assistência, tente nosso [Fórum de análise de fluxo de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics). 


## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 
