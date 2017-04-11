<properties
    pageTitle="Implantar um aplicativo web usando MSDeploy com certificado hostname e ssl"
    description="Usar um modelo do Gerenciador de recursos do Azure para implantar um aplicativo web usando MSDeploy e configuração hostname personalizado e um certificado SSL"
    services="app-service\web"
    manager="erikre"
    documentationCenter=""
    authors="jodehavi"
    />

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="john.dehavilland"/>

# <a name="deploy-a-web-app-with-msdeploy-custom-hostname-and-ssl-certificate"></a>Implantar um aplicativo web com MSDeploy, hostname personalizado e certificado SSL

Este guia percorre a criação de uma implantação de ponta a ponta para um aplicativo Web do Azure, aproveitando MSDeploy bem como a adição de um nome de host personalizado e um certificado SSL para o modelo ARM.

Para obter mais informações sobre a criação de modelos, consulte [Criação de modelos do Azure Gerenciador de recursos](../resource-group-authoring-templates.md).

###<a name="create-sample-application"></a>Criar o aplicativo de amostra

Você vai ser Implantando um aplicativo web ASP.NET. A primeira etapa é criar um aplicativo web simples (ou você pode optar por usar um existente - nesse caso, você pode ignorar esta etapa).

Abra o Visual Studio 2015 e escolha Arquivo > Novo projeto. Na caixa de diálogo que aparece, escolha Web > aplicativo Web ASP.NET. Em modelos escolha Web e escolha o modelo MVC. Selecione _Alterar tipo de autenticação_ para _Sem autenticação_. Isso é apenas para tornar o aplicativo de amostra tão simples quanto possível.

Neste ponto, você terá um aplicativo básico de web de ASP.Net pronto para ser usado como parte do seu processo de implantação.

###<a name="create-msdeploy-package"></a>Criar pacote MSDeploy

Próxima etapa é criar o pacote para implantar o aplicativo web do Azure. Para fazer isso, salve seu projeto e, em seguida, execute o seguinte na linha de comando:

    msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

Isso criará um pacote compactado na pasta PackageLocation. O aplicativo está pronto para ser implantado, que você pode criar um modelo do Gerenciador de recursos do Azure fazer isso agora.

###<a name="create-arm-template"></a>Criar modelo BRAÇO
Primeiro, vamos começar com um modelo ARM básico que irá criar um aplicativo da web e um plano de hospedagem (Observe que parâmetros e variáveis não são mostrados para abreviar).

    {
        "name": "[parameters('appServicePlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "apiVersion": "2014-06-01",
        "dependsOn": [ ],
        "tags": {
            "displayName": "appServicePlan"
        },
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "sku": "[parameters('appServicePlanSKU')]",
            "workerSize": "[parameters('appServicePlanWorkerSize')]",
            "numberOfWorkers": 1
        }
    },
    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        }
    }

Em seguida, você precisará modificar o recurso de aplicativo da web para tornar um recurso MSDeploy aninhado. Isso permitirá que você a referência o pacote criado anteriormente e informar o Gerenciador de recursos do Azure usar MSDeploy para implantar o pacote para a Web App Azure. A seguir mostra o recurso de Microsoft.Web/sites com o recurso de MSDeploy aninhado:

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

Agora, você observará que o recurso de MSDeploy leva uma propriedade **packageUri** que é definida da seguinte maneira:

    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

Este **packageUri** leva o uri de conta de armazenamento que aponta para a conta de armazenamento onde você irá carregar seu zip de pacote para. O Gerenciador de recursos do Azure utilizará [Compartilhados assinaturas de acesso](../storage/storage-dotnet-shared-access-signature-part-1.md) para suspenso o pacote localmente da conta de armazenamento quando você implanta o modelo. Esse processo será automatizado por meio de um PowerShell script que carregar o pacote e chame a API de gerenciamento do Azure para criar as teclas obrigatório e passe-os para o modelo como parâmetros (*_artifactsLocation* e *_artifactsLocationSasToken*). Você precisará definir parâmetros para a pasta e nome de arquivo do pacote é carregado sob o recipiente de armazenamento.

Em seguida, você precisa adicionar em outro recurso aninhado para configurar as ligações hostname para utilizar um domínio personalizado. Você precisará primeiro certifique-se de que você possui o nome do host e configurá-lo para ser verificado por Azure que você é o proprietário - consulte [Configurar um nome de domínio personalizado no serviço de aplicativo do Azure](web-sites-custom-domain-name.md). Depois disso, você pode adicionar o seguinte ao seu modelo na seção de recursos Microsoft.Web/sites:

    {
        "apiVersion": "2015-08-01",
        "type": "hostNameBindings",
        "name": "www.yourcustomdomain.com",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
        ],
        "properties": {
            "domainId": null,
            "hostNameType": "Verified",
            "siteName": "variables('webAppName')"
        }
    }

Finalmente, você precisa adicionar outro superior nível recurso, Microsoft.Web/certificates. Este recurso conterá o certificado SSL e continuará a existir no mesmo nível como seu aplicativo web e o plano de hospedagem.

    {
        "name": "[parameters('certificateName')]",
        "apiVersion": "2014-04-01",
        "type": "Microsoft.Web/certificates",
        "location": "[resourceGroup().location]",
        "properties": {
            "pfxBlob": "pfx base64 blob",
            "password": "some pass"
        }
    }

Você precisará ter um certificado SSL válido para configurar esse recurso. Depois que você tiver o certificado válido, em seguida, você precisa extrair os bytes pfx como uma cadeia de caracteres na Base 64. Uma opção para extrair isso é usar o seguinte comando do PowerShell:

    $fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

    [System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

Em seguida, você poderia passar isso como um parâmetro ao seu modelo de implantação de ARM.

Neste ponto, o modelo ARM está pronto.

###<a name="deploy-template"></a>Implantar o modelo

As etapas finais são a juntar todos juntos em uma implantação completa ponta a ponta. Para facilitar a implantação que você pode aproveitar o script do PowerShell de **Implantar AzureResourceGroup.ps1** que é adicionado quando você cria um projeto de grupo de recursos do Azure no Visual Studio para ajudá-lo com o upload de qualquer artefatos necessários no modelo. Ela requer que você criou uma conta de armazenamento que você deseja usar antecedência. Neste exemplo, eu criou uma conta de armazenamento compartilhado para o package.zip a ser carregado. O script utilizará AzCopy para carregar o pacote para a conta de armazenamento. Você passar em seu local de pasta de artefato e o script carregará automaticamente todos os arquivos dentro dessa pasta para o contêiner de armazenamento nomeado. Depois de chamar implantar-AzureResourceGroup.ps1 você precisa atualizar as ligações SSL para mapear o nome de host personalizado com o certificado SSL.

O PowerShell a seguir mostra a implantação completa chamando implantar-AzureResourceGroup.ps1:

    #Set resource group name
    $rgName = "Name-of-resource-group"

    #call deploy-azureresourcegroup script to deploy web app

    .\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
                                    -ResourceGroupName $rgName `
                                    -UploadArtifacts `
                                    -StorageAccountName "name-of-storage-acct-for-package" `
                                    -StorageAccountResourceGroupName "resource-group-name-storage-acct" `
                                    -TemplateFile "web-app-deploy.json" `
                                    -TemplateParametersFile "web-app-deploy-parameters.json" `
                                    -ArtifactStagingDirectory "C:\path\to\packagefolder\"

    #update web app to bind ssl certificate to hostname. This has to be done after creation above.

    $cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

    $ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

    $props = $ar.Properties

    $props.HostNameSslStates[2].'SslState' = 1
    $props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
    $props.hostNameSslStates[2].'toUpdate' = $true

    Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

Neste ponto seu aplicativo deveriam ter sido implantado e será possível procurá-lo por meio de https://www.yourcustomdomain.com
