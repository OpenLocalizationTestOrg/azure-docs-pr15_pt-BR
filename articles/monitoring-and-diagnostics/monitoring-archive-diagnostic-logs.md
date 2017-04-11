<properties
    pageTitle="Arquivar os Logs de diagnóstico Azure | Microsoft Azure"
    description="Saiba como arquivar seus Logs de diagnóstico do Azure para retenção de longo prazo em uma conta de armazenamento."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="johnkem"/>

# <a name="archive-azure-diagnostic-logs"></a>Arquivar os Logs de diagnóstico Azure
Neste artigo, vamos mostrar como você pode usar o portal do Azure, Cmdlets do PowerShell, CLI ou API REST para arquivar os [Logs de diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md) em uma conta de armazenamento. Essa opção é útil se você quiser manter seus Logs de diagnóstico com uma política de retenção opcional para auditoria, análise estática ou backup.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, você precisa [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) à qual você pode arquivar os Logs de diagnóstico. É altamente recomendável que você não usa uma conta de armazenamento existente com dados de outros, não monitoramento armazenados para que você pode controlar melhor acesso aos dados de monitoramento. No entanto, se você estiver arquivando também o Log de atividade e métricas de diagnósticos para uma conta de armazenamento, faz sentido para usar essa conta de armazenamento para seus Logs de diagnóstico também manter todos os dados de monitoramento em um local central. A conta de armazenamento usada deve ser uma conta de armazenamento de finalidade geral, não uma conta de armazenamento de blob.

## <a name="diagnostic-settings"></a>Configurações de diagnóstico
Para arquivar seus Logs de diagnóstico usando qualquer um dos métodos abaixo, você definir uma **Configuração de diagnóstico** para um determinado recurso. Uma configuração diagnóstico para um recurso define as categorias de logs que estão armazenados ou transmitida e saídas — hub de conta e/ou o evento de armazenamento. Ele também define a política de retenção (número de dias para reter) para eventos de cada categoria de log armazenados em uma conta de armazenamento. Se uma política de retenção é definida como zero, eventos para essa categoria de log são armazenados indefinidamente (ou seja, uma eternidade). Uma política de retenção caso contrário, pode ser qualquer número de dias entre 1 e 2147483647. [Você pode ler mais sobre configurações de diagnóstico aqui](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).

## <a name="archive-diagnostic-logs-using-the-portal"></a>Arquivar os Logs de diagnóstico usando o portal

1. No portal do, clique na lâmina do recurso para o recurso no qual você gostaria de habilitar o arquivamento de Logs de diagnóstico.
2. Na seção **monitoramento** do menu de configurações do recurso, selecione **Diagnóstico**.

    ![Seção monitoramento do menu do recurso](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-sec.png)
3. Marque a caixa **Exportar para conta de armazenamento**e selecione uma conta de armazenamento. Opcionalmente, defina um número de dias para manter esses logs usando o **retenção (dias)** os controles deslizantes. Uma retenção de zero dias armazena os logs indefinidamente.

    ![Blade de Logs de diagnóstico](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-blade.png)
4. Clique em **Salvar**.

Logs de diagnóstico são arquivados a essa conta de armazenamento assim que novos dados de evento são gerados.

## <a name="archive-diagnostic-logs-via-the-powershell-cmdlets"></a>Arquivar os Logs de diagnóstico via Cmdlets do PowerShell

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Propriedade         | Necessário | Descrição                                                                                           |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| Identificação de recurso       | Sim      | Identificação do recurso do recurso no qual você deseja definir uma configuração de diagnóstico.                            |
| StorageAccountId | Não       | Identificação do recurso da conta de armazenamento para o qual os Logs de diagnóstico devem ser salvas.                          |
| Categorias       | Não       | Lista separada por vírgulas de categorias de log para habilitar.                                                     |
| Habilitado          | Sim      | Booliano que indica se o diagnóstico é habilitado ou desabilitado neste recurso.                  |
| RetentionEnabled | Não       | Booliano que indica se uma política de retenção são habilitado este recurso.                            |
| RetentionInDays  | Não       | Número de dias para o qual os eventos devem ser mantidos entre 1 e 2147483647. Um valor zero armazena os logs indefinidamente. |

## <a name="archive-the-activity-log-via-the-cross-platform-cli"></a>Arquivar o Log de atividade por meio da CLI entre plataformas

```
azure insights diagnostic set --resourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg --storageId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage –categories networksecuritygroupevent,networksecuritygrouprulecounter --enabled true
```

| Propriedade         | Necessário | Descrição                                                                                           |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| identificação de recurso       | Sim      | Identificação do recurso do recurso no qual você deseja definir uma configuração de diagnóstico.                            |
| storageId        | Não       | Identificação do recurso da conta de armazenamento para o qual os Logs de diagnóstico devem ser salvas.                          |
| categorias       | Não       | Lista separada por vírgulas de categorias de log para habilitar.                                                     |
| habilitado          | Sim      | Booliano que indica se o diagnóstico é habilitado ou desabilitado neste recurso.                  |

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Logs de diagnóstico de arquivamento por meio da API REST
[Consulte este documento](https://msdn.microsoft.com/library/azure/dn931931.aspx) para obter informações sobre como você pode configurar uma configuração de diagnóstico usando a API REST do Azure Monitor.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Esquema de Logs de diagnóstico na conta de armazenamento
Depois que você configurar arquivamento, um contêiner de armazenamento é criado na conta de armazenamento assim que ocorre um evento em uma das categorias log que você ativou. Os blobs dentro do contêiner siga o mesmo formato em Logs de diagnóstico e Log de atividades. A estrutura destas blobs é:

> ideias - logs-{nome de categoria de log} / resourceId = / assinaturas / {ID da assinatura} {nome do grupo de recursos} /RESOURCEGROUPS/ /PROVIDERS/ {nome do provedor de recurso} / {recurso digite} / {nome do recurso} / y = {quatro dígitos numérico ano} / m = {dois dígitos numérico mês} / d = {dois dígitos numérico day} / h = {relógio de 24 horas de dois dígitos hour}/m=00/PT1H.json

Ou, mais simplesmente,

> ideias - logs-{nome de categoria de log} / resourceId = / {recurso Id} / y = {quatro dígitos numérico ano} / m = {dois dígitos numérico mês} / d = {dois dígitos numérico day} / h = {relógio de 24 horas de dois dígitos hour}/m=00/PT1H.json

Por exemplo, pode ser um nome de blob:

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json

Cada blob PT1H.json contém um blob JSON de eventos que ocorreram na hora especificada na URL blob (por exemplo, h = 12). Durante a hora presente, eventos são acrescentados ao arquivo PT1H.json conforme eles ocorrem. O valor de minuto (m = 00) é sempre 00, como eventos de Log de diagnóstico são divididos em blobs individuais por hora.

Dentro do arquivo de PT1H.json, cada evento é armazenado na matriz "registros", seguindo este formato:

```
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Nome do elemento  | Descrição                                                                                                 |
|---------------|-------------------------------------------------------------------------------------------------------------|
| tempo          | Carimbo de hora, quando o evento foi gerado pelo serviço do Azure processamento da solicitação correspondente do evento. |
| identificação de recurso    | Identificação do recurso do recurso afetado.                                                                       |
| operationName | Nome da operação.                                                                                      |
| categoria      | Categoria de log do evento.                                                                                  |
| Propriedades    | Conjunto de `<Key, Value>` pares (isto é, dicionário) descrevendo os detalhes do evento.                            |

> [AZURE.NOTE] As propriedades e o uso dessas propriedades podem variar dependendo do recurso.

## <a name="next-steps"></a>Próximas etapas
- [Baixar blobs para análise](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [Logs de diagnóstico de fluxo para Hubs de evento](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [Leia mais sobre Logs de diagnóstico](monitoring-overview-of-diagnostic-logs.md)
