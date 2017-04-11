<properties
    pageTitle="Começar a usar funções, permissões e segurança com o Azure Monitor | Microsoft Azure"
    description="Saiba como usar permissões e funções internas do Monitor do Azure para restringir o acesso aos recursos de monitoramento."
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
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Começar a usar funções, permissões e segurança com o Monitor do Azure

Muitas equipes precisam terminantemente regular o acesso a dados e configurações de monitoramento. Por exemplo, se você tiver os membros da equipe que trabalham exclusivamente em monitoramento (engenheiros de suporte devops engenheiros) ou se você usar um provedor de serviços gerenciados, você talvez queira conceder-lhes acesso somente monitoramento dados enquanto restringe a capacidade de criar, modificar ou excluir recursos. Este artigo mostra como aplicar uma função RBAC monitoramento interna a um usuário no Azure ou criar sua própria função personalizada para um usuário que precisa ter permissões de monitoramento limitadas rapidamente. Em seguida, ele discute considerações de segurança para seus recursos relacionados Monitor do Azure e como você pode limitar o acesso aos dados que eles contêm.

## <a name="built-in-monitoring-roles"></a>Funções de monitoramento internas

Funções internas do Azure Monitor foram projetadas para ajudar a limitar o acesso aos recursos em uma assinatura, permitindo que os responsáveis por monitoramento infraestrutura para obter e configurar os dados necessários. Monitor Azure fornece duas funções de-prontos: um leitor de monitoramento e colaborador monitoramento.

### <a name="monitoring-reader"></a>Leitor de monitoramento

Pessoas atribuídas a função de monitoramento leitor podem exibir todos os dados de monitoramento em uma assinatura, mas não pode modificar qualquer recurso ou edite quaisquer configurações relacionadas ao monitoramento de recursos. Esta função é apropriada para usuários em uma organização, como engenheiros de suporte ou operações, que precisam ser capazes de:

- Exibir o monitoramento painéis no portal e criar seus próprios painéis de monitoramento particulares.
- Consulta de métricas usando a [API do Azure Monitor REST](https://msdn.microsoft.com/library/azure/dn931930.aspx), [cmdlets do PowerShell](insights-powershell-samples.md)ou [CLI entre plataformas](insights-cli-samples.md).
- Consulte o Log de atividade usando o portal, API do Azure Monitor REST, cmdlets do PowerShell ou CLI entre plataformas.
- Exiba as [configurações de diagnóstico](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) para um recurso.
- Exiba o [perfil de log](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles) para uma assinatura.
- Exibir configurações de escala automática.
- Atividade de alerta do modo de exibição e configurações.
- Acessar dados de obtenção de informações do aplicativo e exibir dados de análise de AI.
- Pesquisar dados de espaço de trabalho de análise de Log (OMS), incluindo dados de uso do espaço de trabalho.
- Exibir grupos de gerenciamento de análise de Log (OMS).
- Recupere o esquema de pesquisa de análise de Log (OMS).
- Listar os pacotes de inteligência de análise de Log (OMS).
- Recuperar e executar a análise de Log (OMS) pesquisas salvas.
- Recupere a configuração de armazenamento de análise de Log (OMS).

> [AZURE.NOTE] Esta função não dá acesso de leitura aos dados de log que foi transmitidos a um hub de evento ou armazenados em uma conta de armazenamento. [Veja abaixo](#security-considerations-for-monitoring-data) para obter informações sobre como configurar o acesso a esses recursos.

### <a name="monitoring-contributor"></a>Monitoramento Colaborador

Pessoas atribuídas a função Colaborador monitoramento podem exibir todos os dados de monitoramento de uma assinatura e criar ou modificar as configurações de monitoramento, mas não poderá modificar quaisquer outros recursos. Esta função é um subconjunto da função Leitor de monitoramento e é adequada para os membros da equipe monitoramento ou provedores de serviços gerenciados que, além das permissões acima, também precisam ser capaz de uma organização:

- Publica painéis monitoramento como um painel compartilhado.
- Definir [configurações de diagnóstico](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) para um resource.*
- Definir o [perfil de log](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles) para um subscription.*
- Definir configurações e atividade de alerta.
- Crie testes de web de obtenção de informações do aplicativo e componentes.
- Lista teclas de espaço de trabalho compartilhado de análise de Log (OMS).
- Habilitar ou desabilitar pacotes de inteligência de análise de Log (OMS).
- Criar, excluir e executar a análise de Log (OMS) pesquisas salvas.
- Criar e excluir a configuração de armazenamento de análise de Log (OMS).

* usuário também separadamente deve receber permissão de ListKeys o recurso de destino (namespace de armazenamento conta ou evento hub) para definir um perfil de log ou a configuração de diagnóstico.

> [AZURE.NOTE] Esta função não dá acesso de leitura aos dados de log que foi transmitidos a um hub de evento ou armazenados em uma conta de armazenamento. [Veja abaixo](#security-considerations-for-monitoring-data) para obter informações sobre como configurar o acesso a esses recursos.

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Permissões de monitoramento e funções RBAC personalizadas
Se as funções internas acima não atender às necessidades exatas de sua equipe, você pode [criar uma função RBAC personalizada](../active-directory/role-based-access-control-custom-roles.md) com permissões mais granulares. Abaixo estão as operações de Azure Monitor RBAC comuns e suas descrições.

| Operação                                                   | Descrição                                                                                                                                               |
|-------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Microsoft.Insights/AlertRules/[Read, gravar, excluir]         | Regras de alerta de leitura/gravação/excluir.                                                                                                                            |
| Microsoft.Insights/AlertRules/Incidents/Read                | Lista de ocorrências (histórico da regra de alerta sendo acionado) para regras de alerta. Aplica-se somente ao portal.                                              |
| Microsoft.Insights/AutoscaleSettings/[Read, gravar, excluir]  | Configurações de escala automática de leitura/gravação/excluir.                                                                                                                     |
| Microsoft.Insights/DiagnosticSettings/[Read, gravar, excluir] | Configurações de diagnóstico de leitura/gravação/excluir.                                                                                                                    |
| Microsoft.Insights/eventtypes/digestevents/Read             | Essa permissão é necessária para os usuários que precisam de acesso aos Logs de atividade através do portal.                                                                   |
| Microsoft.Insights/eventtypes/values/Read                   | Lista eventos de Log de atividade (eventos de gerenciamento) em uma assinatura. Essa permissão é aplicável a programação e o portal acessem o registro de atividades. |
| Microsoft.Insights/LogDefinitions/Read                      | Essa permissão é necessária para os usuários que precisam de acesso aos Logs de atividade através do portal.                                                                   |
| Microsoft.Insights/MetricDefinitions/Read                   | Definições de métrica leitura (lista de tipos de métrica disponíveis para um recurso).                                                                                  |
| Microsoft.Insights/Metrics/Read                             | Leia métricas de um recurso.                                                                                                                              |

> [AZURE.NOTE] Acessar alertas, configurações de diagnóstico e métricas para um recurso exige que o usuário tenha acesso de leitura para o tipo de recurso e o escopo do recurso. Um perfil de configuração ou log de diagnóstico que arquivos para uma conta de armazenamento ou fluxos de hubs de evento criando ("gravação") requer que o usuário também tenha permissão de ListKeys do recurso de destino.

Por exemplo, usando a tabela anterior, você pode criar uma função RBAC personalizada para um "leitor de Log de atividade" assim:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Considerações de segurança para monitorar os dados
Dados de monitoramento — especialmente arquivos de log — podem conter informações confidenciais, como nomes de usuário ou endereços de IP. Monitorar os dados do Azure vem em três formas básicas:
1. Log de atividades, que descreve todas as ações de plano de controle na sua assinatura do Azure.
2. Logs de diagnóstico, que são logs emitidos por um recurso.
3. Métricas, que são emitidas pelos recursos.

Todos os três desses tipos de dados podem ser armazenados em uma conta de armazenamento ou transmitidos a um Hub de evento, que são recursos Azure gerais. Porque esses recursos gerais, criando, excluindo e acessá-los é uma operação privilegiada normalmente reservada para um administrador. Sugerimos que você use as seguintes práticas para recursos relacionados ao monitoramento para evitar o uso incorreto:

- Use uma conta de armazenamento dedicado única para monitoramento de dados. Se for necessário separar monitoramento de dados em várias contas de armazenamento, nunca compartilhe o uso de uma conta de armazenamento entre monitoramento e dados não monitoramento, pois isso inadvertidamente podem dar a quem precisa apenas de acesso para monitoramento de dados (ex. SIEM um terceiro) acessem não monitoramento dados.
- Use um namespace de barramento de serviço ou Hub de evento único, dedicado entre todas as configurações de diagnósticos para o mesmo motivo acima.
- Limitar o acesso a contas de armazenamento relacionado ao monitoramento ou hubs de evento, mantendo-os em um grupo de recursos separado e [usar o escopo](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure) em suas funções monitoramento para limitar o acesso a apenas desse grupo de recursos.
- Nunca conceda a permissão de ListKeys para contas de armazenamento ou hubs de evento no escopo de assinatura quando um usuário precisa apenas acessar dados de monitoramento. Em vez disso, conceder essas permissões ao usuário a um recurso ou grupo de recursos (se você tiver um grupo de recursos de monitoramento dedicado) escopo.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Limitar o acesso a contas de armazenamento relacionado ao monitoramento
Quando um usuário ou aplicativo precisa acessar dados de uma conta de armazenamento de monitoramento, você deve [gerar um SAS de conta](https://msdn.microsoft.com/library/azure/mt584140.aspx) da conta de armazenamento que contém os dados de monitoramento com acesso de somente leitura de nível de serviço para o armazenamento de blob. No PowerShell, isso pode parecer com:

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Em seguida, você pode dar o token à entidade que precisam ler a partir desse armazenamento conta e ele pode listar e ler de todos os blobs nessa conta de armazenamento.

Como alternativa, se você precisar controlar essa permissão com RBAC, você pode conceder entidade a permissão de Microsoft.Storage/storageAccounts/listkeys/action dessa conta de armazenamento específica. Isso é necessário para os usuários que precisam ser capaz de definir uma configuração de diagnóstico ou efetuar perfil para arquivar em uma conta de armazenamento. Por exemplo, você poderia criar a seguinte função RBAC personalizada para um usuário ou o aplicativo que precisa apenas a leitura de uma conta de armazenamento:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [AZURE.WARNING] A permissão de ListKeys permite ao usuário listar as teclas de conta de armazenamento principal e secundário. Estas teclas conceder ao usuário todos os assinados permissões (ler, gravar, criar blobs, excluir blobs, etc.) em todas as assinado services (blob, fila, tabela, arquivo) nessa conta de armazenamento. É recomendável usar um SAS conta descrito acima, quando possível.

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Limitar o acesso a hubs de evento relacionado ao monitoramento
Um padrão semelhante pode ser seguido com hubs de evento, mas primeiro você precisa criar uma regra de autorização de ouvir dedicada. Se você deseja conceder acesso a um aplicativo que precisa apenas de ouvir hubs de evento relacionado ao monitoramento, faça o seguinte:

1. Crie uma política de acesso compartilhado no hub(s) evento que foram criados para o fluxo de dados de monitoramento com apenas declarações de ouvir. Isso pode ser feito no portal. Por exemplo, você pode chamá-lo "monitoringReadOnly". Se possível, você desejará dar chave diretamente para o consumidor e ignorar a próxima etapa.
2. Se o consumidor precisa ser capaz de obter a chave ad-hoc, conceda ao usuário a ação de ListKeys do hub evento. Isso também é necessário para os usuários que precisam ser capaz de definir uma configuração de diagnóstico ou faça logon perfil fluxo para hubs de evento. Por exemplo, você pode criar uma regra RBAC:

   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```


## <a name="next-steps"></a>Próximas etapas
- [Leia sobre RBAC e permissões no Gerenciador de recursos](../active-directory/role-based-access-control-what-is.md)
- [Leia a visão geral de monitoramento no Azure](monitoring-overview.md)
