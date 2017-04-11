<properties
    pageTitle="Configurar webhooks no Azure alertas métricas | Microsoft Azure"
    description="Redirecionar Azure alertas para outros sistemas não Azure."
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
    ms.date="09/15/2016"
    ms.author="ashwink"/>

# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>Configurar um webhook em um alerta de métrica Azure

Webhooks permitem que você rotear uma notificação de alerta Azure para outros sistemas para ações de processamento posterior ou personalizados. Você pode usar um webhook em um alerta para roteá-lo para serviços que enviar SMS, registrar bugs, notifique uma equipe por meio de serviços de bate-papo/mensagens ou fazer qualquer número de outras ações. Este artigo descreve como configurar um webhook em um alerta de métrica Azure e aparência a carga para a POSTAGEM HTTP um webhook. Para obter informações sobre a configuração e o esquema para um Log de atividades do Azure alertar (alerta eventos), [Consulte esta página em vez disso](./insights-auditlog-to-webhook-email.md).

Alertas Azure HTTP POST o conteúdo do alerta em JSON formato, esquema definido abaixo, para um webhook URI que você fornecer ao criar o alerta. Esta URI deve ser um ponto de extremidade HTTP ou HTTPS válido. Azure postagens uma entrada por solicitação quando um alerta é ativado.

## <a name="configuring-webhooks-via-the-portal"></a>Configurando webhooks através do portal

Você pode adicionar ou atualizar o URI webhook a tela de alertas de Create/Update no [portal](https://portal.azure.com/).

![Adicionar uma regra de alerta](./media/insights-webhooks-alerts/Alertwebhook.png)

Você também pode configurar um alerta para postar um webhook URI usando os [Cmdlets do PowerShell do Azure](./insights-powershell-samples.md#create-alert-rules), [Entre plataformas CLI](./insights-cli-samples.md#work-with-alerts)ou [API do Azure Monitor REST](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>Autenticação do webhook

O webhook pode autenticar usando um destes métodos:

1. **Autorização baseada em token** - o webhook URI é salvo com uma identificação de token, ex. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.  **Autorização básica** - o webhook URI é salvo com um nome de usuário e senha, por exemplo. `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Esquema de carga

A operação de POSTAGEM contém a seguinte carga JSON e esquema para todos os alertas baseados em métrica.

```JSON
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```


| Campo | Obrigatório | Conjunto fixo de valores | Anotações |
| :-------------| :-------------   | :-------------   | :-------------   |
|status|Y|"Ativado", "Resolvido"|Status do alerta baseada as condições que você definiu.|
|contexto| Y | | O contexto de alerta.|
|carimbo de hora| Y | | A hora em que o alerta foi acionado.|
|ID | Y | | Cada regra de alerta tenha uma id exclusiva.|
|nome               |Y                  |                   | O nome de alerta.|
|Descrição        |Y                  |                           |Descrição do alerta.|
|conditionType      |Y                  |"Métrico", "Evento"          |Dois tipos de alertas são suportados. Um baseado em uma condição métrica e outro com base em um evento no registro de atividade. Use esse valor para verificar se o alerta se baseia métrica ou evento.|
|condição          |Y                  |                           | Os campos específicos para verificar de acordo com o conditionType.|
|metricName         |para alertas de métricas  |                           |O nome da métrica que define o que monitora a regra.|
|metricUnit         |para alertas de métricas  |"Bytes", "BytesPerSecond", "Contagem", "CountPerSecond", "Porcentagem", "Segundos"|     A unidade permitida na métrica. [Valores permitidos são listados aqui](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx).|
|metricValue        |para alertas de métricas  |                           |O valor real da métrica que causou o alerta.|
|limite          |para alertas de métricas  |                           |O valor de limite no qual o alerta está ativado.|
|Tamanho_da_janela         |para alertas de métricas  |                           |O período de tempo que é usado para monitorar a atividade de alerta com base no limite. Deve estar entre 5 minutos e 1 dia. Formato de duração de ISO 8601.|
|timeAggregation    |para alertas de métricas  |"Média", "Sobrenome", "Máximo", "Mínimo", "Nenhum", "totais" | Como os dados coletados devem ser combinados ao longo do tempo. O valor padrão é média. [Valores permitidos são listados aqui](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx).|
|operador           |para alertas de métricas  |                           |O operador usado para comparar os dados de métrica atuais para o limite definido.|
|subscriptionId     |Y                  |                           |ID do Azure assinatura.|
|resourceGroupName  |Y                  |                           |Nome do grupo de recursos para o recurso afetado.|
|resourceName       |Y                  |                           |Nome de recurso do recurso afetado.|
|tipo de recurso       |Y                  |                           |Tipo de recurso do recurso afetado.|
|identificação de recurso         |Y                  |                           |Identificação do recurso do recurso afetado.|
|resourceRegion     |Y                  |                           |Região ou local do recurso afetado.|
|portalLink         |Y                  |                           |Link direto para a página de resumo do recurso portal.|
|Propriedades         |N                  |Opcional                   |Conjunto de `<Key, Value>` pares (isto é, `Dictionary<String, String>`) que inclui detalhes sobre o evento. O campo propriedades é opcional. Personalizado UI ou lógica baseada em aplicativo fluxo de trabalho, os usuários possam inserir chave/valores que pode ser passados via a carga. A maneira alternativa de passar propriedades personalizadas para a webhook é via o uri webhook próprio (como parâmetros de consulta)|


>[AZURE.NOTE] O campo propriedades só pode ser definido usando a [API do Azure Monitor REST](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre alertas Azure e webhooks em vídeo [Integrar o Azure alertas com PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080)
- [Executar scripts de automação do Azure (Runbooks) no Azure alertas](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Use o aplicativo de lógica para enviar um SMS via Twilio de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
- [Use o aplicativo de lógica para enviar uma mensagem a margem de atraso de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
- [Use o aplicativo de lógica para enviar uma mensagem para uma fila do Azure de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)
