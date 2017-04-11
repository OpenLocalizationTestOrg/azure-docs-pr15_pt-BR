<properties
    pageTitle="Visão geral do Log de atividade Azure | Microsoft Azure"
    description="Saiba o que é o Log de atividade do Azure e como você pode usá-lo entender os eventos que ocorrem dentro de sua assinatura do Azure."
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
    ms.date="10/25/2016"
    ms.author="johnkem"/>

# <a name="overview-of-the-azure-activity-log"></a>Visão geral do Log de atividade Azure
O **Log de atividade do Azure** é um log que fornece ideias sobre as operações que foram executadas em recursos em sua assinatura. O Log de atividade era conhecido anteriormente como "Logs de auditoria" ou "Logs operacionais", desde que ele reporta eventos de plano de controle para suas assinaturas. Usando o Log de atividades, você pode determinar a ', quem e quando ' para qualquer gravação operações (colocar, POST, DELETE) tiradas dos recursos em sua assinatura. Você também pode entender o status da operação e outras propriedades relevantes. O Log de atividade não inclui operações de leitura (GET).

O Log de atividade difere [Logs de diagnóstico](./monitoring-overview-of-diagnostic-logs.md), que são todos os logs emitidos por um recurso. Esses logs fornecem dados sobre a operação do recurso, ao invés de operações em desse recurso.

Você pode recuperar eventos de seu registro de atividades usando o portal do Azure, CLI, cmdlets do PowerShell e API do Azure Monitor REST.

Exiba este [vídeo de Introdução ao registro de atividade](https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz).  

## <a name="what-you-can-do-with-the-activity-log"></a>O que você pode fazer com o Log de atividade
Aqui estão algumas coisas que você pode fazer com o Log de atividade:

- Consultar e exibi-lo no **portal do Azure**.
- Consulta-via API REST, Cmdlet do PowerShell ou CLI.
- [Crie um alerta de email ou webhook que aciona um evento de Log de atividade.](./insights-auditlog-to-webhook-email.md)
- [Salvá-lo em uma **Conta de armazenamento** para inspeção manual ou de arquivamento](./monitoring-archive-activity-log.md). Você pode especificar o tempo de retenção (em dias) usando **Perfis de Log**.
- Analisá-lo no PowerBI usando o [**pacote de conteúdo do PowerBI**](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/).
- [Transfira a um **Hub de evento** ](./monitoring-stream-activity-logs-event-hubs.md) para inclusão por um serviço de terceiros ou a solução de análise personalizado como PowerBI.

## <a name="export-the-activity-log-with-log-profiles"></a>Exportar o Log de atividade com perfis de Log
Um **Perfil de Log** controla como o seu registro de atividade é exportado. Usando um perfil de Log, você pode configurar:

- Onde o Log de atividade devem ser enviado (conta de armazenamento ou Hubs de evento)
- Quais categorias de eventos (gravação, excluir, ação) devem ser enviadas
- Quais são as regiões (locais) devem ser exportadas
- Por quanto tempo o registro de atividades devem ser mantido em uma conta de armazenamento – uma retenção de zero dias significa que os registros serão mantidos para sempre. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 2147483647. Se as políticas de retenção são definidas, mas armazenar logs em uma conta de armazenamento está desativado (por exemplo, se somente Hubs de evento ou OMS opções selecionadas), as políticas de retenção não terão efeito.

Essas configurações podem ser definidas por meio da opção de "Exportar" na lâmina Log da atividade no portal. Eles também podem ser configurado programaticamente [usando a API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), cmdlets do PowerShell ou CLI. Uma assinatura só pode ter um perfil de log.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Configurar perfis de log usando o portal do Azure
Você pode transmitir o Log de atividade a um Hub de evento ou armazená-los em uma conta de armazenamento usando a opção de "Exportação" no portal do Azure.

1. Navegue até a lâmina de **Registro de atividades** usando o menu no lado esquerdo do portal.

    ![Navegue até o Log de atividades no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Clique no botão **Exportar** na parte superior da lâmina.

    ![Botão Exportar no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Na lâmina que aparece, você pode selecionar:  
    - regiões para o qual você deseja exportar eventos
    - a conta de armazenamento à qual você deseja salvar os eventos
    - o número de dias que você deseja manter esses eventos em armazenamento. Uma configuração de 0 dias retém os logs uma eternidade.
    - o Namespace de barramento de serviço no qual você gostaria de um Hub de evento seja criado por streaming esses eventos.

    ![Exportar blade de Log de atividade](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Clique em **Salvar** para salvar essas configurações. As configurações são aplicadas imediatamente à sua assinatura.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Configurar perfis de log usando os Cmdlets do PowerShell do Azure
#### <a name="get-existing-log-profile"></a>Obter o perfil de log existente
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Adicionar um perfil de log
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Propriedade         | Necessário | Descrição   |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome             | Sim      | Nome do seu perfil de log.                                 |
| StorageAccountId | Não       | Identificação do recurso da conta de armazenamento para o qual o Log de atividade deve ser salvo.                         |
| serviceBusRuleId | Não       | ID de regra de barramento de serviço do namespace barramento de serviço que você gostaria de ter hubs de evento criados no. É uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| Locais        | Sim      | Lista separada por vírgulas das regiões para o qual você gostaria de coletar eventos de Log de atividade.              |
| RetentionInDays  | Sim      | Número de dias para quais eventos devem ser mantidos, entre 1 e 2147483647. Um valor igual a zero armazena os logs indefinidamente (sempre). |
| Categorias       | Não       | Lista separada por vírgulas de categorias de eventos que devem ser coletados. Valores possíveis são gravação, exclusão e ação.                                 |

#### <a name="remove-a-log-profile"></a>Remover um perfil de log
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Configurar perfis de log utilizando a CLI de plataforma híbrida do Azure
#### <a name="get-existing-log-profile"></a>Obter o perfil de log existente
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
O `name` propriedade deve ser o nome do seu perfil de log.

#### <a name="add-a-log-profile"></a>Adicionar um perfil de log
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Propriedade         | Necessário | Descrição   |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nome             | Sim      | Nome do seu perfil de log.                                 |
| storageId        | Não       | Identificação do recurso da conta de armazenamento para o qual o Log de atividade deve ser salvo.                         |
| serviceBusRuleId | Não       | ID de regra de barramento de serviço do namespace barramento de serviço que você gostaria de ter hubs de evento criados no. É uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| locais        | Sim      | Lista separada por vírgulas das regiões para o qual você gostaria de coletar eventos de Log de atividade.              |
| retentionInDays  | Sim      | Número de dias para quais eventos devem ser mantidos, entre 1 e 2147483647. Um valor igual a zero armazena os logs indefinidamente (sempre).     |
| categorias       | Não       | Lista separada por vírgulas de categorias de eventos que devem ser coletados. Valores possíveis são gravação, exclusão e ação.                                 |

#### <a name="remove-a-log-profile"></a>Remover um perfil de log
```
azure insights logprofile delete --name my_log_profile
```

## <a name="event-schema"></a>Esquema de evento
Cada evento no registro de atividade tem um blob JSON semelhante a este exemplo:

```
{
  "value": [ {
    "authorization": {
      "action": "microsoft.support/supporttickets/write",
      "role": "Subscription Admin",
      "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
    },
    "caller": "admin@contoso.com",
    "channels": "Operation",
    "claims": {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
      "iat": "1421876371",
      "nbf": "1421876371",
      "exp": "1421880271",
      "ver": "1.0",
      "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
      "puid": "20030000801A118C",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
      "name": "John Smith",
      "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
      "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.microsoft.com/claims/authnclassreference": "1"
    },
    "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "description": "",
    "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
    "eventName": {
      "value": "EndRequest",
      "localizedValue": "End request"
    },
    "eventSource": {
      "value": "Microsoft.Resources",
      "localizedValue": "Microsoft Resources"
    },
    "httpRequest": {
      "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
      "clientIpAddress": "192.168.35.115",
      "method": "PUT"
    },
    "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
    "level": "Informational",
    "resourceGroupName": "MSSupportGroup",
    "resourceProviderName": {
      "value": "microsoft.support",
      "localizedValue": "microsoft.support"
    },
    "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
    "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "operationName": {
      "value": "microsoft.support/supporttickets/write",
      "localizedValue": "microsoft.support/supporttickets/write"
    },
    "properties": {
      "statusCode": "Created"
    },
    "status": {
      "value": "Succeeded",
      "localizedValue": "Succeeded"
    },
    "subStatus": {
      "value": "Created",
      "localizedValue": "Created (HTTP Status Code: 201)"
    },
    "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
    "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
    "subscriptionId": "s1"
  } ],
"nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

| Nome do elemento         | Descrição             |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| autorização        | Blob de propriedades RBAC do evento. Geralmente inclui as propriedades de "ação", "função" e "escopo".|
| chamador               | Endereço de email do usuário que executou a operação, a declaração UPN ou a declaração SPN com base na disponibilidade.|
| canais             | Um dos seguintes valores: "Administrador", "Operação"|
| correlationId        | Geralmente um GUID no formato de cadeia de caracteres. Eventos que compartilham um correlationId pertencem à mesma ação uber.   |
| Descrição          | Descrição de texto estático de um evento.                              |
| eventDataId          | Identificador exclusivo de um evento.    |
| eventSource          | Nome do serviço Azure ou infraestrutura que gerou esse evento.    |
| httpRequest          | Blob descrevendo a solicitação Http. Geralmente inclui "clientRequestId", "clientIpAddress" e "método" (método HTTP. Por exemplo, colocar).                            |
| nível                | Nível do evento. Um dos seguintes valores: "Crítico", "Erro", "Aviso", "Informativo" e "Detalhado"  |
| resourceGroupName    | Nome do grupo de recursos para o recurso afetado.               |
| resourceProviderName | Nome do provedor do recurso para o recurso afetado             |
| resourceUri          | Identificação do recurso do recurso afetado.                               |
| operationId          | Um GUID compartilhado entre os eventos que correspondem a uma única operação.         |
| operationName        | Nome da operação.  |
| Propriedades           | Conjunto de `<Key, Value>` pares (ou seja, um dicionário) descrevendo os detalhes do evento.                                |
| status               | Cadeia de caracteres que descreve o status da operação. Alguns valores comuns estão: iniciado, em andamento, com êxito, falha, ativo, resolvido.                                |
| subStatus            | Geralmente chamar o código de status HTTP do resto correspondente, mas também pode incluir outras cadeias de caracteres descrevendo um substatus, como esses valores comuns: Okey (código de Status HTTP: 200) criados (código de Status HTTP: 201), aceito (código de Status HTTP: 202), não conteúdo (código de Status HTTP: 204), solicitação incorreta (código de Status HTTP: 400), não encontrado (código de Status HTTP: 404), conflito (código de Status de HTTP : 409), erro interno do servidor (código de Status HTTP: 500), serviço indisponível (código de Status HTTP: 503), tempo limite do Gateway (código de Status HTTP: 504). |
| eventTimestamp       | Carimbo de hora, quando o evento foi gerado pelo serviço do Azure processamento da solicitação correspondente do evento.     |
| submissionTimestamp  | Carimbo de hora, quando o evento se tornou disponível para consultar.             |
| subscriptionId       | ID do Azure assinatura.  |
| nextLink             | Símbolo de continuação para buscar o próximo conjunto de resultados quando eles são divididos em várias respostas. Normalmente necessária quando há mais de 200 registros.     |

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre o Log de atividade (antigo Logs de auditoria)](../resource-group-audit.md)
- [Fluxo o Log de atividade Azure para Hubs de evento](./monitoring-stream-activity-logs-event-hubs.md)
