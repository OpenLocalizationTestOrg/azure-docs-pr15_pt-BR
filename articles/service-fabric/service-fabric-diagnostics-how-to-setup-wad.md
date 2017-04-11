<properties
   pageTitle="Coletar logs usando o diagnóstico do Azure | Microsoft Azure"
   description="Este artigo descreve como configurar o diagnóstico do Azure para coletar logs de um cluster de estrutura de serviço em execução no Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="toddabel"/>


# <a name="collect-logs-by-using-azure-diagnostics"></a>Coletar logs usando o diagnóstico do Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Quando você está executando um cluster de estrutura de serviço do Azure, é uma boa ideia coletar logs de todos os nós em um local central. Ter os logs em um local central ajuda você a analisar e solucionar problemas no seu cluster, ou em aplicativos e serviços executando desse cluster.

Uma maneira de carregar e coletar logs é usar a extensão de diagnóstico do Azure, que carrega os logs para armazenamento do Azure. Os logs não são útil diretamente no armazenamento. Mas você pode usar um processo externo para ler os eventos de armazenamento e colocá-los em um produto como [A análise de Log](../log-analytics/log-analytics-service-fabric.md), [Pesquisa elástica](service-fabric-diagnostic-how-to-use-elasticsearch.md)ou outra solução de análise de log.

## <a name="prerequisites"></a>Pré-requisitos
Você usará essas ferramentas para executar algumas das operações neste documento:

* [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (relacionados aos serviços de nuvem do Azure, mas tem boas informações e exemplos)
* [Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md)
* [PowerShell Azure](../powershell-install-configure.md)
* [Azure cliente do Gerenciador de recursos](https://github.com/projectkudu/ARMClient)
* [Modelo do Gerenciador de recursos Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)


## <a name="log-sources-that-you-might-want-to-collect"></a>Fontes de registro que você talvez queira coletar
- **Logs de serviço tecidos**: emitida da plataforma aos canais de rastreamento de eventos para Windows (ETW) e EventSource padrão. Logs podem ser um dos vários tipos:
  - Eventos operacionais: Logs de operações que executa a plataforma de serviço tecidos. Exemplos incluem a criação de aplicativos e serviços, alterações de estado de nó e informações de atualização.
  - [Atores confiáveis eventos do modelo de programação](service-fabric-reliable-actors-diagnostics.md)
  - [Serviços confiáveis eventos do modelo de programação](service-fabric-reliable-services-diagnostics.md)
- **Eventos do aplicativo**: eventos emitida de código do seu serviço e gravados usando a classe de auxiliar EventSource fornecida em modelos do Visual Studio. Para obter mais informações sobre como gravar logs de seu aplicativo, consulte [Monitor e diagnosticar serviços em uma configuração de desenvolvimento de máquina local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).


## <a name="deploy-the-diagnostics-extension"></a>Implantar a extensão de diagnóstico
A primeira etapa coletar logs é implantar a extensão de diagnóstico em cada uma das VMs no cluster tecidos de serviço. A extensão de diagnóstico coleta logs em cada máquina virtual e carrega-las para a conta de armazenamento que você especificar. As etapas variam um pouco com base em se você usar o portal do Azure ou o Gerenciador de recursos do Azure. As etapas também variam com base em se a implantação for parte da criação de cluster ou for para um cluster que já existe. Vejamos as etapas para cada cenário.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-the-portal"></a>Implantar a extensão de diagnóstico como parte da criação de cluster por meio do portal
Para implantar a extensão de diagnóstico VMs no cluster como parte da criação do cluster, use o painel de configurações de diagnóstico mostrado na imagem a seguir. Para habilitar a coleção de evento atores confiável ou serviços confiáveis, certifique-se de que o diagnóstico está definido como **em** (a configuração padrão). Depois de criar o cluster, você não pode alterar essas configurações usando o portal.

![Configurações do diagnóstico Azure no portal para a criação de um cluster](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

O Azure equipe *requer* suporte logs para ajudar a resolver as solicitações de suporte que você criar. Esses logs são coletados em tempo real e são armazenados em uma das contas de armazenamento criadas no grupo de recursos. As configurações do diagnóstico configuram eventos de nível de aplicativo. Esses eventos incluem eventos de [Atores confiável](service-fabric-reliable-actors-diagnostics.md) , eventos de [Serviços confiáveis](service-fabric-reliable-services-diagnostics.md) e alguns eventos de tecidos de serviço de nível de sistema para ser armazenado no armazenamento do Azure.

Produtos como [Pesquisa elástica](service-fabric-diagnostic-how-to-use-elasticsearch.md) ou seu próprio processo podem acessar os eventos da conta de armazenamento. Atualmente, não há nenhuma maneira para filtrar ou limpe bem os eventos que são enviados para a tabela. Se você não implementar um processo para remover eventos da tabela, a tabela continuará a crescer.

Quando você estiver criando um cluster usando o portal, é altamente recomendável que você baixe o modelo *antes de clicar em * *Okey** * para criar o cluster. Para obter detalhes, consulte [Configurar um cluster de estrutura de serviço usando um modelo do Gerenciador de recursos do Azure](service-fabric-cluster-creation-via-arm.md). Você precisará o modelo para fazer alterações mais tarde, pois não é possível fazer algumas alterações usando o portal.

Você pode exportar modelos do portal usando as etapas a seguir. No entanto, esses modelos podem ser mais difíceis de usar porque eles podem ter valores nulos que estão faltando informações necessárias.

1. Abra seu grupo de recursos.
2. Selecione **configurações** para exibir o painel de configurações.
3. Selecione **implantações** para exibir o painel de histórico de implantação.
4. Selecione uma implantação para exibir os detalhes da implantação.
5. Selecione **Exportar modelo** para exibir o painel de modelo.
6. Selecione **Salvar arquivo** para exportar um arquivo. zip que contém o modelo, parâmetro e arquivos do PowerShell.

Após exportar os arquivos, você precisa fazer uma modificação. Edite o arquivo parameters.json e remova o elemento **adminPassword** . Isso fará com que um prompt para a senha quando o script de implantação é executado. Quando você estiver executando o script de implantação, talvez seja necessário corrigir valores de parâmetros nulos.

Para usar o modelo baixado para atualizar uma configuração:

1. Extraia o conteúdo para uma pasta no computador local.
2. Modifica o conteúdo para refletir a nova configuração.
3. Iniciar o PowerShell e mude para a pasta onde foi extraído o conteúdo.
4. Execute **deploy.ps1** e preencha a ID da assinatura, o nome do grupo de recursos (use o mesmo nome para atualizar a configuração) e um nome de implantação exclusivo.


### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Implantar a extensão de diagnóstico como parte da criação de cluster usando o Gerenciador de recursos do Azure
Para criar um cluster usando o Gerenciador de recursos, você precisa adicionar a configuração de diagnóstico JSON para o modelo do Gerenciador de recursos de cluster completo antes de criar o cluster. Fornecemos uma amostra cinco-máquina virtual cluster Gerenciador de recursos de modelo com a configuração de diagnóstico adicionada a ele como parte de nossos exemplos de modelo do Gerenciador de recursos. Você pode vê-la nesse local na Galeria de exemplos do Azure: [cluster cinco nós com amostra de modelo do Gerenciador de recursos de diagnóstico](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

Para ver a configuração de diagnóstico no modelo do Gerenciador de recursos, abra o arquivo azuredeploy.json e procure por **IaaSDiagnostics**. Para criar um cluster usando este modelo, selecione o botão de **implantar no Azure** disponível no link anterior.

Como alternativa, você pode baixar o Gerenciador de recursos de exemplo, fazer alterações e criar um cluster com o modelo modificado usando o `New-AzureRmResourceGroupDeployment` comando em uma janela do PowerShell do Azure. Consulte o seguinte código para os parâmetros que você passa para o comando. Para obter informações detalhadas sobre como implantar um grupo de recursos usando o PowerShell, consulte o artigo [implantar um grupo de recursos com o modelo do Gerenciador de recursos do Azure](../resource-group-template-deploy.md).

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Implantar a extensão de diagnóstico em um cluster existente
Se você tiver um cluster existente que não tem o diagnóstico implantado ou se você deseja modificar uma configuração existente, você pode adicionar ou atualizá-lo. Modifica o modelo do Gerenciador de recursos que é usado para criar o cluster existente ou baixar o modelo a partir do portal, conforme descrito anteriormente. Modificar o arquivo template.json, executando as seguintes tarefas.

Adicione um novo recurso de armazenamento para o modelo, adicionando à seção de recursos.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Em seguida, adicione a seção parâmetros logo após as definições de conta de armazenamento, entre `supportLogStorageAccountName` e `vmNodeType0Name`. Substitua o texto de espaço reservado para *o nome de conta de armazenamento aqui* o nome da conta de armazenamento.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Em seguida, atualize o `VirtualMachineProfile` seção do arquivo template.json, adicionando o seguinte código dentro da matriz de extensões. Certifique-se de adicionar uma vírgula no início ou fim, dependendo de onde ele é inserido.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Depois de modificar o arquivo template.json conforme descrito, republicar o modelo do Gerenciador de recursos. Se o modelo foi exportado, executando o arquivo deploy.ps1 publicá novamente o modelo. Depois de implantar, certifique-se de que **ProvisioningState** é **bem-sucedido**.


## <a name="update-diagnostics-to-collect-and-upload-logs-from-new-eventsource-channels"></a>Atualizar o diagnóstico para coletar e carregar logs de novos EventSource canais
Para atualizar o diagnóstico para coletar logs de novos canais EventSource que representam um novo aplicativo que você está prestes a implantar, execute as mesmas etapas da [seção anterior](#deploywadarm) para a instalação do diagnóstico para um cluster existente.

Atualizar o `EtwEventSourceProviderConfiguration` seção no arquivo template.json para adicionar entradas para os novos canais de EventSource antes de aplicar a configuração de atualização usando o `New-AzureRmResourceGroupDeployment` comando do PowerShell. O nome de origem do evento é definido como parte do seu código no arquivo ServiceEventSource.cs gerados pelo Visual Studio.

Por exemplo, se sua fonte de evento for chamado Meus Eventsource, adicione o seguinte código para colocar os eventos do meu Eventsource em uma tabela chamada MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Para coletar contadores de desempenho ou logs de eventos, modifique o modelo do Gerenciador de recursos, usando os exemplos fornecidos em [criar uma máquina de virtual do Windows com o monitoramento e diagnóstico usando um modelo do Gerenciador de recursos do Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md). Em seguida, republicar o modelo do Gerenciador de recursos.

## <a name="next-steps"></a>Próximas etapas
Para compreender mais detalhadamente os eventos que você deve procurar durante a solução de problemas, consulte os eventos de diagnósticos emitidos para [Atores confiável](service-fabric-reliable-actors-diagnostics.md) e [Serviços confiáveis](service-fabric-reliable-services-diagnostics.md).


## <a name="related-articles"></a>Artigos relacionados
* [Saiba como coletar contadores de desempenho ou logs usando a extensão de diagnóstico](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)
* [Solução de serviço tecidos em análise de Log](../log-analytics/log-analytics-service-fabric.md)
