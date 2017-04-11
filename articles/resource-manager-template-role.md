<properties
   pageTitle="Modelo do Gerenciador de recursos para atribuições de função | Microsoft Azure"
   description="Mostra o esquema de Gerenciador de recursos para implantar uma atribuição de função através de um modelo."
   services="azure-resource-manager"
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
   ms.date="10/03/2016"
   ms.author="tomfitz"/>

# <a name="role-assignments-template-schema"></a>Esquema de modelo de atribuições de função

Atribui um usuário, grupo ou entidade de serviço a uma função em um escopo especificado.

## <a name="resource-format"></a>Formato de recurso

Para criar uma atribuição de função, adicione o seguinte esquema na seção de recursos do modelo.
    
    {
        "type": string,
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## <a name="values"></a>Valores

As tabelas a seguir descrevem os valores que você precisa definir no esquema.

| Nome | Necessário | Descrição |
| ---- | -------- | ----------- |
| tipo | Sim    | O tipo de recurso para criar.<br /><br /> Grupo de recursos:<br />**Microsoft.Authorization/roleAssignments**<br /><br />Recurso:<br />**{namespace do provedor} / {tipo de recurso} / provedores/roleAssignments** |
| apiVersion |Sim | A versão da API utilizar para criar o recurso.<br /><br /> Use **2015-07-01**. | 
| nome | Sim | Um identificador globalmente exclusivo para a nova atribuição de função. |
| dependsOn | Não | Uma matriz separados por vírgula de um recurso nomes ou identificadores exclusivos do recurso.<br /><br />O conjunto de recursos que desta atribuição de função depende. Se atribuir uma função que com escopo de um recurso e recurso implantado no mesmo modelo, inclua esse nome de recurso nesse elemento para garantir que o recurso é implantado pela primeira vez. |
| Propriedades | Sim | O objeto de propriedades que identifica a definição de função, capital e escopo. |

### <a name="properties-object"></a>objeto de propriedades

| Nome | Necessário | Descrição |
| ---- | -------- | ----------- |
| roleDefinitionId | Sim |  O identificador de uma definição de função existente a ser usado na atribuição de função.<br /><br /> Use o seguinte formato:<br /> **/ subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** |
| principalId | Sim | O identificador exclusivo de um capital existente. Este valor mapas para a id de dentro do diretório e podem apontar para um usuário, principal do serviço ou grupo de segurança. |
| escopo | Não | O escopo no qual essa atribuição de função se aplica ao.<br /><br />Grupos de recursos, use:<br />**/resourceGroups/ /Subscriptions/ {id da assinatura} {nome do grupo recurso}**  <br /><br />Para obter recursos, use:<br />**/ subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** |  |


## <a name="how-to-use-the-role-assignment-resource"></a>Como usar o recurso de atribuição de função

Você pode adicionar uma atribuição de função para seu modelo quando você precisa adicionar um usuário, grupo ou serviço principal a uma função durante a implantação. Atribuições de função são herdadas de níveis superiores do escopo, então se você já tiver adicionado um objeto a uma função no nível de assinatura, você não precisa reatribui-lo para o grupo de recursos ou recurso.

Há muitos valores de identificador, que você precisará fornecer ao trabalhar com atribuições de função. Você pode recuperar os valores através do PowerShell ou CLI do Azure.

### <a name="powershell"></a>PowerShell

O nome da atribuição de função requer um identificador exclusivo. Você pode gerar um novo identificador de **nome** com:

    $name = [System.Guid]::NewGuid().toString()

Você pode recuperar o identificador de definição de função com:

    $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Você pode recuperar o identificador para o capital com um dos seguintes comandos.

Para um grupo chamado **auditores**:

    $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Para um usuário chamado **exampleperson**:

    $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Para um serviço capital chamado **exampleapp**:

    $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 

### <a name="azure-cli"></a>CLI Azure

Você pode recuperar o identificador de definição de função com:

    azure role show Reader --json | jq .[].Id -r

Você pode recuperar o identificador para o capital com um dos seguintes comandos.

Para um grupo chamado **auditores**:

    azure ad group show --search Auditors --json | jq .[].objectId -r

Para um usuário chamado **exampleperson**:

    azure ad user show --search exampleperson --json | jq .[].objectId -r

Para um serviço capital chamado **exampleapp**:

    azure ad sp show --search exampleapp --json | jq .[].objectId -r

## <a name="examples"></a>Exemplos

O seguinte modelo recebe um identificador para uma função e um identificador para um usuário, grupo ou entidade de serviço. Ele atribui a função no nível de grupo de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }



O modelo seguinte cria uma conta de armazenamento e atribui a função de leitor para a conta de armazenamento. Os identificadores de dois grupos e a função de leitor foram incluídos no modelo para simplificar a implantação. Esses valores podem ser recuperados em tempo de implantação através de script e passados como parâmetros.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "roleName": {
          "type": "string"
        },
        "groupToAssign": {
          "type": "string",
          "allowedValues": [
            "Auditors",
            "Limited"
          ]
        }
      },
      "variables": {
        "readerRole": "[concat('/subscriptions/',subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]",
        "scope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Storage/storageAccounts/', variables('storageName'))]",
        "Auditors": "1c272299-9729-462a-8d52-7efe5ece0c5c",
        "Limited": "7c7250f0-7952-441c-99ce-40de5e3e30b5",
        "fullRoleName": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleName'))]"
      },
      "resources": [
        {
          "apiVersion": "2016-01-01",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageName')]",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "tags": {
            "displayName": "MyStorageAccount"
          },
          "properties": {}
        },
        {
          "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
          "apiVersion": "2015-07-01",
          "name": "[variables('fullRoleName')]",
          "dependsOn": [
            "[variables('storageName')]"
          ],
          "properties": {
            "roleDefinitionId": "[variables('readerRole')]",
            "principalId": "[variables(parameters('groupToAssign'))]"
          }
        }
      ]
    }

## <a name="quickstart-templates"></a>Modelos de início rápido

Os modelos a seguir mostram como usar o recurso de atribuição de função:

- [Atribuir função interna ao grupo de recursos](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
- [Atribuir função interna para máquina virtual existente](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
- [Atribuir função interna a várias VMs existentes](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre a estrutura do modelo, consulte [modelos de coautoria Gerenciador de recursos do Azure](resource-group-authoring-templates.md).
- Para obter mais informações sobre controle de acesso baseado em função, consulte [Controle de acesso baseado em função do Azure Active Directory](./active-directory/role-based-access-control-configure.md).
