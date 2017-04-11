<properties
   pageTitle="Criar clusters baseados no Windows Hadoop em HDInsight usando modelos do Gerenciador de recursos do Azure | Microsoft Azure"
    description="Aprenda a criar clusters para Azurehdinsight usando modelos do Gerenciador de recursos do Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/19/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-resource-manager-templates"></a>Criar clusters baseados no Windows Hadoop em HDInsight usando modelos do Gerenciador de recursos do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Aprenda a criar clusters HDInsight usando modelos do Gerenciador de recursos do Azure. Para obter mais informações, consulte [implantar um aplicativo com o Gerenciador de recursos do Azure modelo](../resource-group-template-deploy.md). Para a criação de cluster outros recursos e ferramentas clique em selecionar a guia na parte superior desta página ou consulte [métodos de criação de Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Pré-requisitos:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Antes de começar as instruções neste artigo, você deve ter o seguinte:

- [Assinatura do azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- PowerShell Azure ou Azure CLI

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)] 

### <a name="access-control-requirements"></a>Requisitos de controle de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="resource-manager-templates"></a>Modelos do Gerenciador de recursos

Modelo do Gerenciador de recursos torna mais fácil criar clusters de HDInsight, seus recursos dependentes (como a conta de armazenamento padrão) e outros recursos (como Azure SQL Database usar Apache Sqoop) para seu aplicativo em uma operação única e coordenado. No modelo, você pode define os recursos que são necessários para o aplicativo e especificar parâmetros de implantação para valores para diferentes ambientes de entrada. O modelo consiste em JSON e expressões que você pode usar para construir valores para a sua implantação.

Um modelo do Gerenciador de recursos para criar um cluster de HDInsight e a conta de armazenamento do Azure dependente pode ser encontrado no [Apêndice-A](#appx-a-arm-template). Use um editor de texto para salvar o modelo para um arquivo em sua estação de trabalho. Você aprenderá a chamar o modelo usando várias ferramentas.

Para obter mais informações sobre o modelo do Gerenciador de recursos, consulte

- [Modelos de autor do Gerenciador de recursos do Azure](../resource-group-authoring-templates.md)
- [Implantar um aplicativo com o modelo do Gerenciador de recursos do Azure](../resource-group-template-deploy.md)


## <a name="deploy-with-powershell"></a>Implantar com PowerShell

O procedimento a seguir cria um cluster de HDInsight.

**Implantar um cluster usando o modelo do Gerenciador de recursos**

1. Salve o arquivo de json no [Apêndice A](#appx-a-arm-template) sua estação de trabalho.
2. Defina os parâmetros, se necessário.
3. Execute o modelo usando o seguinte script do PowerShell:

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>" 
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and varialbes
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName";clusterStorageAccountName="$defaultStorageAccountName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

    O script do PowerShell apenas configura o nome do cluster e o nome da conta de armazenamento.  Você pode definir outros valores no modelo do Gerenciador de recursos. 
    
Para obter mais informações, consulte [implantar com PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## <a name="deploy-with-azure-cli"></a>Implantar com CLI Azure

O exemplo a seguir cria um cluster e sua conta de armazenamento dependentes e contêiner chamando um modelo do Gerenciador de recursos:

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US 2"
    azure group deployment create "hdi1229rg" "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json" -p "{\"clusterName\":{\"value\":\"hdi1229win\"},\"clusterStorageAccountName\":{\"value\":\"hdi1229store\"},\"location\":{\"value\":\"East US 2\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"}}"





## <a name="deploy-with-rest-api"></a>Implantar com a API REST

Consulte [implantar com a API REST](../resource-group-template-deploy.md#deploy-with-the-rest-api).

## <a name="deploy-with-visual-studio"></a>Implantar com o Visual Studio

Com o Visual Studio, você pode criar um projeto de grupo de recursos e implantá-lo no Azure por meio da interface de usuário. Selecionar o tipo de recursos para incluir em seu projeto e os recursos são adicionados automaticamente ao modelo de Gerenciador de recursos. O projeto também fornece um script do PowerShell para implantar o modelo.

Para obter uma introdução ao uso do Visual Studio com grupos de recursos, consulte [Criando e implantando grupos de recursos Azure por meio do Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

##<a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu várias maneiras de criar um cluster de HDInsight. Para saber mais, consulte os seguintes artigos:


- Para um exemplo de implantação de recursos por meio da biblioteca de cliente .NET, consulte [implantar recursos usando bibliotecas .NET e um modelo](../virtual-machines/virtual-machines-windows-csharp-template.md).
- Para um exemplo detalhado de implantar um aplicativo, consulte [provisionar e implantar microservices previsíveis no Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
- Para obter orientações sobre como implantar sua solução para diferentes ambientes, consulte [ambientes de desenvolvimento e teste no Microsoft Azure](../solution-dev-test-environments.md).
- Para saber mais sobre as seções do modelo de Gerenciador de recursos do Azure, consulte [modelos de criação de páginas](../resource-group-authoring-templates.md).
- Para obter uma lista das funções que você pode usar em um modelo do Gerenciador de recursos do Azure, consulte [funções de modelo](../resource-group-template-functions.md).



##<a name="appx-a-resource-manager-template"></a>Modelo do Gerenciador de recursos de AppX-r:

O modelo de Gerenciador de recursos do Azure a seguir cria um cluster Hadoop baseado no Windows com a conta de armazenamento do Azure dependentes.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
        "type": "string",
        "defaultValue": "East US 2",
        "allowedValues": [
            "Central US",
            "North Europe",
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Brizil South",
            "Australia East",
            "Australia Southeast",
            "Central India"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the cluster login."
        }
        },
        "clusterStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "The name of the storage account to be created and be used as the cluster's storage."
        }
        },
        "clusterStorageType": {
        "type": "string",
        "defaultValue": "Standard_LRS",
        "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS"
        ]
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 4,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
        "variables": {},
        "resources": [
            {
            "name": "[parameters('clusterStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2015-05-01-preview",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "accountType": "[parameters('clusterStorageType')]"
            }
            },
            {
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
            ],
            "tags": {},
            "properties": {
                "clusterVersion": "3.2",
                "osType": "Windows",
                "clusterDefinition": {
                "kind": "hadoop",
                "configurations": {
                    "gateway": {
                    "restAuthCredential.isEnabled": true,
                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                    }
                }
                },
                "storageProfile": {
                "storageaccounts": [
                    {
                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                    "isDefault": true,
                    "container": "[parameters('clusterName')]",
                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                    }
                ]
                },
                "computeProfile": {
                "roles": [
                    {
                    "name": "headnode",
                    "targetInstanceCount": "1",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    },
                    {
                    "name": "workernode",
                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    }
                ]
                }
            }
            }
        ],
        "outputs": {
            "cluster": {
            "type": "object",
            "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
            }
        }
    }

