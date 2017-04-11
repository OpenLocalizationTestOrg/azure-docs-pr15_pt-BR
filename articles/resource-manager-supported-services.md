<properties
   pageTitle="Gerenciador de recursos de suporte para serviços | Microsoft Azure"
   description="Descreve os provedores de recurso Gerenciador de recursos, seus esquemas e as versões de API disponíveis e as regiões que podem hospedar os recursos de suporte."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="magoedte;tomfitz"/>

# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>Provedores do Gerenciador de recursos, regiões, as versões de API e esquemas

Gerenciador de recursos de Azure fornece uma nova maneira para implantar e gerenciar os serviços que constituem seus aplicativos. Gerenciador de recursos de suporte a maioria dos, mas não todos, os serviços e alguns serviços Gerenciador de recursos de suporte apenas parcialmente. Este tópico fornece uma lista de provedores de recursos com suporte para o Gerenciador de recursos do Azure.

Ao implantar seus recursos, você também precisa saber quais regiões esses recursos de suporte e quais versões de API estão disponíveis para os recursos. A seção [suportados regiões](#supported-regions) mostra como descobrir quais trabalho regiões para sua assinatura e recursos. A seção [suporte API versões](#supported-api-versions) mostra como determinar quais versões de API que você pode usar.

Para ver quais serviços são compatíveis com o Azure portal e o portal clássico, consulte o [gráfico de disponibilidade de portal Azure](https://azure.microsoft.com/features/azure-portal/availability/). Para ver a movimentação de recursos de suporte de quais serviços, consulte [Mover recursos para novo grupo de recursos ou assinatura](resource-group-move-resources.md).

As tabelas a seguir listam quais implantação de suporte do Microsoft services e gerenciamento por meio do Gerenciador de recursos e quais não. O link na coluna **Início rápido modelos** envia uma consulta para o repositório de modelos de início rápido do Azure para o provedor de recurso especificado. Modelos de início rápido sejam adicionados e atualizados com frequência. A presença de um link para um determinado serviço não necessariamente que a consulta retorna modelos do repositório. Também existem muitos provedores de recurso de terceiros que oferecem suporte ao Gerenciador de recursos. Saiba como ver todos os provedores de recursos na seção [tipos e provedores de recursos](#resource-providers-and-types) .


## <a name="compute"></a>Calcular

| Serviço | Gerenciador de recursos habilitados | API REST | Esquema | Modelos de início rápido |
| ------- | ------------------------ |-------- | ------ | ------ |
| Em lotes   | Sim     | [Lote restante](https://msdn.microsoft.com/library/azure/dn820158.aspx) | [2015-12-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-12-01/Microsoft.Batch.json)       |  |
|Contêiner | Sim | [Serviço de contêiner restante](https://msdn.microsoft.com/library/azure/mt711470.aspx) | [2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.ContainerService.json) | [Microsoft.ContainerService](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ContainerService%22&type=Code) |
| Serviços de ciclo de vida do Dynamics | Sim  |      |        |  |
| Conjuntos de escala | Sim | [Escala definida restante](https://msdn.microsoft.com/library/azure/mt705635.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [virtualMachineScaleSets](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=virtualMachineScaleSets&type=Code) | 
| Estrutura de serviço | Sim  | [Serviço tecidos restante](https://msdn.microsoft.com/library/azure/dn707692.aspx)  |      | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Máquinas virtuais | Sim | [RESTO DE MÁQUINA VIRTUAL](https://msdn.microsoft.com/library/azure/mt163647.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [virtualMachines](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%2Fvirtualmachines%22&type=Code) |
| Máquinas virtuais (clássico) | Limitado | - | - | - |
| Aplicativo Remote | Não      | -  | - | - |
| Serviços de nuvem (clássico) | Limitado (veja abaixo) | - | - | - |

Máquinas virtuais (clássico) refere-se aos recursos que foram implantados através do modelo de implantação clássico, em vez de por meio do modelo de implantação do Gerenciador de recursos. Em geral, esses recursos não dão suporte a operações do Gerenciador de recursos, mas há algumas operações que foram habilitadas. Para obter mais informações sobre esses modelos de implantação, consulte [Gerenciador de recursos de Noções básicas sobre implantação e implantação clássica](resource-manager-deployment-model.md). 

Serviços de nuvem (clássicos) podem ser usados com outros recursos clássicos; Entretanto, os recursos clássicos não tirar proveito de todos os recursos do Gerenciador de recursos e não são uma boa opção para soluções futuras. Em vez disso, considere a possibilidade de alterar sua infraestrutura de aplicativo para usar recursos dos namespaces Microsoft.Compute, Microsoft.Storage e Microsoft.Network.


## <a name="networking"></a>Rede

| Serviço | Gerenciador de recursos habilitados | API REST | Esquema | Modelos de início rápido |
| ------- | -------  | -------- | ------ | ------ |
| Gateway de aplicativo | Sim | [Gateway de aplicativos REST](https://msdn.microsoft.com/library/azure/mt684939.aspx)   |        | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS     | Sim | [RESTO DE DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx)         | [2016-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| Rota expressa | Sim  | [Rota expressa restante](https://msdn.microsoft.com/library/azure/mt586720.aspx)  |       | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| Balanceador de carga | Sim  | [Balanceador de carga restante](https://msdn.microsoft.com/library/azure/mt163651.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Gerenciador de tráfego | Sim | [Gerenciador de tráfego restante](https://msdn.microsoft.com/library/azure/mt163667.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json)  | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Redes virtuais | Sim| [Rede virtual restante](https://msdn.microsoft.com/en-us/library/azure/mt163650.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Gateway VPN | Sim | [Gateway de rede restante](https://msdn.microsoft.com/library/azure/mt163859.aspx) |  | [virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[conexões](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |


## <a name="storage"></a>Armazenamento

| Serviço | Gerenciador de recursos habilitados | API REST | Esquema | Modelos de início rápido |
| ------- | ------- | -------- | ------ | ------- | ------ |
| Armazenamento | Sim  | [Armazenamento restante](https://msdn.microsoft.com/library/azure/mt163683.aspx) | [Conta de armazenamento](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| StorSimple | Sim  |         |        |  |

## <a name="databases"></a>Bancos de dados

| Serviço | Gerenciador de recursos habilitados | API REST | Esquema | Modelos de início rápido |
| ------- | ------- | -------- | ------ | ------- | ------ |
| DocumentDB | Sim  | [DocumentDB restante](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json)  | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Relacionada Cache | Sim |   | [2016-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Banco de dados SQL | Sim | [Banco de dados do SQL restante](https://msdn.microsoft.com/library/azure/mt163571.aspx) | [visualização-de-01-04-2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| Depósito de dados do SQL | Sim |   |      |


## <a name="web--mobile"></a>Web & celular

| Serviço | Gerenciador de recursos habilitados | API REST | Esquema | Modelos de início rápido |
| ------- | ------- | -------- | ------ | ------ |
| Aplicativos de API | Sim |   | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Aplicativos de API](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Gerenciamento de API | Sim  | [Gerenciamento de API REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) | [2016-07-07](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-07-07/Microsoft.ApiManagement.json)       | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| Conteúdo moderador | Sim |   |   |   |
| Aplicativo de função | Sim |   |   | [functionApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22functionApp%22&type=Code) |
| Aplicativos de lógica | Sim   | [API do serviço de fluxo de trabalho restante](https://msdn.microsoft.com/library/azure/mt643787.aspx) | [2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.Logic.json) | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Aplicativos móveis | Sim |  | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json)  | [mobileApp](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22mobileApp%22&type=Code)   |
| Contratos móveis | Sim  |  [Contrato móvel restante](https://msdn.microsoft.com/library/azure/mt683754.aspx)        |        | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Pesquisa | Sim  | [Pesquisa restante](https://msdn.microsoft.com/library/azure/dn798935.aspx) |  | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Aplicativos Web | Sim |          | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## <a name="analytics"></a>Análise

| Serviço | Gerenciador de recursos habilitados | API REST | Esquema | Modelos de início rápido |
| ------- | -------  | -------- | ------ | ------ |
| Catálogo de dados | Sim  | [Catálogo de dados restante](https://msdn.microsoft.com/library/azure/mt267595.aspx)  | [2016-03-30](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |  |
| Fábrica de dados | Sim | [Dados fábrica restante](https://msdn.microsoft.com/library/azure/dn906738.aspx) |    | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Lucerne a análise de dados | Sim |   |   [2015-10-01-visualização](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) | [Microsoft.DataLakeAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeAnalytics%22&type=Code) |
| Armazenamento de Lucerne de dados | Sim  | [Dados Lucerne armazenamento restante](https://msdn.microsoft.com/library/azure/mt693424.aspx) | [2015-10-01-visualização](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01-preview/Microsoft.DataLakeAnalytics.json) | [Microsoft.DataLakeStore](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataLakeStore%22&type=Code) |
| HDInsights | Sim | [HDInsights restante](https://msdn.microsoft.com/library/azure/mt622197.aspx) |        | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Aprendizado de máquina | Sim | [RESTO de aprendizado de máquina](https://msdn.microsoft.com/library/azure/mt767538.aspx)        | [2016-05-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-01-preview/Microsoft.MachineLearning.json) |
| Análise de fluxo | Sim  | [Análise de vapor restante](https://msdn.microsoft.com/library/azure/dn835031.aspx)   |        |  |
| Power BI | Sim | [Power BI incorporado restante](https://msdn.microsoft.com/library/azure/mt712303.aspx) | [2016-01-29](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-01-29/Microsoft.PowerBI.json) |  |

## <a name="intelligence"></a>Inteligência de dados

| Serviço | Gerenciador de recursos habilitados | API REST | Esquema | Modelos de início rápido |
| ------- | ------- | -------- | ------ | ------ |
| Serviços de cognitivas | Sim |  | [2016-02-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-01-preview/Microsoft.CognitiveServices.json) |   |

## <a name="internet-of-things"></a>Internet das coisas

| Serviço | Gerenciador de recursos habilitados | API REST | Esquema | Modelos de início rápido |
| ------- | ------- | -------- | ------ | ------ |
| Hub de evento | Sim  | [Hub de evento restante](https://msdn.microsoft.com/library/azure/dn790674.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.EventHub.json) | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code)  |
| IoTHubs | Sim | [Hub de IoT restante](https://msdn.microsoft.com/library/azure/mt589014.aspx) | [2016-02-03](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-02-03/Microsoft.Devices.json) | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Hubs de notificação | Sim | [Hub de notificação restante](https://msdn.microsoft.com/library/azure/dn495827.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## <a name="media--cdn"></a>Mídia & CDN

| Serviço | Gerenciador de recursos habilitados | API REST | Esquema | Modelos de início rápido |
| ------- | ------- | -------- | ------ | ------ |
| CDN | Sim | [RESTO DE CDN](https://msdn.microsoft.com/library/azure/mt634456.aspx)  | [2016-04-02](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-04-02/Microsoft.Cdn.json) | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Serviço de mídia | Sim | [Serviços de mídia restante](https://msdn.microsoft.com/library/azure/hh973617.aspx)  | [2015-10-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-01/Microsoft.Media.json) |


## <a name="hybrid-integration"></a>Integração de híbrido

| Serviço | Gerenciador de recursos habilitados | API REST | Esquema | Modelos de início rápido |
| ------- | ------- | -------- | ------ | ------ |
| Serviços do BizTalk | Sim |          | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |  |
| Serviço de recuperação | Sim | [Recuperação de site restante](https://msdn.microsoft.com/library/azure/mt750497.aspx) | [2016-06-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-06-01/Microsoft.RecoveryServices.json) | [Microsoft.RecoveryServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.RecoveryServices%22&type=Code) |
| Barramento de serviço | Sim | [Barramento de serviço REST](https://msdn.microsoft.com/library/azure/mt639375.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.ServiceBus.json)       | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |

## <a name="identity--access-management"></a>Identidade e gerenciamento de acesso 

Active Directory do Azure funciona com o Gerenciador de recursos para habilitar o controle de acesso baseado em função para sua assinatura. Para saber sobre como usar o controle de acesso baseado em função e o Active Directory, consulte [Controle de acesso baseado em função do Azure](./active-directory/role-based-access-control-configure.md).

## <a name="developer-services"></a>Serviços de desenvolvedor 

| Serviço | Gerenciador de recursos habilitados | API REST | Esquema | Modelos de início rápido |
| ------- | ------- | -------- | ------ | ------ |
| Obtenção de informações de aplicativo | Sim  | [Ideias restante](https://msdn.microsoft.com/library/azure/dn931943.aspx)  | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.Insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing Maps | Sim  |          |        |  |
| DevTest Labs | Sim |   | [2016-05-15](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-05-15/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code)  |
| Conta do Visual Studio | Sim   |          | [26 / 02 / 2014](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |  |

## <a name="management-and-security"></a>Gerenciamento e segurança

| Serviço | Gerenciador de recursos habilitados | API REST | Esquema | Modelos de início rápido |
| ------- | ------- | -------- | ------ | ------ |
| Automação | Sim | [Automação restante](https://msdn.microsoft.com/library/azure/mt662285.aspx) | [31-10-2015](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-10-31/Microsoft.Automation.json)       | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Chave cofre | Sim | [Chave cofre restante](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [Chave cofre](resource-manager-template-keyvault.md)<br />[Secreta cofre chave](resource-manager-template-keyvault-secret.md)   | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Obtenção de informações operacionais | Sim |          |        |  |
| Agendador | Sim  | [Agendador restante](https://msdn.microsoft.com/library/azure/mt629143.aspx)     | [2016-03-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Segurança (prévia) | Sim | [Segurança restante](https://msdn.microsoft.com/library/azure/mt704034.aspx)  |  | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code)  |

## <a name="resource-manager"></a>Gerenciador de recursos

| Recurso | Gerenciador de recursos habilitados | API REST | Esquema | Modelos de início rápido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Autorização | Sim | [Bloqueios de gerenciamento](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Controle de acesso baseado em função](https://msdn.microsoft.com/library/azure/dn906885.aspx)  | [Bloqueio de recurso](resource-manager-template-lock.md)<br />[Atribuições de função](resource-manager-template-role.md)  | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Recursos | Sim | [Recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Links de recursos](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## <a name="resource-providers-and-types"></a>Tipos e provedores de recursos

Ao implantar recursos, você precisa frequentemente recuperar informações sobre os tipos e provedores de recursos. Você pode recuperar essas informações através de API REST, Azure PowerShell ou CLI do Azure.

Para trabalhar com um provedor de recursos, esse provedor de recursos deve ser registrado com sua conta. Por padrão, muitos provedores de recurso são automaticamente registrados; No entanto, você talvez precise registrar manualmente alguns provedores de recursos. Os exemplos a seguir mostram como obter o status do registro de um provedor de recursos e registrar o provedor de recursos, se necessário.

### <a name="portal"></a>Portal

Você pode ver facilmente uma lista de provedores de recursos compatíveis com as seguintes etapas:

1. Selecione **Minhas permissões**.

    ![Minhas permissões](./media/resource-manager-supported-services/my-permissions.png)

2. Selecione o **status de provedor do recurso**

    ![status de provedor do recurso](./media/resource-manager-supported-services/resource-provider-status.png)

3. Você pode ver a lista completa dos provedores de recursos para sua assinatura.

    ![provedores de lista de recursos](./media/resource-manager-supported-services/list-providers.png)

### <a name="rest-api"></a>API REST

Para obter todos os recursos disponíveis provedores, incluindo seus tipos, locais, as versões de API e status do registro, usam a operação de [todos os provedores de recurso de lista](https://msdn.microsoft.com/library/azure/dn790524.aspx) . Se você precisar registrar um provedor de recurso, consulte [registrar uma assinatura com um provedor de recursos](https://msdn.microsoft.com/library/azure/dn790548.aspx).

### <a name="powershell"></a>PowerShell

O exemplo a seguir mostra como acessar todos os provedores de recursos disponíveis.

    Get-AzureRmResourceProvider -ListAvailable
    

O exemplo a seguir mostra como obter os tipos de recursos para um provedor de determinado recurso.

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
    
A saída é semelhante a:

    ResourceTypeName                Locations                                         
    ----------------                ---------                                         
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} 
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} .
    ...
    
Para registrar um provedor de recursos, forneça o namespace:

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### <a name="azure-cli"></a>CLI Azure

O exemplo a seguir mostra como acessar todos os provedores de recursos disponíveis.

    azure provider list
    
A saída é semelhante a:

    info:    Executing command provider list
    + Getting registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

Você pode salvar as informações para um provedor de recurso específico em um arquivo com o comando a seguir.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Para registrar um provedor de recursos, forneça o namespace:

    azure provider register -n Microsoft.ServiceBus

## <a name="supported-regions"></a>Regiões com suporte

Ao implantar recursos, normalmente você precisa especificar uma região para os recursos. Gerenciador de recursos é suportado em todas as regiões, mas podem não ser compatíveis com os recursos que você implanta em todas as regiões. Além disso, pode haver limitações em sua assinatura que impedem a utilização algumas regiões que oferecem suporte ao recurso. Essas limitações podem estar relacionadas ao imposto problemas para o seu país inicial ou o resultado de uma política colocado pelo seu administrador de assinatura para usar somente determinadas regiões. 

Para obter uma lista completa de todas as regiões com suporte para todos os serviços do Azure, consulte [serviços por região](https://azure.microsoft.com/regions/#services); No entanto, essa lista pode incluir regiões que sua assinatura não oferece suporte. Você pode determinar as regiões para um tipo de recurso específico que sua assinatura do suporte por meio do portal, API REST, PowerShell ou CLI do Azure.

### <a name="portal"></a>Portal

Você pode ver as regiões com suporte para um tipo de recurso pelas seguintes etapas:

1. Selecione **mais serviços** > **Resource Explorer**.

    ![Gerenciador de recursos](./media/resource-manager-supported-services/select-resource-explorer.png)

2. Abra o nó de **provedores** .

    ![Selecionar provedores](./media/resource-manager-supported-services/select-providers.png)

3. Selecione um provedor de recursos e exibir as versões de API e regiões com suporte.

    ![provedor de visualização](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>API REST

Para descobrir quais regiões estão disponíveis para um tipo de recurso específico na sua assinatura, use a operação de [todos os provedores de recurso de lista](https://msdn.microsoft.com/library/azure/dn790524.aspx) . 

### <a name="powershell"></a>PowerShell

O exemplo a seguir mostra como acessar as regiões com suporte para sites da web.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
A saída é semelhante a:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

### <a name="azure-cli"></a>CLI Azure

O exemplo a seguir retorna todos os locais com suporte para cada tipo de recurso.

    azure location list

Você também pode filtrar os resultados do local com um utilitário JSON como [jq](https://stedolan.github.io/jq/).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Que retorna:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## <a name="supported-api-versions"></a>Versões de API com suporte

Quando você implanta um modelo, você deverá especificar uma versão de API para usar para a criação de cada recurso. A versão da API corresponde a uma versão de operações de API REST lançadas pelo provedor de recursos. Como um provedor de recursos permite que os novos recursos, ele libera uma nova versão da API REST. Portanto, a versão da API do que você especifica em seu modelo afeta quais propriedades que você pode especificar no modelo. Em geral, você deseja selecionar a versão mais recente do API ao criar modelos. Para modelos existentes, você pode decidir se você deseja continuar usando uma versão anterior do API ou atualizar seu modelo da versão mais recente tirar proveito dos novos recursos.

### <a name="portal"></a>Portal

Você pode determinar as versões de API com suporte da mesma maneira que você determinado suportados regiões (mostrados anteriormente).

### <a name="rest-api"></a>API REST

Para descobrir quais versões de API estão disponíveis para tipos de recurso, use a operação de [todos os provedores de recurso de lista](https://msdn.microsoft.com/library/azure/dn790524.aspx) . 

### <a name="powershell"></a>PowerShell

O exemplo a seguir mostra como acessar as versões de API disponíveis para um tipo de recurso específico.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
A saída é semelhante a:
    
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

### <a name="azure-cli"></a>CLI Azure

Você pode salvar as informações (incluindo as versões de API disponíveis) para um provedor de recursos em um arquivo com o comando a seguir.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Você pode abrir o arquivo e localize o elemento de **apiVersions**

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a criação de modelos do Gerenciador de recursos, consulte [modelos de coautoria Gerenciador de recursos do Azure](resource-group-authoring-templates.md).
- Para saber sobre a implantação de recursos, consulte [implantar um aplicativo com o Gerenciador de recursos do Azure modelo](resource-group-template-deploy.md).
