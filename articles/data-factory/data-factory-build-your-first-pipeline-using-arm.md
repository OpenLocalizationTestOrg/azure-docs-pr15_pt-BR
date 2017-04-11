<properties
    pageTitle="Criar sua primeira fábrica de dados (modelo do Gerenciador de recursos) | Microsoft Azure"
    description="Neste tutorial, você cria um pipeline de fábrica do Azure dados de amostra usando um modelo do Gerenciador de recursos do Azure."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/12/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-azure-data-factory-using-azure-resource-manager-template"></a>Tutorial: Criar sua primeira fábrica de dados Azure usando o modelo do Gerenciador de recursos do Azure
> [AZURE.SELECTOR]
- [Visão geral e os pré-requisitos](data-factory-build-your-first-pipeline.md)
- [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [O Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modelo do Gerenciador de recursos](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

Neste artigo, você pode usar um modelo de Gerenciador de recursos do Azure para criar sua primeira fábrica de dados do Azure.

## <a name="prerequisites"></a>Pré-requisitos
- Leia o artigo de [Visão geral do Tutorial](data-factory-build-your-first-pipeline.md) e conclua as etapas de **pré-requisito** .
- Siga as instruções no artigo de [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para instalar a versão mais recente do PowerShell do Azure em seu computador.
- Consulte a [Criação de modelos do Azure Gerenciador de recursos](../resource-group-authoring-templates.md) para saber mais sobre modelos do Gerenciador de recursos do Azure. 

## <a name="in-this-tutorial"></a>Neste tutorial
Entidade | Descrição  
------ | ----------- 
Serviço de armazenamento vinculado do Azure | Vincula a sua conta de armazenamento do Azure para a fábrica de dados. A conta de armazenamento do Azure mantém os dados de entrada e saídos para o pipeline neste exemplo. 
Serviço vinculado do HDInsight sob demanda| Links uma HDInsight sob demanda cluster à fábrica dados. O cluster é criado automaticamente para processar dados e é excluído depois que o processamento é feito.
Dataset de entrada Azure Blob | Refere-se ao serviço de armazenamento do Azure vinculado. O serviço vinculado se refere a uma conta de armazenamento do Azure e o conjunto de dados do Azure Blob Especifica o contêiner, pasta e nome de arquivo no armazenamento que contém os dados de entrada. 
Conjunto de dados de saída do Azure Blob | Refere-se ao serviço de armazenamento do Azure vinculado. O serviço vinculado se refere a uma conta de armazenamento do Azure e o conjunto de dados do Azure Blob Especifica o contêiner, pasta e nome de arquivo no armazenamento que mantém os dados de saída. 
Pipeline de dados | O pipeline tem uma atividade do tipo HDInsightHive consome do dataset de entrada e produz o conjunto de dados de saída.   

Uma fábrica de dados pode ter um ou mais canais. Um pipeline pode ter uma ou mais atividades. Há dois tipos de atividades: [atividades de movimentação de dados](data-factory-data-movement-activities.md) e [atividades de transformação de dados](data-factory-data-transformation-activities.md). Neste tutorial, você pode criar um pipeline com uma atividade (atividade de cópia).

A seção a seguir fornece o modelo completo do Gerenciador de recursos para definir entidades de fábrica de dados para que você possa rapidamente percorrer o tutorial e testar o modelo. Para entender como cada entidade de fábrica de dados está definida, consulte a seção de [entidades de fábrica de dados no modelo](#data-factory-entities-in-the-template) .

## <a name="data-factory-json-template"></a>Modelo de fábrica JSON de dados
O modelo de Gerenciador de recursos de nível superior para definir uma fábrica de dados é: 

    {
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
                    { ... },
                    { ... },
                    { ... },
                    { ... }
                ]
            }
        ]
    }

Crie um arquivo JSON chamado **ADFTutorialARM.json** na pasta **C:\ADFGetStarted** com o seguinte conteúdo:

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
            "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the input/output data." } },
            "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
            "blobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
            "inputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that has the input file." } },
            "inputBlobName": { "type": "string", "metadata": { "description": "Name of the input file/blob." } },
            "outputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that will hold the transformed data." } },
            "hiveScriptFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that contains the Hive query file." } },
            "hiveScriptFile": { "type": "string", "metadata": { "description": "Name of the hive query (HQL) file." } }
        },
        "variables": {
            "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
            "azureStorageLinkedServiceName": "AzureStorageLinkedService",
            "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
            "blobInputDatasetName": "AzureBlobInput",
            "blobOutputDatasetName": "AzureBlobOutput",
            "pipelineName": "HiveTransformPipeline"
        },
        "resources": [
        {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "2015-10-01",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "West US",
            "resources": [
            {
                "type": "linkedservices",
                "name": "[variables('azureStorageLinkedServiceName')]",
                "dependsOn": [
                    "[variables('dataFactoryName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "type": "AzureStorage",
                    "description": "Azure Storage linked service",
                    "typeProperties": {
                        "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                    }
                }
            },
            {
                "type": "linkedservices",
                "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
                "dependsOn": [
                    "[variables('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "type": "HDInsightOnDemand",
                    "typeProperties": {
                        "clusterSize": 1,
                        "version": "3.2",
                        "timeToLive": "00:05:00",
                        "osType": "windows",
                        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
                    }
                }
            },
            {
                "type": "datasets",
                "name": "[variables('blobInputDatasetName')]",
                "dependsOn": [
                    "[variables('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                    "typeProperties": {
                        "fileName": "[parameters('inputBlobName')]",
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
                        "format": {
                            "type": "TextFormat",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "external": true
                }
            },
            {
                "type": "datasets",
                "name": "[variables('blobOutputDatasetName')]",
                "dependsOn": [
                    "[variables('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
                        "format": {
                            "type": "TextFormat",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Month",
                        "interval": 1
                    }
                }
            },
            {
                "type": "datapipelines",
                "name": "[variables('pipelineName')]",
                "dependsOn": [
                    "[variables('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]",
                    "[variables('hdInsightOnDemandLinkedServiceName')]",
                    "[variables('blobInputDatasetName')]",
                    "[variables('blobOutputDatasetName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "description": "Pipeline that transforms data using Hive script.",
                    "activities": [
                    {
                        "type": "HDInsightHive",
                        "typeProperties": {
                            "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                            "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                            "defines": {
                                "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                                "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                            }
                        },
                        "inputs": [
                            {
                                "name": "[variables('blobInputDatasetName')]"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "[variables('blobOutputDatasetName')]"
                            }
                        ],
                        "policy": {
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Month",
                            "interval": 1
                        },
                        "name": "RunSampleHiveActivity",
                        "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
                    }
                    ],
                    "start": "2016-10-01T00:00:00Z",
                    "end": "2016-10-02T00:00:00Z",
                    "isPaused": false
                }
            }
            ]
        }
        ]
    }

> [AZURE.NOTE] Você pode encontrar outro exemplo de modelo do Gerenciador de recursos para a criação de uma fábrica dados Azure em [Tutorial: criar um pipeline com atividade de cópia usando um modelo do Gerenciador de recursos do Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md).  

## <a name="parameters-json"></a>Parâmetros JSON 
Crie um arquivo JSON chamado **ADFTutorialARM Parameters.json** que contém parâmetros para o modelo do Gerenciador de recursos do Azure.  

> [AZURE.IMPORTANT] Especifique o nome e a chave da sua conta de armazenamento do Azure para os parâmetros **storageAccountName** e **storageAccountKey** neste arquivo de parâmetro. 

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "storageAccountName": {
                "value": "<Name of your Azure Storage account>"
            },
            "storageAccountKey": {
                "value": "<Key of your Azure Storage account>"
            },
            "blobContainer": {
                "value": "adfgetstarted"
            },
            "inputBlobFolder": {
                "value": "inputdata"
            },
            "inputBlobName": {
                "value": "input.log"
            },
            "outputBlobFolder": {
                "value": "partitioneddata"
            },
            "hiveScriptFolder": {
                "value": "script"
            },
            "hiveScriptFile": {
                "value": "partitionweblogs.hql"
            }
        }
    }

> [AZURE.IMPORTANT] Você pode ter arquivos JSON de parâmetro separado para desenvolvimento, teste e ambientes de produção que você pode usar com o mesmo modelo de dados JSON de fábrica. Usando um script de Power Shell, você pode automatizar Implantando entidades de fábrica de dados nesses ambientes. 

## <a name="create-data-factory"></a>Criar fábrica de dados

1. Iniciar o **PowerShell do Azure** e execute o seguinte comando: 
    - Executar `Login-AzureRmAccount` e insira o nome de usuário e senha que você usa para entrar no portal do Azure.  
    - Executar `Get-AzureRmSubscription` para exibir todas as assinaturas para essa conta.
    - Executar `Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext` para selecionar a assinatura à qual você deseja trabalhar. Esta assinatura deve ser o mesmo usado no portal do Azure.
1. Execute o seguinte comando para implantar entidades de fábrica de dados usando o modelo do Gerenciador de recursos que você criou na etapa 1. 

        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFTutorialARM-Parameters.json

## <a name="monitor-pipeline"></a>Pipeline de monitor
 
1.  Após fazer o login no [portal do Azure](https://portal.azure.com/), clique em **Procurar** e selecione **fábricas de dados**.
        ![Procurar -> fábricas de dados](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.  Na lâmina **Fábricas de dados** , clique na fábrica de dados (**TutorialFactoryARM**) que você criou.   
2.  Na lâmina **Fábrica de dados** para sua fábrica de dados, clique em **diagrama**.
        ![Bloco do diagrama](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.  No modo **De exibição de diagrama**de, você vê uma visão geral dos canais e conjuntos de dados usados neste tutorial.
    
    ![Modo de exibição de diagrama](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. No modo de exibição de diagrama, clique duas vezes o conjunto de dados **AzureBlobOutput**. Você verá que a fatia que no momento está sendo processada.

    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Quando o processamento é feito, você verá a fatia no estado **pronto** . Criação de um cluster de HDInsight sob demanda normalmente leva um dia (aproximadamente 20 minutos). Portanto, espere o pipeline leva **aproximadamente 30 minutos** para processar na fatia.

    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png) 
10. Quando a fatia está no estado **pronto** , verifique a pasta **partitioneddata** no contêiner de **adfgetstarted** em seu armazenamento de blob para os dados de saída.  

Consulte [pipeline e conjuntos de dados do Monitor](data-factory-monitor-manage-pipelines.md) para obter instruções sobre como usar os Azure blades portal para monitorar o pipeline e conjuntos de dados que você criou neste tutorial.

Você também pode usar o Monitor e gerenciar aplicativos para monitorar seu pipeline de dados. Consulte [Monitor e gerenciar canais de fábrica de dados do Azure usando o aplicativo de monitoramento](data-factory-monitor-manage-app.md) para obter detalhes sobre como usar o aplicativo. 

> [AZURE.IMPORTANT] O arquivo de entrada é excluído quando a fatia é processada com êxito. Portanto, se você quiser executar novamente na fatia ou faça o tutorial novamente, carregue o arquivo de entrada (input.log) na pasta inputdata do contêiner adfgetstarted.

## <a name="data-factory-entities-in-the-template"></a>Entidades de fábrica de dados no modelo
### <a name="define-data-factory"></a>Definir fábrica de dados
Você pode definir uma fábrica de dados no modelo do Gerenciador de recursos conforme mostrado no exemplo a seguir:  

    "resources": [
    {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US"
    }

O dataFactoryName é definido como: 
      
      "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",

É uma cadeia de caracteres exclusiva com base na ID do grupo de recursos.  

### <a name="defining-data-factory-entities"></a>Definindo entidades de fábrica de dados
As seguintes entidades de fábrica de dados são definidas no modelo JSON: 

- [Serviço de armazenamento vinculado do Azure](#azure-storage-linked-service)
- [Serviço vinculado do HDInsight sob demanda](#hdinsight-on-demand-linked-service)
- [Conjunto de dados de entrada do Azure blob](#azure-blob-input-dataset)
- [Conjunto de dados de saída de blob do Microsoft Azure](#azure-blob-output-dataset)
- [Pipeline de dados com uma atividade de cópia](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Serviço de armazenamento vinculado do Azure
Especifique o nome e a chave da sua conta de armazenamento do Azure nesta seção. Consulte o [armazenamento do Azure vinculado serviço](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter detalhes sobre propriedades JSON usados para definir um serviço de armazenamento do Azure vinculado. 

      {
        "type": "linkedservices",
        "name": "[variables('azureStorageLinkedServiceName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "type": "AzureStorage",
          "description": "Azure Storage linked service",
          "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
          }
        }
      }

**ConnectionString** usa os parâmetros storageAccountName e storageAccountKey. Os valores para esses parâmetros passados usando um arquivo de configuração. A definição também usa variáveis: azureStroageLinkedService e dataFactoryName definidos no modelo. 
    
#### <a name="hdinsight-on-demand-linked-service"></a>Serviço vinculado do HDInsight sob demanda
Consulte o artigo [vinculada de serviços de computação](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes sobre propriedades JSON usados para definir um serviço de vinculada sob demanda HDInsight.  

      {
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "type": "HDInsightOnDemand",
          "typeProperties": {
            "clusterSize": 1,
            "version": "3.2",
            "timeToLive": "00:05:00",
            "osType": "windows",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
          }
        }
      }

Observe os seguintes pontos: 

- Fábrica dados cria um cluster de HDInsight **baseado no Windows** com o JSON acima. Você também poderia ter-criar um cluster de HDInsight **baseado em Linux** . Consulte [Serviço vinculada de HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 
- Você pode usar **seu próprio cluster HDInsight** em vez de usar um cluster de HDInsight sob demanda. Consulte [Serviço vinculada HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
- Cluster HDInsight cria um **contêiner padrão** no armazenamento de blob que você especificou no JSON (**linkedServiceName**). HDInsight não exclui este contêiner quando o cluster é excluído. Esse comportamento é por design. Com o serviço de HDInsight vinculado sob demanda, um HDInsight cluster é criado sempre que uma fatia precisa ser processada a menos que haja uma existente live cluster (**timeToLive**) e é excluído quando o processamento é feito.

    Conforme mais fatias são processadas, você vê muitos contêineres em seu armazenamento de blob do Microsoft Azure. Se você não precisar delas para solução de problemas dos trabalhos, você talvez queira excluí-las para reduzir o custo de armazenamento. Os nomes desses contêineres seguem um padrão: "AAD**yourdatafactoryname**-**linkedservicename**- datetimestamp". Use ferramentas como o [Gerenciador de armazenamento da Microsoft](http://storageexplorer.com/) para excluir contêineres em seu armazenamento de blob do Microsoft Azure.

Consulte [Serviço vinculada de HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.



#### <a name="azure-blob-input-dataset"></a>Conjunto de dados de entrada do Azure blob
Especifique os nomes de contêiner de blob, pasta e arquivo que contém os dados de entrada. Consulte [Propriedades de conjunto de dados do Azure Blob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) para obter detalhes sobre propriedades JSON usados para definir um conjunto de dados do Azure Blob. 

      {
        "type": "datasets",
        "name": "[variables('blobInputDatasetName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "type": "AzureBlob",
          "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
          "typeProperties": {
            "fileName": "[parameters('inputBlobName')]",
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
            "format": {
              "type": "TextFormat",
              "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Month",
            "interval": 1
          },
          "external": true
        }
      }

Essa definição usa os seguintes parâmetros definidos no modelo de parâmetro: blobContainer, inputBlobFolder e inputBlobName. 

#### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída do Azure Blob
Especifique os nomes de contêiner de blob e pasta que contém os dados de saída. Consulte [Propriedades de conjunto de dados do Azure Blob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) para obter detalhes sobre propriedades JSON usados para definir um conjunto de dados do Azure Blob.  

      {
        "type": "datasets",
        "name": "[variables('blobOutputDatasetName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "type": "AzureBlob",
          "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
          "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
            "format": {
              "type": "TextFormat",
              "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Month",
            "interval": 1
          }
        }
      }

Essa definição usa os seguintes parâmetros definidos no modelo de parâmetro: blobContainer e outputBlobFolder. 

#### <a name="data-pipeline"></a>Pipeline de dados
Definir um pipeline que transformar dados executando o script de seção em um cluster de Azurehdinsight sob demanda. Consulte [Pipeline JSON](data-factory-create-pipelines.md#pipeline-json) para descrições dos elementos JSON usados para definir um pipeline neste exemplo. 

    {
        "type": "datapipelines",
        "name": "[variables('pipelineName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]",
          "[variables('hdInsightOnDemandLinkedServiceName')]",
          "[variables('blobInputDatasetName')]",
          "[variables('blobOutputDatasetName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "description": "Pipeline that transforms data using Hive script.",
          "activities": [
            {
              "type": "HDInsightHive",
              "typeProperties": {
                "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                "defines": {
                  "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                  "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                }
              },
              "inputs": [
                {
                  "name": "[variables('blobInputDatasetName')]"
                }
              ],
              "outputs": [
                {
                  "name": "[variables('blobOutputDatasetName')]"
                }
              ],
              "policy": {
                "concurrency": 1,
                "retry": 3
              },
              "scheduler": {
                "frequency": "Month",
                "interval": 1
              },
              "name": "RunSampleHiveActivity",
              "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
            }
          ],
          "start": "2016-10-01T00:00:00Z",
          "end": "2016-10-02T00:00:00Z",
          "isPaused": false
        }
      }

## <a name="reuse-the-template"></a>Reutilizar o modelo 
No tutorial, você criou um modelo para definir entidades de fábrica de dados e um modelo para passar valores para parâmetros. Para usar o mesmo modelo para implantar entidades de fábrica de dados em diferentes ambientes, você cria um arquivo de parâmetro para cada ambiente e usá-lo ao implantar o ambiente.     

Exemplo:  

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json

Observe que o primeiro comando usa o arquivo de parâmetro para o ambiente de desenvolvimento, um segundo para o ambiente de teste e a uma terceira do ambiente de produção.  

Você também pode reutilizar o modelo para executar tarefas repetidas. Por exemplo, você precisa criar várias fábricas de dados com um ou mais canais que implementam a mesma lógica, mas cada fábrica dados usa o armazenamento do Azure diferentes e contas do Azure SQL Database. Neste cenário, você use o mesmo modelo no mesmo ambiente (desenvolvimento, teste ou produção) com arquivos de parâmetros diferentes para criar fábricas de dados. 

## <a name="resource-manager-template-for-creating-a-gateway"></a>Modelo do Gerenciador de recursos para a criação de um gateway
Veja aqui uma amostra de modelo de Gerenciador de recursos para a criação de um gateway lógico no verso. Instalar um gateway em seu computador local ou a máquina virtual do Azure IaaS e registrar o gateway com o serviço de fábrica de dados usando uma chave. Para obter detalhes, consulte [mover dados entre locais e nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
        },
        "variables": {
            "dataFactoryName":  "GatewayUsingArmDF",
            "apiVersion": "2015-10-01",
            "singleQuote": "'"
        },
        "resources": [
            {
                "name": "[variables('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "eastus",
                "resources": [
                    {
                        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                        "type": "gateways",
                        "apiVersion": "[variables('apiVersion')]",
                        "name": "GatewayUsingARM",
                        "properties": {
                            "description": "my gateway"
                        }
                    }            
                ]
            }
        ]
    }

Este modelo cria uma fábrica de dados denominada GatewayUsingArmDF com um gateway denominado: GatewayUsingARM. 

## <a name="see-also"></a>Consulte também
| Tópico | Descrição |
| :---- | :---- |
| [Atividades de transformação de dados](data-factory-data-transformation-activities.md) | Este artigo fornece uma lista de atividades de transformação de dados (como HDInsight seção transformação usada neste tutorial) suportados pelo fábrica de dados do Azure. |
| [Planejamento e à execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspectos de programação e a execução do modelo de aplicativo de fábrica de dados do Azure. |
| [Canais](data-factory-create-pipelines.md) | Este artigo ajuda você a entender canais e atividades em fábrica de dados do Azure e como usá-las para construir ponta a ponta fluxos de trabalho orientados a dados para sua situação ou empresa. |
| [Conjuntos de dados](data-factory-create-datasets.md) | Este artigo ajuda você a entender conjuntos de dados em fábrica de dados do Azure.
| [Monitorar e gerenciar canais usando o aplicativo de monitoramento](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorar, gerenciar e depurar canais usando o monitoramento e gerenciamento de aplicativo. 

  

