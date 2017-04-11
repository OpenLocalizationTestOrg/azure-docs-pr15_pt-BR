<properties
    pageTitle="Comandos do PowerShell Azure baseado em Gerenciador de recursos do Azure Web App | Microsoft Azure"
    description="Saiba como usar os novos comandos do PowerShell baseado em Gerenciador de recursos do Azure para gerenciar seus aplicativos Web do Azure."
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
    ms.date="09/29/2016"
    ms.author="aelnably"/>

# <a name="using-azure-resource-manager-based-powershell-to-manage-azure-web-apps"></a>Usando o recurso Azure baseadas Manager PowerShell para gerenciar aplicativos Web do Azure#

> [AZURE.SELECTOR]
- [CLI Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [PowerShell Azure](app-service-web-app-azure-resource-manager-powershell.md)

Com o Microsoft Azure PowerShell versão 1.0.0 foram adicionados novos comandos, que permite que o usuário usar comandos do PowerShell baseado em Gerenciador de recursos do Azure para gerenciar aplicativos Web.

Para saber sobre o gerenciamento de grupos de recursos, consulte [Usando o PowerShell Azure com o Gerenciador de recursos do Azure](../powershell-azure-resource-manager.md). 

Para saber mais sobre a lista completa de parâmetros e opções para os cmdlets do PowerShell, consulte a [Referência de Cmdlet completa baseada em Gerenciador de recursos do Web App Azure de Cmdlets do PowerShell](https://msdn.microsoft.com/library/mt619237.aspx)

## <a name="managing-app-service-plans"></a>Gerenciando o serviço de aplicativo planos ##

### <a name="create-an-app-service-plan"></a>Criar um plano de serviço de aplicativo ###
Para criar um plano de serviço do aplicativo, use o cmdlet **New-AzureRmAppServicePlan** .

A seguir estão descrições dos parâmetros diferentes:

-   **Nome**: nome do plano de serviço de aplicativo.
-   **Local**: local do plano de serviço.
-   **ResourceGroupName**: grupo de recursos que inclui o plano de serviço de aplicativo recém-criado.
-   **Camadas**: a camada de preços desejada (o padrão é livre, outras opções são compartilhadas, Basic, Standard e Premium)
-   **WorkerSize**: o tamanho de trabalhadores (o padrão é pequeno se o parâmetro de nível foi especificado como básico, padrão ou Premium. Outras opções são médios e grandes.)
-   **NumberofWorkers**: o número de trabalhadores no aplicativo de serviço plano (o valor padrão é 1). 

Exemplo para usar esse cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### <a name="create-an-app-service-plan-in-an-app-service-environment"></a>Criar um plano de serviço de aplicativos em um ambiente de serviço de aplicativo ###
Para criar um plano de serviço do aplicativo em um ambiente de serviço de aplicativo, use o mesmo comando **New-AzureRmAppServicePlan** comando com parâmetros adicionais para especificar o nome do ASE e nome do grupo de recursos do ASE.

Exemplo para usar esse cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

Para saber mais sobre o ambiente de serviço de aplicativo, marque [Introdução ao ambiente de serviço de aplicativo](app-service-app-service-environment-intro.md)

### <a name="list-existing-app-service-plans"></a>Serviço de aplicativo existente da lista de planos ###

Para listar os planos de serviço de aplicativo existente, use o cmdlet **Get-AzureRmAppServicePlan** .

Para listar todos os planos de serviço de aplicativo em sua assinatura, use: 

    Get-AzureRmAppServicePlan

Para listar todos os planos de serviço de aplicativo em um grupo de recursos específico, use:

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

Para obter um plano de serviço de aplicativo específico, use:

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### <a name="configure-an-existing-app-service-plan"></a>Configurar um plano de serviço de aplicativo existente ###

Para alterar as configurações para um plano de serviço de aplicativo existente, use o cmdlet **Set-AzureRmAppServicePlan** . Você pode alterar a camada, tamanho de trabalho e o número de trabalhadores 

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### <a name="scaling-an-app-service-plan"></a>Dimensionamento de um plano de serviço de aplicativo ####

Para dimensionar um plano de serviço de aplicativo existente, use:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### <a name="changing-the-worker-size-of-an-app-service-plan"></a>Alterar o tamanho de trabalho de um plano de serviço de aplicativo ####

Para alterar o tamanho de trabalhadores em um plano de serviço de aplicativo existente, use:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### <a name="changing-the-tier-of-an-app-service-plan"></a>Alterar o nível de um plano de serviço de aplicativo ####

Para alterar o nível de um plano de serviço de aplicativo existente, use:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### <a name="delete-an-existing-app-service-plan"></a>Excluir um plano de serviço de aplicativo existente ###

Para excluir um plano de serviço de aplicativo existente, todos os aplicativos web atribuído precisam ser movido ou excluído primeiro. Usando o cmdlet **Remove-AzureRmAppServicePlan** você pode excluir o plano de serviço do aplicativo.

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## <a name="managing-app-service-web-apps"></a>Gerenciando aplicativos do serviço de aplicativo Web ##

### <a name="create-a-web-app"></a>Criar um aplicativo Web ###

Para criar um aplicativo web, use o cmdlet **New-AzureRmWebApp** .

A seguir estão descrições dos parâmetros diferentes:

- **Nome**: nome para o aplicativo web.
- **AppServicePlan**: nome para o plano de serviço usado para hospedar o aplicativo web.
- **ResourceGroupName**: grupo de recursos que hospeda o plano de serviço do aplicativo.
- **Local**: o local de aplicativo da web.

Exemplo para usar esse cmdlet:

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### <a name="create-a-web-app-in-an-app-service-environment"></a>Criar um aplicativo Web em um ambiente de serviço de aplicativo ###

Para criar um aplicativo web em um ambiente de serviço de aplicativo (ASE). Use o mesmo comando **New-AzureRmWebApp** com parâmetros extras para especificar o nome do ASE e o nome do grupo de recursos que o ASE pertence.

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

Para saber mais sobre o ambiente de serviço de aplicativo, marque [Introdução ao ambiente de serviço de aplicativo](app-service-app-service-environment-intro.md)

### <a name="delete-an-existing-web-app"></a>Excluir um aplicativo Web existente ###

Para excluir um aplicativo web existente que você pode usar o cmdlet **Remover AzureRmWebApp** , você precisará especificar o nome do aplicativo web e o nome do grupo de recursos.

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### <a name="list-existing-web-apps"></a>Lista existente Web Apps ###

Para listar os aplicativos da web existente, use o cmdlet **Get-AzureRmWebApp** .

Para listar todos os aplicativos da web em sua assinatura, use:

    Get-AzureRmWebApp

Para listar todos os aplicativos da web em um grupo de recursos específico, use:

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

Para obter um aplicativo web específico, use:

    Get-AzureRmWebApp -Name ContosoWebApp

### <a name="configure-an-existing-web-app"></a>Configurar um aplicativo Web existente ###

Para alterar as configurações e configurações para um aplicativo web existente, use o cmdlet **Set-AzureRmWebApp** . Para obter uma lista completa de parâmetros, verifique o [vínculo de referência de Cmdlet](https://msdn.microsoft.com/library/mt652487.aspx)

Exemplo (1): use esse cmdlet para alterar cadeias de caracteres de conexão

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

Exemplo (2): Adicionar ou alterar configurações do aplicativo

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


Exemplo (3): defina o web app para executar no modo de 64 bits

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### <a name="change-the-state-of-an-existing-web-app"></a>Alterar o estado de um aplicativo Web existente ###

#### <a name="restart-a-web-app"></a>Reiniciar um aplicativo web ####

Para reiniciar um aplicativo web, você deverá especificar o grupo de recursos e o nome do aplicativo web.

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Parar de um aplicativo web ####

Para parar de um aplicativo web, você deverá especificar o grupo de recursos e o nome do aplicativo web.

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Iniciar um aplicativo web ####

Para iniciar um aplicativo web, você deverá especificar o grupo de recursos e o nome do aplicativo web.

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Gerenciar perfis de publicação de aplicativo Web ###

Cada aplicativo web tem um perfil de publicação que pode ser usado para publicar seus aplicativos, diversas operações podem ser executadas em perfis de publicação.

#### <a name="get-publishing-profile"></a>Obter o perfil de publicação ####

Para obter o perfil de publicação de um aplicativo web, use:

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

Este comando ecos o perfil de publicação para a linha de comando como saída bem o perfil de publicação para um arquivo de texto.

#### <a name="reset-publishing-profile"></a>Redefinir o perfil de publicação ####

Para redefinir tanto a senha de publicação da web e FTP implantar para um aplicativo web, use:

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-certificates"></a>Gerenciar os certificados de aplicativo Web ###

Para saber sobre como gerenciar os certificados de aplicativo web, consulte [usando o PowerShell de vinculação de certificados SSL](app-service-web-app-powershell-ssl-binding.md)


### <a name="next-steps"></a>Próximas etapas ###
- Para saber sobre o suporte do Azure Gerenciador de recursos do PowerShell, consulte [usando o PowerShell Azure com o Gerenciador de recursos do Azure.](../powershell-azure-resource-manager.md)
- Para saber mais sobre ambientes de serviço de aplicativo, consulte [Introdução ao aplicativo de serviço ambiente.](app-service-app-service-environment-intro.md)
- Para saber sobre o gerenciamento de certificados SSL de serviço de aplicativo usando o PowerShell, consulte [vinculação de certificados SSL usando o PowerShell.](app-service-web-app-powershell-ssl-binding.md)
- Para saber mais sobre a lista completa de cmdlets do PowerShell baseado em Gerenciador de recursos do Azure Azure Web Apps, consulte [referência de cmdlets do Azure Web Apps Azure Gerenciador de recursos de Cmdlets do PowerShell.](https://msdn.microsoft.com/library/mt619237.aspx)
- - Para saber sobre como gerenciar o serviço de aplicativo usando CLI, consulte [Using Azure Resource Manager-Based XPlat CLI do Azure Web App.](app-service-web-app-azure-resource-manager-xplat-cli.md)
