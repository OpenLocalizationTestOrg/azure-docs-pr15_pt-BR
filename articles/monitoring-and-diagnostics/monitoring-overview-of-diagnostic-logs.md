<properties
    pageTitle="Visão geral dos Logs de diagnóstico Azure | Microsoft Azure"
    description="Saiba quais são os Logs de diagnóstico do Azure e como você pode usá-los entender os eventos que ocorrem dentro de um recurso Azure."
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
    ms.date="10/12/2016"
    ms.author="johnkem; magoedte"/>

# <a name="overview-of-azure-diagnostic-logs"></a>Visão geral do Azure Logs de diagnóstico
**Logs de diagnóstico do Azure** são logs emitidos por um recurso que fornecem dados sofisticados, frequentes sobre a operação do recurso. O conteúdo desses logs varia por tipo de recurso (por exemplo, logs de sistema de eventos do Windows são uma categoria de Log de diagnóstico para VMs e blob, tabela e logs de fila são categorias de Logs de diagnóstico para contas de armazenamento) e diferir o [Log de atividade (anteriormente conhecido como Log de auditoria ou Log operacional)](monitoring-overview-activity-logs.md), que fornece percepção as operações que foram executadas em recursos em sua assinatura. Nem todos os recursos de suporte do novo tipo de Logs de diagnóstico descrito aqui. A lista de serviços de suporte abaixo mostra quais tipos de recursos de suporte a novos Logs de diagnóstico.

![Posicionamento lógico dos Logs de diagnóstico](./media/monitoring-overview-of-diagnostic-logs/logical-placement-chart.png)

## <a name="what-you-can-do-with-diagnostic-logs"></a>O que você pode fazer com os Logs de diagnóstico
Aqui estão algumas coisas que você pode fazer com os Logs de diagnóstico:

- Salvá-los em uma **Conta de armazenamento** para inspeção de auditoria ou manual. Você pode especificar o tempo de retenção (em dias) usando as **Configurações de diagnóstico**.
- [Transmitir eles **Hubs de evento** ](monitoring-stream-diagnostic-logs-to-event-hubs.md) para inclusão por um serviço de terceiros ou a solução de análise personalizado como PowerBI.
- Analisá-los com [A análise de Log OMS](../log-analytics/log-analytics-azure-storage-json.md)

## <a name="diagnostic-settings"></a>Configurações de diagnóstico
Logs de diagnóstico para recursos de computação-não é configurado usando configurações de diagnóstico. **Configurações de diagnóstico** para um controle de recursos:

- Onde os Logs de diagnóstico são enviados (conta de armazenamento, Hubs de evento, e/ou a análise de Log OMS).
- Quais categorias de Log são enviadas.
- Por quanto tempo cada categoria de log deve ser mantida em uma conta de armazenamento – uma retenção de zero dias significa que os registros são mantidos para sempre. Caso contrário, esse valor pode variar de 1 a 2147483647. Se as políticas de retenção são definidas, mas armazenar logs em uma conta de armazenamento está desativado (por exemplo se apenas Hubs de evento ou OMS estão selecionadas), as políticas de retenção não tem efeito.

Essas configurações são configuradas facilmente via a lâmina de diagnóstico para um recurso no portal do Azure, por meio de comandos do PowerShell do Azure e CLI ou por meio da [API do Azure Monitor REST](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [AZURE.WARNING] Logs de diagnóstico e métricas para recursos de computação (por exemplo, VMs ou serviço tecidos) usam [um mecanismo separado para configuração e seleção de saídas](../azure-diagnostics.md).

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Como habilitar a coleção de Logs de diagnóstico
Coleção de Logs de diagnóstico pode ser habilitada como parte da criação de um recurso ou depois de um recurso é criado por meio de blade do recurso no Portal. Você também pode ativar Logs de diagnóstico em qualquer ponto usando comandos do PowerShell do Azure ou CLI ou usar a API REST do Azure Monitor.

> [AZURE.TIP] Estas instruções não podem aplicar diretamente para cada recurso. Consulte os links de esquema na parte inferior desta página para entender as etapas especiais que podem se aplicar a determinados tipos de recursos.

[Este artigo mostra como você pode usar um modelo de recurso para habilitar configurações de diagnóstico ao criar um recurso](./monitoring-enable-diagnostic-logs-using-template.md)

### <a name="enable-diagnostic-logs-in-the-portal"></a>Ativar Logs de diagnóstico no portal
Você pode ativar Logs de diagnóstico no portal do Azure, quando você cria tipos de recursos de computação habilitando a extensão do Windows ou Linux Azure diagnósticos:

1.  Vá para **novo** e escolha o recurso que você está interessado.
2.  Após definir as configurações básicas e selecionando um tamanho, na lâmina **configurações** , em **monitoramento**, selecione **habilitado** e escolha uma conta de armazenamento onde você deseja armazenar os Logs de diagnóstico. Cobrado taxas de dados normal para armazenamento e transações ao enviar diagnósticos para uma conta de armazenamento.

    ![Ativar Logs de diagnóstico durante a criação de recurso](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3.  Clique em **Okey** e criar o recurso.

Para obter recursos não computação, você pode ativar Logs de diagnóstico no portal do Azure após um recurso tiver sido criado, fazendo o seguinte:

1.  Vá para a lâmina para o recurso e abra a lâmina de **Diagnóstico** .
2.  Clique **em** e selecione uma conta de armazenamento e/ou o Hub de evento.

    ![Ativar Logs de diagnóstico após a criação do recurso](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3.  Em **Logs**, selecione quais **Categorias de Log** que você gostaria de coletar ou fluxo.
4.  Clique em **Salvar**.

### <a name="enable-diagnostic-logs-via-powershell"></a>Ativar Logs de diagnóstico por meio do PowerShell
Para ativar Logs de diagnóstico via Cmdlets do PowerShell do Azure, use os seguintes comandos.

Para habilitar o armazenamento de Logs de diagnóstico em uma conta de armazenamento, use este comando:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true

A ID da conta de armazenamento é a identificação do recurso para a conta de armazenamento ao qual você deseja enviar os logs.

Para habilitar o streaming de Logs de diagnóstico a um Hub de evento, use este comando:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true

O serviço de regra é uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`.

Para habilitar o envio de Logs de diagnóstico para um espaço de trabalho de análise de Log, use este comando:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [log analytics workspace id] -Enabled $true

> [AZURE.NOTE] O parâmetro WorkspaceId não está disponível na edição de outubro. Ele estará disponível na versão de novembro.

Você pode obter sua ID de espaço de trabalho de análise de Log no portal do Azure.

Você pode combinar esses parâmetros para habilitar várias opções de saída.

### <a name="enable-diagnostic-logs-via-cli"></a>Ativar Logs de diagnóstico via CLI
Para ativar Logs de diagnóstico por meio da CLI do Azure, use os seguintes comandos:

Para habilitar o armazenamento de Logs de diagnóstico em uma conta de armazenamento, use este comando:

    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true

A ID da conta de armazenamento é a identificação do recurso para a conta de armazenamento ao qual você deseja enviar os logs.

Para habilitar o streaming de Logs de diagnóstico a um Hub de evento, use este comando:

    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true

O serviço de regra é uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`.

Para habilitar o envio de Logs de diagnóstico para um espaço de trabalho de análise de Log, use este comando:

    azure insights diagnostic set --resourceId <resourceId> --workspaceId <workspaceId> --enabled true

> [AZURE.NOTE] O parâmetro workspaceId não está disponível na edição de outubro. Ele estará disponível na versão de novembro.

Você pode obter sua ID de espaço de trabalho de análise de Log no portal do Azure.

Você pode combinar esses parâmetros para habilitar várias opções de saída.

### <a name="enable-diagnostic-logs-via-rest-api"></a>Ativar Logs de diagnóstico via API REST
Para alterar as configurações de diagnóstico usando a API REST do Azure Monitor, consulte [Este documento](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-diagnostic-settings-in-the-portal"></a>Gerenciar as configurações de diagnóstico no portal

Para garantir que todos os recursos estão corretamente configurados com configurações de diagnósticos, você pode navegar para a lâmina de **monitoramento** no portal e abrir a lâmina de **Logs de diagnóstico** .

![Blade de Logs de diagnóstico no portal](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

Você talvez precise clicar "Mais serviços" para encontrar a lâmina de monitoramento.

Neste lâmina, você pode visualizar e filtrar todos os recursos que os Logs de diagnóstico para ver se ele tiver habilitado de diagnóstico e qual conta de armazenamento, hub de evento e/ou espaço de trabalho de análise de Log esses logs estão fluindo para de suporte.

![Resultados de blade de Logs de diagnóstico no portal](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

Clicando em um recurso mostrará todos os logs que foram armazenados na conta de armazenamento e oferecem a opção para desabilitar ou modificar as configurações de diagnósticos. Clique no ícone de download para baixar logs para um determinado período.

![Diagnóstico Logs blade um recurso](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [AZURE.NOTE] Logs de diagnóstico só serão aparecem neste modo de exibição e estará disponível para download, se você tiver configurado as definições de diagnóstico para salvá-los em uma conta de armazenamento.

Clicando no link para **Configurações de diagnóstico** exibirá a lâmina de configurações de diagnóstico, onde você pode habilitar, desabilitar ou modificar as configurações de diagnósticos para o recurso selecionado.

## <a name="supported-services-and-schema-for-diagnostic-logs"></a>Serviços com suporte e esquema para Logs de diagnóstico
O esquema para Logs de diagnóstico varia dependendo a categoria de recurso e de log. Abaixo estão os serviços com suporte e seu esquema.

| Serviço                       | Esquema & documentos                                                                                                   |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------|
|    Balanceador de carga de software     |    [Análise de log de Balanceador de carga do Azure (visualização)](../load-balancer/load-balancer-monitor-log.md)             |
|    Grupos de segurança de rede    |    [Análise de log de grupos de segurança de rede (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md)     |
|    Gateways de aplicativos       |    [Log de diagnóstico para que o Gateway de aplicativo](../application-gateway/application-gateway-diagnostics.md)     |
|    Chave cofre                  |    [Log do Azure cofre chave](../key-vault/key-vault-logging.md)                                                 |
|    Pesquisa do Azure               |    [Ativar e usar a análise de tráfego de pesquisa](../search/search-traffic-analytics.md)                         |
|    Armazenamento de Lucerne de dados            |    [Acesse os logs de diagnósticos para armazenamento de Lucerne de dados do Azure](../data-lake-store/data-lake-store-diagnostic-logs.md) |
|    Lucerne a análise de dados        |    [Acesse os logs de diagnósticos para análise de Lucerne de dados do Azure](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
|    Aplicativos de lógica                 |    Nenhum esquema disponível.                                                                                         |
|    Lote Azure                |    [Log de diagnóstico lote Azure](../batch/batch-diagnostics.md)                                              |
|    Automação Azure           |    [Análise de log de automação do Azure](../automation/automation-manage-send-joblogs-log-analytics.md)          |
|    Hub de evento                  |    Nenhum esquema disponível.                                                                                         |
|    Barramento de serviço                |    Nenhum esquema disponível.                                                                                         |
|    Análise de fluxo           |    Nenhum esquema disponível.                                                                                         |

## <a name="supported-log-categories-per-resource-type"></a>Suporte para categorias de log por tipo de recurso

|Tipo de recurso|Categoria|Nome de exibição de categoria|
|---|---|---|
|Microsoft.Automation/automationAccounts|JobLogs|Logs de trabalho|
|Microsoft.Automation/automationAccounts|JobStreams|Fluxos de trabalho|
|Microsoft.Batch/batchAccounts|ServiceLog|Logs de serviço|
|Microsoft.DataLakeAnalytics/accounts|Auditoria|Logs de auditoria|
|Microsoft.DataLakeAnalytics/accounts|Solicitações|Logs de solicitação|
|Microsoft.DataLakeStore/accounts|Auditoria|Logs de auditoria|
|Microsoft.DataLakeStore/accounts|Solicitações|Logs de solicitação|
|Microsoft.EventHub/namespaces|ArchiveLogs|Logs de arquivamento|
|Microsoft.EventHub/namespaces|OperationalLogs|Logs operacionais|
|Microsoft.KeyVault/vaults|AuditEvent|Logs de auditoria|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventos de diagnóstico de tempo de execução do fluxo de trabalho|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Eventos de grupo de segurança de rede|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contador de regra de grupo de segurança de rede|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Evento de fluxo de regra de grupo de segurança de rede|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventos de alerta de Balanceador de carga|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Status de integridade de teste de Balanceador de carga|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Log de aplicativo do Access de Gateway|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Log de desempenho do Gateway de aplicativo|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Log de Firewall do Gateway de aplicativo|
|Microsoft.Search/searchServices|OperationLogs|Logs de operação|
|Microsoft.ServerManagement/nodes|RequestLogs|Logs de solicitação|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Logs operacionais|
|Microsoft.StreamAnalytics/streamingjobs|Execução|Execução|
|Microsoft.StreamAnalytics/streamingjobs|Coautoria|Coautoria|

## <a name="next-steps"></a>Próximas etapas
- [Logs de diagnóstico de fluxo para **Hubs de evento**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [Alterar as configurações de diagnóstico usando a API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
- [Analisar os logs com a análise de Log OMS](../log-analytics/log-analytics-azure-storage-json.md)
