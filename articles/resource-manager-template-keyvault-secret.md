<properties
   pageTitle="Modelo do Gerenciador de recursos para um segredo em um cofre chave | Microsoft Azure"
   description="Mostra o esquema de Gerenciador de recursos para implantar segredos cofre chave através de um modelo."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="key-vault-secret-template-schema"></a>Esquema de modelo secreta do cofre chave

Cria um segredo que está armazenado em um cofre chave. Esse tipo de recurso com frequência é implantado como um recurso de filho de [chave cofre](resource-manager-template-keyvault.md).

## <a name="schema-format"></a>Formato de esquema

Para criar um segredo cofre chave, adicione o seguinte esquema seu modelo. O segredo pode ser definido como um recurso de filho de um cofre chave ou recurso de nível superior. Você pode defini-lo como um recurso de filho quando o cofre chave for implantado no mesmo modelo. Você precisará definir o segredo como um recurso de nível superior quando o cofre chave não for implantado no mesmo modelo ou quando você precisa criar várias segredos pelo loop no tipo de recurso. 

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## <a name="values"></a>Valores

As tabelas a seguir descrevem os valores que você precisa definir no esquema.

| Nome | Valor |
| ---- | ---- | 
| tipo | Enumeração<br />Necessário<br />**segredos** (quando implantado como um recurso de filho do cofre chave) ou<br /> **Microsoft.KeyVault/vaults/secrets** (quando implantado como um recurso de nível superior)<br /><br />O tipo de recurso para criar. |
| apiVersion | Enumeração<br />Necessário<br />**2015-06-01** ou **2014-12-19-preview**<br /><br />A versão da API utilizar para criar o recurso. | 
| nome | Cadeia de caracteres<br />Necessário<br />Uma única palavra quando implantado como um recurso de filho de um cofre chave ou no formato **{nome-cofre-chave} / {nome do segredo}** quando implantado como um recurso de nível superior a ser adicionada a um cofre de chave existente.<br /><br />O nome do segredo para criar. |
| Propriedades | Objeto<br />Necessário<br />[objeto de propriedades](#properties)<br /><br />Um objeto que especifica o valor do segredo para criar. |
| dependsOn | Matriz<br />Opcional<br />Uma lista separada por vírgulas de um recurso nomes ou identificadores exclusivos do recurso.<br /><br />A coleção de recursos que esse link depende. Se o cofre chave para o segredo for implantado no mesmo modelo, inclua o nome do cofre chave nesse elemento para garantir que ele é implantado pela primeira vez. |

<a id="properties" />
### <a name="properties-object"></a>objeto de propriedades

| Nome | Valor |
| ---- | ---- | 
| valor | Cadeia de caracteres<br />Necessário<br /><br />O valor secreto para armazenar no cofre de chave. Ao passar um valor para essa propriedade, use um parâmetro de tipo **securestring**.  |

    
## <a name="examples"></a>Exemplos

O primeiro exemplo implanta um segredo como um recurso de filho de um cofre chave.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

O segundo exemplo implanta o segredo como um recurso de nível superior que é armazenado em um cofre de chave existente.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the existing vault"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.KeyVault/vaults/secrets",
                "apiVersion": "2015-06-01",
                "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
                "properties": {
                    "value": "[parameters('secretValue')]"
                }
            }
        ],
        "outputs": {}
    }


## <a name="next-steps"></a>Próximas etapas

- Para obter informações gerais sobre compartimentos chaves, consulte [Introdução ao Azure chave cofre](./key-vault/key-vault-get-started.md).
- Para obter um exemplo de referenciar um segredo cofre chave ao implantar modelos, consulte [passar valores seguros durante a implantação](resource-manager-keyvault-parameter.md).


