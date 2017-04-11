<properties 
    pageTitle="Modelo de preços de aplicativos de lógica | Microsoft Azure" 
    description="Detalhes sobre como preços funciona nos aplicativos de lógica" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="klam"/>

# <a name="logic-apps-pricing-model"></a>Modelo de preços de aplicativos de lógica

Aplicativos do Azure lógica permite que você dimensionar e executar fluxos de trabalho de integração na nuvem.  Veja a seguir para obter detalhes sobre os planos de preços para aplicativos de lógica e a cobrança.

## <a name="consumption-pricing"></a>Preços de consumo

Aplicativos de lógica recém-criado use um plano de consumo. Com o modelo de preços de consumo de aplicativos de lógica, você paga apenas pelo que você usar.  Aplicativos de lógica não são resolvidos quando usando um plano de consumo.
Todas as ações executadas em uma execução de uma instância de aplicativo de lógica são limitadas.

### <a name="what-are-action-executions"></a>Quais são as execuções de ação?

Cada etapa em uma definição de aplicativo de lógica é uma ação.  Isso inclui disparadores, etapas do fluxo de controle como condições, escopos, cada loops, até loops, chamadas aos conectores e chamadas para ações nativas.
Disparadores são ações apenas especiais que foram projetadas para criar uma nova instância de um aplicativo de lógica quando ocorre um evento particular.  Há um número de diferentes comportamentos para disparadores que podem afetar como o aplicativo de lógica é limitado.

-   **Gatilho de pesquisa** – esse disparador continuamente controla a um ponto de extremidade até receber uma mensagem que atenda aos critérios para a criação de uma nova instância de um aplicativo de lógica.  O intervalo de sondagem pode ser configurado no disparador no designer de aplicativos de lógica.  Cada solicitação de sondagem, mesmo se ele não criar uma nova instância de um aplicativo de lógica, contará como uma execução da ação.

-   **Gatilho de Webhook** – este disparador aguarda para um cliente envie uma solicitação de um determinado ponto de extremidade.  Cada solicitação enviada para o ponto de extremidade de webhook conta como uma execução da ação. A solicitação e o disparador HTTP Webhook são os dois disparadores webhook.

-   **Gatilho de recorrência** – esse disparador criará uma nova instância do aplicativo lógica baseado no intervalo de recorrência configurado no disparador.  Por exemplo, um gatilho de recorrência pode ser configurado para executar a cada três dias ou até mesmo cada minuto.

Gatilho execuções podem ser vistas na lâmina recurso lógica aplicativos na parte histórico de disparador.

Todas as ações que foram executadas, se elas foram bem-sucedidas ou não são limitadas como uma execução da ação.  Ações que foram ignoradas devido a uma condição não sendo atendida ou ações que não foi executada porque o aplicativo de lógica encerrada antes da conclusão não são contadas como execuções de ação.

Ações executadas nos loops são contadas por iteração do loop.  Por exemplo, uma única ação em um para cada iteração de loop em uma lista de 10 itens será contada como a contagem de itens na lista (10) multiplicado pelo número de ações no loop (1) mais uma para o início do loop que, neste exemplo, seria (10 * 1) + 1 = 11 execuções de ação.

Lógica de aplicativos que estão desabilitados não pode ter novas instâncias criadas e, portanto, durante o tempo em que eles estão desabilitadas será não obter cobrada.  Lembre-se de que depois de desabilitar um aplicativo de lógica poderá demorar um pouco de tempo para as instâncias para desativar antes sendo completamente desativado.

## <a name="app-service-plans"></a>Planos de serviço de aplicativo

Planos de serviço de aplicativo já não são necessárias para criar um aplicativo de lógica.  Você também pode referenciar um plano de serviço de aplicativo com um aplicativo de lógica existente.  Aplicativos de lógica criados anteriormente com um plano de serviço de aplicativo continuará se comportam antes onde, dependendo do plano escolhido, irá obter limitado após um número de execuções diárias é excedido e será não cobrado usando o medidor de execução da ação.

Planos de serviço de aplicativo e seus diária execuções de ação permitida:

| |Livre/compartilhados/Basic|Padrão|Premium|
|---|---|---|---|
|Execuções de ação por dia| 200|10.000|50.000|

### <a name="convert-from-consumption-to-app-service-plan-pricing"></a>Converter do consumo para o plano de serviço de aplicativo preços

Para fazer referência a um plano de serviço de aplicativo para um aplicativo de lógica de consumo, você poderá simplesmente [executar o abaixo de script do PowerShell](https://github.com/logicappsio/ConsumptionToAppServicePlan).  Certificar-se de que tem as [Ferramentas do PowerShell do Azure](https://github.com/Azure/azure-powershell) instaladas primeiro.

``` powershell
Param(
    [string] $AppService_RG = '<app-service-resource-group>',
    [string] $AppService_Name = '<app-service-name>',
    [string] $LogicApp_RG = '<logic-app-resource-group>',
    [string] $LogicApp_Name = '<logic-app-name>',
    [string] $subscriptionId = '<azure-subscription-id>'
)

Login-AzureRmAccount 
$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId
$appserviceplan = Get-AzureRmResource -ResourceType "Microsoft.Web/serverFarms" -ResourceGroupName $AppService_RG -ResourceName $AppService_Name
$logicapp = Get-AzureRmResource -ResourceType "Microsoft.Logic/workflows" -ResourceGroupName $LogicApp_RG -ResourceName $LogicApp_Name

$sku = @{
    "name" = $appservicePlan.Sku.tier;
    "plan" = @{
      "id" = $appserviceplan.ResourceId;
      "type" = "Microsoft.Web/ServerFarms";
      "name" = $appserviceplan.Name  
    }
}

$updatedProperties = $logicapp.Properties | Add-Member @{sku = $sku;} -PassThru

$updatedLA = Set-AzureRmResource -ResourceId $logicapp.ResourceId -Properties $updatedProperties -ApiVersion 2015-08-01-preview
```

### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>Converter do plano de serviço de aplicativo preços de consumo

Para alterar um aplicativo de lógica que tem um plano de serviço de aplicativo associada a ele a um modelo de consumo remova a referência para o plano de serviço de aplicativo na definição de aplicativo de lógica.  Isso pode ser feito com uma chamada para um cmdlet do PowerShell:

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## <a name="pricing"></a>Preços

Para obter detalhes sobre preços, consulte [Preços de aplicativos de lógica](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Próximas etapas

- [Uma visão geral dos aplicativos de lógica][whatis]
- [Criar seu primeiro aplicativo de lógica][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[create]: app-service-logic-create-a-logic-app.md

