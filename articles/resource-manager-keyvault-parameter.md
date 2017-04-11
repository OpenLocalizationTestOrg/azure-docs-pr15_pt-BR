<properties
   pageTitle="Secreta Cofre de tecla com o modelo do Gerenciador de recursos | Microsoft Azure"
   description="Mostra como passar um segredo de um cofre chave como um parâmetro durante a implantação."
   services="azure-resource-manager,key-vault"
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
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="pass-secure-values-during-deployment"></a>Passar valores seguros durante a implantação

Quando você precisa passar um valor de seguro (como uma senha) como um parâmetro durante a implantação, você pode armazenar esse valor como um segredo em um [Cofre de chave do Azure](./key-vault/key-vault-whatis.md) e referenciar o valor em outros modelos do Gerenciador de recursos. Você pode incluir apenas uma referência para o segredo no seu modelo para que o segredo nunca é exposto e você não precisa inserir manualmente o valor para o segredo sempre que você implantar os recursos. Você especificar quais usuários ou objetos de serviço podem acessar o segredo.  

## <a name="deploy-a-key-vault-and-secret"></a>Implantar uma chave cofre e segredo

Para criar cofre chave que pode ser referenciado de outros modelos do Gerenciador de recursos, você deve definir a propriedade de **enabledForTemplateDeployment** como **true**, e você deve conceder acesso ao usuário ou entidade de serviço que executará a implantação que faz referência o segredo.

Para saber mais sobre implantar uma chave cofre e segredo, consulte [esquema de Cofre de chave](resource-manager-template-keyvault.md) e [esquema secreta de Cofre de chave](resource-manager-template-keyvault-secret.md).

## <a name="reference-a-secret-with-static-id"></a>Referenciar um segredo com identificação estático

Referência o segredo de dentro de um arquivo de parâmetros que passa valores para seu modelo. Você se referir o segredo passando o identificador de recurso do Cofre de chave e o nome do segredo. Neste exemplo, o segredo cofre chave já deve existir e você estiver usando um valor estático para ele identificação do recurso.

    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          }, 
          "secretName": "sqlAdminPassword" 
        } 
      }
    }

Um arquivo de parâmetro inteiro pode parecer com:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sqlsvrAdminLogin": {
          "value": ""
        },
        "sqlsvrAdminLoginPassword": {
          "reference": {
            "keyVault": {
              "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
            },
            "secretName": "adminPassword"
          }
        }
      }
    }

O parâmetro que aceita o segredo deve ser uma **securestring**. O exemplo a seguir mostra as seções relevantes de um modelo que implanta um SQL server que requer uma senha de administrador.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }

## <a name="reference-a-secret-with-dynamic-id"></a>Referenciar um segredo com identificação dinâmico

A seção anterior mostrava como passar uma id de recurso estático para o segredo chave cofre. No entanto, em alguns cenários, você precisará fazer referência a um segredo do cofre chave que varia com base na implantação atual. Nesse caso, você não pode embutir a identificação do recurso no arquivo de parâmetros. Infelizmente, você não pode gerar dinamicamente a identificação do recurso no arquivo de parâmetros porque expressões de modelo não são permitidas no arquivo de parâmetros.

Para gerar dinamicamente a identificação do recurso para um segredo cofre chave, você deve mover o recurso que precisa ser o segredo em um modelo aninhado. No modelo mestre, adicione o modelo aninhado e passar um parâmetro que contém a identificação do recurso gerado dinamicamente.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "vaultName": {
          "type": "string"
        },
        "secretName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "nestedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "adminPassword": {
                "reference": {
                  "keyVault": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
                  },
                  "secretName": "[parameters('secretName')]"
                }
              }
            }
          }
        }
      ],
      "outputs": {}
    }


## <a name="next-steps"></a>Próximas etapas

- Para obter informações gerais sobre compartimentos chaves, consulte [Introdução ao Azure chave cofre](./key-vault/key-vault-get-started.md).
- Para obter informações sobre como usar um cofre chave com uma máquina Virtual, consulte [Considerações de segurança para o Gerenciador de recursos do Azure](best-practices-resource-manager-security.md).
- Para obter exemplos completos de referenciando secretas, consulte [exemplos de chave cofre](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

