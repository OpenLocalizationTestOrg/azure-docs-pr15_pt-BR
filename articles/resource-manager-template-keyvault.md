<properties
   pageTitle="Modelo do Gerenciador de recursos para chave cofre | Microsoft Azure"
   description="Mostra o esquema de Gerenciador de recursos para implantar chaves compartimentos por meio de um modelo."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="key-vault-template-schema"></a>Esquema de modelo do cofre chave

Cria um cofre chave.

## <a name="schema-format"></a>Formato de esquema

Para criar um chave cofre, adicione o seguinte esquema a seção de recursos do modelo.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## <a name="values"></a>Valores

As tabelas a seguir descrevem os valores que você precisa definir no esquema.

| Nome | Valor |
| ---- | ---- | 
| tipo | Enumeração<br />Necessário<br />**Microsoft.KeyVault/vaults**<br /><br />O tipo de recurso para criar. |
| apiVersion | Enumeração<br />Necessário<br />**2015-06-01** ou **2014-12-19-preview**<br /><br />A versão da API utilizar para criar o recurso. | 
| nome | Cadeia de caracteres<br />Necessário<br />Um nome que seja exclusivo entre Azure.<br /><br />O nome do cofre chave para criar. Considere a possibilidade de usar a função de [uniqueString](resource-group-template-functions.md#uniquestring) com a convenção de nomenclatura para criar um nome exclusivo, conforme mostrado no exemplo abaixo. |
| local | Cadeia de caracteres<br />Necessário<br />Uma região válida para compartimentos chaves. Para determinar as regiões válidas, veja [compatíveis regiões](resource-manager-supported-services.md#supported-regions).<br /><br />A região para hospedar o cofre chave. |
| Propriedades | Objeto<br />Necessário<br />[objeto de propriedades](#properties)<br /><br />Um objeto que especifica o tipo de chave cofre para criar. |
| recursos | Matriz<br />Opcional<br />Permitido valores: [recursos secretos do Cofre de chave](resource-manager-template-keyvault-secret.md)<br /><br />Recursos de filho para o cofre chave. |

<a id="properties" />
### <a name="properties-object"></a>objeto de propriedades

| Nome | Valor |
| ---- | ---- | 
| enabledForDeployment | Booliano<br />Opcional<br />**verdadeiro** ou **Falso**<br /><br />Especifica se o cofre está habilitado para implantação de máquina Virtual ou tecidos do serviço. |
| enabledForTemplateDeployment | Booliano<br />Opcional<br />**verdadeiro** ou **Falso**<br /><br />Especifica se o cofre está habilitado para uso em implantações de modelo do Gerenciador de recursos. Para obter mais informações, consulte [passar valores seguros durante a implantação](resource-manager-keyvault-parameter.md) |
| enabledForVolumeEncryption | Booliano<br />Opcional<br />**verdadeiro** ou **Falso**<br /><br />Especifica se o cofre está habilitado para criptografia de volume. |
| tenantId | Cadeia de caracteres<br />Necessário<br />**Identificador exclusivo globalmente**<br /><br />O identificador de locatário para a assinatura. Você poderá recuperá-la com o cmdlet do PowerShell [Get-AzureRmSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) ou a **conta do Microsoft azure Mostrar** comando CLI do Azure. |
| accessPolicies | Matriz<br />Necessário<br />[objeto de accessPolicies](#accesspolicies)<br /><br />Uma matriz de até 16 objetos que especificam as permissões do usuário ou entidade de serviço. |
| SKU | Objeto<br />Necessário<br />[objeto de SKU](#sku)<br /><br />O SKU para o cofre chave. |

<a id="accesspolicies" />
### <a name="propertiesaccesspolicies-object"></a>objeto de properties.accessPolicies

| Nome | Valor |
| ---- | ---- | 
| tenantId | Cadeia de caracteres<br />Necessário<br />**Identificador exclusivo globalmente**<br /><br />O identificador de locatário do locatário Azure Active Directory que contém a **ID do objeto** nesta política de acesso |
| ID do objeto | Cadeia de caracteres<br />Necessário<br />**Identificador exclusivo globalmente**<br /><br />O identificador de objeto do usuário do Active Directory do Azure ou entidade de serviço que terão acesso ao cofre. Você pode recuperar o valor do [Get-AzureRmADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) ou os cmdlets do PowerShell [Get-AzureRmADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx) ou os **usuário do azure ad** ou **sp azure ad** Azure comandos. |
| permissões | Objeto<br />Necessário<br />[objeto de permissões](#permissions)<br /><br />As permissões concedidas cofre para o objeto do Active Directory. |

<a id="permissions" />
### <a name="propertiesaccesspoliciespermissions-object"></a>objeto de properties.accessPolicies.permissions

| Nome | Valor |
| ---- | ---- | 
| chaves | Matriz<br />Necessário<br />**todos os**, **backup**, **criar**, **descriptografar**, **Excluir**, **criptografar**, **obter**, **Importar**, **lista**, **Restaurar**, **entrada**, **unwrapkey**, **Atualizar**, **Verifique se**, **wrapkey**<br /><br />As permissões concedidas teclas no cofre para esse objeto do Active Directory. Este valor deve ser especificado como uma matriz de um ou mais valores permitidos. |
| segredos | Matriz<br />Necessário<br />**tudo**, **Excluir**, **obter**, **lista**, **Definir**<br /><br />As permissões concedidas segredos no cofre para esse objeto do Active Directory. Este valor deve ser especificado como uma matriz de um ou mais valores permitidos. |

<a id="sku" />
### <a name="propertiessku-object"></a>objeto de Properties.SKU

| Nome | Valor |
| ---- | ---- | 
| nome | Enumeração<br />Necessário<br />**padrão**ou **premium** <br /><br />O nível de serviço de KeyVault usar.  Padrão é compatível com segredos e teclas protegido por software.  Premium adiciona suporte para teclas protegidos por HSM. |
| família | Enumeração<br />Necessário<br />**R** <br /><br />A família sku usar. |
 
    
## <a name="examples"></a>Exemplos

O exemplo a seguir implanta um cofre chave e um segredo.

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

## <a name="quickstart-templates"></a>Modelos de início rápido

O modelo de início rápido a seguir implanta um cofre chave.

- [Criar chave cofre](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)


## <a name="next-steps"></a>Próximas etapas

- Para obter informações gerais sobre compartimentos chaves, consulte [Introdução ao Azure chave cofre](./key-vault/key-vault-get-started.md).
- Para obter um exemplo de referenciar um segredo cofre chave ao implantar modelos, consulte [passar valores seguros durante a implantação](resource-manager-keyvault-parameter.md).

