<properties
    pageTitle="RBAC: Funções internas | Microsoft Azure"
    description="Este tópico descreve os criados em funções para controle de acesso baseado em função (RBAC)."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/25/2016"
    ms.author="kgremban"/>

#<a name="rbac-built-in-roles"></a>RBAC: Funções internas

Azure controle de acesso baseado em função (RBAC) vem com as seguintes funções internas que podem ser atribuídas a usuários, grupos e serviços. Você não pode modificar as definições de funções internas. No entanto, você pode criar [funções personalizadas no Azure RBAC](role-based-access-control-custom-roles.md) para atender às necessidades específicas de sua organização.

## <a name="roles-in-azure"></a>Funções no Azure

A tabela a seguir fornece breves descrições das funções internas. Clique no nome de função para ver a lista detalhada de **ações** e **notactions** para a função. A propriedade de **ações** Especifica as ações permitidas em recursos Azure. Cadeias de caracteres de ação podem usar caracteres curinga. A propriedade **notactions** Especifica as ações que são excluídas as ações permitidas.

>[AZURE.NOTE] As definições de função Azure estão em constante evolução. Este artigo é mantido como atualizado possível, mas você sempre pode encontrar as definições de funções mais recentes do PowerShell do Azure. Usar os cmdlets `(get-azurermroledefinition "<role name>").actions` ou `(get-azurermroledefinition "<role name>").notactions` conforme aplicável.

| Nome de função | Descrição |
| --------- | ----------- |
| [Colaborador de serviço de gerenciamento de API](#api-management-service-contributor) | Pode gerenciar serviços de gerenciamento de API |
| [Aplicativo ideias componente Colaborador](#application-insights-component-contributor) | Pode gerenciar os componentes de obtenção de informações de aplicativo |
| [Operador de automação](#automation-operator) | Capaz de iniciar, parar, suspender e continuar trabalhos |
| [BizTalk Colaborador](#biztalk-contributor) | Pode gerenciar serviços BizTalk |
| [ClearDB MySQL DB Colaborador](#cleardb-mysql-db-contributor) | Pode gerenciar bancos de dados ClearDB MySQL |
| [Colaborador](#contributor) | Pode gerenciar tudo, exceto acesso. |
| [Colaborador de fábrica de dados](#data-factory-contributor) | Pode criar e gerenciar fábricas de dados e os recursos de filho neles. |
| [DevTest Labs usuário](#devtest-labs-user) | Pode exibir tudo e conectar, iniciar, reinicialização e desligamento máquinas virtuais |
| [Colaborador de zona DNS](#dns-zone-contributor) | Pode gerenciar registros e zonas DNS |
| [Conta de DocumentDB Colaborador](#documentdb-account-contributor) | Pode gerenciar contas de DocumentDB |
| [Sistemas inteligente conta Colaborador](#intelligent-systems-account-contributor) | Pode gerenciar contas de sistemas inteligente |
| [Colaborador de rede](#network-contributor) | Pode gerenciar todos os recursos de rede |
| [Novo Relíquia APM conta Colaborador](#new-relic-apm-account-contributor) | Pode gerenciar contas de gerenciamento de desempenho de aplicativo Relíquia novo e aplicativos |
| [Proprietário](#owner) | Pode gerenciar tudo, inclusive acesso |
| [Leitor](#reader) | Pode exibir tudo, mas não pode fazer alterações |
| [Relacionada Colaborador de Cache](#redis-cache-contributor) | Pode gerenciar caches relacionada |
| [Agendador Colaborador de conjuntos de trabalho](#scheduler-job-collections-contributor) | Pode gerenciar conjuntos de trabalho do Agendador |
| [Colaborador de serviço de pesquisa](#search-service-contributor) | Pode gerenciar serviços de pesquisa |
| [Gerenciador de segurança](#security-manager) | Pode gerenciar os componentes de segurança, políticas de segurança e máquinas virtuais |
| [SQL DB Colaborador](#sql-db-contributor) | Pode gerenciar bancos de dados SQL, mas não suas políticas de segurança |
| [Gerenciador de segurança do SQL](#sql-security-manager) | Pode gerenciar as políticas de segurança de servidores SQL e bancos de dados |
| [SQL Server Colaborador](#sql-server-contributor) | Pode gerenciar servidores SQL e bancos de dados, mas não suas políticas de segurança |
| [Colaborador de conta de armazenamento clássico](#classic-storage-account-contributor) | Pode gerenciar contas de armazenamento clássico |
| [Colaborador de conta de armazenamento](#storage-account-contributor) | Pode gerenciar contas de armazenamento |
| [Administrador de acesso do usuário](#user-access-administrator) | Pode gerenciar o acesso do usuário aos recursos do Azure |
| [Colaborador clássico Máquina Virtual](#classic-virtual-machine-contributor) | Gerenciam clássicas máquinas virtuais, mas não a rede ou armazenamento conta virtual à qual eles estão conectados |
| [Colaborador de máquina virtual](#virtual-machine-contributor) | Pode gerenciar máquinas virtuais, mas não a rede ou armazenamento conta virtual à qual eles estão conectados |
| [Rede clássico Colaborador](#classic-network-contributor) | Pode gerenciar IPs reservadas e redes virtuais clássicas |
| [Colaborador do plano de Web](#web-plan-contributor) | Pode gerenciar os planos de web |
| [Colaborador de site](#website-contributor) | Pode gerenciar sites, mas não os planos da web à qual eles estão conectados |

## <a name="role-permissions"></a>Permissões de função
As tabelas a seguir descrevem as permissões específicas fornecidas para cada função. Isso pode incluir **ações**, que conceder permissões e **NotActions**, que restringi-los.

### <a name="api-management-service-contributor"></a>Colaborador de serviço de gerenciamento de API
Pode gerenciar serviços de gerenciamento de API

| **Ações** | |
| ------- | ------ |
| Microsoft.ApiManagement/Service/* | Criar e gerenciar serviços de gerenciamento de API |
| Microsoft.Authorization/*/read | Autorização de leitura |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Funções de leitura e atribuições de função |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### <a name="application-insights-component-contributor"></a>Aplicativo ideias componente Colaborador
Pode gerenciar os componentes de obtenção de informações de aplicativo

| **Ações** | |
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.Insights/components/* | Criar e gerenciar componentes de ideias |
| Microsoft.Insights/webtests/* | Criar e gerenciar testes da web |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### <a name="automation-operator"></a>Operador de automação
Capaz de iniciar, parar, suspender e continuar trabalhos

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e atribuições de função |
| Microsoft.Automation/automationAccounts/jobs/read | Leia automação trabalhos de conta |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Retomar um trabalho de conta de automação |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Interromper um trabalho de conta de automação |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Leia automação fluxos de trabalho de conta |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspender um trabalho de conta de automação |
| Microsoft.Automation/automationAccounts/jobs/write | Escrever automação trabalhos de conta |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Ler uma cronograma de trabalho de conta de automação |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Ler uma cronograma de trabalho de conta de automação |
| Microsoft.Automation/automationAccounts/read | Ler contas de automação |
| Microsoft.Automation/automationAccounts/runbooks/read | Leia runbooks de automação |
| Microsoft.Automation/automationAccounts/schedules/read | Leia automação cronogramas de conta |
| Microsoft.Automation/automationAccounts/schedules/write | Escrever automação cronogramas de conta |
| Microsoft.Insights/components/* | Criar e gerenciar componentes de ideias |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### <a name="biztalk-contributor"></a>BizTalk Colaborador
Pode gerenciar serviços BizTalk

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e atribuições de função |
| Microsoft.BizTalkServices/BizTalk/* | Criar e gerenciar serviços BizTalk |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL DB Colaborador
Pode gerenciar bancos de dados ClearDB MySQL

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
| successbricks.cleardb/Databases/* | Criar e gerenciar bancos de dados ClearDB MySQL |

### <a name="contributor"></a>Colaborador
Pode gerenciar tudo, exceto acesso

| **Ações** ||
| ------- | ------ |
| * | Criar e gerenciar recursos de todos os tipos |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Authorization/*/Delete | Não é possível excluir funções e atribuições de função |  
| Microsoft.Authorization/*/Write | Não é possível criar funções e atribuições de função |

### <a name="data-factory-contributor"></a>Colaborador de fábrica de dados
Criar e gerenciar fábricas de dados e os recursos de filho neles.

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
| Microsoft.DataFactory/dataFactories/* | Criar e gerenciar fábricas de dados e os recursos de filho neles. |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### <a name="devtest-labs-user"></a>DevTest Labs usuário
Pode exibir tudo e conectar, iniciar, reinicialização e desligamento máquinas virtuais

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
| Microsoft.Compute/availabilitySets/read | Ler as propriedades de conjuntos de disponibilidade |
| Microsoft.Compute/virtualMachines/*/read | Leia as propriedades de uma máquina virtual (tamanhos de máquina virtual runtime status, extensões de máquina virtual, etc.) |
| Microsoft.Compute/virtualMachines/deallocate/action | Desalocar máquinas virtuais |
| Microsoft.Compute/virtualMachines/read | Leia as propriedades de uma máquina virtual |
| Microsoft.Compute/virtualMachines/restart/action | Reinicie máquinas virtuais |
| Microsoft.Compute/virtualMachines/start/action | Iniciar máquinas virtuais |
| Microsoft.DevTestLab/*/read | Ler as propriedades de um laboratório |
| Microsoft.DevTestLab/labs/createEnvironment/action | Criar um ambiente de laboratório |
| Microsoft.DevTestLab/labs/formulas/delete | Excluir fórmulas |
| Microsoft.DevTestLab/labs/formulas/read | Fórmulas de leitura |
| Microsoft.DevTestLab/labs/formulas/write | Adicionar ou modificar fórmulas |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Avaliar as políticas de laboratório |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ingressar em um pool de endereços de back-end de Balanceador de carga |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Ingressar em um balanceador de carga regra de NAT de entrada |
| Microsoft.Network/networkInterfaces/*/read | Ler as propriedades de uma interface de rede (por exemplo, todos os balanceadores de carga que a interface de rede é uma parte da) |
| Microsoft.Network/networkInterfaces/join/action | Ingressar em uma máquina Virtual para uma interface de rede |
| Microsoft.Network/networkInterfaces/read | Interfaces de rede de leitura |
| Microsoft.Network/networkInterfaces/write | Escrever interfaces de rede |
| Microsoft.Network/publicIPAddresses/*/read | Ler as propriedades de um endereço IP público |
| Microsoft.Network/publicIPAddresses/join/action | Ingressar em um endereço IP público |
| Microsoft.Network/publicIPAddresses/read | Leia endereços IP públicos de rede |
| Microsoft.Network/virtualNetworks/subnets/join/action | Ingressar em uma rede virtual |
| Microsoft.Resources/deployments/operations/read | Operações de implantação de leitura |
| Microsoft.Resources/deployments/read | Implantações de leitura |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Storage/storageAccounts/listKeys/action | Lista de teclas de conta de armazenamento |

### <a name="dns-zone-contributor"></a>Colaborador de zona DNS
Pode gerenciar registros e zonas DNS.

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/\*/ler | Funções de leitura e atribuições de função |
| Microsoft.Insights/alertRules/\* | Criar e gerenciar regras de alerta |
| Microsoft.Network/dnsZones/\* | Criar e gerenciar registros e zonas DNS |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/\* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/\* | Criar e gerenciar tíquetes de suporte |


### <a name="documentdb-account-contributor"></a>Conta de DocumentDB Colaborador
Pode gerenciar contas de DocumentDB

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
| Microsoft.DocumentDb/databaseAccounts/* | Criar e gerenciar contas de DocumentDB |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### <a name="intelligent-systems-account-contributor"></a>Sistemas inteligente conta Colaborador
Pode gerenciar contas de sistemas inteligente

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.IntelligentSystems/accounts/* | Criar e gerenciar contas de sistemas inteligente |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### <a name="network-contributor"></a>Colaborador de rede
Pode gerenciar todos os recursos de rede

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.Network/* | Criar e gerenciar redes |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### <a name="new-relic-apm-account-contributor"></a>Novo Relíquia APM conta Colaborador
Pode gerenciar contas de gerenciamento de desempenho de aplicativo Relíquia novo e aplicativos

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
| NewRelic.APM/accounts/* | Criar e gerenciar contas de gerenciamento de desempenho do Relíquia novo aplicativo |

### <a name="owner"></a>Proprietário
Pode gerenciar tudo, inclusive acesso

| **Ações** ||
| ------- | ------ |
| * | Criar e gerenciar recursos de todos os tipos |

### <a name="reader"></a>Leitor
Pode exibir tudo, mas não pode fazer alterações

| **Ações** ||
| ------- | ------ |
| * / leitura | Leia os recursos de todos os tipos, exceto segredos. |

### <a name="redis-cache-contributor"></a>Relacionada Colaborador de Cache
Pode gerenciar caches relacionada

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
| Microsoft.Cache/redis/* | Criar e gerenciar caches relacionada |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### <a name="scheduler-job-collections-contributor"></a>Agendador Colaborador de conjuntos de trabalho
Pode gerenciar conjuntos de trabalho do Agendador

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Scheduler/jobcollections/* | Criar e gerenciar conjuntos de trabalho |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte  |

### <a name="search-service-contributor"></a>Colaborador de serviço de pesquisa
Pode gerenciar serviços de pesquisa

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Search/searchServices/* | Criar e gerenciar os serviços de pesquisa |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte  |

### <a name="security-manager"></a>Gerenciador de segurança
Pode gerenciar os componentes de segurança, políticas de segurança e máquinas virtuais

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
| Microsoft.ClassicCompute/*/read | Ler informações de configuração clássico calcular máquinas virtuais |
| Microsoft.ClassicCompute/virtualMachines/*/write | Gravar a configuração para máquinas virtuais |
| Microsoft.ClassicNetwork/*/read | Ler informações de configuração sobre rede clássico  |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Security/* | Criar e gerenciar políticas e componentes de segurança |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte  |

### <a name="sql-db-contributor"></a>SQL DB Colaborador
Pode gerenciar bancos de dados SQL, mas não suas políticas de segurança

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de função |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Sql/servers/databases/* | Criar e gerenciar bancos de dados SQL |
| Microsoft.Sql/servers/read | Leia os servidores SQL |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Não é possível editar diretivas de auditoria |
| Microsoft.Sql/servers/databases/auditingSettings/* | Não consigo editar as configurações de auditoria |
| Microsoft.Sql/servers/databases/auditRecords/read  | Não pode ler os registros de auditoria |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Não é possível editar diretivas de conexão |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Não é possível editar dados masking políticas |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Não é possível editar políticas de alerta de segurança |
| Microsoft.Sql/servers/databases/securityMetrics/* | Não é possível editar métricas de segurança |

### <a name="sql-security-manager"></a>Gerenciador de segurança do SQL
Pode gerenciar as políticas de segurança de servidores SQL e bancos de dados

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorização da Microsoft de leitura |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta de ideias |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Sql/servers/auditingPolicies/* | Criar e gerenciar diretivas de auditoria do SQL server |
| Microsoft.Sql/servers/auditingSettings/* | Criar e gerenciar as configurações de auditoria do SQL server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Criar e gerenciar diretivas de auditoria de banco de dados do SQL server |
| Microsoft.Sql/servers/databases/auditingSettings/* | Criar e gerenciar configurações de auditoria de banco de dados do SQL server |
| Microsoft.Sql/servers/databases/auditRecords/read | Registros de auditoria de leitura |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Criar e gerenciar políticas de conexão de banco de dados do SQL server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Criar e gerenciar dados de banco de dados do SQL server masking políticas |
| Microsoft.Sql/servers/databases/read | Bancos de dados SQL de leitura |
| Microsoft.Sql/servers/databases/schemas/read | Esquemas de banco de dados do leitura SQL server |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Colunas de tabela de banco de dados de servidor SQL de leitura |
| Microsoft.Sql/servers/databases/schemas/tables/read | Tabelas de banco de dados do servidor SQL de leitura |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Criar e gerenciar políticas alertas de segurança do SQL server banco de dados |
| Microsoft.Sql/servers/databases/securityMetrics/* | Criar e gerenciar métricas de segurança de banco de dados do SQL server |
| Microsoft.Sql/servers/read | Leia os servidores SQL |
| Microsoft.Sql/servers/securityAlertPolicies/* | Criar e gerenciar políticas de alerta de segurança do SQL server |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### <a name="sql-server-contributor"></a>SQL Server Colaborador
Pode gerenciar servidores SQL e bancos de dados, mas não suas políticas de segurança

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorização de leitura|
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta de ideias |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Sql/servers/* | Criar e gerenciar servidores SQL |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | Não é possível editar diretivas de auditoria do SQL server |
| Microsoft.Sql/servers/auditingSettings/* | Não é possível editar configurações de auditoria do SQL server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Não é possível editar diretivas de auditoria de banco de dados do SQL server |
| Microsoft.Sql/servers/databases/auditingSettings/* | Não é possível editar configurações de auditoria de banco de dados do SQL server |
| Microsoft.Sql/servers/databases/auditRecords/read  | Não pode ler os registros de auditoria |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Não é possível editar diretivas de conexão de banco de dados do SQL server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Não é possível editar dados de banco de dados do SQL server masking políticas |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Não é possível editar políticas alertas de segurança do SQL server banco de dados |
| Microsoft.Sql/servers/databases/securityMetrics/* | Não é possível editar métricas de segurança de banco de dados do SQL server |
| Microsoft.Sql/servers/securityAlertPolicies/* | Não é possível editar políticas de alerta de segurança do SQL server |

### <a name="classic-storage-account-contributor"></a>Colaborador de conta de armazenamento clássico
Pode gerenciar contas de armazenamento clássico

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorização de leitura |
| Microsoft.ClassicStorage/storageAccounts/* | Criar e gerenciar contas de armazenamento |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta de ideias |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### <a name="storage-account-contributor"></a>Colaborador de conta de armazenamento
Pode gerenciar contas de armazenamento, mas não acessar a elas.

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler todas as autorização |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta de ideias |
| Microsoft.Insights/diagnosticSettings/* | Gerenciar as configurações de diagnósticos |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Storage/storageAccounts/* | Criar e gerenciar contas de armazenamento |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### <a name="user-access-administrator"></a>Administrador de acesso do usuário
Pode gerenciar o acesso do usuário aos recursos do Azure

| **Ações** ||
| ------- | ------ |
| * / leitura | Leia os recursos de todos os tipos, exceto segredos. |
| Microsoft.Authorization/* | Gerenciar a autorização |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### <a name="classic-virtual-machine-contributor"></a>Colaborador clássico Máquina Virtual
Gerenciam clássicas máquinas virtuais, mas não a rede ou armazenamento conta virtual à qual eles estão conectados

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorização de leitura |
| Microsoft.ClassicCompute/domainNames/* | Criar e gerenciar nomes de domínio de computação clássico |
| Microsoft.ClassicCompute/virtualMachines/* | Criar e gerenciar máquinas virtuais |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | Ingressar em grupos de segurança de rede |
| Microsoft.ClassicNetwork/reservedIps/link/action | Vincular IPs reservadas |
| Microsoft.ClassicNetwork/reservedIps/read | Endereços IP reservados de leitura |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Ingressar em redes virtuais |
| Microsoft.ClassicNetwork/virtualNetworks/read | Redes virtuais de leitura |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Ler discos de conta de armazenamento |
| Microsoft.ClassicStorage/storageAccounts/images/read | Ler imagens de conta de armazenamento |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lista de teclas de conta de armazenamento |
| Microsoft.ClassicStorage/storageAccounts/read | Contas de armazenamento clássico de leitura |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta de ideias |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### <a name="virtual-machine-contributor"></a>Colaborador de máquina virtual
Pode gerenciar máquinas virtuais, mas não a rede ou armazenamento conta virtual à qual eles estão conectados

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorização de leitura |
| Microsoft.Compute/availabilitySets/* | Criar e gerenciar conjuntos de disponibilidade de computação |
| Microsoft.Compute/locations/* | Criar e gerenciar locais de computação |
| Microsoft.Compute/virtualMachines/* | Criar e gerenciar máquinas virtuais |
| Microsoft.Compute/virtualMachineScaleSets/* | Criar e gerenciar conjuntos de escala de máquina virtual |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta de ideias |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Ingressar em pools de endereços back-end do gateway de aplicativo de rede |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ingressar em pools de endereços de back-end de Balanceador de carga |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Participar de Balanceador de carga pools NAT de entrada |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Participar de Balanceador de carga NAT regras de entrada |
| Microsoft.Network/loadBalancers/read | Balanceadores de carga de leitura |
| Microsoft.Network/locations/* | Criar e gerenciar locais de rede |
| Microsoft.Network/networkInterfaces/* | Criar e gerenciar interfaces de rede |
| Microsoft.Network/networkSecurityGroups/join/action | Ingressar em grupos de segurança de rede |
| Microsoft.Network/networkSecurityGroups/read | Ler grupos de segurança de rede |
| Microsoft.Network/publicIPAddresses/join/action | Ingressar em endereços IP públicos de rede |
| Microsoft.Network/publicIPAddresses/read | Leia endereços IP públicos de rede |
| Microsoft.Network/virtualNetworks/read | Redes virtuais de leitura |
| Microsoft.Network/virtualNetworks/subnets/join/action | Ingressar em sub-redes de uma rede virtual |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Storage/storageAccounts/listKeys/action | Lista de teclas de conta de armazenamento |
| Microsoft.Storage/storageAccounts/read | Contas de armazenamento de leitura |
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### <a name="classic-network-contributor"></a>Rede clássico Colaborador
Pode gerenciar IPs reservadas e redes virtuais clássicas

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorização de leitura |
| Microsoft.ClassicNetwork/* | Criar e gerenciar redes clássicos |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta de ideias |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |

### <a name="web-plan-contributor"></a>Colaborador do plano de Web
Pode gerenciar os planos de web

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorização de leitura |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta de ideias |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
| Microsoft.Web/serverFarms/* | Criar e gerenciar server farms |

### <a name="website-contributor"></a>Colaborador de site
Pode gerenciar sites, mas não os planos da web à qual eles estão conectados

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorização de leitura |
| Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta de ideias |
| Microsoft.Insights/components/* | Criar e gerenciar componentes de ideias |
| Microsoft.ResourceHealth/availabilityStatuses/read | Leia a integridade dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerenciar implantações de grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
| Microsoft.Web/certificates/* | Criar e gerenciar certificados de site |
| Microsoft.Web/listSitesAssignedToHostName/read | Sites de leitura atribuídos a um nome de host |
| Microsoft.Web/serverFarms/join/action | Ingressar em farms do servidor |
| Microsoft.Web/serverFarms/read | Farms de leitura |
| Microsoft.Web/sites/* | Criar e gerenciar sites |

## <a name="see-also"></a>Consulte também
- [Controle de acesso baseado em função](role-based-access-control-configure.md): Introdução ao RBAC no portal do Azure.
- [Funções personalizadas no Azure RBAC](role-based-access-control-custom-roles.md): Aprenda a criar funções personalizadas para atender às suas necessidades de acesso.
- [Criar um acesso alterar relatório de histórico](role-based-access-control-access-change-history-report.md): controlar as atribuições de função mudanças no RBAC.
- [Solução de problemas de controle de acesso baseado em função](role-based-access-control-troubleshooting.md): obter sugestões para corrigir problemas comuns.
