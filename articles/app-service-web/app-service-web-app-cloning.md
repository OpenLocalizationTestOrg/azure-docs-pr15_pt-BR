<properties
    pageTitle="Clonagem de aplicativo Web usando o PowerShell"
    description="Saiba como clonar seus aplicativos da Web para novos aplicativos Web usando o PowerShell."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/13/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-powershell"></a>Aplicativo de serviço de aplicativo Azure clonagem usando o PowerShell#

Com o lançamento do Microsoft Azure PowerShell versão 1.1.0 uma nova opção foi adicionada ao novo-AzureRMWebApp dar ao usuário a capacidade de clonar um aplicativo Web existente para um aplicativo recém-criado em uma região diferente ou na mesma região. Isso permitirá aos clientes implantar um número de aplicativos em diferentes regiões rapidez e facilidade.

Aplicativo clonagem está atualmente suporte apenas para os planos de serviço de aplicativo de nível premium. O novo recurso usa as mesmas limitações como recurso de Backup de aplicativos Web, consulte [fazer backup de um aplicativo web no serviço de aplicativo do Azure](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

Para saber como usar o Gerenciador de recursos do Azure com base Azure PowerShell cmdlets para gerenciar seus aplicativos Web verificar [Gerenciador de recursos do Azure com base em comandos do PowerShell do Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="cloning-an-existing-app"></a>Clonagem um aplicativo existente ##

Cenário: Um aplicativo web existente na região Sul Central-nos, o usuário gostaria de clonar o conteúdo para um novo aplicativo web na região Centro Norte dos EUA. Isso pode ser feito usando a versão do Gerenciador de recursos do Azure do cmdlet do PowerShell para criar um novo aplicativo web com a opção - SourceWebApp.

Se souber o nome do grupo de recursos que contém o aplicativo da web de origem, podemos usar o seguinte comando do PowerShell para obter informações sobre a origem do web app (neste caso chamado de origem-Web App):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Para criar um novo plano de serviço de aplicativo, podemos usar comando New-AzureRmAppServicePlan como no exemplo seguinte

    New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

Usando o comando New-AzureRmWebApp, podemos criar o novo aplicativo web na região Centro Norte dos EUA e relacioná-lo para um nível premium existente plano de serviço do aplicativo, além disso podemos pode usar o mesmo grupo de recursos como o aplicativo da web de origem ou definir um novo grupo de recursos, demonstra o seguinte:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

Para clonar um aplicativo web existente, incluindo todos os respectivos implantação slots, o usuário precisará usar o parâmetro IncludeSourceWebAppSlots, o seguinte comando do PowerShell demonstra o uso desse parâmetro com o comando New-AzureRmWebApp:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots $true

Para clonar um aplicativo web existente dentro da mesma região, o usuário precisará criar um novo grupo de recursos e um novo serviço de aplicativo planejar na mesma região e, em seguida, usando o seguinte comando do PowerShell para clonar o web app

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonagem um aplicativo existente para um ambiente de serviço de aplicativo ##

Cenário: Um aplicativo web existente na região Sul Central-nos, o usuário gostaria de clonar o conteúdo para um novo aplicativo web para uma existente aplicativo serviço ambiente (ASE).

Se souber o nome do grupo de recursos que contém o aplicativo da web de origem, podemos usar o seguinte comando do PowerShell para obter informações sobre a origem do web app (neste caso chamado de origem-Web App):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Conhecer o nome do ASE e o nome do grupo de recursos que o ASE pertence, o usuário pode usar o comando New-AzureRmWebApp para criar o novo aplicativo web do ASE existente, demonstra o seguinte:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

O parâmetro de local é necessário devido a razão herdado, mas no caso de criação de um aplicativo em um ASE ele será ignorado. 

## <a name="cloning-an-existing-app-slot"></a>Clonagem um Slot de aplicativo existente ##

Cenário: O usuário gostaria de clonar um Slot de aplicativo da Web existente para tanto um novo aplicativo Web ou um novo slot Web App. O novo aplicativo Web pode estar na mesma região como o slot original do Web App ou em uma região diferente.

Se souber o nome do grupo de recursos que contém o aplicativo da web de origem, podemos usar o seguinte comando do PowerShell para obter informações do slot fonte web app (neste caso chamadas de origem webappslot) vinculadas a origem do Web App-Web App:

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

O seguinte demonstra Criando um clonar do aplicativo da web de origem para um novo aplicativo web:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## <a name="configuring-traffic-manager-while-cloning-a-app"></a>Configurando o Gerenciador de tráfego enquanto clonagem um aplicativo ##

Criando aplicativos web de várias regiões e configurando o Gerenciador de tráfego do Azure para rotear o tráfego para todos esses aplicativos web, é um cenário importante n para garantir que aplicativos clientes estão altamente disponíveis, ao clonar um aplicativo web existente que você tem a opção de conectar os dois aplicativos web a um novo perfil de Gerenciador de tráfego ou uma já existente - Observe que somente Gerenciador de recursos do Azure versão do Gerenciador de tráfego é suportada.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-a-app"></a>Criando um novo perfil de tráfego Manager enquanto clonagem um aplicativo ###

Cenário: O usuário gostaria de clonar um web app para outra região, ao configurar um perfil de Gerenciador de tráfego do Gerenciador de recursos do Azure que incluem os dois aplicativos web. O seguinte demonstra Criando um clonar do aplicativo da web de origem para um novo aplicativo web ao configurar um novo perfil de Gerenciador de tráfego:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Adicionar novo clonar Web App em um perfil de Gerenciador de tráfego existente ###

Cenário: O usuário já tem um perfil de Gerenciador de tráfego do Gerenciador de recursos do Azure que ele gostaria de adicionar os dois aplicativos web como pontos de extremidade. Para fazer isso, primeiro, precisamos montar o id de perfil de Gerenciador de tráfego existente, teremos o id de assinatura, nome do grupo de recursos e o nome de perfil de Gerenciador de tráfego existente.

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

Após ter a identificação do Gerenciador de tráfego, o seguinte demonstra Criando um clonar do aplicativo da web de origem para um novo aplicativo web ao adicioná-los a um perfil de Gerenciador de tráfego existente:

    $destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## <a name="current-restrictions"></a>Restrições atuais ##

Este recurso está atualmente no modo de visualização, estamos trabalhando para adicionar novos recursos ao longo do tempo, a lista a seguir estão as restrições conhecidas na versão atual do aplicativo clonagem:

- Configurações de escala automática não são duplicadas
- Configurações de agendamento de backup não são duplicadas
- Configurações de VNET não são duplicadas
- Obtenção de informações de aplicativo não está automaticamente configuradas no aplicativo web do destino
- Configurações de Auth fácil não são duplicadas
- Extensão de kudu não são duplicado
- Dica regras não são duplicadas
- Conteúdo do banco de dados não são duplicado


### <a name="references"></a>Referências ###
- [Gerenciador de recursos de Azure com base em comandos do PowerShell do Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)
- [Clonagem de aplicativo Web usando o Portal do Azure](app-service-web-app-cloning-portal.md)
- [Fazer backup de um aplicativo web no serviço de aplicativo do Azure](web-sites-backup.md)
- [Azure suporte de Gerenciador de recursos para visualização de tráfego Gerenciador do Azure](../../articles/traffic-manager/traffic-manager-powershell-arm.md)
- [Introdução ao ambiente de serviço de aplicativo](app-service-app-service-environment-intro.md)
- [Usando o PowerShell Azure com o Azure Resource Manager](../powershell-azure-resource-manager.md)
