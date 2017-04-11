<properties
   pageTitle="Obtenha os valores necessários para autenticar um aplicativo para acessar o banco de dados SQL do código | Microsoft Azure"
   description="Crie uma entidade de segurança do serviço para acessar o banco de dados SQL do código."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/30/2016"
   ms.author="sstein"/>

# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Obter os valores necessários para um aplicativo para acessar o banco de dados SQL do código de autenticação

Para criar e gerenciar o banco de dados SQL do código, você deve registrar seu aplicativo no domínio do Azure Active Directory (AAD) na assinatura onde seus recursos Azure foram criados.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Criar um serviço principal para acessar os recursos de um aplicativo

Você precisa ter mais recente [Do PowerShell do Azure](https://msdn.microsoft.com/library/mt619274.aspx) instalado e em execução. Para obter informações detalhadas, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

O seguinte script do PowerShell cria o aplicativo do Active Directory (AD) e o capital de serviço que precisamos para autenticar nosso aplicativo c#. O script saídas de valores que precisamos para a amostra c# anterior. Para obter informações detalhadas, consulte [Usar o PowerShell do Azure para criar um serviço principal para acessar recursos](../resource-group-authenticate-service-principal.md).

   
    # Sign in to Azure.
    Add-AzureRmAccount
    
    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.
    
    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"
    
    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret
    
    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    
    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15
    
    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    
    
    # Output the values we need for our C# application to successfully authenticate
    
    Write-Output "Copy these values into the C# sample app"
    
    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Consulte também

- [Criar um banco de dados do SQL com c#](sql-database-get-started-csharp.md)
- [Conectando ao banco de dados do SQL usando a autenticação do Active Directory do Azure](sql-database-aad-authentication.md)


