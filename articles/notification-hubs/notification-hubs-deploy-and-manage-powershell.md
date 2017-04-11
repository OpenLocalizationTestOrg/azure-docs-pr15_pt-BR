<properties 
    pageTitle="Implantar e gerenciar Hubs de notificação usando o PowerShell" 
    description="Como criar e gerenciar Hubs de notificação usando o PowerShell para automação" 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Implantar e gerenciar Hubs de notificação usando o PowerShell

##<a name="overview"></a>Visão geral

Este artigo mostra como usar a criar e gerenciar um Azure notificação Hubs usando o PowerShell. As seguintes tarefas comuns de automação são mostradas neste tópico.

+ Criar um Hub de notificação
+ Definir credenciais

Se você também precisa criar um novo namespace de barramento de serviço para seu hubs de notificação, consulte [Gerenciar barramento de serviço com o PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Gerenciar Hubs de notificações não é suportado diretamente pelos cmdlets incluídos com o Azure PowerShell. A melhor abordagem do PowerShell é fazer referência ao conjunto de Microsoft.Azure.NotificationHubs.dll. O conjunto é distribuído com o [pacote do Microsoft Azure notificação Hubs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- Uma assinatura do Azure. Azure é uma plataforma baseada em assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra], [Oferece membro]ou [Avaliação gratuita].

- Um computador com o PowerShell do Azure. Para obter instruções, consulte [instalar e configurar o Azure PowerShell].

- Uma compreensão geral de scripts do PowerShell, pacotes do NuGet e o .NET Framework.


## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Incluindo uma referência ao assembly .NET para barramento de serviço

Gerenciar Hubs de notificação do Azure ainda não está incluído no cmdlets do PowerShell do PowerShell do Azure. Para provisionar hubs de notificação, você pode usar o cliente .NET fornecido no [pacote do Microsoft Azure notificação Hubs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Primeiro, verifique se que seu script pode localizar o conjunto de **Microsoft.Azure.NotificationHubs.dll** , que é instalado como um pacote do NuGet em um projeto do Visual Studio. Para ser flexível, o script executa estas etapas:

1. Determina o caminho no qual ele foi chamado.
2. Percorre o caminho até encontrar uma pasta chamada `packages`. Esta pasta é criada quando você instala pacotes NuGet para projetos do Visual Studio.
3. Pesquisas de repetidamente a `packages` pasta para um assembly denominado **Microsoft.Azure.NotificationHubs.dll**.
4. Referências assembly para que os tipos estão disponíveis para uso posterior.

Aqui está como essas etapas são implementadas em um script do PowerShell:

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Criar a classe de Gerenciador de namespace

Para provisionar Hubs de notificação, crie uma instância da classe [Gerenciador de namespace](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) do SDK. 

Você pode usar o cmdlet [Get-AzureSBAuthorizationRule] incluído com o Azure PowerShell para recuperar uma regra de autorização que é usada para fornecer uma cadeia de conexão. Podemos vai armazenar uma referência para o `NamespaceManager` instância na `$NamespaceManager` variável. Usaremos `$NamespaceManager` provisionar um hub de notificação.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## <a name="provisioning-a-new-notification-hub"></a>Um novo Hub de notificação de provisionamento 

Para provisionar um novo hub de notificação, use a [API do .NET para Hubs de notificação].

Nesta parte do script você configurar quatro variáveis locais. 

1. `$Namespace`: Defina isso para o nome do namespace onde você deseja criar um hub de notificação.
2. `$Path`: Defina esse caminho para o nome do novo hub notificação.  Por exemplo, "MyHub".    
3. `$WnsPackageSid`: Defina o pacote SID para você aplicativo Windows no [Centro de desenvolvimento do Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).
4. `$WnsSecretkey`: Defina a chave secreta para você aplicativo Windows no [Centro de desenvolvimento do Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).

Essas variáveis são usadas para se conectar ao seu espaço para nome e criar um novo Hub de notificação configurado para tratar notificações de serviços de notificação do Windows (WNS) com credenciais WNS para um aplicativo do Windows. Para obter informações sobre como obter o pacote SID e chave secreta ver, o tutorial de [Introdução com Hubs de notificação](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) . 

+ O trecho de script usa a `NamespaceManager` objeto para verificar se o Hub de notificação identificado por `$Path` existe.

+ Se não existir, o script criará uma `NotificationHubDescription` com WNS credenciais e passar para a `NamespaceManager` classe `CreateNotificationHub` método.

``` powershell

$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## <a name="additional-resources"></a>Recursos adicionais

- [Gerenciar barramento de serviço com o PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Como criar filas barramento de serviço, tópicos e assinaturas usando um script do PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Como criar um Namespace de barramento de serviço e um Hub de evento usando um script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Alguns scripts prontas também estão disponíveis para download:
- [Scripts do PowerShell barramento de serviço](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)
 

[Opções de compra]: http://azure.microsoft.com/pricing/purchase-options/
[Ofertas de membro]: http://azure.microsoft.com/pricing/member-offers/
[Avaliação gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Instalar e configurar o PowerShell do Azure]: ../powershell-install-configure.md
[API do .NET para Hubs de notificação]: https://msdn.microsoft.com/library/azure/mt414893.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
 
