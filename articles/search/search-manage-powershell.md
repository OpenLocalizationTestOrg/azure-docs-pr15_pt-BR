<properties 
    pageTitle="Gerenciar pesquisa Azure com scripts do Powershell | Microsoft Azure | Serviço de pesquisa de nuvem hospedado" 
    description="Gerencie o serviço de pesquisa do Azure com scripts do PowerShell. Criar ou atualizar um serviço de pesquisa do Azure e gerenciar chaves de administração de pesquisa do Azure" 
    services="search" 
    documentationCenter="" 
    authors="seansaleh" 
    manager="mblythe" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="search" 
    ms.devlang="na" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="powershell" 
    ms.date="08/15/2016" 
    ms.author="seasa"/>

# <a name="manage-your-azure-search-service-with-powershell"></a>Gerenciar o serviço de pesquisa do Azure com o PowerShell
> [AZURE.SELECTOR]
- [Portal](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [API REST](search-get-started-management-api.md)

Este tópico descreve os comandos do PowerShell para realizar muitas das tarefas de gerenciamento de serviços de pesquisa do Azure. Vamos percorrer criando um serviço de pesquisa, dimensioná-lo e gerenciar suas chaves de API.
Esses comandos paralela as opções de gerenciamento disponíveis na [API REST do Azure pesquisa gerenciamento](http://msdn.microsoft.com/library/dn832684.aspx).

## <a name="prerequisites"></a>Pré-requisitos
 
- Você deve ter Azure PowerShell 1.0 ou maior. Para obter instruções, consulte [instalar e configurar o Azure PowerShell](../powershell-install-configure.md).
- Você deve estar conectado à sua assinatura do Azure no PowerShell conforme descrito abaixo.

Primeiro, você deve fazer login no Azure com este comando:

    Login-AzureRmAccount

Especifique o endereço de email da sua conta do Azure e sua senha na caixa de diálogo de login Microsoft Azure.

Como alternativa, você pode [fazer login não interativa com um serviço principal](../resource-group-authenticate-service-principal.md).

Se você tiver várias assinaturas Azure, você precisa definir sua assinatura do Azure. Para ver uma lista de suas assinaturas atuais, execute este comando.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar a assinatura, execute o seguinte comando. No exemplo a seguir, o nome da assinatura é `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Comandos para ajudá-lo a começar

    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search" -Force

    # Create a new search service
    # This command will return once the service is fully created
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1
    
    # Get information about your new service and store it in $resource
    $resource = Get-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19
    
    # View your resource
    $resource
    
    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key
    
    # View your query key
    $queryKey

    # Delete query key
    Remove-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19
        
    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzureRmResource
    
    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzureRmResource
    
## <a name="next-steps"></a>Próximas etapas
    
Agora que seu serviço for criado, você pode levar as próximas etapas: criar um [índice](search-what-is-an-index.md), [um índice de consulta](search-query-overview.md)e finalmente criar e gerenciar seu próprio aplicativo de pesquisa que usa a pesquisa do Azure.

- [Criar um índice de pesquisa do Azure no portal do Azure](search-create-index-portal.md)

- [Consultar um índice de pesquisa do Azure usando o Gerenciador de pesquisa no portal do Azure](search-explorer.md)

- [Configurar um indexador para carregar dados de outros serviços](search-indexer-overview.md)

- [Como usar a pesquisa do Azure no .NET](search-howto-dotnet-sdk.md)

- [Analisar o tráfego de pesquisa do Azure](search-traffic-analytics.md)
