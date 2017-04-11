<properties
   pageTitle="Vinculado modelos com o Gerenciador de recursos | Microsoft Azure"
   description="Descreve como usar modelos vinculados em um modelo do Gerenciador de recursos do Azure para criar uma solução de modelo modular. Mostra como passar valores de parâmetros, especifique um arquivo de parâmetro e URLs criados dinamicamente."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/02/2016"
   ms.author="tomfitz"/>

# <a name="using-linked-templates-with-azure-resource-manager"></a>Usando modelos vinculados com o Gerenciador de recursos do Azure

De dentro de um modelo do Gerenciador de recursos do Azure, você pode vincular a outro modelo, que permite que você decompor sua implantação em um conjunto de destino, modelos específicos finalidade. Como com a decomposição de um aplicativo em várias classes de código, Decomposição fornece benefícios em termos de teste, reutilização e legibilidade.  

Você pode passar parâmetros de um modelo principal para um modelo vinculado e esses parâmetros diretamente podem mapear para parâmetros ou variáveis expostos pelo modelo de chamada. O modelo vinculado também pode passar uma variável de saída para o modelo de origem, permitindo que uma troca de dados bidirecional entre modelos.

## <a name="linking-to-a-template"></a>Vincular a um modelo

Criar um vínculo entre dois modelos, adicionando um recurso de implantação dentro do modelo principal que aponta para o modelo vinculado. Você pode definir a propriedade **templateLink** para o URI do modelo vinculado. Você pode fornecer valores de parâmetro para o modelo vinculado, especificando os valores diretamente em seu modelo ou vinculando a um arquivo de parâmetro. O exemplo a seguir usa a propriedade de **parâmetros** para especificar um valor de parâmetro diretamente.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

O serviço do Gerenciador de recursos deve ser capaz de acessar o modelo vinculado. Você não pode especificar um arquivo local ou um arquivo que só está disponível em sua rede local para o modelo vinculado. Você só pode fornecer um valor de URI que inclui **http** ou **https**. Uma opção é colocar seu modelo vinculado em uma conta de armazenamento e usar o URI para esse item, como mostrado no exemplo a seguir.

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }

Embora o modelo vinculado deve estar disponível externamente, ele não precisa estará disponível para o público. Você pode adicionar o seu modelo a uma conta de armazenamento particular acessível para somente o proprietário da conta de armazenamento. Em seguida, você criar um token de assinatura (SAS) de acesso compartilhado para habilitar o acesso durante a implantação. Você pode adicionar esse token SAS para o URI para o modelo vinculado. Para obter etapas sobre como definir um modelo em uma conta de armazenamento e gerando um símbolo SAS, consulte [recursos de implantar com modelos do Gerenciador de recursos e Azure PowerShell](resource-group-template-deploy.md) ou [recursos de implantar com modelos do Gerenciador de recursos e CLI do Azure](resource-group-template-deploy-cli.md). 

O exemplo a seguir mostra um modelo de pai que está vinculado a outro modelo. O modelo vinculado é acessado com um símbolo SAS que é passado como um parâmetro.

    "parameters": {
        "sasToken": { "type": "securestring" }
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "incremental",
              "templateLink": {
                "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
                "contentVersion": "1.0.0.0"
              }
            }
        }
    ],

Embora o token é passado como uma cadeia de caracteres segura, o URI do modelo vinculado, incluindo o token SAS, está conectado as operações de implantação para esse grupo de recursos. Para limitar a exposição, defina uma expiração para o token.

## <a name="linking-to-a-parameter-file"></a>Vinculando a um arquivo de parâmetro

O exemplo a seguir usa a propriedade de **parametersLink** para vincular a um arquivo de parâmetro.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

O valor URI para o arquivo de parâmetro vinculada não pode ser um arquivo local e deve incluir **http** ou **https**. O arquivo de parâmetro também pode ser limitado ao acesso por meio de um token SAS.

## <a name="using-variables-to-link-templates"></a>Usar variáveis para vincular modelos

Os exemplos anteriores mostraram valores codificados de URL para os links de modelo. Essa abordagem pode funcionar para um modelo simples, mas não funciona bem quando trabalhar com um conjunto de modelos modulares grande. Em vez disso, você pode criar uma variável estática que armazena uma URL base para o modelo principal e, em seguida, criar dinamicamente URLs para os modelos vinculados partir desse URL base. A vantagem dessa abordagem é facilmente mova ou bifurcação o modelo porque você só precisa alterar a variável estática no modelo principal. O modelo principal passa os URIs correta em todo o modelo decomposto.

O exemplo a seguir mostra como usar uma URL de base para criar duas URLs para modelos vinculados (**sharedTemplateUrl** e **vmTemplate**). 

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

Você também pode usar [deployment()](resource-group-template-functions.md#deployment) para obter a URL base para o modelo atual e usá-lo para obter a URL para outros modelos no mesmo local. Essa abordagem é útil se muda de seu local de modelo (talvez devido a controle de versão) ou se você deseja evitar a codificação URLs no arquivo de modelo. 

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## <a name="conditionally-linking-to-templates"></a>Vinculando condicionalmente a modelos

Você pode vincular a diferentes modelos, passando em um valor de parâmetro é usado para construir o URI do modelo vinculado. Essa abordagem funciona bem quando você precisa especificar durante a implantação que vinculado modelo deve ser usado. Por exemplo, você pode especificar um modelo para usar uma conta existente do armazenamento e outro modelo para usar em uma nova conta de armazenamento.

O exemplo a seguir mostra um parâmetro para um nome de conta de armazenamento e um parâmetro para especificar se a conta de armazenamento é novo ou existente.

    "parameters": {
        "storageAccountName": {
            "type": "String"
        },
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },

Você cria uma variável para o modelo de URI que inclui o valor do parâmetro novo ou existente.

    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/exampleuser/templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },

Você pode fornecer esse valor variável para o recurso de implantação.

    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "StorageAccountName": {
                        "value": "[parameters('storageAccountName')]"
                    }
                }
            }
        }
    ],

O URI é resolvida como um modelo chamado **existingStorageAccount.json** ou **newStorageAccount.json**. Crie modelos para esses URIs.

O exemplo a seguir mostra o modelo de **existingStorageAccount.json** .

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

O exemplo a seguir mostra o modelo de **newStorageAccount.json** . Observe que, como o armazenamento existente, o objeto de conta de armazenamento de modelo de conta é retornado em saídas. O modelo mestre funciona com o modelo vinculado.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('StorageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {
          }
        }
      ],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('StorageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

## <a name="complete-example"></a>Exemplo completo

Os modelos de exemplo a seguir mostram uma disposição simplificada dos modelos vinculados para ilustrar vários dos conceitos neste artigo. Ele pressupõe que os modelos foram adicionados ao mesmo recipiente em uma conta de armazenamento com acesso público desativado. O modelo vinculado passa um valor para o modelo principal da seção de **saídas** .

O arquivo de **parent.json** consiste em:

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "containerSasToken": { "type": "string" }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "linkedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
              "contentVersion": "1.0.0.0"
            }
          }
        }
      ],
      "outputs": {
        "result": {
          "type": "object",
          "value": "[reference('linkedTemplate').outputs.result]"
        }
      }
    }

O arquivo de **helloworld.json** consiste em:

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {
        "result": {
            "value": "Hello World",
            "type" : "string"
        }
      }
    }
    
No PowerShell, você pode obter um símbolo para o contêiner e implantar os modelos com:

    Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
    $token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ("https://storagecontosotemplates.blob.core.windows.net/templates/parent.json" + $token) -containerSasToken $token

Na CLI do Azure, você obter um símbolo para o contêiner e implantar os modelos com o seguinte código. Atualmente, você deve fornecer um nome para a implantação ao usar um modelo de URI que inclui um token SAS.  

    expiretime=$(date -I'minutes' --date "+30 minutes")  
    azure storage container sas create --container templates --permissions r --expiry $expiretime --json | jq ".sas" -r
    azure group deployment create -g ExampleGroup --template-uri "https://storagecontosotemplates.blob.core.windows.net/templates/parent.json?{token}" -n tokendeploy  

Você será solicitado a fornecer o token SAS como um parâmetro. Você precisa preceda o token com **?**.

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre o define a ordem de implantação para os seus recursos, consulte [Definindo dependências nos modelos do Gerenciador de recursos do Azure](resource-group-define-dependencies.md)
- Para saber como definir um recurso, mas criar várias instâncias dele, consulte [criar várias instâncias de recursos no Gerenciador de recursos do Azure](resource-group-create-multiple.md)
