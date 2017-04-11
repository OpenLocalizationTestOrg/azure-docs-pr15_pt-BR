<properties
    pageTitle="Usar a Interface de linha de comando de plataforma híbrida (CLI) para criar alertas para os serviços do Azure | Microsoft Azure"
    description="Use a interface de linha de comando para criar alertas Azure, que podem disparar notificações ou automação quando as condições especificadas forem atendidas."
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
    ms.date="10/24/2016"
    ms.author="robb"/>

# <a name="use-the-cross-platform-command-line-interface-cli-to-create-alerts-for-azure-services"></a>Usar a Interface de linha de comando de plataforma híbrida (CLI) para criar alertas para os serviços do Azure

> [AZURE.SELECTOR]
- [Portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Visão geral

Este artigo mostra como configurar alertas Azure usando a Interface de linha de comando (CLI).

>[AZURE.NOTE] Monitor Azure é o novo nome para o que era chamado "Azure ideias" até 25 de setembro de 2016. No entanto, os namespaces e, portanto, os comandos abaixo ainda contenham "Percepções".

Você pode receber um alerta com base em métricas de monitoramento para ou eventos em, seus serviços Azure.

- **Valores métricas** - os disparadores alertas quando o valor de uma métrica especificado exceder um limite que atribuir em qualquer direção. Ou seja, ele aciona ambos quando a condição é atendida primeiro e, em seguida, posteriormente quando que condição for não está mais sendo atendida.    
- **Eventos de log de atividade** - um alerta pode disparar em *todos os* eventos ou, somente quando ocorre um determinado número de eventos.

Você pode configurar um alerta para fazer o seguinte quando ele disparar:

- enviar notificações por email para o administrador do serviço e coadministradores
- Envie email emails adicionais que você especificar.
- chamar um webhook
- Iniciar a execução de um runbook Azure (apenas do portal do Azure neste momento)

Você pode configurar e obter informações sobre o uso de regras de alerta

- [Portal do Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [interface de linha (comando)](insights-alerts-command-line-interface.md)
- [API REST de Monitor Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)


Você sempre pode receber ajuda para os comandos digitando um comando e posicionando - ajuda no final. Por exemplo:

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>Criar regras de alerta utilizando a CLI

1. Execute os pré-requisitos e fazer login no Azure. Consulte [exemplos do Azure Monitor CLI](insights-cli-samples.md). Em resumo, instale a CLI e execute esses comandos. Eles você conectado, mostrar qual assinatura que você está usando e prepará-lo para executar comandos do Monitor do Azure.


    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2.  Para listar as regras existentes em um grupo de recursos, use o seguinte formulário **azure ideias lista de regra de alertas** *[Opções] &lt;resourceGroup&gt; *

    ```console
    azure insights alerts rule list myresourcegroupname

    ```
3. Para criar uma regra, você precisa ter vários componentes de informações importantes primeiro.
    - A **Identificação do recurso** para o recurso que você deseja definir um alerta para
    - As **definições de métrica** disponíveis para o recurso

    Uma maneira de obter a identificação do recurso é usar o portal do Azure. Considerando que o recurso já foi criado, selecione-o no portal. Na próxima lâmina, selecione *Propriedades* na seção *configurações* . A *Identificação do recurso* é um campo na próxima lâmina. Outra maneira é usar o [Gerenciador de recursos do Azure](https://resources.azure.com/).

    É uma id de recurso de exemplo para um aplicativo web

    ```console
    /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
    ```

    Para obter uma lista das métricas disponíveis e unidades para essas métricas para o exemplo anterior do recurso, use o seguinte comando CLI:  

    ```console
    azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
    ```

    _PT1M_ é o detalhamento da medição disponível (intervalos de 1 minuto). Usar diferentes granularidades fornece opções métricas diferentes.


4. Para criar uma regra de alerta com métrico, use um comando da seguinte forma:

    **métrica de regra de alertas de ideias Azure definir** *[Opções] &lt;nome_da_regra&gt; &lt;local&gt; &lt;resourceGroup&gt; &lt;Tamanho_da_janela&gt; &lt;operador&gt; &lt;limite&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt; *

    O exemplo a seguir define um alerta em um recurso de site da web. Os disparadores alertas sempre que ele recebe consistentemente qualquer tráfego de 5 minutos e novamente quando ele recebe nenhum tráfego por 5 minutos.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```

5. Para criar webhook ou enviar email quando um alerta é acionado, primeiro crie o email e/ou webhooks. Em seguida, crie a regra imediatamente posteriormente. Você não pode associar webhook ou emails com já criou regras usando a CLI.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```


6. Para criar um alerta que aciona em uma condição específica no registro de atividades, use o formulário:

    **regra de alertas de ideias registrar o conjunto** *[Opções] &lt;nome_da_regra&gt; &lt;local&gt; &lt;resourceGroup&gt; &lt;operationName&gt; *

    Por exemplo

    ```console
    azure insights alerts rule log set myActivityLogRule eastus myresourceGroupName Microsoft.Storage/storageAccounts/listKeys/action
    ```

    A operationName corresponde a um tipo de evento para uma entrada no registro de atividade. Exemplos incluem *Microsoft.Compute/virtualMachines/delete* e *microsoft.insights/diagnosticSettings/write*.

    Você pode usar o comando [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) do PowerShell para obter uma lista de possíveis operationNames. Como alternativa, você pode usar o portal do Azure para consultar o log de atividade e localizar específico passaram operações que você deseja criar um alerta para. As operações mostradas no modo de exibição gráfico de log de nomes amigáveis. Analisar o JSON para a entrada e retirar o valor de OperationName.   

7. Você pode confirmar que os alertas foram criados corretamente examinando uma regra individual.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```

8. Para excluir regras, use um comando do formulário:

    **Excluir regra de alertas de ideias** [Opções] &lt;resourceGroup&gt; &lt;nome_da_regra&gt;

    Esses comandos exclua as regras que criou anteriormente neste artigo.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```



## <a name="next-steps"></a>Próximas etapas

* [Obtenha uma visão geral de monitoramento Azure](monitoring-overview.md) , incluindo os tipos de informações você pode coletar e monitorar.
* Saiba mais sobre [Configurando webhooks em alertas](insights-webhooks-alerts.md).
* Saiba mais sobre [Runbooks de automação do Azure](..\automation\automation-starting-a-runbook.md).
* Obtenha uma [Visão geral de coletar logs de diagnóstico](monitoring-overview-of-diagnostic-logs.md) para coletar métricas de alta frequência detalhadas no seu serviço.
* Obtenha uma [Visão geral do conjunto de métricas](insights-how-to-customize-monitoring.md) para garantir que seu serviço está disponível e responde.
