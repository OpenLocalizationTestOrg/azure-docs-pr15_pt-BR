<properties
   pageTitle="Criar clusters baseados em Linux Hadoop em HDInsight usando modelos do Gerenciador de recursos do Azure | Microsoft Azure"
    description="Aprenda a criar clusters para Azurehdinsight usando modelos do Gerenciador de recursos do Azure Azure."
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
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="create-linux-based-hadoop-clusters-in-hdinsight-using-azure-resource-manager-templates"></a>Criar clusters baseados em Linux Hadoop em HDInsight usando modelos do Gerenciador de recursos do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Aprenda a criar clusters HDInsight usando modelos de Manager(ARM) de recurso do Azure. Para obter mais informações, consulte [implantar um aplicativo com o Gerenciador de recursos do Azure modelo](../resource-group-template-deploy.md). Para a criação de cluster outros recursos e ferramentas clique em selecionar a guia na parte superior desta página ou consulte [métodos de criação de Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Pré-requisitos:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de começar as instruções neste artigo, você deve ter o seguinte:

- [Assinatura do azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- CLI PowerShell Azure e/ou do Azure

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

### <a name="access-control-requirements"></a>Requisitos de controle de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="resource-manager-templates"></a>Modelos do Gerenciador de recursos

Modelo do Gerenciador de recursos torna mais fácil criar clusters de HDInsight, seus recursos dependentes (como a conta de armazenamento padrão) e outros recursos (como Azure SQL Database usar Apache Sqoop) para seu aplicativo em uma operação única e coordenado. No modelo, você pode define os recursos que são necessários para o aplicativo e especificar parâmetros de implantação para valores para diferentes ambientes de entrada. O modelo consiste em JSON e expressões que você pode usar para construir valores para a sua implantação.

Um modelo do Gerenciador de recursos para criar um cluster de HDInsight e a conta de armazenamento do Azure dependente pode ser encontrado no [Apêndice-A](#appx-a-arm-template). Use várias plataformas [VSCode](https://code.visualstudio.com/#alt-downloads) com a [extensão do Gerenciador de recursos](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) ou um editor de texto para salvar o modelo para um arquivo em sua estação de trabalho. Você aprenderá a chamar o modelo usando diferentes métodos.

Para obter mais informações sobre o modelo do Gerenciador de recursos, consulte

- [Modelos de autor do Gerenciador de recursos do Azure](../resource-group-authoring-templates.md)
- [Implantar um aplicativo com o modelo do Gerenciador de recursos do Azure](../resource-group-template-deploy.md)

Para descobrir o esquema JSON para determinados elementos, você pode seguir este procedimento:

1. Abra o [portal do Azure](https://porta.azure.com) para criar um cluster de HDInsight.  Consulte [baseados em Linux criar clusters em HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).
2. Configure os elementos necessários e os elementos que você precisa o esquema JSON.
3. Antes de clicar em **criar**, clique em **Opções de automação** , conforme mostrado na seguinte captura de tela:

    ![HDInsight Hadoop criar cluster opções de automação de esquema de modelo de Gerenciador de recursos](./media/hdinsight-hadoop-create-linux-clusters-arm-templates/hdinsight-create-cluster-resource-manager-template-automation-option.png)

    O portal cria um modelo do Gerenciador de recursos com base em suas configurações.
## <a name="deploy-with-powershell"></a>Implantar com PowerShell

O procedimento a seguir cria um cluster HDInsight baseados em Linux.

**Implantar um cluster usando o modelo do Gerenciador de recursos**

1. Salve o arquivo de json no [Apêndice A](#appx-a-arm-template) sua estação de trabalho. No script do PowerShell, o nome do arquivo é *C:\HDITutorials-ARM\hdinsight-arm-template.json*.
2. Defina os parâmetros e variáveis, se necessário.
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
        $parameters = @{clusterName="$hdinsightClusterName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

    O script do PowerShell apenas configura o nome do cluster. O nome da conta de armazenamento é codificada no modelo. Você será solicitado a digitar a senha de usuário de cluster (o nome de usuário padrão é *admin*); e a senha do usuário SSH (o nome de usuário SSH padrão é *sshuser*).  
    
Para obter mais informações, consulte [implantar com PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## <a name="deploy-with-azure-cli"></a>Implantar com CLI Azure

O exemplo a seguir cria um cluster e sua conta de armazenamento dependentes e contêiner chamando um modelo do Gerenciador de recursos:

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US"
    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json"
    
Você será solicitado a inserir o nome do cluster, senha de usuário de cluster (o nome de usuário padrão é *administrador*) e a senha do usuário SSH (o nome de usuário SSH padrão é *sshuser*). Para fornecer parâmetros em linha:

    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "c:\Tutorials\HDInsightARM\create-linux-based-hadoop-cluster-in-hdinsight.json" --parameters '{\"clusterName\":{\"value\":\"hdi1229\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"},\"sshPassword\":{\"value\":\"Pass@word1\"}}'

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

O modelo de Gerenciador de recursos do Azure a seguir cria um cluster baseado no Linux Hadoop com a conta de armazenamento do Azure dependentes. 

> [AZURE.NOTE] O exemplo inclui informações de configuração metastore de seção e Oozie metastore.  Remover a seção ou configurar a seção antes de usar o modelo.

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
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
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "sshUserName": {
        "type": "string",
        "defaultValue": "sshuser",
        "metadata": {
            "description": "These credentials can be used to remotely access the cluster."
        }
        },
        "sshPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "location": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
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
            "Australia East",
            "Australia Southeast"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterType": {
        "type": "string",
        "defaultValue": "hadoop",
        "allowedValues": [
            "hadoop",
            "hbase",
            "storm",
            "spark"
        ],
        "metadata": {
            "description": "The type of the HDInsight cluster to create."
        }
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 2,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
    "variables": {
        "defaultApiVersion": "2015-05-01-preview",
        "clusterApiVersion": "2015-03-01-preview",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": { },
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
        {
        "name": "[parameters('clusterName')]",
        "type": "Microsoft.HDInsight/clusters",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('clusterApiVersion')]",
        "dependsOn": [ "[concat('Microsoft.Storage/storageAccounts/',variables('clusterStorageAccountName'))]" ],
        "tags": {

        },
        "properties": {
            "clusterVersion": "3.4",
            "osType": "Linux",
            "tier": "standard",
            "clusterDefinition": {
            "kind": "[parameters('clusterType')]",
            "configurations": {
                "gateway": {
                "restAuthCredential.isEnabled": true,
                "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                },
                "hive-site": {
                    "javax.jdo.option.ConnectionDriverName": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "javax.jdo.option.ConnectionURL": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "javax.jdo.option.ConnectionUserName": "johndole",
                    "javax.jdo.option.ConnectionPassword": "myPassword$"
                },
                "hive-env": {
                    "hive_database": "Existing MSSQL Server database with SQL authentication",
                    "hive_database_name": "myhive20160901",
                    "hive_database_type": "mssql",
                    "hive_existing_mssql_server_database": "myhive20160901",
                    "hive_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "hive_hostname": "myadla0901dbserver.database.windows.net"
                },
                "oozie-site": {
                    "oozie.service.JPAService.jdbc.driver": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "oozie.service.JPAService.jdbc.url": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "oozie.service.JPAService.jdbc.username": "johndole",
                    "oozie.service.JPAService.jdbc.password": "myPassword$",
                    "oozie.db.schema.name": "oozie"
                },
                "oozie-env": {
                    "oozie_database": "Existing MSSQL Server database with SQL authentication",
                    "oozie_database_name": "myhive20160901",
                    "oozie_database_type": "mssql",
                    "oozie_existing_mssql_server_database": "myhive20160901",
                    "oozie_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "oozie_hostname": "myadla0901dbserver.database.windows.net"
                }            
            }
            },
            "storageProfile": {
            "storageaccounts": [
                {
                "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                "isDefault": true,
                "container": "[parameters('clusterName')]",
                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                }
            ]
            },
            "computeProfile": {
            "roles": [
                {
                "name": "headnode",
                "targetInstanceCount": "2",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
                },
                {
                "name": "workernode",
                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
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
