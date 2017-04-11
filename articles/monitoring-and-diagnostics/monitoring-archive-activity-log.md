<properties
    pageTitle="Arquivar o Log de atividade Azure | Microsoft Azure"
    description="Saiba como arquivar o registro de atividade do Azure retenção de longo prazo em uma conta de armazenamento."
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
    ms.date="08/23/2016"
    ms.author="johnkem"/>

# <a name="archive-the-azure-activity-log"></a>Arquivar o Log de atividade Azure
Neste artigo, vamos mostrar como você pode usar o portal do Azure, Cmdlets do PowerShell ou entre plataformas CLI para arquivar seu [**Registro de atividade do Azure**](monitoring-overview-activity-logs.md) em uma conta de armazenamento. Essa opção é útil se você quiser manter seu registro de atividade mais de 90 dias (com controle total sobre a política de retenção) para auditoria, análise estática ou backup. Se você só precisa manter seus eventos por 90 dias ou menos você não precisa configurar o arquivamento para uma conta de armazenamento, como eventos de Log de atividade são mantidos na plataforma Windows Azure por 90 dias sem ativar arquivamento.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, você precisa [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) à qual você pode arquivar o registro de atividade. É altamente recomendável que você não usa uma conta de armazenamento existente com dados de outros, não monitoramento armazenados para que você pode controlar melhor acesso aos dados de monitoramento. No entanto, se você também estiver arquivando Logs de diagnóstico e métricas para uma conta de armazenamento, faz sentido para usar essa conta de armazenamento para o Log de atividade também manter todos os dados de monitoramento em um local central. A conta de armazenamento usada deve ser uma conta de armazenamento de finalidade geral, não uma conta de armazenamento de blob.

## <a name="log-profile"></a>Perfil de log
Para arquivar o registro de atividades usando qualquer um dos métodos abaixo, você deve definir o **Perfil de Log** para uma assinatura. O perfil de Log define o tipo de eventos que são armazenados ou transmitidos e saídas — hub de conta e/ou o evento de armazenamento. Ele também define a política de retenção (número de dias para reter) para eventos armazenados em uma conta de armazenamento. Se a política de retenção é definida como zero, eventos são armazenados indefinidamente. Caso contrário, isso pode ser definido para qualquer valor entre 1 e 2147483647. [Você pode ler mais sobre log perfis aqui](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles).

## <a name="archive-the-activity-log-using-the-portal"></a>Arquivar o Log de atividade usando o portal
1. No portal do, clique no link de **Registro de atividade** na barra de navegação esquerda. Se você não vir um link para o registro de atividades, clique no link de **Mais serviços** primeiro.

    ![Navegue até blade de Log de atividade](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. Na parte superior da lâmina, clique em **Exportar**.

    ![Clique no botão Exportar](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. Na lâmina que aparece, marque a caixa **Exportar para uma conta de armazenamento** e selecione uma conta de armazenamento.

    ![Definir uma conta de armazenamento](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. Usando o controle deslizante ou a caixa de texto, defina um número de dias para o qual os eventos de Log de atividade devem ser mantidos em sua conta de armazenamento. Se você preferir que seus dados persistentes na conta de armazenamento indefinidamente, defina este número como zero.
5. Clique em **Salvar**.

## <a name="archive-the-activity-log-via-powershell"></a>Arquivar o Log de atividade por meio do PowerShell
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus -RetentionInDays 180 -Categories Write,Delete,Action
```

| Propriedade         | Necessário | Descrição                                                                                                                                                                                                                                                                                       |
|------------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| StorageAccountId | Não       | Identificação do recurso da conta de armazenamento para o qual os Logs de atividades deve ser salvo.                                                                                                                                                                                                                        |
| Locais        | Sim      | Lista separada por vírgulas das regiões para o qual você gostaria de coletar eventos de Log de atividade. Você pode exibir uma lista de todas as regiões [visitando esta página](https://azure.microsoft.com/en-us/regions) ou usando [A API de REST de gerenciamento do Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| RetentionInDays  | Sim      | Número de dias para quais eventos devem ser mantidos, entre 1 e 2147483647. Um valor igual a zero armazena os logs indefinidamente (sempre).                                                                                                                                                                                             |
| Categorias       | Sim      | Lista separada por vírgulas de categorias de eventos que devem ser coletados. Valores possíveis são gravação, exclusão e ação.                                                                                                                                                                                 |
## <a name="archive-the-activity-log-via-cli"></a>Arquivar o Log de atividade via CLI
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --locations global,westus,eastus,northeurope --retentionInDays 180 –categories Write,Delete,Action
```

| Propriedade        | Necessário | Descrição                                                                                                                                                                                                                                                                                       |
|-----------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nome            | Sim      | Nome do seu perfil de log.                                                                                                                                                                                                                                                                         |
| storageId       | Não       | Identificação do recurso da conta de armazenamento para o qual os Logs de atividades deve ser salvo.                                                                                                                                                                                                                        |
| locais       | Sim      | Lista separada por vírgulas das regiões para o qual você gostaria de coletar eventos de Log de atividade. Você pode exibir uma lista de todas as regiões [visitando esta página](https://azure.microsoft.com/en-us/regions) ou usando [A API de REST de gerenciamento do Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| retentionInDays | Sim      | Número de dias para quais eventos devem ser mantidos, entre 1 e 2147483647. Um valor zero armazenará os logs indefinidamente (sempre).                                                                                                                                                                                             |
| categorias      | Sim      | Lista separada por vírgulas de categorias de eventos que devem ser coletados. Valores possíveis são gravação, exclusão e ação.                                                                                                                                                                                 |

## <a name="storage-schema-of-the-activity-log"></a>Esquema de armazenamento do Log de atividade
Depois que você configurar arquivamento, um contêiner de armazenamento será criado na conta de armazenamento assim que ocorre um evento de Log de atividade. Os blobs dentro do contêiner siga o mesmo formato em toda a atividade de Log e Logs de diagnóstico. A estrutura destas blobs é:

> ideias-operacionais-logs/name = padrão/resourceId = / assinaturas / {ID da assinatura} / y = {quatro dígitos numérico ano} / m = {dois dígitos numérico mês} / d = {dois dígitos numérico day} / h = {relógio de 24 horas de dois dígitos hour}/m=00/PT1H.json

Por exemplo, pode ser um nome de blob:

> Insights-Operational-logs/Name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.JSON

Cada blob PT1H.json contém um blob JSON de eventos que ocorreram na hora especificada na URL blob (por exemplo, h = 12). Durante a hora presente, eventos são acrescentados ao arquivo PT1H.json conforme eles ocorrem. O valor de minuto (m = 00) é sempre 00, como eventos de Log de atividade são divididos em blobs individuais por hora.

Dentro do arquivo de PT1H.json, cada evento é armazenado na matriz "registros", seguindo este formato:

```
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
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
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| Nome do elemento    | Descrição                                                                                                    |
|-----------------|----------------------------------------------------------------------------------------------------------------|
| tempo            | Carimbo de hora, quando o evento foi gerado pelo serviço do Azure processamento da solicitação correspondente do evento.    |
| identificação de recurso      | Identificação do recurso do recurso afetado.                                                                          |
| operationName   | Nome da operação.                                                                                         |
| categoria        | Categoria da ação, por exemplo. Gravação, ler, ação.                                                               |
| resultType      | O tipo de resultado, por exemplo. Início de sucesso, falha,                                                            |
| resultSignature | Depende do tipo de recurso.                                                                                  |
| durationMs      | Duração da operação em milissegundos                                                                      |
| callerIpAddress | Endereço IP do usuário que executou a operação, a declaração UPN ou a declaração SPN com base na disponibilidade.         |
| correlationId   | Geralmente um GUID no formato de cadeia de caracteres. Eventos que compartilham um correlationId pertencem à mesma ação uber.         |
| identidade        | Blob JSON descrevendo a autorização e declarações.                                                             |
| autorização   | Blob de propriedades RBAC do evento. Geralmente inclui as propriedades de "ação", "função" e "escopo".            |
| nível           | Nível do evento. Um dos seguintes valores: "Crítico", "Erro", "Aviso", "Informativos" e "Detalhado" |
| local        | Região em que ocorreu no local (ou global).                                                             |
| Propriedades      | Conjunto de `<Key, Value>` pares (isto é, dicionário) descrevendo os detalhes do evento.                             |

> [AZURE.NOTE] As propriedades e o uso dessas propriedades podem variar dependendo do recurso.

## <a name="next-steps"></a>Próximas etapas
- [Baixar blobs para análise](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [Fluxo o Log de atividade para Hubs de evento](monitoring-stream-activity-logs-event-hubs.md)
- [Leia mais sobre o Log de atividade](monitoring-overview-activity-logs.md)
