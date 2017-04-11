<properties
    pageTitle="Azure monitoramento passo a passo de API REST | Microsoft Azure"
    description="Como autenticar solicitações e usar a API REST de monitoramento do Azure."
    authors="mcollier, rboucher"
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
    ms.date="09/27/2016"
    ms.author="mcollier"/>

# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure monitoramento passo a passo de API REST
Este artigo mostra como executar autenticação para que seu código pode usar a [Referência de API do restante do Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

A API do Azure Monitor possibilita recuperar de programação as definições de métrica padrão disponíveis (o tipo de métrica como o tempo de CPU, solicitações, etc.), detalhamento e valores métricas. Depois que recuperados, os dados podem ser salvos em um repositório de dados separado como banco de dados do SQL Azure, DocumentDB ou Lucerne de dados do Azure. A partir daí análises adicionais podem ser executadas conforme necessário.

Além de trabalhar com vários pontos de dados métrica, como mostra este artigo, a API Monitor possibilita a lista regras de alerta, exibir logs de atividade e muito mais. Para obter uma lista completa de operações disponíveis, consulte a [Referência de API do restante do Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Autenticação de solicitações de Monitor Azure

A primeira etapa é autenticar a solicitação.

Todas as tarefas executadas a API do Azure Monitor usam o modelo de autenticação do Gerenciador de recursos do Azure. Portanto, todas as solicitações devem ser autenticadas com o Azure Active Directory (AD Azure). Uma abordagem para autenticar o aplicativo cliente é para criar um anúncio Azure principal do serviço e recuperar o token de autenticação (JWT). O script de exemplo a seguir demonstra a criação de um serviço de Azure AD principal por meio do PowerShell. Para uma orientação sobre mais detalhada, consulte a documentação sobre como [usar o PowerShell do Azure para criar um serviço principal para acessar recursos](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password—powershell). Também é possível [criar um princípio de serviço por meio do portal do Azure](../resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"
$location = "centralus"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Para consultar a API do Monitor do Azure, o aplicativo cliente deve usar o capital de serviço criado anteriormente para autenticar. Script do PowerShell do exemplo a seguir mostra uma abordagem, usando a [Biblioteca de autenticação do Active Directory](../active-directory/active-directory-authentication-libraries.md) (ADAL) para ajudar a autenticação JWT token. O token JWT é passado como parte de um parâmetro de autorização de HTTP em solicitações à API REST Azure Monitor.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.windows.net/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)
 
$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values 
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Uma vez concluída a etapa de configuração de autenticação, consultas, em seguida, podem ser executadas na API do Azure Monitor restante. Há duas consultas úteis:

1. Lista as definições de métrica para um recurso

2. Recuperar os valores de métrica


## <a name="retrieve-metric-definitions"></a>Recuperar definições de métricas
>[AZURE.NOTE] Para recuperar definições de métrica usando a API do Azure Monitor REST, use "2016-03-01" como a versão da API.

```PowerShell
$apiVersion = "2016-03-01"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metricDefinitions?api-version=${apiVersion}"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -Verbose
```
Para um aplicativo de lógica do Azure, as definições de métrica seria semelhantes à seguinte captura de tela:

![ALT "Visualização JSON de resposta de definição de métrica".](./media/monitoring-rest-api-walkthrough/available_metric_definitions_logic_app_json_response_clean.png)

Para obter mais informações, consulte a documentação de [lista as definições de métrica para um recurso em API do Azure Monitor REST](https://msdn.microsoft.com/library/azure/mt743621.aspx) .

## <a name="retrieve-metric-values"></a>Recuperar valores métricas
Depois que as definições de métrica disponíveis são conhecidas, em seguida, é possível recuperar os valores de métrica relacionados. Usar nome 'valor' a métrica (não a ' localizedValue') para qualquer solicitações de filtragem (por exemplo, recuperar os pontos de dados métrica 'CpuTime' e 'Solicitar'). Se sem filtros forem especificados, a métrica padrão será retornada.

>[AZURE.NOTE] Para recuperar valores métricas usando a API do Azure Monitor REST, use "2016-06-01" como a versão da API.

**Método**: GET

**Solicitar URI**: /providers/ https://management.azure.com/subscriptions/_{id da assinatura}_/resourceGroups/_{nome do grupo recurso}_de_{recurso-provedor-namespace}_/_{tipo de recurso}_/de /providers/microsoft.insights/metrics?$filter=_{nome do recurso}__{filtro}_& versão da api =_{apiVersion}_

Por exemplo, para recuperar os pontos de dados métricas de RunsSucceeded para o intervalo de tempo determinado e um detalhamento de tempo de 1 hora, a solicitação seria assim:

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2016-09-23 and endTime eq 2016-09-24 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

O resultado seria exibida semelhante ao exemplo seguinte captura de tela:

![ALT "Resposta JSON mostrando o valor de tempo de resposta médio métrica"](./media/monitoring-rest-api-walkthrough/available_metrics_logic_app_json_response.png)

Para recuperar vários pontos de dados ou agregação, adicione os nomes de definição de métrica e tipos de agregação para o filtro, conforme visto no exemplo a seguir:

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2016-09-23T13:30:00Z and endTime eq 2016-09-23T14:30:00Z and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

### <a name="use-armclient"></a>Use ARMClient
Uma alternativa para usar o PowerShell (como mostrado acima), é usar [ARMClient](https://github.com/projectkudu/ARMClient) no seu computador Windows. ARMClient controla a autenticação do Azure AD (e token JWT resultante) automaticamente. As etapas a seguir descrevem uso de ARMClient para recuperar dados de métrica:

1. Instale o [Chocolatey](https://chocolatey.org/) e [ARMClient](https://github.com/projectkudu/ARMClient).

2. Em uma janela terminal, digite _armclient.exe login_. Isso solicitará que você faça logon no Azure.

3. Tipo de _armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01_

4. Tipo de _armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-06-01_


![ALT "Usando ARMClient para trabalhar com o Azure Monitoring API REST"](./media/monitoring-rest-api-walkthrough/armclient_metricdefinitions.png)


## <a name="retrieve-the-resource-id"></a>Recuperar a identificação do recurso
Usando a API REST realmente pode ajudar a entender as definições de métrica disponíveis, detalhamento e valores relacionados. Essas informações são útil quando usando a [Biblioteca de gerenciamento do Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Para o código anterior, a identificação do recurso usar é o caminho completo para o recurso Azure desejado. Por exemplo, para consultar em relação a um aplicativo Web do Azure, a identificação do recurso seria:

*/Subscriptions/{Subscription-ID}/resourceGroups/{Resource-Group-name}/providers/Microsoft.Web/sites/{Site-Name}/*

A lista a seguir contém alguns exemplos de formatos de ID do recurso para vários recursos Azure:

* **Hub de IoT** - /subscriptions/_{id da assinatura}_/resourceGroups/_{nome do grupo recurso}_/providers/Microsoft.Devices/IotHubs/_{iot-hub-name}_

* **Pool de SQL elástica** - /subscriptions/_{id da assinatura}_/resourceGroups/_{nome do grupo recurso}_/providers/Microsoft.Sql/servers/_{pool-db}_/elasticpools/_{sql-pool-name}_

* **Banco de dados do SQL (v12)** - /subscriptions/_{id da assinatura}_/resourceGroups/_{nome do grupo recurso}_/providers/Microsoft.Sql/servers/_{nome do servidor}_/databases/_{nome do banco de dados}_

* **Barramento de serviço** - /subscriptions/_{id da assinatura}_/resourceGroups/_{nome do grupo recurso}_/providers/Microsoft.ServiceBus/_{namespace}_/_{nome do servicebus}_

* **Conjuntos de escala de máquina virtual** - /subscriptions/_{id da assinatura}_/resourceGroups/_{nome do grupo recurso}_/providers/Microsoft.Compute/virtualMachineScaleSets/_{nome da máquina virtual}_

* **VMs** - /subscriptions/_{id da assinatura}_/resourceGroups/_{nome do grupo recurso}_/providers/Microsoft.Compute/virtualMachines/_{nome da máquina virtual}_

* **Hubs de evento** - /subscriptions/_{id da assinatura}_/resourceGroups/_{nome do grupo recurso}_/providers/Microsoft.EventHub/namespaces/_{eventhub-namespace}_


Há abordagens alternativas para recuperar a identificação do recurso, incluindo o uso do Azure Resource Explorer, exibindo o recurso desejado no portal do Azure e por meio do PowerShell ou CLI Azure.

### <a name="azure-resource-explorer"></a>Gerenciador de recursos do Azure
Para localizar a identificação do recurso para um recurso desejado, uma abordagem útil é usar a ferramenta de [Azure Resource Explorer](https://resources.azure.com) . Navegue até o recurso desejado e, em seguida, examine a identificação mostrada, como a captura de tela a seguir:

![ALT "Azure Resource Explorer"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Portal do Azure
A identificação do recurso também pode ser obtida no portal do Azure. Para fazer isso, navegue até o recurso desejado e selecione Propriedades. A identificação do recurso é exibida na lâmina propriedades, conforme visto no seguinte captura de tela:

![ALT "identificação do recurso exibida na lâmina propriedades no portal do Azure"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>PowerShell Azure
A identificação do recurso pode ser recuperada usando cmdlets do PowerShell do Azure também. Por exemplo, para obter a identificação do recurso para o aplicativo Web do Azure, execute o cmdlet Get-AzureRmWebApp, como a captura de tela a seguir:

![ALT "identificação do recurso obtida por meio do PowerShell"](./media\monitoring-rest-api-walkthrough\resourceid_powershell.png)

### <a name="azure-cli"></a>CLI Azure
Para recuperar a identificação do recurso utilizando a CLI do Azure, execute o comando 'Mostrar Web App azure', especificando a ' – json' opção, conforme mostrado na seguinte captura de tela:

![ALT "identificação do recurso obtida por meio do PowerShell"](./media\monitoring-rest-api-walkthrough\resourceid_azurecli.png)

## <a name="retrieve-activity-log-data"></a>Recuperar dados de Log de atividade
Além de trabalhar com métricas definições e valores relacionados, também é possível recuperar adicionais ideias interessantes relacionadas aos recursos Azure. Como exemplo, é possível aos dados do [registro de atividade](https://msdn.microsoft.com/library/azure/dn931934.aspx) de consulta. O exemplo a seguir demonstra usando a API do Azure Monitor REST aos dados do registro de atividade de consulta dentro de um intervalo de data específico para uma assinatura do Azure:

```PowerShell
$apiVersion = "2014-04-01"
$filter = "eventTimestamp ge '2016-09-23' and eventTimestamp le '2016-09-24'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

## <a name="next-steps"></a>Próximas etapas
* Examine a [Visão geral de monitoramento](monitoring-overview.md).
* Exiba as [métricas de compatíveis com o Monitor do Azure](monitoring-supported-metrics.md).
* Examine a [Referência de API do Microsoft Azure Monitor restante](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Examine a [biblioteca de gerenciamento do Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).
