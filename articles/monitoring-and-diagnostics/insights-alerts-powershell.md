<properties
    pageTitle="Usar o PowerShell para criar alertas para os serviços do Azure | Microsoft Azure"
    description="Use o PowerShell para criar alertas Azure, que podem disparar notificações ou automação quando as condições especificadas forem atendidas."
    authors="rboucher"
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
    ms.date="10/20/2016"
    ms.author="robb"/>

# <a name="use-powershell-to-create-alerts-for-azure-services"></a>Usar o PowerShell para criar alertas para os serviços do Azure

> [AZURE.SELECTOR]
- [Portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Visão geral

Este artigo mostra como configurar alertas Azure usando o PowerShell.  

Você pode receber um alerta com base em métricas de monitoramento para ou eventos em, seus serviços Azure.

- **Valores métricas** - os disparadores alertas quando o valor de uma métrica especificado exceder um limite que atribuir em qualquer direção. Ou seja, ele aciona ambos quando a condição é atendida primeiro e, em seguida, posteriormente quando que condição for não está mais sendo atendida.    
- **Eventos de log de atividade** - um alerta pode disparar em *todos os* eventos ou, somente quando ocorre um determinado número de eventos.

Você pode configurar um alerta para fazer o seguinte quando ele disparar:

- enviar notificações por email para o administrador do serviço e coadministradores
- Envie email emails adicionais que você especificar.
- chamar um webhook
- Iniciar a execução de um runbook Azure (apenas do portal do Azure)

Você pode configurar e obter informações sobre o uso de regras de alerta

- [Portal do Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [interface de linha (comando)](insights-alerts-command-line-interface.md)
- [API REST de Monitor Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)


Para obter informações adicionais, você pode digitar sempre ```get-help``` e, em seguida, o comando do PowerShell que você deseja Ajuda.

## <a name="create-alert-rules-in-powershell"></a>Criar regras de alerta no PowerShell

1. Faça logon no Azure.   

    ```PowerShell
    Login-AzureRmAccount

    ```

2. Obter uma lista das assinaturas tiver disponíveis. Verifique se que você está trabalhando com a assinatura à direita. Se não, configurá-lo para a direita usando a saída de `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

3.  Para listar as regras existentes em um grupo de recursos, use o seguinte comando:

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

4. Para criar uma regra, você precisa ter vários componentes de informações importantes primeiro. 
    - A **Identificação do recurso** para o recurso que você deseja definir um alerta para
    - As **definições de métrica** disponíveis para o recurso

    Uma maneira de obter a identificação do recurso é usar o portal do Azure. Considerando que o recurso já foi criado, selecione-o no portal. Na próxima lâmina, selecione *Propriedades* na seção *configurações* . A identificação do recurso é um campo na próxima lâmina. Outra maneira é usar o [Gerenciador de recursos do Azure](https://resources.azure.com/).

    É uma id de recurso de exemplo para um aplicativo web

    ```
    /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
    ```

    Você pode usar `Get-AzureRmMetricDefinition` para exibir a lista de todas as definições de métrica para um recurso específico.

    ```PowerShell
    Get-AzureRmMetricDefinition -ResourceId <resource_id>
    ```

    O exemplo a seguir gera uma tabela com a métrica nome e a unidade de métrica.

    ```PowerShell
    Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

    ```
    Uma lista completa das opções disponíveis para Get-AzureRmMetricDefinition está disponível executando Get-MetricDefinitions.


5. O exemplo a seguir define um alerta em um recurso de site da web. Os disparadores alertas sempre que ele recebe consistentemente qualquer tráfego de 5 minutos e novamente quando ele recebe nenhum tráfego por 5 minutos.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```

6. Para criar webhook ou enviar email quando dispara um alerta, primeiro crie o email e/ou webhooks. Em seguida, imediatamente crie a regra posteriormente com-marca de ações e conforme mostrado no exemplo a seguir. Você não pode associar webhook ou emails com já criou regras por meio do PowerShell.


    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```


7. Para criar um alerta que aciona em uma condição específica no registro de atividade, use os comandos do seguinte formato

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmLogAlertRule -Name myLogAlertRule -Location "East US" -ResourceGroup myresourcegroup -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup resourcegroupbeingmonitored -Actions $actionEmail, $actionWebhook
    ```

    O - OperationName corresponde a um tipo de evento no registro de atividade. Exemplos incluem *Microsoft.Compute/virtualMachines/delete* e *microsoft.insights/diagnosticSettings/write*.

    Você pode usar o comando [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) do PowerShell para obter uma lista de possíveis operationNames. Como alternativa, você pode usar o portal do Azure para consultar o log de atividade e localizar específico passaram operações que você deseja criar um alerta para. As operações mostradas no modo de exibição gráfico de log de nomes amigáveis. Analisar o JSON para a entrada e retirar o valor de OperationName.   

8. Verifique se que seus alertas foram criadas corretamente examinando as regras individuais.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```

9. Exclua os alertas. Esses comandos exclua as regras que criou anteriormente neste artigo.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Próximas etapas

* [Obtenha uma visão geral de monitoramento Azure](monitoring-overview.md) , incluindo os tipos de informações você pode coletar e monitorar.
* Saiba mais sobre [Configurando webhooks em alertas](insights-webhooks-alerts.md).
* Saiba mais sobre [Runbooks de automação do Azure](..\automation\automation-starting-a-runbook.md).
* Obtenha uma [Visão geral de coletar logs de diagnóstico](monitoring-overview-of-diagnostic-logs.md) para coletar métricas de alta frequência detalhadas no seu serviço.
* Obtenha uma [Visão geral do conjunto de métricas](insights-how-to-customize-monitoring.md) para garantir que seu serviço está disponível e responde.
