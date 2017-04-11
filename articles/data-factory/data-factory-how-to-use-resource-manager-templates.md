<properties 
    pageTitle="Modelos de usar o Gerenciador de recursos na Data Factory | Microsoft Azure" 
    description="Saiba como criar e usar modelos do Gerenciador de recursos do Azure para criar entidades de fábrica de dados." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016" 
    ms.author="shlo"/>

# <a name="use-templates-to-create-azure-data-factory-entities"></a>Use modelos para criar entidades de fábrica de dados do Azure

## <a name="overview"></a>Visão geral
Durante o uso do Azure dados fábrica para suas necessidades de integração de dados, você descobrirá que você mesmo reutilizando o mesmo padrão em diferentes ambientes ou implementar a mesma tarefa repetidamente dentro da mesma solução. Modelos o ajudarão a implementar e gerenciar esses cenários de uma maneira fácil. Modelos no Azure Data Factory são ideais para cenários que envolvem reutilização e recorrência.
 
Considere a situação onde uma organização tem 10 plantas de fabricação em todo o mundo. Os logs de cada fábrica são armazenados em um banco de dados do SQL Server local separada. A empresa deseja criar um único data warehouse na nuvem para análise ad-hoc. Ele também quer ter a mesma lógica mas diferentes configurações para ambientes de desenvolvimento, teste e produção. 

Nesse caso, uma tarefa precisa ser repetido no mesmo ambiente, mas com valores diferentes entre as fábricas 10 dados para cada fábrica. Na verdade, **Recorrência** está presente. Modelagem permite a abstração desse fluxo genérico (ou seja, canais ter as mesmas atividades em cada fábrica de dados), mas usa um arquivo de parâmetro separado para cada fábrica.

Além disso, como a organização deseja implantar dessas fábricas 10 dados várias vezes em diferentes ambientes, modelos podem usar este **reutilização** utilizando arquivos de parâmetro separado para ambientes de desenvolvimento, teste e produção.

## <a name="templating-with-azure-resource-manager"></a>Modelos com o Azure Resource Manager
[Gerenciador de recursos do Azure modelos](../azure-resource-manager/resource-group-overview.md#template-deployment) são uma ótima maneira de obter modelagem em fábrica de dados do Azure. Modelos do Gerenciador de recursos definem a infraestrutura e a configuração de sua solução Azure através de um arquivo JSON. Como gerente de recursos do Azure modelos trabalham com serviços todos/maioria Azure, pode ser usado amplamente para gerenciar com facilidade todos os recursos de seus ativos Azure. Consulte [modelos de coautoria Gerenciador de recursos do Azure](../resource-group-authoring-templates.md) para saber mais sobre os modelos de Gerenciador de recursos em geral. 

## <a name="tutorials"></a>Tutoriais
Consulte os seguintes tutoriais para obter instruções passo a passo para criar entidades de fábrica de dados usando o recurso Gerenciador de modelos:

- [Tutorial: Criar um pipeline para copiar dados usando o modelo do Gerenciador de recursos do Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [Tutorial: Criar um pipeline para processar dados usando o modelo do Gerenciador de recursos do Azure](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Modelos de fábrica de dados no Github
Confira os seguintes modelos de início rápido Azure no Github: 

- [Criar uma fábrica de dados para copiar dados do armazenamento de Blob do Azure para o banco de dados do Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
- [Criar uma fábrica de dados com a atividade de seção em cluster Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
- [Criar uma fábrica de dados para copiar dados de vendas para Blobs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)

Fique à vontade para compartilhar seus modelos de fábrica de dados do Azure no [início rápido do Azure](https://azure.microsoft.com/documentation/templates/). Consulte o [guia de contribuição](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) ao desenvolver modelos que podem ser compartilhados por meio deste repositório. 

As seções a seguir fornecem detalhes sobre como definir recursos de fábrica de dados em um modelo do Gerenciador de recursos. 

## <a name="defining-data-factory-resources-in-templates"></a>Definindo os recursos de fábrica de dados em modelos
O modelo de nível superior para definir uma fábrica de dados é:

    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
    {
        "name": "[parameters('dataFactoryName')]",
        "apiVersion": "[variables('apiVersion')]",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "westus",
        "resources": [
        { "type": "linkedservices",
            ...
        },
        {"type": "datasets",
            ...
        },
        {"type": "dataPipelines",
            ...
        }
    }

### <a name="define-data-factory"></a>Definir fábrica de dados

Você pode definir uma fábrica de dados no modelo do Gerenciador de recursos conforme mostrado no exemplo a seguir:

    "resources": [
    {
        "name": "[variables('<mydataFactoryName>')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "East US"
    }

O dataFactoryName é definido em "variáveis" como:

    "dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",

### <a name="define-linked-services"></a>Definir serviços vinculados 
    
    "type": "linkedservices",
    "name": "[variables('<LinkedServiceName>')]",
    "apiVersion": "2015-10-01",
    "dependsOn": [ "[variables('<dataFactoryName>')]" ],
    "properties": {
        ...
    }


Consulte [Serviço de armazenamento de vinculadas](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [Calcular vinculadas serviços](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes sobre as propriedades JSON para o serviço de vinculada específico que você deseja implantar. O parâmetro "dependsOn" Especifica o nome da fábrica dados correspondentes. Um exemplo de definição de um serviço vinculado para o armazenamento do Azure é mostrado na definição de JSON a seguir:

### <a name="define-datasets"></a>Definir conjuntos de dados

    "type": "datasets",
    "name": "[variables('<myDatasetName>')]",
    "dependsOn": [
        "[variables('<dataFactoryName>')]",
        "[variables('<myDatasetLinkedServiceName>')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        ...
    }

Consulte [suporte para armazenamentos de dados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para obter detalhes sobre as propriedades JSON para o tipo de conjunto de dados específicos que deseja implantar. Observação que o parâmetro "dependsOn" especifica nome da fábrica de dados correspondente e armazenamento vinculado serviço. Um exemplo de definir o tipo de conjunto de dados de armazenamento de blob do Microsoft Azure é mostrado na definição de JSON a seguir:

    "type": "datasets",
    "name": "[variables('storageDataset')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('storageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "[variables('storageLinkedServiceName')]",
    "typeProperties": {
        "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
        "fileName": "[parameters('sourceBlobName')]",
        "format": {
            "type": "TextFormat"
        }
    },
    "availability": {
        "frequency": "Hour",
        "interval": 1
    }

### <a name="define-pipelines"></a>Definir canais

    "type": "dataPipelines",
    "name": "[variables('<mypipelineName>')]",
    "dependsOn": [
        "[variables('<dataFactoryName>')]",
        "[variables('<inputDatasetLinkedServiceName>')]",
        "[variables('<outputDatasetLinkedServiceName>')]",
        "[variables('<inputDataset>')]",
        "[variables('<outputDataset>')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        activities: {
            ...
        }
    }

Consulte [Definindo canais](data-factory-create-pipelines.md#pipeline-json) para obter detalhes sobre as propriedades JSON para definir a pipeline específico e atividades que você deseja implantar. Observe o parâmetro "dependsOn" especifica nome da fábrica dados, e qualquer correspondente vinculado serviços ou conjuntos de dados. Um exemplo de um canal que copia dados do armazenamento de Blob do Azure para o banco de dados do Azure SQL é mostrado no seguinte trecho JSON:

    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('azureSqlLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('sqlOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "activities": [
        {
            "name": "CopyFromAzureBlobToAzureSQL",
            "description": "Copy data frm Azure blob to Azure SQL",
            "type": "Copy",
            "inputs": [
                {
                    "name": "[variables('blobInputDatasetName')]"
                }
            ],
            "outputs": [
                {
                    "name": "[variables('sqlOutputDatasetName')]"
                }
            ],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink",
                    "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "columnMappings": "Column0:FirstName,Column1:LastName"
                }
            },
            "Policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }
        ],
        "start": "2016-10-03T00:00:00Z",
        "end": "2016-10-04T00:00:00Z"

## <a name="parameterizing-data-factory-template"></a>Modelo de dados Factory parametrizando
Para as práticas recomendadas de parametrização, consulte o artigo de [práticas recomendadas para a criação de modelos do Gerenciador de recursos do Azure](../resource-manager-template-best-practices.md#parameters) . Em geral, o uso de parâmetro deve ser minimizado, especialmente se variáveis podem ser usadas em vez disso. Apenas fornece parâmetros nas seguintes situações:

- Configurações variar pelo ambiente (exemplo: desenvolvimento, teste e produção)
- Segredos (como senhas)

Se você precisar retirar segredos do [Azure chave cofre](../key-vault/key-vault-get-started.md) ao implantar entidades de fábrica de dados do Azure usando modelos, especifique o **cofre chave** e o **nome secreta** conforme mostrado no exemplo a seguir:

    "parameters": {
        "storageAccountKey": { 
            "reference": {
                "keyVault": {
                    "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
                },
                "secretName": "<secretName>"
            }, 
        },
        ...
    }

> [AZURE.NOTE] Embora exportar modelos para fábricas de dados existente no momento ainda não é suportado, é no works. 


