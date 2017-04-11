<properties
    pageTitle="Gerenciar barramento de serviço com o PowerShell | Microsoft Azure"
    description="Gerenciar barramento de serviço com scripts do PowerShell"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="sethm"/>

# <a name="manage-service-bus-with-powershell"></a>Gerenciar barramento de serviço com o PowerShell

## <a name="overview"></a>Visão geral

Microsoft Azure PowerShell é um ambiente de script que você pode usar para controlar e automatizar a implantação e gerenciamento de suas cargas de trabalho no Azure. Este artigo descreve como usar o PowerShell para provisionar e gerenciar entidades barramento de serviço como namespaces, filas e Hubs de evento usando um console local do PowerShell do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter os seguintes pré-requisitos:

- Uma assinatura do Azure. Azure é uma plataforma baseada em assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra][], [Oferece membro][]ou [Avaliação gratuita][].

- Um computador com o PowerShell do Azure. Para obter instruções, consulte [instalar e configurar o Azure PowerShell][].

- Uma compreensão geral de scripts do PowerShell, pacotes do NuGet e o .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Incluindo uma referência ao assembly .NET para barramento de serviço

Há um número limitado de cmdlets do PowerShell disponíveis para gerenciar barramento de serviço. Para provisionar entidades que não estão expostas pelos cmdlets existentes, você pode usar o cliente .NET para barramento de serviço no [pacote NuGet de barramento de serviço][].

Primeiro, certifique-se de que o script pode localizar o conjunto de **Microsoft.ServiceBus.dll** , que é instalado com o pacote NuGet. Para ser flexível, o script executa estas etapas:

1. Determina o caminho no qual ele foi chamado.
2. Percorre o caminho até encontrar uma pasta chamada `packages`. Esta pasta é criada quando você instala pacotes do NuGet.
3. Pesquisas de repetidamente a `packages` pasta para um assembly denominado **Microsoft.ServiceBus.dll**.
4. Referências assembly para que os tipos estão disponíveis para uso posterior.

Aqui está como essas etapas são implementadas em um script do PowerShell:

```
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error "Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script."
}
```

## <a name="provision-a-service-bus-namespace"></a>Provisionar um namespace barramento de serviço

Dois cmdlets do PowerShell suporte para operações de namespace barramento de serviço. Em vez de APIs o SDK .NET, você pode usar [Get-AzureSBNamespace][] e [AzureSBNamespace de novo][].

Este exemplo cria alguns variáveis locais no script; `$Namespace` and `$Location`.

- `$Namespace`é o nome do namespace barramento de serviço com a qual queremos trabalhar.
- `$Location`identifica o Centro de dados no qual o script provisiona namespace.
- `$CurrentNamespace`armazene o namespace de referência que o script recupera (ou cria).

Em um script real, `$Namespace` e `$Location` podem ser passados como parâmetros.

Essa parte do script executa as seguintes tarefas:

1. Tenta recuperar um namespace barramento de serviço com o nome fornecido.
2. Se o namespace for encontrado, ele informa o que foi encontrado.
3. Se o espaço para nome não for encontrado, ele cria o namespace e recupera namespace recém-criado.

    ```
    $Namespace = "MyServiceBusNS"
    $Location = "West US"
    
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
    
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
    }
    else
    {
        Write-Host "The [$Namespace] namespace does not exist."
        Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```

Para provisionar outras entidades barramento de serviço, crie uma instância da classe [Gerenciador de namespace][] do SDK.
Você pode usar o cmdlet [Get-AzureSBAuthorizationRule][] para recuperar uma regra de autorização que é usada para fornecer uma cadeia de conexão. Podemos vai armazenar uma referência para o `NamespaceManager` instância na `$NamespaceManager` variável. Usaremos `$NamespaceManager` posterior no script para provisionar outras entidades.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-other-service-bus-entities"></a>Outras entidades barramento de serviço de provisionamento

Para provisionar outras entidades, como filas, tópicos e Hubs de evento, use a [API do .NET para barramento de serviço][]. Este artigo aborda apenas Hubs de evento, mas as etapas para outras entidades são semelhantes. Além disso, os exemplos mais detalhados, incluindo outras entidades, referenciados no final deste artigo.

Essa parte do script cria quatro variáveis locais mais. Essas variáveis são usadas para criar uma instância de um `EventHubDescription` objeto. O script executa as seguintes tarefas:

1. Usando o `NamespaceManager` objeto, verifique se o Hub de evento identificado por `$Path` existe.
2. Se não existir, crie um `EventHubDescription` e passar para a `NamespaceManager` classe `CreateEventHubIfNotExists` método.
3. Após determinar que o Hub de evento está disponível, criar um grupo de consumidor usando `ConsumerGroupDescription` e `NamespaceManager`.

    ```
    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"
        
    # Check to see if the Event Hub already exists
    if ($NamespaceManager.EventHubExists($Path))
    {
        Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
        $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
        $EventHubDescription.PartitionCount = $PartitionCount
        $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
        $EventHubDescription.UserMetadata = $UserMetadata
        $EventHubDescription.Path = $Path
        $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
        Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
    }
        
    # Create the consumer group if it doesn't exist
    Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
    $ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
    $ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
    $NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
    Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
    ```

## <a name="migrate-a-namespace-to-another-azure-subscription"></a>Migrar um namespace para outra assinatura do Azure

A seguinte sequência de comandos move um namespace de uma assinatura do Azure para outra. Para executar essa operação, namespace já deve estar ativo e o usuário executando os comandos do PowerShell deve ser um administrador no assinaturas de origem e de destino.

```
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Próximas etapas

Este artigo fornecida uma estrutura de tópicos básica para provisionamento entidades barramento de serviço usando o PowerShell. Algo que você pode fazer ao usar bibliotecas de cliente do .NET, você também pode fazer em um script do PowerShell.

Exemplos mais detalhados estão disponíveis nessas postagens de blog:

- [Como criar filas barramento de serviço, tópicos e assinaturas usando um script do PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Como criar um Namespace de barramento de serviço e um Hub de evento usando um script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Alguns scripts prontas também estão disponíveis para download:
- [Scripts do PowerShell barramento de serviço](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[Opções de compra]: http://azure.microsoft.com/pricing/purchase-options/
[Ofertas de membro]: http://azure.microsoft.com/pricing/member-offers/
[Avaliação gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Instalar e configurar o PowerShell do Azure]: ../powershell-install-configure.md
[Pacote NuGet de barramento de serviço]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[Novo AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API do .NET para barramento de serviço]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.aspx
[Gerenciador de namespace]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
