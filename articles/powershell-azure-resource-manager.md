<properties 
    pageTitle="Com o Gerenciador de recursos do PowerShell Azure | Microsoft Azure" 
    description="Introdução ao uso do PowerShell do Azure para implantar vários recursos como um grupo de recursos para o Azure." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/18/2016" 
    ms.author="tomfitz"/>

# <a name="using-azure-powershell-with-azure-resource-manager"></a>Usando o PowerShell Azure com o Azure Resource Manager

> [AZURE.SELECTOR]
- [Portal](azure-portal/resource-group-portal.md) 
- [CLI Azure](xplat-cli-azure-resource-manager.md)
- [PowerShell Azure](powershell-azure-resource-manager.md)
- [API REST](resource-manager-rest-api.md)


Gerenciador de recursos de Azure implementa uma abordagem moderna para o controle de ciclo de vida do Azure recursos. Em vez de criar e gerenciar recursos individuais, você comece imaginando uma solução inteira, como um blog, uma galeria de fotos, um portal do SharePoint ou um wiki. Você usa um modelo – uma representação declarativa da solução – para definir um grupo de recursos que contém todos os recursos que necessários para oferecer suporte à solução. Em seguida, você implantar e gerenciar desse grupo de recursos como uma unidade lógica. 

Neste tutorial, você saiba como usar o PowerShell do Azure com o Gerenciador de recursos do Azure. Ele orienta você pelo processo de implantar uma solução e trabalhando com essa solução. Você usará o PowerShell do Azure e um modelo do Gerenciador de recursos para implantar:

- SQL server - para hospedar o banco de dados
- Banco de dados SQL - para armazenar os dados
- Regras de firewall - para permitir que o aplicativo da web conectar-se ao banco de dados
- Plano de serviço de aplicativo - para definir os recursos e o custo do aplicativo web
- Site - para executando o aplicativo web
- Web config - para armazenar a cadeia de conexão ao banco de dados 
- Regras de alerta - para monitorar o desempenho e erros
- Obtenção de informações de aplicativo - configurações de dimensionar automaticamente

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa:

- Uma conta do Azure
  + Você pode [Abrir uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F): obtenha créditos que você pode usar para experimentar serviços Azure pagos e até mesmo depois que eles são usados até você pode manter a conta e uso livre Azure serviços, como sites. Seu cartão de crédito nunca será cobrado, a menos que você explicitamente alterar suas configurações e peça para ser cobrados.
  
  + Você pode [Ativar benefícios do assinante MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): assinatura MSDN Your lhe créditos todo mês que você pode usar para serviços do Azure pagos.
- Azure PowerShell 1.0. Para obter informações sobre esta versão e como instalá-lo, veja [como instalar e configurar o Azure PowerShell](powershell-install-configure.md).

Este tutorial é projetado para iniciantes do PowerShell, mas ele pressupõe que você entender os conceitos básicos, como módulos, cmdlets e sessões.

## <a name="get-help-for-cmdlets"></a>Obter ajuda para os cmdlets

Para obter ajuda detalhada para qualquer cmdlet que você vê neste tutorial, use o cmdlet Get-Help. 

    Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda para o cmdlet Get-AzureRmResource, digite:

    Get-Help Get-AzureRmResource -Detailed

Para obter uma lista de cmdlets no módulo recursos com um resumo de Ajuda, digite: 

    Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

A saída terá aparência semelhante à seguinte trecho:

    Name                                   Synopsis
    ----                                   --------
    Find-AzureRmResource                   Searches for resources using the specified parameters.
    Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
    Get-AzureRmADGroup                     Filters active directory groups.
    Get-AzureRmADGroupMember               Get a group members.
    ...

Para obter ajuda completa de um cmdlet, digite um comando com o formato:

    Get-Help <cmdlet-name> -Full
  
## <a name="login-to-your-azure-account"></a>Efetuar login em sua conta do Azure

Antes de trabalhar em sua solução, você deve efetuar login em sua conta.

Para fazer logon em sua conta do Azure, use o cmdlet **Add-AzureRmAccount** .

    Add-AzureRmAccount

O cmdlet solicita as credenciais de logon para sua conta do Azure. Após fazer o login, ele baixa suas configurações de conta para que estejam disponíveis para o PowerShell do Azure. 

As configurações de conta expirarem, então você precisa atualizá-los ocasionalmente. Para atualizar as configurações de conta, execute **AzureRmAccount de adicionar** novamente. 

>[AZURE.NOTE] Os módulos do Gerenciador de recursos requer Add-AzureRmAccount. Um arquivo de configurações de publicação não é suficiente.     

Se você tiver mais de uma assinatura, forneça a identificação de assinatura que você deseja usar para implantação com o cmdlet **Set-AzureRmContext** .

    Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de implantar os recursos à sua assinatura, você deve criar um grupo de recursos que conterão os recursos. 

Para criar um grupo de recursos, use o cmdlet **New-AzureRmResourceGroup** .

O comando usa o parâmetro de **nome** para especificar um nome para o grupo de recursos e o parâmetro de **localização** especificar seu local. Com base no que descobrimos na seção anterior, usaremos "Oeste EUA" para o local.

    New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
O resultado será semelhante a:

    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

O grupo de recursos foi criado com êxito.

## <a name="deploy-your-solution"></a>Implantar sua solução

Este tópico não mostram como criar seu modelo ou discutir a estrutura do modelo. Para essas informações, consulte [modelos de coautoria Gerenciador de recursos do Azure](resource-group-authoring-templates.md) e [Instruções passo a passo de modelo do Gerenciador de recursos](resource-manager-template-walkthrough.md). Você implantará o modelo de [provisionar um aplicativo Web com um banco de dados do SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) predefinido de [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/).

Você tem seu grupo de recursos e você tem seu modelo, portanto, agora você está pronto para implantar a infraestrutura definida no seu modelo ao grupo de recursos. Você implantar recursos com o cmdlet **New-AzureRmResourceGroupDeployment** . O modelo especifica muitos valores de padrão, que usaremos para que você não precisa fornecer valores para esses parâmetros. A sintaxe básica aparência:

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -administratorLogin exampleadmin -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

Especifique o grupo de recursos e o local do modelo. Se seu modelo é um arquivo local, use o parâmetro **- TemplateFile** e especifique o caminho para o modelo. Você pode definir o **-modo** parâmetro para **Incremental** ou **completo**. Por padrão, o Gerenciador de recursos realiza uma atualização incremental durante a implantação; Portanto, não é essencial para definir **-modo** quando quiser **Incremental**. Para compreender as diferenças entre esses modos de implantação, consulte [implantar um aplicativo com o Gerenciador de recursos do Azure modelo](resource-group-template-deploy.md). 

###<a name="dynamic-template-parameters"></a>Parâmetros de modelo dinâmico

Se você estiver familiarizado com o PowerShell, sabe que você pode percorrer os parâmetros disponíveis para um cmdlet digitando um sinal de subtração (-) e, em seguida, pressionar a tecla TAB. Essa mesma funcionalidade também funciona com parâmetros que você define no seu modelo. Assim que você digitar o nome do modelo, o cmdlet busca o modelo, analisa e adiciona os parâmetros de modelo ao comando dinamicamente. Isso facilita muito especificar os valores de parâmetro de modelo.

Quando você insere o comando, você será solicitado para o parâmetro obrigatório ausente, **administratorLoginPassword**. E, quando você digita a senha, o valor de cadeia de caracteres seguro é ocultado. Essa estratégia elimina o risco de fornecer uma senha em texto sem formatação.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Se o modelo inclui um parâmetro com um nome que corresponda a um dos parâmetros no comando para implantar o modelo (como incluindo um parâmetro chamado **ResourceGroupName** no modelo que é o mesmo que o parâmetro **ResourceGroupName** no cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) ), você será solicitado a fornecer um valor para um parâmetro com o sufixo **FromTemplate** (como **ResourceGroupNameFromTemplate**). Em geral, você deve evitar essa confusão nomeando não parâmetros com o mesmo nome usado para operações de implantação de parâmetros.

O comando é executado e retorna mensagens como os recursos são criados. Por fim, você pode ver o resultado da sua implantação.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 4/11/2016 7:26:11 PM
    Mode              : Incremental
    TemplateLink      :
                Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json
                ContentVersion : 1.0.0.0
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                skuName          String                     F1
                skuCapacity      Int                        1
                administratorLogin  String                  exampleadmin
                administratorLoginPassword  SecureString
                databaseName     String                     sampledb
                collation        String                     SQL_Latin1_General_CP1_CI_AS
                edition          String                     Basic
                maxSizeBytes     String                     1073741824
                requestedServiceObjectiveName  String       Basic

    Outputs           :
                Name             Type                       Value
                ===============  =========================  ==========
                siteUri          String                     websites5wdai7p2k2g4.azurewebsites.net
                sqlSvrFqdn       String                     sqlservers5wdai7p2k2g4.database.windows.net
                    
    DeploymentDebugLogLevel :

Em apenas algumas etapas, criado e implantado os recursos necessários para um site complexo. 

### <a name="log-debug-information"></a>Informações de depuração de log

Quando implantar um modelo, você pode registrar informações adicionais sobre a solicitação e resposta, especificando o parâmetro **- DeploymentDebugLogLevel** durante a execução **AzureRmResourceGroupDeployment de novo**. Essas informações podem ajudá-lo a solucionar problemas de erros de implantação. O valor padrão é **Nenhum** que significa que nenhuma solicitação ou conteúdo de resposta está conectado. Você pode especificar o registro em log o conteúdo da solicitação, a resposta ou ambos.  Para obter mais informações sobre implantações de solução de problemas e informações de depuração de log, consulte [implantações de grupo de recursos de solução de problemas com Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md). O exemplo a seguir registra o conteúdo de solicitação e resposta para a implantação.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -DeploymentDebugLogLevel All -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

> [AZURE.NOTE] Ao definir o parâmetro DeploymentDebugLogLevel, considere cuidadosamente o tipo de informação que você está passando em durante a implantação. Informações de log sobre a solicitação ou resposta, você pode potencialmente expor dados confidenciais que são recuperados por meio de operações de implantação. 


## <a name="get-information-about-your-resource-groups"></a>Obter informações sobre os grupos de recursos

Após criar um grupo de recursos, você pode usar os cmdlets no módulo Gerenciador de recursos para gerenciar os grupos de recursos.

- Para acessar um grupo de recursos em sua assinatura, use o cmdlet **Get-AzureRmResourceGroup** :

        Get-AzureRmResourceGroup -ResourceGroupName TestRG1
    
    Que retorna as seguintes informações:

        ResourceGroupName : TestRG1
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
        
        ...

    Se você não especificar um nome de grupo de recursos, o cmdlet retorna todos os grupos de recursos em sua assinatura.

- Para acessar os recursos no grupo de recursos, use o cmdlet **AzureRmResource de localizar** e seu parâmetro **ResourceGroupNameContains** . Sem parâmetros, o AzureRmResource localizar obtém todos os recursos na sua assinatura do Azure.

        Find-AzureRmResource -ResourceGroupNameContains TestRG1
    
     Que retorna uma lista de recursos formatada como:
        
        Name              : sqlservers5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/sqlservers5wdai7p2k2g4
        ResourceName      : sqlservers5wdai7p2k2g4
        ResourceType      : Microsoft.Sql/servers
        Kind              : v2.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
        Tags              : {System.Collections.Hashtable}
        ...
            
- Você pode usar marcas para logicamente organizar os recursos em sua assinatura e recuperar os recursos com os cmdlets de **Localizar AzureRmResource** e **Localizar AzureRmResourceGroup** .

        Find-AzureRmResource -TagName displayName -TagValue Website

        Name              : webSites5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/webSites5wdai7p2k2g4
        ResourceName      : webSites5wdai7p2k2g4
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      There is much more you can do with tags. For more information, see [Using tags to organize your Azure resources](resource-group-using-tags.md).

## <a name="add-to-a-resource-group"></a>Adicionar a um grupo de recursos

Para adicionar um recurso ao grupo de recursos, você pode usar o cmdlet **New-AzureRmResource** . No entanto, adicionando um recurso dessa maneira pode causar confusão futura, porque o novo recurso não existe no modelo. Se implantado novamente o modelo antigo, você faria implantar uma solução incompleta. Se você estiver implantando muitas vezes, você encontrará-lo mais fácil e confiável para adicionar o novo recurso ao seu modelo e implantá-lo novamente.

## <a name="move-a-resource"></a>Mover um recurso

Você pode mover recursos existentes para um novo grupo de recursos. Para obter exemplos, consulte [Mover recursos para novo grupo de recursos ou assinatura](resource-group-move-resources.md).

## <a name="export-template"></a>Exportar modelo

Para um grupo de recursos existente (implantado por meio do PowerShell ou um dos outros métodos de como o portal), você pode exibir o modelo do Gerenciador de recursos do grupo de recursos. Exportar o modelo oferece dois benefícios:

1. Você pode facilmente automatizar implantações futuras da solução porque todos da infraestrutura está definido no modelo.

2. Você pode se familiarizar com sintaxe de modelo olhando na JSON JavaScript Object Notation () que representa a sua solução.

Através do PowerShell, você pode gerar um modelo que representa o estado atual do seu grupo de recursos, ou recuperar o modelo que foi usado para uma determinada implantação.

Exportar o modelo para um grupo de recursos é útil quando você tiver feito alterações em um grupo de recursos e precisa recuperar a representação JSON de seu estado atual. No entanto, o modelo gerado contém apenas um número mínimo de parâmetros e nenhuma variáveis. A maioria dos valores no modelo está embutida. Antes de implantar o modelo gerado, você talvez queira converter mais dos valores de parâmetros e você pode personalizar a implantação para ambientes diferentes.

Exportar o modelo para uma determinada implantação é útil quando você precisa exibir o modelo real que foi usado para implantar recursos. O modelo incluirá todos os parâmetros e variáveis definidas para a implantação original. No entanto, se alguém da sua organização tiver feito alterações ao grupo de recursos fora do que está definido no modelo, esse modelo não representará o estado atual do grupo de recursos.

> [AZURE.NOTE] O recurso de modelo de exportação está no modo de visualização e nem todos os tipos de recurso suportam exportar um modelo. Ao tentar exportar um modelo, você poderá ver o erro afirmando que alguns recursos não foram exportados. Se necessário, você poderá definir manualmente esses recursos no seu modelo após fazer o download.

###<a name="export-template-from-resource-group"></a>Exportar modelo do grupo de recursos

Para exibir o modelo para um grupo de recursos, execute o cmdlet **AzureRmResourceGroup de exportação** .

    Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json
    
###<a name="download-template-from-deployment"></a>Baixar o modelo de implantação

Para baixar o modelo usado para uma determinada implantação, execute o cmdlet **AzureRmResourceGroupDeploymentTemplate salvar** .

    Save-AzureRmResourceGroupDeploymentTemplate -DeploymentName azuredeploy -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\azuredeploy.json

## <a name="delete-resources-or-resource-group"></a>Excluir recursos ou grupo de recursos

- Para excluir um recurso do grupo de recursos, use o cmdlet **Remove-AzureRmResource** . Esse cmdlet exclui o recurso, mas não exclui o grupo de recursos.

    Este comando remove o site de TestSite do grupo de recursos TestRG1.

        Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- Para excluir um grupo de recursos, use o cmdlet **Remove-AzureRmResourceGroup** . Esse cmdlet exclui o grupo de recursos e seus recursos.

        Remove-AzureRmResourceGroup -Name TestRG1
        
    Você será solicitado a confirmar a exclusão.
        
        Confirm
        Are you sure you want to remove resource group 'TestRG1'
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

## <a name="deployment-script"></a>Script de implantação

Os exemplos de implantação anteriores neste tópico mostrava somente os cmdlets individuais necessários para implantar recursos no Azure. O exemplo a seguir mostra um script de implantação que cria o grupo de recursos e implanta os recursos.

    <#
      .SYNOPSIS
      Deploys a template to Azure
      
      .DESCRIPTION
      Deploys an Azure Resource Manager template

      .PARAMETER subscriptionId
      The subscription id where the template will be deployed.

      .PARAMETER resourceGroupName
      The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

      .PARAMETER resourceGroupLocation
      Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

      .PARAMETER deploymentName
      The deployment name.

      .PARAMETER templateFilePath
      Optional, path to the template file. Defaults to template.json.

      .PARAMETER parametersFilePath
      Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    #>

    param(
      [Parameter(Mandatory=$True)]
      [string]
      $subscriptionId,

      [Parameter(Mandatory=$True)]
      [string]
      $resourceGroupName,

      [string]
      $resourceGroupLocation,

      [Parameter(Mandatory=$True)]
      [string]
      $deploymentName,

      [string]
      $templateFilePath = "template.json",

      [string]
      $parametersFilePath = "parameters.json"
    )

    #******************************************************************************
    # Script body
    # Execution begins here 
    #******************************************************************************
    $ErrorActionPreference = "Stop"

    # sign in
    Write-Host "Logging in...";
    Add-AzureRmAccount;

    # select subscription
    Write-Host "Selecting subscription '$subscriptionId'";
    Set-AzureRmContext -SubscriptionID $subscriptionId;

    #Create or check for existing resource group
    $resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
    if(!$resourceGroup)
    {
      Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
      if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
      }
      Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
      New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
    }
    else{
      Write-Host "Using existing resource group '$resourceGroupName'";
    }

    # Start the deployment
    Write-Host "Starting deployment...";
    if(Test-Path $parametersFilePath) {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
    } else {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
    }

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a criação de modelos do Gerenciador de recursos, consulte [Criação de modelos do Azure Gerenciador de recursos](./resource-group-authoring-templates.md).
- Para saber sobre como implantar modelos, consulte [implantar um aplicativo com modelo de Gerenciador de recursos do Azure](./resource-group-template-deploy.md).
- Para obter um exemplo detalhado de implantação de um projeto, consulte [implantar microservices previsíveis no Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Para saber mais sobre uma implantação que falha de solução de problemas, consulte [implantações de grupo de recursos de solução de problemas no Azure](./resource-manager-troubleshoot-deployments-powershell.md).

