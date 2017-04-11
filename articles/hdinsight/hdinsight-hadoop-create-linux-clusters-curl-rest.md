<properties
    pageTitle="Criar clusters Hadoop, HBase ou tempestade no Linux em HDInsight usando ondulação e a API REST Azure | Microsoft Azure"
    description="Aprenda a criar clusters baseados em Linux HDInsight usando ondulação, modelos do Gerenciador de recursos do Azure e a API REST Azure. Você pode especificar o tipo de cluster (Hadoop, HBase ou tempestade) ou use scripts para instalar componentes personalizados."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

#<a name="create-linux-based-clusters-in-hdinsight-using-curl-and-the-azure-rest-api"></a>Criar clusters baseados em Linux em HDInsight usando ondulação e a API REST do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

A API REST Azure permite executar operações de gerenciamento em serviços hospedados em plataforma Windows Azure, incluindo a criação de novos recursos como clusters HDInsight baseados em Linux. Neste documento, você aprenderá a criar modelos de Gerenciador de recursos do Azure para configurar um cluster de HDInsight e armazenamento associado, em seguida, usar ondulação para implantar o modelo para API REST Azure para criar um novo cluster HDInsight.

> [AZURE.IMPORTANT] As etapas neste documento usam o número padrão de nós de trabalhador (4) para um cluster de HDInsight. Se você planeja mais de 32 nós de trabalho, na criação de cluster ou dimensionando cluster após a criação, você deve selecionar um tamanho de nó principal com pelo menos 8 cores e 14GB de ram.
>
> Para obter mais informações sobre tamanhos de nó e os custos associados, consulte [HDInsight preços](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __CLI azure__. A CLI do Azure é usada para criar um serviço principal, que é usado para gerar tokens de autenticação para solicitações à API REST Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

- __cURL__. Este utilitário está disponível por meio de seu sistema de gerenciamento de pacote, ou pode ser baixado em [http://curl.haxx.se/](http://curl.haxx.se/).

    > [AZURE.NOTE] Se você estiver usando o PowerShell para executar os comandos neste documento, você deve primeiro remover o `curl` alias que ele cria por padrão. Este alias usa chamar-WebRequest, um cmdlet do PowerShell, em vez de ondulação quando você usa o `curl` comando em um prompt de PowerShell e retornarão erros para muitos dos comandos usados neste documento.
    > 
    > Para remover este alias, use o seguinte do prompt do PowerShell:
    >
    > `Remove-item alias:curl`
    >
    > Depois que o alias tenha sido removido, você deve ser capaz de usar a versão do ondulação que você instalou em seu sistema.

### <a name="access-control-requirements"></a>Requisitos de controle de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="create-a-template"></a>Criar um modelo

Os modelos de gerenciamento de recursos Azure estão documentos JSON que descrevem um __grupo de recursos__ e todos os recursos nele (como HDInsight). Essa abordagem de modelo com base permite que você definir todos os recursos que você precisa para HDInsight de um modelo e para gerenciar as alterações no grupo como um todo por meio de __implantações__ que aplicar alterações ao grupo.

Modelos são geralmente fornecidos em duas partes; o próprio modelo e um arquivo de parâmetros que você preencher com valores específicos para sua configuração. Exmaple, o nome do cluster, nome do administrador e senha. Ao usar diretamente a API REST, você deve combinar essas em um único arquivo. O formato deste documento JSON é:

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

Por exemplo, a seguir está uma fusão os arquivos de modelo e parâmetros do [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), que cria um cluster baseado em Linux usando uma senha para proteger a conta de usuário SSH.

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
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
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
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
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

Este exemplo será usado nas etapas neste documento. Você deve substituir os _valores_ do espaço reservado na seção __parâmetros__ no final do documento com os valores que você deseja usar para o seu cluster.

##<a name="login-to-your-azure-subscription"></a>Login à sua assinatura do Azure

Siga as etapas documentadas em [conectar a uma assinatura do Azure a Interface do Azure de linha (Azure comando)](../xplat-cli-connect.md) e conectar-se a sua assinatura usando o `azure login` comando.

##<a name="create-a-service-principal"></a>Criar uma entidade de serviço

> [AZURE.NOTE] Essas etapas são uma versão resumida das informações fornecidas na seção _serviço de autenticar principal com uma senha - Azure CLI_ do documento [autenticar um principal de serviço com o Gerenciador de recursos do Azure](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password---azure-cli) . Estas etapas criam um novo serviço principal que pode ser usado para autenticar as solicitações de API REST usadas para criar recursos Azure como um cluster de HDInsight.

1. No prompt de comando, sessão de terminal ou shell, use o seguinte comando para listar suas assinaturas Azure.

        azure account list
        
    Na lista, selecione a assinatura que você deseja usar e observe a coluna de __identificação__ . Isso é a __ID da assinatura__ e será usado na maioria das etapas neste documento.

2. Crie um novo aplicativo no Active Directory do Azure.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Substitua os valores para a `--name`, `--home-page`, e `--identifier-uris` com seus próprios valores. Fornece uma senha para a nova entrada do Active Directory.
    
    > [AZURE.NOTE] Como você está criando este aplicativo para autenticação por meio de um objeto de serviço, o `--home-page` e `--identifier-uris` valores não precisam fazer referência a uma página da web hospedado na internet; eles precisam ser URIs únicos.
    
    Dos dados retornados, salve o valor de __AppId__ .
    
        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK
    
3. Crie um serviço principal usando o valor de __AppId__ retornado anteriormente.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
     Dos dados retornados, salve o valor de __Id do objeto__ .
     
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
4. Atribua a função de __proprietário__ para o serviço principal usando o valor de __ID do objeto__ retornado anteriormente. Você também deve usar a __ID da assinatura__ obtido anteriormente.
    
        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Owner -c /subscriptions/{SubscriptionID}/
        
    Quando esse comando for concluída, o serviço principal agora tem acesso de proprietário para a identificação de inscrição especificada.

##<a name="get-an-authentication-token"></a>Obter um token de autenticação

1. Use o seguinte para localizar a __Identificação de locatário__ de sua assinatura.

        azure account show -s <subscription ID>
        
    Os dados retornados, encontre a __ID de locatário__.
    
        info:    Executing command account show
        data:    Name                        : MyAzureAccount
        data:    ID                          : 45a1014d-0f27-25d2-b838-b8f373d6d52e
        data:    State                       : Enabled
        data:    Tenant ID                   : 22f988bf-56f1-41af-91ab-3d7cd011db47
        data:    Is Default                  : true
        data:    Environment                 : AzureCloud
        data:    Has Certificate             : No
        data:    Has Access Token            : Yes
        data:    User name                   : myname@contoso.org
        data:    
        info:    account show command OK

2. Gere um novo token de usar a API REST do Azure.

        curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
        -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        --data-urlencode "client_id=AppID" \
        --data-urlencode "grant_type=client_credentials" \
        --data-urlencode "client_secret=password" \
        --data-urlencode "resource=https://management.azure.com/"
    
    Substitua os valores obtido ou usado anteriormente __TenantID__, __AppID__e __senha__ .

    Se essa solicitação for bem-sucedido, você receberá uma resposta de 200 série e corpo da resposta conterá um documento JSON.

    O documento JSON retornado por essa solicitação conterá um elemento chamado __access_token__; o valor desse elemento é o token de acesso que você deve usar para autenticação as solicitações usadas nas próximas seções deste documento.
    
        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

##<a name="create-a-resource-group"></a>Criar um grupo de recursos

Use o seguinte para criar um novo grupo de recursos. Você deve criar o grupo primeiro, antes de criar os recursos como cluster HDInsight. 

* Substitua __SubscriptionID__ com a ID de inscrição recebida durante a criação de capital de serviço.
* Substitua __AccessToken__ com o token de acesso recebido na etapa anterior.
* Substitua o Centro de dados que você deseja criar o grupo de recursos e de recursos, __DataCenterLocation__ . Por exemplo, 'Centro Sul dos EUA'. 
* Substitua __ResourceGroupName__ com o nome que você deseja usar para este grupo:

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

Se essa solicitação for bem-sucedido, você receberá uma resposta de 200 série e o corpo de resposta conterá um documento JSON contendo informações sobre o grupo. O `"provisioningState"` elemento conterá um valor de `"Succeeded"`.

##<a name="create-a-deployment"></a>Criar uma implantação

Use este procedimento para implantar a configuração de cluster (valores modelo e um parâmetro,) ao grupo de recursos.

* Substitua os valores usados anteriormente __SubscriptionID__ e __AccessToken__ . 
* Substitua __ResourceGroupName__ com o nome do grupo de recursos que você criou na seção anterior.
* Substitua __DeploymentName__ com o nome que você deseja usar para essa implantação.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [AZURE.NOTE] Se você tiver salvo o documento JSON que contém o modelo e parâmetros para um arquivo, você pode usar o seguinte, em vez de `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Se essa solicitação for bem-sucedido, você receberá uma resposta de 200 série e o corpo de resposta conterá um documento JSON contendo informações sobre a operação de implantação.

> [AZURE.IMPORTANT] Observe que a implantação foi enviada, mas não foi concluída neste momento. Pode levar alguns minutos, geralmente cerca de 15, para a implantação ser concluída.

##<a name="check-the-status-of-a-deployment"></a>Verificar o status de uma implantação

Para verificar o status da implantação, use o seguinte:

* Substitua os valores usados anteriormente __SubscriptionID__ e __AccessToken__ . 
* Substitua __ResourceGroupName__ com o nome do grupo de recursos que você criou na seção anterior.

```
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

Isso retornará informações de um documento JSON contendo informações sobre a operação de implantação. O `"provisioningState"` elemento conterá o status da implantação; Se isso contiver um valor de `"Succeeded"`, e em seguida, a implantação foi concluída com êxito. Neste ponto, seu cluster deve estar disponível para uso.

##<a name="next-steps"></a>Próximas etapas

Agora que você criou com êxito um cluster de HDInsight, use o seguinte para aprender a trabalhar com seu cluster. 

###<a name="hadoop-clusters"></a>Hadoop clusters

* [Use a seção com HDInsight](hdinsight-use-hive.md)
* [Usar porco com HDInsight](hdinsight-use-pig.md)
* [Usar MapReduce com HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>HBase clusters

* [Introdução ao HBase em HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicativos Java para HBase em HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Clusters de tempestade

* [Desenvolva topologias Java para tempestade em HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usar componentes de Python em tempestade em HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com tempestade em HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)
