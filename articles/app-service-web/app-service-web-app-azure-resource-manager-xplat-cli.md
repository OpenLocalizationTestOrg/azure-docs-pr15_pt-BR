<properties
    pageTitle="Ferramentas de linha de comando de várias plataformas de baseada em Gerenciador de recursos Azure Azure Web App | Microsoft Azure"
    description="Saiba como usar as novas ferramentas de linha de comando de plataforma híbrida baseado em Gerenciador de recursos do Azure para gerenciar seus aplicativos Web do Azure."
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

# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-web-app"></a>Usando o recurso Azure baseadas Manager XPlat CLI do Azure Web App#

> [AZURE.SELECTOR]
- [CLI Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [PowerShell Azure](app-service-web-app-azure-resource-manager-powershell.md)

Com o lançamento da versão de ferramentas do Microsoft Azure entre plataformas de linha de comando 0.10.5, foram adicionados novos comandos. Esses comandos dar ao usuário a capacidade de usar comandos do PowerShell baseado em Gerenciador de recursos do Azure para gerenciar aplicativos Web.

Para saber sobre o gerenciamento de grupos de recursos, consulte [usar a CLI do Azure para gerenciar recursos do Azure e grupos de recursos](../xplat-cli-azure-resource-manager.md). 


## <a name="managing-app-service-plans"></a>Gerenciando o serviço de aplicativo planos ##

### <a name="create-an-app-service-plan"></a>Criar um plano de serviço de aplicativo ###
Para criar um plano de serviço do aplicativo, use o comando **criar appserviceplan azure** .

A seguir estão descrições dos parâmetros diferentes:

-   **-grupo de recursos**: grupo de recursos que inclui o plano de serviço de aplicativo recém-criado.
-   **-nome**: nome do plano de serviço de aplicativo.
-   **-local**: local do plano de serviço de aplicativo.
-   **– camada**: o sku de preços desejado (as opções são: F1 (grátis). D1 (compartilhado). B1 (pequeno básico), B2 (médio básico) e B3 (Basic grande). S1 (pequeno padrão), S2 (médio padrão) e S3 (padrão grande). P1 (Premium pequeno), P2 (Premium médio) e P3 (Premium grande).)
-   **– instâncias**: o número de trabalhadores no aplicativo de serviço plano (o valor padrão é 1).

Exemplo para usar esse cmdlet:

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

### <a name="list-existing-app-service-plans"></a>Serviço de aplicativo existente da lista de planos ###

Para listar os planos de serviço de aplicativo existente, use o comando de **lista de appserviceplan azure** .

Para listar todos os planos de serviço de aplicativo em um grupo de recursos específico, use:

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

Para obter um plano de serviço de aplicativo específico, use o comando **Mostrar appserviceplan azure** :

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>Configurar um plano de serviço de aplicativo existente ###

Para alterar as configurações para um plano de serviço de aplicativo existente, use o comando **appserviceplan azure config** . Você pode alterar a sku e o número de trabalhadores 

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>Dimensionamento de um plano de serviço de aplicativo ####

Para dimensionar um plano de serviço de aplicativo existente, use:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>Alterando o SKU de um plano de serviço de aplicativo ####

Para alterar o sku de um plano de serviço de aplicativo existente, use:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>Excluir um plano de serviço de aplicativo existente ###

Para excluir um plano de serviço de aplicativo existente, todos os aplicativos web atribuído precisam ser movido ou excluído primeiro. Usando o comando **excluir Web App azure** , que você pode excluir o plano de serviço do aplicativo.

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-web-apps"></a>Gerenciando aplicativos do serviço de aplicativo Web ##

### <a name="create-a-web-app"></a>Criar um aplicativo Web ###

Para criar um aplicativo web, use o comando **criar Web App azure** .

A seguir estão descrições dos parâmetros diferentes:

- **-nome**: nome para o aplicativo web.
- **-plano**: nome para o plano de serviço usado para hospedar o aplicativo web.
- **-grupo de recursos**: grupo de recursos que hospeda o plano de serviço do aplicativo.
- **-local**: o local de aplicativo da web.

Exemplo para usar esse cmdlet:

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-web-app"></a>Excluir um aplicativo Web existente ###

Para excluir um aplicativo web existente que você pode usar o comando **excluir Web App azure** , você precisará especificar o nome do aplicativo web e o nome do grupo de recursos.

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-web-apps"></a>Lista existente Web Apps ###

Para listar os aplicativos da web existente, use o comando de **lista de Web App azure** .

Para listar todos os aplicativos da web em um grupo de recursos específico, use:

    azure webapp list --resource-group ContosoAzureResourceGroup

Para obter um aplicativo web específico, use o comando **Mostrar Web App azure** .

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-web-app"></a>Configurar um aplicativo Web existente ###

Para alterar as configurações e configurações para um aplicativo web existente, use o comando **set config Web App azure** .

Exemplo (1): alterar a versão de php de um aplicativo web 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

Exemplo (2): Adicionar ou alterar configurações do aplicativo

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

Para saber o que outra configuração pode ser alterado, use o comando **config Web App azure set -h** .

### <a name="change-the-state-of-an-existing-web-app"></a>Alterar o estado de um aplicativo Web existente ###

#### <a name="restart-a-web-app"></a>Reiniciar um aplicativo web ####

Para reiniciar um aplicativo web, você deverá especificar o grupo de recursos e o nome do aplicativo web.

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Parar de um aplicativo web ####

Para parar de um aplicativo web, você deverá especificar o grupo de recursos e o nome do aplicativo web.

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Iniciar um aplicativo web ####

Para iniciar um aplicativo web, você deverá especificar o grupo de recursos e o nome do aplicativo web.

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Gerenciar perfis de publicação de aplicativo Web ###

Cada aplicativo web tem um perfil de publicação que pode ser usado para publicar seus aplicativos.

#### <a name="get-publishing-profile"></a>Obter o perfil de publicação ####

Para obter o perfil de publicação de um aplicativo web, use:

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

Este comando exibe o nome de perfil de publicação e senha à linha de comando.

### <a name="manage-web-app-hostnames"></a>Gerenciar nomes de host do Web App ###

Para gerenciar hostname ligações para o aplicativo web, use o comando de **nomes de host do Web App azure config**  

#### <a name="list-hostname-bindings"></a>Lista hostname ligações ####

Para obter as ligações hostname atual para um aplicativo web, use:

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>Adicionar ligações hostname ####

Para adicionar ligações hostname a um aplicativo web, use:

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>Excluir hostname ligações ####

Para excluir hostname ligações, use:

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

### <a name="next-steps"></a>Próximas etapas ###
- Para saber sobre o suporte à CLI do Azure Gerenciador de recursos, consulte [usar a CLI do Azure para gerenciar grupos de recursos e recursos Azure.](../xplat-cli-azure-resource-manager.md)
- Para saber sobre como gerenciar o serviço de aplicativo usando o PowerShell, consulte [Using Azure Resource Manager-Based PowerShell para gerenciar aplicativos de Web do Azure.](app-service-web-app-azure-resource-manager-powershell.md)
