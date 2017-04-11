<properties
    pageTitle="Exemplos de início rápido do Monitor CLI Azure. | Microsoft Azure"
    description="Amostra comandos para recursos do Monitor do Azure. Monitor Azure é um serviço do Microsoft Azure que permite que você enviar notificações de alerta, chame URLs de web com base em valores de dados de telemetria configurado e os serviços de nuvem de escala automática, máquinas virtuais e aplicativos Web."
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
    ms.date="09/08/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor--cross-platform-cli-quick-start-samples"></a>Exemplos de início rápido do Azure Monitor entre plataformas CLI

Este artigo mostra exemplos de linha de comando interface de comandos para ajudá-lo a acessar os recursos de Monitor Azure. Monitor Azure permite para serviços de nuvem de escala automática, máquinas virtuais e aplicativos da Web e para enviar notificações de alerta ou URLs de web com base em valores de dados de telemetria configurado de chamadas.

>[AZURE.NOTE] Monitor Azure é o novo nome para o que era chamado "Azure ideias" até 25 de setembro de 2016. No entanto, os namespaces e, portanto, os comandos abaixo ainda contenham "Percepções".


## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não tiver instalado CLI Azure, consulte [instalar o CLI do Azure](../xplat-cli-install.md). Se você estiver familiarizado com o Azure CLI, você pode ler mais sobre isso em [usar a CLI do Azure para Mac, Linux e Windows com o Gerenciador de recursos do Azure](../xplat-cli-azure-resource-manager.md).


No Windows, instale npm [Node site](https://nodejs.org/)do. Depois de concluir a instalação, usando CMD.exe com privilégios de executar como administrador, execute o seguinte da pasta onde npm está instalado:

```console
npm install azure-cli --global
```

Em seguida, navegue até qualquer pasta/local desejado e digite na linha de comando:

```console
azure help
```

## <a name="log-in-to-azure"></a>Faça logon no Azure

A primeira etapa é para efetuar login em sua conta do Azure.

```console
azure login
```

Depois de executar este comando, é necessário entrar com as instruções na tela. Posteriormente, você vê sua conta, TenantId e padrão ID da assinatura. Todos os comandos funcionam no contexto da sua assinatura padrão.

Para listar os detalhes da sua assinatura atual, use o comando a seguir.

```console
azure account show
```

Para alterar o contexto de trabalho para uma assinatura diferente, use o comando a seguir.

```console
azure account set "subscription ID or subscription name"
```

Para usar comandos do Gerenciador de recursos do Azure e Monitor do Azure, é necessário estar no modo do Gerenciador de recursos do Azure.

```console
azure config mode arm
```

Para exibir uma lista de todos os comandos do Azure Monitor compatíveis, execute o seguinte procedimento.

```console
azure insights
```

## <a name="view-audit-logs-for-a-subscription"></a>Exibir logs de auditoria para uma assinatura

Para exibir uma lista de logs de auditoria, execute o seguinte procedimento.

```console
azure insights logs list [options]
```

Tente o seguinte para exibir todas as opções disponíveis.

```console
azure insights logs list -help
```

Aqui está um exemplo logs de lista por um resourceGroup

```console
azure insights logs list --resourceGroup "myrg1"
```

Exemplo para logs de lista pelo chamador

```console
azure insights logs list --caller "myname@company.com"
```

Exemplo para logs de lista pelo chamador em um tipo de recurso, dentro de uma data de início e término

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Trabalhar com alertas
Você pode usar as informações da seção para trabalhar com alertas.

### <a name="get-alert-rules-in-a-resource-group"></a>Obter regras de alerta em um grupo de recursos

```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>Criar uma regra de alerta métrica

```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-a-log-alert-rule"></a>Criar uma regra de alerta de log

```console
azure insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### <a name="create-webtest-alert-rule"></a>Criar regra de alerta de webtest

```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>Excluir uma regra de alerta

```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>Perfis de log
Use as informações desta seção para trabalhar com perfis de log.

### <a name="get-a-log-profile"></a>Obter um perfil de log

```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>Adicionar um perfil de log sem retenção

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Remover um perfil de log

```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>Adicionar um perfil de log com retenção

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Adicionar um perfil de log com retenção e EventHub

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>Diagnósticos
Use as informações desta seção para trabalhar com configurações de diagnósticos.

### <a name="get-a-diagnostic-setting"></a>Obter uma configuração de diagnóstico

```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>Desabilitar uma configuração de diagnóstico

```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>Habilitar uma configuração de diagnóstico sem retenção

```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Escala automática
Use as informações desta seção para trabalhar com configurações de escala automática. Você precisa modificar estes exemplos.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obter configurações de escala automática para um grupo de recursos

```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obter configurações de escala automática por nome em um grupo de recursos

```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>Definir configurações de auotoscale

```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```
