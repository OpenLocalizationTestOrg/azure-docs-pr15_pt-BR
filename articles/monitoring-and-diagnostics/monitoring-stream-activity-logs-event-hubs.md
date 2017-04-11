<properties
    pageTitle="O Log de atividade Azure para Hubs de eventos de fluxo | Microsoft Azure"
    description="Saiba como transmitir o Log de atividade do Azure para Hubs de evento."
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
    ms.date="10/03/2016"
    ms.author="johnkem"/>

# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Fluxo o Log de atividade Azure para Hubs de evento
O [**Log de atividade do Azure**](./monitoring-overview-activity-logs.md) pode ser transmitido em quase em tempo real para qualquer aplicativo usando a opção de "Exportar" interna no portal ou habilitando a Id de regra de barramento de serviço em um perfil de Log por meio do Azure Cmdlets do PowerShell ou CLI do Azure.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>O que você pode fazer com o Log de atividade e Hubs de evento
Aqui estão algumas maneiras que você pode usar o recurso de fluxo para o Log de atividade:

- **Fluxo para sistemas de registro em log e telemetria de terceiros** – ao longo do tempo, Hubs de evento streaming se tornará o mecanismo de soluções de análise de log e inserido seu registro de atividade SIEMs de terceiros.
- **Criar uma plataforma de log e telemetria personalizada** – se você já tiver uma plataforma de telemetria personalizados ou está pensando sobre a criação de um, altamente escaláveis publicação assinatura natureza do evento Hubs permite flexibilidade inclusão o log de atividade. [Consulte Guia de Dan Rosanova usando Hubs de evento em uma plataforma de telemetria escala global aqui.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>Habilitar o streaming do Log de atividade
Você pode habilitar o streaming do Log de atividade programaticamente ou através do portal. De qualquer forma, que você escolha um Namespace de barramento de serviço e uma política de acesso compartilhado para aquele namespace e um Hub de evento é criado no namespace quando o primeiro novo evento de Log de atividade ocorre. Se você não tiver um Namespace de barramento de serviço, primeiro é necessário criar uma. Se você tiver transmitidos anteriormente eventos de Log da atividade para este Namespace de barramento de serviço, o Hub de evento criada anteriormente será reutilizado. A política de acesso compartilhado define as permissões que tem o mecanismo de streaming. Hoje, o fluxo para um evento Hubs requer permissões de **Gerenciar**, **ler**e **Enviar** . Você pode criar ou modificar políticas de acesso do serviço barramento Namespace compartilhadas no portal do clássico sob a guia "Configurar" para seu Namespace de barramento de serviço. Para atualizar o perfil de log do Log de atividade para incluir streaming, o usuário que fará a alteração deve ter a permissão de ListKey na regra de autorização de barramento desse serviço.

### <a name="via-azure-portal"></a>Por meio do portal do Azure 
1. Navegue até a lâmina de **Registro de atividades** usando o menu no lado esquerdo do portal.

    ![Navegue até o Log de atividades no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Clique no botão **Exportar** na parte superior da lâmina.

    ![Botão Exportar no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Na lâmina que aparece, você pode selecionar as regiões para o qual você gostaria de eventos de fluxo e o Namespace do barramento de serviço no qual você gostaria de um Hub de evento seja criado por streaming esses eventos.

    ![Exportar blade de Log de atividade](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Clique em **Salvar** para salvar essas configurações. As configurações são aplicadas imediatamente à sua assinatura.


### <a name="via-powershell-cmdlets"></a>Por meio de Cmdlets do PowerShell
Se já existe um perfil de log, primeiro é necessário remover o perfil.

1. Use `Get-AzureRmLogProfile` para identificar se existe um perfil de log
2. Em caso afirmativo, use `Remove-AzureRmLogProfile` para removê-lo.
3. Use `Set-AzureRmLogProfile` para criar um perfil:

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

O serviço de regra é uma cadeia de caracteres com este formato: {identificação do recurso de barramento de serviço} /authorizationrules/ {chave name}, por exemplo 

### <a name="via-azure-cli"></a>Via CLI Azure
Se já existe um perfil de log, primeiro é necessário remover o perfil.

1. Use `azure insights logprofile list` para identificar se existe um perfil de log
2. Em caso afirmativo, use `azure insights logprofile delete` para removê-lo.
3. Use `azure insights logprofile add` para criar um perfil:

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

O serviço de regra é uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`.
 
## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Como posso consumir os dados do log de eventos Hubs?
[O esquema para o Log de atividade está disponível aqui](./monitoring-overview-activity-logs.md). Cada evento está em uma matriz de blobs JSON chamado "registros".

## <a name="next-steps"></a>Próximas etapas
- [Arquivar o Log de atividade para uma conta de armazenamento](./monitoring-archive-activity-log.md)
- [Leia a visão geral do Log de atividade do Azure](./monitoring-overview-activity-logs.md)
- [Configurar um alerta com base em um evento de Log de atividade](./insights-auditlog-to-webhook-email.md)
